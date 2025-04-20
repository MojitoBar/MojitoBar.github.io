---
title: "Python 예외 처리 제대로 이해하기 – try-except의 모든 것"
author: mojitobar
date: 2025-04-10 20:49:00 +0900
categories: [Python]
tags: [Python]
comments: true
---

# Python 예외 처리 제대로 이해하기 – try-except의 모든 것

파이썬으로 개발하다 보면 예상치 못한 오류 상황을 마주치게 됩니다. 이런 상황에서 프로그램이 그대로 중단된다면 사용자 경험은 크게 저하됩니다. 예외 처리는 이런 문제를 해결하고 더 안정적인 코드를 작성하는 핵심 기술입니다. 이번 글에서는 기본 개념부터 실전 패턴까지 예외 처리의 모든 것을 살펴보겠습니다.

## 예외 처리의 기본: try-except

예외 처리의 시작점은 `try-except` 구문입니다. 다음은 웹 API에서 데이터를 가져오는 실제 시나리오입니다.

```python
import requests

def get_user_data(user_id):
    try:
        response = requests.get(f"https://api.example.com/users/{user_id}")
        response.raise_for_status()  # 200 OK가 아니면 예외 발생
        return response.json()
    except requests.exceptions.HTTPError as e:
        print(f"HTTP 오류: {e}")
        return None
    except requests.exceptions.ConnectionError:
        print("서버에 연결할 수 없습니다. 네트워크 연결을 확인하세요.")
        return None
    except requests.exceptions.Timeout:
        print("요청 시간이 초과되었습니다. 나중에 다시 시도하세요.")
        return None
    except requests.exceptions.RequestException:
        print("알 수 없는 오류가 발생했습니다.")
        return None
```

이 예제는 API 호출 시 발생할 수 있는 다양한 오류 상황을 각각 처리합니다. 네트워크 연결 실패, 타임아웃, 서버 오류 등 여러 상황에 맞게 사용자에게 적절한 메시지를 제공하고, 프로그램이 계속 실행될 수 있도록 합니다. 실제 서비스에서는 이런 구체적인 예외 처리로 사용자 경험을 크게 향상시킬 수 있습니다.

## 다양한 예외 상황 처리하기

현실 세계의 애플리케이션에서는 사용자 입력 검증이 매우 중요합니다. 이 예제는 온라인 뱅킹 시스템의 송금 기능을 구현한 것입니다.

```python
def 송금하기():
    try:
        출금계좌 = input("출금 계좌번호를 입력하세요: ")
        입금계좌 = input("입금 계좌번호를 입력하세요: ")
        금액 = float(input("송금액을 입력하세요: "))
        
        if not 계좌_존재_확인(출금계좌):
            raise ValueError("출금 계좌가 존재하지 않습니다.")
        
        if not 계좌_존재_확인(입금계좌):
            raise ValueError("입금 계좌가 존재하지 않습니다.")
        
        if 금액 <= 0:
            raise ValueError("송금액은 0보다 커야 합니다.")
        
        잔액 = 계좌_잔액_조회(출금계좌)
        if 잔액 < 금액:
            raise ValueError(f"잔액이 부족합니다. 현재 잔액: {잔액}원")
        
        계좌_출금(출금계좌, 금액)
        계좌_입금(입금계좌, 금액)
        
    except ValueError as e:
        print(f"입력 오류: {e}")
        return False
    except ConnectionError:
        print("은행 서버에 연결할 수 없습니다. 잠시 후 다시 시도해주세요.")
        return False
    else:
        # 성공적으로 완료되었을 때만 실행
        print(f"{출금계좌}에서 {입금계좌}로 {금액}원이 송금되었습니다.")
        송금_알림_보내기(출금계좌, 입금계좌, 금액)
        return True
    finally:
        # 성공/실패 상관없이 항상 실행
        print("송금 처리가 완료되었습니다.")
        트랜잭션_로그_저장(출금계좌, 입금계좌, 금액)
```

이 예제는 송금 과정에서 발생할 수 있는 여러 오류 상황(잘못된 계좌번호, 잔액 부족 등)을 처리합니다. `else` 블록은 성공했을 때만 실행되어 송금 완료 메시지와 알림을 보내고, `finally` 블록은 성공/실패와 관계없이 트랜잭션 로그를 저장합니다. 이런 구조는 금융 거래와 같은 중요한 작업에서 데이터 일관성과 사용자 피드백을 보장하는 데 필수적입니다.

## 의미 있는 예외 만들기: 사용자 정의 예외

전자상거래 시스템에서는 다양한 비즈니스 규칙이 있습니다. 내장 예외만으로는 이런 규칙 위반을 명확히 표현하기 어렵습니다.

```python
class 상품오류(Exception):
    """상품 관련 기본 예외 클래스"""
    pass

class 재고부족오류(상품오류):
    def __init__(self, 상품코드, 요청수량, 현재재고):
        self.상품코드 = 상품코드
        self.요청수량 = 요청수량
        self.현재재고 = 현재재고
        self.발생시간 = datetime.now()
        메시지 = f"상품({상품코드}) 재고 부족: 요청 {요청수량}개, 현재 재고 {현재재고}개"
        super().__init__(메시지)

class 상품품절오류(상품오류):
    def __init__(self, 상품코드):
        self.상품코드 = 상품코드
        self.발생시간 = datetime.now()
        super().__init__(f"상품({상품코드})이 품절되었습니다.")

# 실제 사용 예시
def 장바구니_담기(사용자ID, 상품코드, 수량):
    try:
        상품 = 상품조회(상품코드)
        if not 상품:
            raise ValueError(f"존재하지 않는 상품입니다: {상품코드}")
            
        if 상품["재고"] == 0:
            raise 상품품절오류(상품코드)
            
        if 상품["재고"] < 수량:
            raise 재고부족오류(상품코드, 수량, 상품["재고"])
            
        장바구니_추가(사용자ID, 상품코드, 수량)
        return {"success": True, "message": "장바구니에 상품이 추가되었습니다."}
        
    except 상품품절오류 as e:
        # 품절 상품은 '재입고 알림' 기능 제안
        알림서비스_등록(사용자ID, e.상품코드)
        return {"success": False, "message": str(e), "suggestion": "재입고 알림을 신청하시겠습니까?"}
        
    except 재고부족오류 as e:
        # 재고 부족은 '최대 구매 가능 수량'을 안내
        return {
            "success": False, 
            "message": str(e), 
            "available": e.현재재고,
            "suggestion": f"최대 {e.현재재고}개까지 구매 가능합니다."
        }
```

이 예제에서는 전자상거래 시스템의 '장바구니 담기' 기능을 구현했습니다. 사용자 정의 예외를 통해 '품절'과 '재고 부족'이라는 서로 다른 비즈니스 상황을 명확히 구분하고, 각 상황에 맞는 추가 정보(재고량, 상품코드 등)를 포함합니다. 이렇게 하면 프론트엔드에서 더 정확한 오류 메시지와 적절한 대안(재입고 알림 신청, 수량 조정 등)을 사용자에게 제공할 수 있습니다.

## 체계적인 예외 관리: 예외 계층 구조

대규모 결제 시스템에서는 다양한 오류 상황을 체계적으로 관리해야 합니다. 계층 구조로 예외를 설계하면 관련 예외를 효율적으로 처리할 수 있습니다.

```python
# 결제 시스템 예외 계층 구조
class 결제오류(Exception): pass

class 카드오류(결제오류): pass
class 카드번호오류(카드오류): pass
class 카드만료오류(카드오류): pass
class 카드한도초과(카드오류): pass

class 계좌오류(결제오류): pass
class 계좌번호오류(계좌오류): pass
class 잔액부족오류(계좌오류): pass

class 결제처리오류(결제오류): pass
class 네트워크오류(결제처리오류): pass
class 시스템오류(결제처리오류): pass

# 결제 프로세스 구현
def 결제진행(결제수단, 금액):
    try:
        # 결제 로직...
        if 결제수단["type"] == "카드":
            카드결제(결제수단["카드번호"], 결제수단["만료일"], 결제수단["CVV"], 금액)
        elif 결제수단["type"] == "계좌이체":
            계좌이체(결제수단["은행코드"], 결제수단["계좌번호"], 금액)
        # ...
        
    except 카드오류 as e:
        # 모든 카드 관련 오류 처리
        에러코드 = "CARD_ERROR"
        if isinstance(e, 카드번호오류):
            안내메시지 = "카드 번호를 다시 확인해주세요."
        elif isinstance(e, 카드만료오류):
            안내메시지 = "카드가 만료되었습니다. 다른 카드를 사용해주세요."
        elif isinstance(e, 카드한도초과):
            안내메시지 = "카드 한도를 초과했습니다. 다른 결제 수단을 이용해주세요."
        else:
            안내메시지 = "카드 결제 중 오류가 발생했습니다."
        
        return {"success": False, "error": 에러코드, "message": 안내메시지}
        
    except 계좌오류:
        # 모든 계좌 관련 오류 처리
        # ...
        
    except 결제처리오류:
        # 모든 결제 처리 관련 오류
        # ...
        
    except 결제오류:
        # 그 외 모든 결제 오류
        # ...
```

이 예제는 결제 시스템의 다양한 오류 상황을 계층적으로 정의하고 처리합니다. 이런 구조는 다음과 같은 장점을 가집니다.

1. **코드 중복 감소**: 비슷한 유형의 오류(모든 카드 관련 오류 등)를 한 번에 처리할 수 있습니다.
2. **확장성**: 새로운 결제 방식이나 오류 유형이 추가되어도 기존 구조에 자연스럽게 통합됩니다.
3. **일관성**: 모든 오류가 같은 구조로 처리되어 프론트엔드에 일관된 응답 형식을 제공합니다.

실제 결제 처리와 같은 민감한 시스템에서는 이런 세밀한 예외 설계가 안정성과 사용자 경험에 직접적인 영향을 미칩니다.

## 예외의 원인 추적하기: 예외 연쇄

마이크로서비스 환경에서는 여러 서비스가 연계되어 작동하며, 한 서비스의 오류가 다른 서비스로 전파됩니다. 예외 연쇄를 통해 근본 원인을 추적할 수 있습니다.

```python
def 사용자_프로필_조회(사용자ID):
    try:
        # 사용자 서비스 API 호출
        response = requests.get(f"http://user-service/users/{사용자ID}")
        response.raise_for_status()
        return response.json()
    except requests.exceptions.HTTPError as e:
        if e.response.status_code == 404:
            raise 사용자없음오류(f"ID: {사용자ID}의 사용자를 찾을 수 없습니다.") from e
        elif e.response.status_code == 401:
            raise 인증오류("사용자 서비스 접근 권한이 없습니다.") from e
        else:
            raise 서비스오류("사용자 서비스 오류가 발생했습니다.") from e
    except requests.exceptions.ConnectionError as e:
        raise 서비스연결오류("사용자 서비스에 연결할 수 없습니다.") from e

# 상위 서비스에서 호출
def 대시보드_데이터_조회(사용자ID):
    try:
        사용자정보 = 사용자_프로필_조회(사용자ID)
        거래내역 = 거래_내역_조회(사용자ID)
        알림목록 = 알림_목록_조회(사용자ID)
        
        return {
            "사용자": 사용자정보,
            "거래": 거래내역,
            "알림": 알림목록
        }
    except 사용자없음오류 as e:
        # 원인 예외 정보 접근
        원인 = e.__cause__
        로그.error(f"사용자 조회 실패: {e}, 원인: {원인}")
        
        return {"error": "사용자 정보를 찾을 수 없습니다.", "code": "USER_NOT_FOUND"}
    except (서비스오류, 서비스연결오류) as e:
        로그.error(f"서비스 오류: {e}, 원인: {e.__cause__}")
        알림.긴급알림("사용자 서비스 장애 발생", str(e))
        
        return {"error": "일시적인 서비스 오류가 발생했습니다.", "code": "SERVICE_ERROR"}
```

이 예제에서는 마이크로서비스 아키텍처에서 API 호출 중 발생한 저수준 오류(HTTP 에러, 연결 오류 등)를 더 의미 있는 비즈니스 예외로 변환하면서, `from` 키워드를 사용해 원인 예외를 연결합니다. 이 패턴은 다음과 같은 장점을 가집니다.

1. **디버깅 용이성**: 트레이스백에서 원인 예외까지 모두 확인 가능합니다.
2. **컨텍스트 보존**: 저수준 오류 정보를 잃지 않으면서 고수준 비즈니스 예외로 변환할 수 있습니다.
3. **모니터링 효율성**: 로그에 원인 예외까지 기록되어 시스템 장애 분석이 수월해집니다.

대규모 분산 시스템에서는 이런 예외 연쇄를 통해 복잡한 오류 상황의 근본 원인을 더 빠르게 파악할 수 있습니다.

## 자원 관리와 예외 처리: 컨텍스트 관리자

데이터베이스 작업은 트랜잭션 관리가 중요합니다. 예외 발생 시에도 리소스가 안전하게 정리되어야 합니다.

```python
class DatabaseTransaction:
    def __init__(self, connection_string):
        self.connection_string = connection_string
        self.connection = None
        self.cursor = None
        
    def __enter__(self):
        import psycopg2
        self.connection = psycopg2.connect(self.connection_string)
        self.cursor = self.connection.cursor()
        return self.cursor
        
    def __exit__(self, exc_type, exc_val, exc_tb):
        if exc_type is not None:
            # 예외가 발생하면 롤백
            print(f"트랜잭션 롤백: {exc_val}")
            self.connection.rollback()
        else:
            # 예외가 없으면 커밋
            self.connection.commit()
            print("트랜잭션 커밋 완료")
            
        # 항상 리소스 정리
        if self.cursor:
            self.cursor.close()
        if self.connection:
            self.connection.close()
        
        # 예외를 처리하지 않고 전파 (False 반환)
        return False

# 실제 사용 예시
def 주문_처리(주문정보):
    try:
        with DatabaseTransaction("host=localhost dbname=shop user=admin") as cursor:
            # 재고 확인
            cursor.execute(
                "SELECT stock FROM products WHERE id = %s FOR UPDATE", 
                (주문정보['상품id'],)
            )
            재고 = cursor.fetchone()[0]
            
            if 재고 < 주문정보['수량']:
                raise 재고부족오류(주문정보['상품id'], 주문정보['수량'], 재고)
            
            # 재고 감소
            cursor.execute(
                "UPDATE products SET stock = stock - %s WHERE id = %s",
                (주문정보['수량'], 주문정보['상품id'])
            )
            
            # 주문 생성
            cursor.execute(
                "INSERT INTO orders (user_id, product_id, quantity, amount) VALUES (%s, %s, %s, %s) RETURNING id",
                (주문정보['사용자id'], 주문정보['상품id'], 주문정보['수량'], 주문정보['금액'])
            )
            주문id = cursor.fetchone()[0]
            
            # 위 코드들 중 어느 하나라도 예외가 발생하면 모든 DB 변경이 롤백됩니다
            # 예외가 발생하지 않으면 모든 변경이 커밋됩니다
            
            return {"success": True, "order_id": 주문id}
    
    except 재고부족오류 as e:
        return {"success": False, "message": str(e), "available": e.현재재고}
    except Exception as e:
        로그.error(f"주문 처리 오류: {e}")
        return {"success": False, "message": "주문 처리 중 오류가 발생했습니다."}
```

이 예제는 데이터베이스 트랜잭션 관리를 위한 컨텍스트 관리자를 구현합니다. 트랜잭션 내에서 예외가 발생하면 모든 DB 변경이 자동으로 롤백되고, 성공하면 커밋됩니다. 또한 커넥션과 커서가 항상 정리되도록 보장합니다.

이런 패턴의 장점은 다음과 같습니다.
1. **데이터 일관성**: 트랜잭션 중 어떤 단계에서 오류가 발생해도 데이터베이스 상태가 일관되게 유지됩니다.
2. **리소스 누수 방지**: 예외 발생 여부와 관계없이 DB 연결이 항상 닫힙니다.
3. **코드 구조화**: 트랜잭션의 시작과 종료가 명확하게 구분되어 코드의 가독성이 향상됩니다.

이 패턴은 데이터베이스 작업뿐만 아니라 파일 처리, 네트워크 소켓, 임시 디렉토리 관리 등 리소스 관리가 필요한 모든 상황에 적용할 수 있습니다.

## 로깅과 예외 처리의 통합

로깅과 예외 처리를 효과적으로 통합하면 시스템 문제를 신속하게 진단하고 해결할 수 있습니다.

```python
import logging
import traceback
import json
from datetime import datetime

# 로깅 설정
logging.basicConfig(
    filename='application.log',
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)
logger = logging.getLogger('payment_service')

def process_payment(payment_data):
    try:
        logger.info(f"결제 요청 시작: 사용자 {payment_data['user_id']}, 금액: {payment_data['amount']}")
        
        # 결제 처리 로직...
        verify_card(payment_data['card_number'])
        charge_amount(payment_data['card_number'], payment_data['amount'])
        
        logger.info(f"결제 성공: 주문번호 {payment_data['order_id']}")
        return {"status": "success", "message": "결제가 완료되었습니다."}
        
    except CardValidationError as e:
        # 상세 오류 로깅 - 개발/운영팀용
        error_context = {
            "user_id": payment_data['user_id'],
            "order_id": payment_data['order_id'],
            "card_number": mask_card_number(payment_data['card_number']),
            "error_time": datetime.now().isoformat(),
            "error_type": type(e).__name__
        }
        logger.error(
            f"카드 유효성 검증 실패: {e}",
            extra={"error_context": json.dumps(error_context)},
            exc_info=True  # 트레이스백 포함
        )
        
        # 사용자에게는 간단한 메시지
        return {
            "status": "error",
            "code": "CARD_VALIDATION_FAILED",
            "message": "카드 정보가 올바르지 않습니다. 다시 확인해주세요."
        }
        
    except PaymentProcessingError as e:
        logger.error(f"결제 처리 실패: {e}", exc_info=True)
        
        # 알림 시스템으로 긴급 알림 발송
        send_alert_to_support("결제 처리 실패", {
            "user_id": payment_data['user_id'],
            "order_id": payment_data['order_id'],
            "error": str(e),
            "traceback": traceback.format_exc()
        })
        
        return {
            "status": "error",
            "code": "PAYMENT_FAILED",
            "message": "결제 처리 중 오류가 발생했습니다. 잠시 후 다시 시도해주세요."
        }
        
    except Exception as e:
        # 예상치 못한 오류 - 최대한 상세히 로깅
        logger.critical(
            f"예상치 못한 결제 오류: {e}",
            exc_info=True,
            extra={"payment_data": json.dumps({k: v for k, v in payment_data.items() if k != 'card_number'})}
        )
        
        # 개발팀에 즉시 알림
        send_emergency_alert("결제 시스템 치명적 오류", traceback.format_exc())
        
        return {
            "status": "error",
            "code": "SYSTEM_ERROR",
            "message": "시스템 오류가 발생했습니다. 고객센터로 문의해주세요."
        }
```

이 예제는 결제 처리 시스템에서 로깅과 예외 처리를 통합한 사례입니다. 이 패턴은 다음과 같은 장점을 가집니다.

1. **단계별 로깅**: 주요 단계마다 정보성 로그를 남겨 처리 흐름을 추적할 수 있습니다.
2. **컨텍스트 정보 포함**: 로그에 사용자 ID, 주문 번호 등 문제 해결에 필요한 맥락 정보를 포함합니다.
3. **보안 고려**: 카드번호와 같은 민감한 정보는 마스킹 처리하여 로그에 기록합니다.
4. **예외 유형별 다른 처리**: 예외 종류에 따라 다른 로깅 레벨, 알림 방식, 사용자 메시지를 사용합니다.
5. **트레이스백 포함**: `exc_info=True`를 통해 예외의 전체 스택 트레이스를 로그에 포함합니다.

이런 접근법은 문제 발생 시 신속한 진단과 해결을 가능하게 하며, 특히 결제와 같은 중요한 시스템에서 필수적입니다.

## 마무리

예외 처리는 안정적인 소프트웨어 개발의 핵심입니다. 기본적인 try-except 구문부터 사용자 정의 예외, 예외 연쇄, 컨텍스트 관리자까지, 이런 도구들을 적절히 활용하면 더 견고하고 사용자 친화적인 프로그램을 만들 수 있습니다.

좋은 예외 처리는 단순히 오류를 잡는 것이 아니라, 예상치 못한 상황에서도 프로그램이 일관되고 예측 가능하게 동작하도록 만드는 것입니다. 이는 개발 과정에서뿐만 아니라 운영 환경에서도 큰 차이를 만들어냅니다.

이러한 예외 처리 기법들을 실제 코드에 적용하면서, 점차 더 안정적이고 유지보수하기 쉬운 코드를 작성하는 역량을 키워보세요.
