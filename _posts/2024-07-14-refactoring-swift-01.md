---
title: 리팩토링 2판 with Swift - 리팩토링 첫 번째 예시
author: mojitobar
date: 2024-07-14 18:51:00 +0900
categories: [Swift, Refactoring]
tags: [Swift, Refactoring]
comments: true
---

## 개요

이번 글에서는 마틴 파울러의 [리팩터링 2판](https://product.kyobobook.co.kr/detail/S000001810241)을 바탕으로, 실제 코드 예제를 통해 리팩터링의 과정과 그 중요성을 살펴보겠습니다.

이 책은 `JavaScript`를 사용한 예제를 다루고 있지만, 여기서는 `Swift`를 사용하여 같은 예제를 다루겠습니다.

## 리팩터링이란?

리팩터링은 기존의 코드 동작은 변경하지 않으면서 내부 구조를 개선하는 과정입니다. 이는 코드의 가독성을 높이고, 유지보수를 쉽게 만들며, 확장성을 향상시키는 데 목적이 있습니다.

마틴 파울러는 
> 좋은 코드를 가늠하는 확실한 방법은 '**얼마나 수정하기 쉬운가**'다.

라고 말합니다. 

이는 리팩터링의 핵심을 잘 설명하고 있습니다. 리팩터링을 통해 우리는 다음과 같은 이점을 얻을 수 있습니다.

- 코드의 구조를 개선하여 이해하기 쉽게 만듭니다.
- 버그를 찾고 수정하기 쉬워집니다.
- 새로운 기능을 추가하기 쉬워집니다.
- 전체적인 개발 속도를 향상시킬 수 있습니다.

그럼 바로 책에서 소개된 예제를 통해 리팩터링의 과정을 살펴보겠습니다.

우리의 예제는 극장 공연료 계산 시스템입니다.

## 초기 코드

먼저, 초기 코드를 살펴보겠습니다.

```swift
func statement(invoice: Invoice, plays: [String: Play]) -> String {
    var totalAmount = 0
    var volumeCredits = 0
    var result = "청구 내역 (고객명: \(invoice.customer))\n"
    let numberFormatter = NumberFormatter()
    numberFormatter.numberStyle = .currency
    numberFormatter.locale = Locale(identifier: "en_US")
    numberFormatter.minimumFractionDigits = 2
    
    func format(_ number: Int) -> String {
        return numberFormatter.string(from: NSNumber(value: Double(number) / 100.0)) ?? ""
    }
    
    for perf in invoice.performances {
        let play = plays[perf.playID]!
        var thisAmount = 0
        
        switch play.type {
        case "tragedy":
            thisAmount = 40000
            if perf.audience > 30 {
                thisAmount += 1000 * (perf.audience - 30)
            }
        case "comedy":
            thisAmount = 30000
            if perf.audience > 20 {
                thisAmount += 10000 + 500 * (perf.audience - 20)
            }
            thisAmount += 300 * perf.audience
        default:
            fatalError("알 수 없는 장르: \(play.type)")
        }
        
        volumeCredits += max(perf.audience - 30, 0)
        if play.type == "comedy" {
            volumeCredits += perf.audience / 5
        }
        
        result += "\(play.name): \(format(thisAmount)) (\(perf.audience)석)\n"
        totalAmount += thisAmount
    }
    
    result += "총액: \(format(totalAmount))\n"
    result += "적립 포인트: \(volumeCredits)점\n"
    return result
}
```
이 코드는 작동은 하지만, 여러 가지 문제점을 가지고 있습니다

- 함수가 너무 길고 복잡합니다.
- 여러 가지 책임을 한 함수에서 처리하고 있습니다.
- 새로운 장르나 규칙을 추가하기 어렵습니다.

## 리팩터링 과정

이제 단계별로 이 코드를 리팩터링해 보겠습니다.

문제점을 해결하는 것에 집중하여 진행하였으며, 아래 과정은 큰 흐름을 보여주는 것입니다.

자세한 단계별 커밋 내역은 [여기](https://github.com/MojitoBar/refactoring-swift)에서 확인할 수 있습니다.

- 함수 추출

먼저, 복잡한 계산 로직을 별도의 함수로 추출합니다

```swift
func amountFor(_ aPerformance: Performance, _ play: Play) -> Int {
    var result = 0
    switch play.type {
    case "tragedy":
        result = 40000
        if aPerformance.audience > 30 {
            result += 1000 * (aPerformance.audience - 30)
        }
    case "comedy":
        result = 30000
        if aPerformance.audience > 20 {
            result += 10000 + 500 * (aPerformance.audience - 20)
        }
        result += 300 * aPerformance.audience
    default:
        fatalError("알 수 없는 장르: \(play.type)")
    }
    return result
}
```

- 변수 인라인화

불필요한 임시 변수를 제거합니다.

```swift
let amount = amountFor(perf, playFor(perf))
```

- 함수 이동

관련 있는 함수들을 그룹화하고 적절한 위치로 이동시킵니다.
```swift
func playFor(_ aPerformance: Performance) -> Play {
    return plays[aPerformance.playID]!
}


func volumCreditsFor(_ aPerformance: Performance) -> Int {
    var result = 0
    result += max(aPerformance.audience - 30, 0)
    if playFor(aPerformance).type == "comedy" {
        result += aPerformance.audience / 5
    }
    return result
}
```

- 반복문 쪼개기

하나의 반복문에서 여러 가지 작업을 수행하던 것을 분리합니다.

```swift
func totalVolumeCredits() -> Int {
    return invoice.performances.reduce(0) { $0 + volumCreditsFor($1) }
}

func totalAmount() -> Int {
    return invoice.performances.reduce(0) { $0 + amountFor($1, playFor($1)) }
}
```

- 다형성 도입

마지막으로, 다형성을 도입하여 새로운 장르를 쉽게 추가할 수 있도록 합니다.
```swift
class PerformanceCalculator {
    var performance: Performance
    var play: Play
    init(_ aPerformance: Performance, _ aPlay: Play) {
        performance = aPerformance
        play = aPlay
    }
    
    func amount() -> Int {
        fatalError("서브클래스에서 처리하도록 설계")
    }
    
    func volumCredits() -> Int {
        return max(performance.audience - 30, 0)
    }
}

class TragedyCalculator: PerformanceCalculator {
    override func amount() -> Int {
        var result = 40000
        if performance.audience > 30 {
            result += 1000 * (performance.audience - 30)
        }
        return result
    }
}

class ComedyCalculator: PerformanceCalculator {
    override func amount() -> Int {
        var result = 30000
        if performance.audience > 20 {
            result += 10000 + 500 * (performance.audience - 20)
        }
        result += 300 * performance.audience
        return result
    }
    
    override func volumCredits() -> Int {
        return super.volumCredits() + performance.audience / 5
    }
}
```

## 리팩터링의 결과

리팩터링 후 우리의 코드는,

- 더 읽기 쉽고 이해하기 쉬워졌습니다.
- 각 기능이 명확히 분리되어 있어 수정이 용이합니다.
- 새로운 장르나 규칙을 추가하기 쉬워졌습니다.
- 테스트하기 쉬워졌습니다.

전체 코드는 [여기](https://github.com/MojitoBar/refactoring-swift)를 참고해주세요.

책 내용을 따라가다보면 너무 잦은 커밋과 테스트를 반복하는게 아닌가 하는 생각이 들었습니다.

그러나 계속해서 저자가 강조하는 것은 동작을 유지한 채, 코드를 더 이해하기 쉽고 수정하기 쉽게 만드는 것이라는 것을 잊지 않아야 한다는 것이었습니다.

또한 이 책의 뒷 부분에서 테스트 코드를 작성하는 방법에 대해 다룬다고 했기 때문에, 아직은 직접 실행해가며 테스트를 진행한 것 같았습니다.

리팩터링 과정에서 개인적으로 궁금한 점이 있었는데, 이 내용은 아래에 추가로 정리하였습니다.

## '반복문 파이프라인으로 바꾸기'가 리팩터링인 이유

책에서는 '**반복문 파이프라인으로 바꾸기**'라는 리팩터링 기법을 소개하고 있습니다.

이 기법은 반복문을 사용하여 데이터를 처리하는 코드를 함수형 스타일로 변경하는 것을 의미합니다.

처음에는 이 방법이 왜 리팩터링에 포함되는지 이해하기 어려웠습니다.

사내 개발 스터디 시간에 이 내용을 공유하면서, 이 기법이 리팩터링에 포함되는 이유에 대해 생각해보았습니다.

결론적으로, '**반복문 파이프라인으로 바꾸기**'는 코드의 역할을 더 명시적으로 만들어 준다는 점에서 리팩터링에 포함되는 것이었습니다.

예를 들어 일반 반복문이 코드를 전부 읽어야만 코드의 역할을 이해할 수 있었다면, 파이프라인으로 변경할 경우 `map`, `filter`, `reduce` 등 그 고차함수의 이름만으로도 코드의 역할을 파악할 수 있습니다.


## 중간 데이터로 옮기는 이유

리팩터링 과정에서 중간 데이터를 통해 데이터를 변환하는 과정이 있었습니다.

굳이 중간 데이터를 사용하지 않고 한 번에 처리하는 것이 더 효율적이지 않을까 하는 생각이 들었는데요,

중간 데이터를 사용하는 이유는 관심사를 분리한다는 점에서 의미가 있습니다.

즉, 데이터 변환과 출력 로직을 명확히 구분하여 코드의 모듈성을 높이기 위함입니다.

예를 들어, 중간 데이터를 사용하게 되면 추후 처리해야하는 데이터가 추가된다면, `createStatementData`에서는 데이터 변환만을 담당하고, `render~~`에서는 출력 로직만을 담당하게 됩니다.

## 강타입 언어에서의 리팩터링

이 책의 예제는 `JavaScript`를 사용하고 있지만, 저는 `Swift`를 사용하여 같은 예제를 다뤘습니다.

`Swift`와 같은 강타입 언어에서는 책의 리팩터링 순서와 같이 바로 객체에 프로퍼티를 수정하거나 타입을 변경하는 것이 어려울 수 있습니다.

그래서 혹시 강타입 언어에서의 리팩터링 방법이나 순서가 다를 수 있지 않을까 하는 생각이 들었습니다.

결론은 당연히 다를 수 있다는 것이었습니다.

하지만 이러한 내용은 매우 사소하거나 디테일한 부분이고, 리팩터링의 핵심은 코드의 품질을 향상시키는 것이기 때문에 결과물에는 큰 차이가 없을 것이라고 생각했습니다.

제가 `Swift`로 진행했어도 똑같이 리팩터링을 진행할 수 있었던 것처럼 말이죠.

## 결론

책에서는 좋은 코드를 가늠하는 확실한 방법은 **‘얼마나 수정하기 쉬운가’**다. 라고 말합니다.

1장의 예제를 통해 리팩터링의 베스트 프랙티스를 배울 수 있었지만, 아직은 어떻게 리팩터링하는게 수정하기 쉬운 방향인지 감이 잡히지 않는 것 같습니다.

이후에는 그러한 감을 잡기 위해 더 많은 예제를 직접 해보는 과정이 필요할 것 같습니다.    