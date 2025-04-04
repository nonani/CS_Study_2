# 프로세스와 스레드

---

## 📌 프로그램 vs. 프로세스

### 프로그램 (Program)

- 윈도우의 `.exe` 파일이나 Mac의 `.dmg` 파일과 같은 컴퓨터에서 실행할 수 있는 파일
- 아직 파일을 실행하지 않은 상태이기 때문에 **정적 프로그램(Static Program)**, 줄여서 *프로그램*이라고 한다.

### 프로세스 (Process)

- 프로그램을 실행시켜 정적인 프로그램이 동적으로 변하여, 프로그램이 돌아가고 있는 상태
- 즉, 컴퓨터에서 작업 중인 프로그램을 의미

![image](https://github.com/user-attachments/assets/fa016189-b548-4577-bbb6-8c4e04909798)

- 프로세스의 각각의 영역에 대한 설명
  - **Code** : 코드 자체를 구성하는 메모리 영역 (프로그램 명령)
  - **Data** : 전역 변수, 정적 변수, 배열 등의 데이터 (초기화된 데이터)
  - **Stack** : 지역 변수, 매개 변수, 리턴 값 등의 임시 데이터 (**임시 메모리 영역**)
    - Stack은 함수의 호출과 함께 할당되며, 함수의 호출이 완료되면 소멸한다.
    - Stack 영역을 초과하면 Stack Overflow 에러 발생
  - **Heap** : 동적 할당 시 사용 (`new()`, `malloc()` 등)
    - 생성자, 인스턴스와 같은 동적으로 할당되는 데이터들을 위해 존재하는 공간
    - 사용자에 의해 메모리 공간이 동적으로 할당되고 해제된다.

- **코드 영역**과 **데이터 영역**은 선언할 때 그 크기가 결정되는 **정적 영역**이지만, **스택 영역**과 **힙 영역**은 프로세스가 실행되는 동안 크기가 늘어났다 줄어드는 **동적 영역**이다.

![image](https://github.com/user-attachments/assets/d1244e23-7643-44b5-9aa5-00f77aa69963)

- 모든 프로그램은 운영체제가 실행되기 위한 메모리 공간을 할당해줘야 실행할 수 있다.
- 프로그램을 실행하는 순간, 파일은 컴퓨터 메모리에 올라가게 되며, 운영체제로부터 시스템 자원(CPU)을 할당받아 프로그램 코드를 실행한다.

| 프로그램 | 프로세스 |
| --- | --- |
| 어떤 작업을 하기 위해 실행할 수 있는 파일 | 실행되어 작업 중인 컴퓨터 프로그램 |
| 파일이 저장 장치에 있지만 메모리에 올라가 있지 않은 정적인 상태 | 메모리에 적재되고 CPU 자원을 할당받아 프로그램이 실행되고 있는 상태 |

---

## 📌 스레드

### 프로세스의 한계

- 과거에는 프로그램을 실행할 때 프로세스 하나만을 사용해서 프로그램을 실행
- 기술이 발전함에 따라 프로그램이 복잡해지고 다채로워지면서, 프로세스 하나만을 사용하기에는 한계가 발생
- **스레드**는 이러한 프로세스 특성의 한계를 해결하기 위해 탄생

### 스레드란?

- 하나의 프로세스 내에서 동시에 진행되는 작업 갈래, **흐름의 단위**

![image](https://github.com/user-attachments/assets/fd5e3ebe-cda1-4b61-aae0-399a40c91993)

- 하나의 프로세스 안에서 여러 가지 작업의 흐름이 동시에 진행되기 때문에 가능한 것  
  일련의 작업 흐름들을 스레드라고 하며, 여러 개라면 **멀티 스레드**라고 한다.
- 일반적으로 하나의 프로그램은 하나 이상의 프로세스를 가지고, 하나의 프로세스는 반드시 하나 이상의 스레드를 갖는다.  
  즉, 프로세스를 생성하면 기본적으로 하나의 `main` 스레드가 생성된다.

![image](https://github.com/user-attachments/assets/585bc129-9ad8-4665-afad-0ea5fd60d2f1)

- **스레드**는 프로세스 내의 **Code, Data, Heap** 영역을 **공유**하고, **Stack** 영역을 따로 할당받는다.
- 여러 스레드는 한 프로세스 내의 Code, Data, Heap 영역을 공유하지만, **프로세스 간에는** 메모리에 **접근할 수 없다**.
- 스레드는 별도의 레지스터와 스택을 갖고 있으며, 나머지 영역을 공유한다. 따라서 한 스레드가 프로세스의 자원을 변경하면, 다른 스레드도 그 변경 결과를 **즉시 확인**할 수 있다.
![image](https://github.com/user-attachments/assets/da8d20a8-eece-4009-8935-8e2caf549e96)


| 프로세스 | 스레드 |
| --- | --- |
| 운영체제로부터 자원을 할당받은 작업의 단위 | 프로세스가 할당받은 자원을 이용하는 실행 흐름의 단위 |

---

## CPU의 작업 처리 방식

### 병렬성 (Parallelism)

- 여러 개의 코어가 실제로 동시에 여러 프로세스나 스레드를 돌려, 작업들을 **동시에 수행**하는 것

![image](https://github.com/user-attachments/assets/4f04c87c-904f-4eab-8593-ccc873f2f26a)

- 두 개 이상의 작업이 물리적으로 **실제로** 동시에 실행되는 것

### 동시성 (Concurrency)

- 두 개 이상의 작업이 동시에 실행되는 것처럼 보이게 하는 것
- 병렬성이 실제로 동시에 실행하는 것이라면, 동시성은 시분할 방식으로 **동시에 실행하는 것처럼 보이게** 하는 것

![image](https://github.com/user-attachments/assets/b767ad40-7deb-4d6c-95e7-25d748f2131f)

- 작업들을 잘게 나누어서 조금씩만 수행하고, 다음 작업으로 넘어가는 식으로 동작
- 여러 작업을 동시에 처리하는 것처럼 보이게 만들어, 사용자에게 더 빠른 반응성을 제공
- 이렇게 진행 중인 작업들을 번갈아 수행하는 것을 **Context Switching**이라고 부른다.

### 동시성이 필요한 이유

1. **하드웨어적 한계**
2. **논리적인 효율**

---
