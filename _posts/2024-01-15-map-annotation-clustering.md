---
title: iOS 맵 어노테이션 클러스터링 문제 해결기
author: mojitobar
date: 2024-01-15 19:15:00 +0900
categories: [iOS, Swift]
tags: [iOS, Swift, MapKit, Annotation, Clustering]
comments: true
---

## 개요

Pins 프로젝트에서 맵 어노테이션을 사용하면서 클러스터링을 구현해야 했는데, 이 과정에서 겪었던 문제와 해결 방법을 공유하려합니다.

## 문제

맵 어노테이션을 사용하면서 클러스터링을 구현하면서 겪었던 문제는 다음과 같습니다.

![Simulator Screen Recording - iPhone 15 - 2024-01-08 at 19 32 22](https://github.com/f-lab-edu/pins/assets/16567811/b32e7dea-e13d-4585-9877-40dc29be8be2)

한 번 게시물을 클릭한 후 다시 맵으로 돌아오면 정상적으로 클러스터링이 되지 않는 문제가 있었습니다.

## 원인

기존에는 클러스터링 핀으로 교체하는 코드를 `MKMapViewDelegate`의 `mapView(_ mapView: MKMapView, viewFor annotation: MKAnnotation)`에서 구현하고 있었습니다.

```swift
// AS IS
mapView.register(PinAnnotationView.self, forAnnotationViewWithReuseIdentifier: PinAnnotationView.identifier)
mapView.register(PinClusterAnnotationView.self, forAnnotationViewWithReuseIdentifier: PinClusterAnnotationView.identifier)

func mapView(_ mapView: MKMapView, viewFor annotation: MKAnnotation) -> MKAnnotationView? {
    ...

    // annotation이 ClusterAnnotation이라면
    if let cluster = annotation as? MKClusterAnnotation {
        let clusterView = mapView.dequeueReusableAnnotationView(withIdentifier: PinClusterAnnotationView.identifier) as? PinClusterAnnotationView
        clusterView?.setClusterCount(count: cluster.memberAnnotations.count)
        return clusterView
    }

    // 아니면 기본 핀 리턴
    ...
}
```

처음에는 맵이 일정 부분 확대 되어 핀이 생성되기 때문에 `MKClusterAnnotation`타입으로 핀이 생성됩니다.

그러나 다른 뷰에서 다시 맵 뷰로 돌아왔을 때는 이미 맵이 확대 되어 있는 상태이기 때문에 기본 핀이 리턴됩니다.

따라서 처음부터 기본 핀 타입으로 지정된 경우 `dequeueReusableAnnotationView` 때문에 계속해서 기본 핀을 재사용하는 것이라는 판단을 하게 되었습니다.

## 해결

먼저 맵 register에 Annotation, ClusterAnnotation 모두 기본 identifier을 지정해주었습니다.

```swift
mapView.register(PinAnnotationView.self, forAnnotationViewWithReuseIdentifier: MKMapViewDefaultAnnotationViewReuseIdentifier)
mapView.register(PinClusterAnnotationView.self, forAnnotationViewWithReuseIdentifier: MKMapViewDefaultClusterAnnotationViewReuseIdentifier)
```

다음으로 `MKMapViewDelegate`의 `mapView(_ mapView: MKMapView, viewFor annotation: MKAnnotation)` 함수를 지우고 각 객체에서 didSet을 활용하도록 수정하였습니다.

```swift
// Cluster Annotation
final class PinClusterAnnotationView: MKAnnotationView, AnnotationIdentifying {
    override var annotation: MKAnnotation? {
        didSet {
            guard let annotation = annotation as? MKClusterAnnotation else { return }
            displayPriority = .defaultHigh
            setLayout()
            setClusterCount(count: annotation.memberAnnotations.count)
        }
    }
}
// Annotation
final class PinAnnotationView: MKAnnotationView, AnnotationIdentifying {
    override var annotation: MKAnnotation? {
        didSet {
            guard annotation is PinAnnotation else { return }
            clusteringIdentifier = "PinCluster"
            setLayout()
            setPinImage()
        }
    }
}
```

이렇게 구현하면 mapKit에서 기본으로 제공하는 Identifier를 사용하기 때문에 재사용 어노테이션을 직접 구현하지 않아도 됩니다.

따라서 자동으로 상태 값이 변경되는 것을 didSet으로 캐치해 각각의 어노테이션 스타일을 적용해주어 문제를 해결할 수 있었습니다.

## 결과

![Simulator Screen Recording - iPhone 15 - 2024-01-08 at 20 17 59](https://github.com/f-lab-edu/pins/assets/16567811/634c9225-fd13-47c5-a42f-48f7dcf401d7)
