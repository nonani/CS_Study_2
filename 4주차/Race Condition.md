# 🏎️ Race Condition이란?
Race Condition(경쟁 상태)이란 여러 개의 프로세스 또는 스레드가 동시에 같은 자원(데이터, 변수 등)을 접근할 때,  
실행 순서에 따라 예상치 못한 결과가 발생하는 문제를 말합니다.

---

## 💡 쉽게 말해서?
**"두 사람이 하나의 화장실을 동시에 사용하려고 할 때"** 를 생각해보면 됩니다.

- 사람이 한 명만 있다면 화장실을 잘 사용하고 나오겠죠?
- 그런데 두 사람이 동시에 달려가면, 누가 먼저 들어가느냐에 따라 결과가 달라집니다.
- 만약 문을 잠그지 않고 둘 다 들어가면? 혼란이 발생할 겁니다!

이처럼 **공유 자원을 동시에 접근하는 과정에서 실행 순서에 따라 결과가 달라지는 것이 Race Condition**입니다.

---

# 🚗 Race Condition이 실제로 어떻게 문제를 일으킬까? (코드 예제)
두 개의 스레드가 같은 **은행 계좌에서 돈을 출금하는 상황**을 가정해보겠습니다.

- **초기 계좌 잔액:** `1,000원`
- **스레드 A가 500원을 출금**
- **스레드 B도 500원을 출금**
- **정상적인 결과:** `1,000 - 500 - 500 = 0원`

❗ 하지만 **Race Condition**이 발생하면?

```python
import threading

# 초기 계좌 잔액
balance = 1000

# 출금 함수
def withdraw(amount):
    global balance
    if balance >= amount:
        temp = balance  # 현재 잔액을 가져옴
        temp -= amount  # 출금
        balance = temp  # 잔액 업데이트
        print(f"출금 완료! 남은 잔액: {balance}")
    else:
        print("잔액 부족!")

# 두 개의 스레드가 동시에 출금 시도
thread1 = threading.Thread(target=withdraw, args=(500,))
thread2 = threading.Thread(target=withdraw, args=(500,))

thread1.start()
thread2.start()

thread1.join()
thread2.join()
```

### 🛑 실행 결과가 어떻게 될까?
- 정상적으로 동작하면 **잔액이 0원**이 되어야 합니다.
- 하지만 **Race Condition이 발생하면 잔액이 500원이 남아 있는 오류가 생길 수 있습니다.**

### ❗ 왜 이런 일이 벌어질까?
```text
1️⃣ 스레드 A가 balance 값을 가져옴 (1,000원)
2️⃣ 스레드 B도 동시에 balance 값을 가져옴 (1,000원)
3️⃣ 스레드 A가 출금 후 잔액을 500원으로 업데이트
4️⃣ 하지만 스레드 B는 이미 이전 값을(1,000원) 보고 있어서 다시 500원을 출금
5️⃣ 스레드 B가 출금 후 잔액을 500원으로 업데이트
6️⃣ 결과적으로 잔액이 500원이 되어버리는 오류 발생!
```

---

# ❓ Race Condition이 왜 문제일까?
### ✅ **예측할 수 없는 결과**
- 실행할 때마다 다른 결과가 나옴

### ✅ **디버깅이 어렵다**
- 항상 발생하는 문제가 아니고, 특정 타이밍에서만 발생

### ✅ **데이터 무결성 문제**
- 실제 **은행 계좌 시스템**처럼 중요한 데이터를 다룰 때 치명적인 오류 발생

---

# 🔧 그렇다면 어떻게 해결할 수 있을까? (💡 답은 동기화!!)
**동기화(Synchronization)** 는 공유 자원의 **순차적 접근을 보장**해주는 방법입니다.

> **CS 개념에서 "한 번에 한 명만 들어갈 수 있는 화장실"이 "임계 구역(Critical Section)"** 이라고 합니다.  
> 그리고 **"오직 한 명만 접근 가능하게 하는 것"** 을 **상호 배제(Mutual Exclusion)** 라고 합니다.

## 🎯 **임계 구역(Critical Section)의 조건**
1. **Mutual Exclusion(상호 배타)** → 한 번에 한 프로세스만 임계 구역에 진입 가능
2. **Progress(진행)** → 임계 구역이 비어있을 때, 즉시 새로운 프로세스가 들어갈 수 있어야 함
3. **Bounded Waiting(유한 대기)** → 진입 신청 후 대기 시간이 유한해야 함

---

# 🔹 상호 배제를 구현하는 방법
### ✅ **1️⃣ Atomic Operation (가장 간단한 해결법)**
- **CPU에서 한 번에 실행되는 연산을 사용하여 Race Condition 방지**
- 임계 구역에 진입하는 연산을 **중간에 끊기지 않도록 보장**

---

### ✅ **2️⃣ Mutex (뮤텍스)**
- 🔐 **하나의 스레드만 공유 자원 접근 가능** (Lock/Unlock 사용)
- 한 번에 하나만 들어갈 수 있도록 **열쇠를 이용한 제한**
- **비유:** 🚪 **"화장실 열쇠"**

```python
import threading

balance = 1000  # 은행 계좌 잔액
lock = threading.Lock()  # 🔐 뮤텍스 생성

def withdraw(amount):
    global balance
    with lock:  # 🔐 뮤텍스를 사용하여 한 번에 하나의 스레드만 접근 가능
        if balance >= amount:
            balance -= amount
            print(f"출금 완료! 남은 잔액: {balance}")
        else:
            print("잔액 부족!")

thread1 = threading.Thread(target=withdraw, args=(500,))
thread2 = threading.Thread(target=withdraw, args=(500,))

thread1.start()
thread2.start()

thread1.join()
thread2.join()
```
✅ **뮤텍스를 사용하면 Race Condition 없이 올바른 결과를 보장!**

---

### ✅ **3️⃣ Semaphore (세마포어)**
- 🚥 **동시에 여러 개의 스레드 접근 가능 (최대 개수 설정)**
- `Semaphore(n)` → **최대 `n`개의 스레드 접근 가능**
- **비유:** 🚪 **"여러칸의 화장실" (최대 n명 수용 가능)**

```python
import threading
import time

semaphore = threading.Semaphore(2)  # 동시에 최대 2명까지 허용

def use_resource(id):
    with semaphore:  #  세마포어로 동시에 최대 2개만 접근 가능
        print(f"스레드 {id} → 공유 자원 사용 중...")
        time.sleep(1)
        print(f"스레드 {id} → 공유 자원 사용 완료!")

threads = [threading.Thread(target=use_resource, args=(i,)) for i in range(5)]

for t in threads:
    t.start()

for t in threads:
    t.join()
```
✅ **한 번에 최대 2개의 스레드만 실행됨 → 나머지는 대기 후 실행!**

---

# 🎯 결론: Race Condition 방지 방법 비교
| 방법 | 허용 개수 | 사용 목적 | 비유           |
|------|----------|----------|--------------|
| **뮤텍스 (Mutex)** | 1개 | 🔐 하나의 스레드만 공유 자원 사용 가능 | 🚪 화장실 열쇠    |
| **세마포어 (Semaphore)** | n개 | 🚥 여러 개의 스레드 접근 가능 | 🚪 여러 칸의 화장실 |
| **Atomic 연산** | 1개 (중단 불가능) | ⚡ 성능 최적화 | 🚀 초고속 실행    |

✅ **Race Condition을 방지하려면?**  
1️⃣ **"가능하면 Atomic 연산을 사용!"**  
2️⃣ **"여러 개의 스레드가 접근해야 한다면, 세마포어 사용!"**  
3️⃣ **"단 하나의 스레드만 접근해야 한다면, Mutex 사용!"** 🚀


## 🎯 **임계 구역(Critical Section)의 조건**
1. **Mutual Exclusion(상호 배타)** → 한 번에 한 프로세스만 임계 구역에 진입 가능
2. **Progress(진행)** → 임계 구역이 비어있을 때, 즉시 새로운 프로세스가 들어갈 수 있어야 함
3. **Bounded Waiting(유한 대기)** → 진입 신청 후 대기 시간이 유한해야 함

---

## 🎯 **임계 구역(Critical Section)의 조건을 다시 리마인드 해보자**
1. **Mutual Exclusion(상호 배타)** → 한 번에 한 프로세스만 임계 구역에 진입 가능
2. **Progress(진행)** → 임계 구역이 비어있을 때, 즉시 새로운 프로세스가 들어갈 수 있어야 함
3. **Bounded Waiting(유한 대기)** → 진입 신청 후 대기 시간이 유한해야 함

## 🚦 추가 조건: 진행(Progress)과 유한 대기(Bounded Waiting)

### ✅ 1️⃣ 진행(Progress)
- 임계 구역이 비어있다면, 대기 중인 프로세스는 즉시 들어갈 수 있어야 함.
- 만약 누군가가 **임계 구역을 다 사용하고 나갔는데도 새로운 프로세스가 진입하지 못한다면 비효율적** 이를 **교착 상태(데드 락)** 라고 한다.
- 예를 들어, 화장실이 비어있는데도 다른 사람이 눈치만 보고 안 들어가면 낭비가 발생하는 것과 같음.

### ✅ 2️⃣ 유한 대기(Bounded Waiting)
- 한 프로세스가 **무한정 대기하는 것을 방지**해야 함.
- 특정 프로세스가 계속 대기하고, 다른 프로세스만 계속 실행되는 상황을 막아야 한다. 이를 막지 못한 상태를 **기아 상태(Starvation)** 라고한다.
- 예를 들어, 화장실을 10명이 줄 서 있는데, 앞사람이 계속 먼저 들어가고 맨 뒤 사람은 계속 못 들어가는 상황을 막아야 함.
