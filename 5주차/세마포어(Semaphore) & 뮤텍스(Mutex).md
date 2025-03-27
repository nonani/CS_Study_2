# 💡 세마포어 & 뮤텍스란?

## ✅ 왜 필요한가?

- **Critical Section**
    
    둘 이상의 스레드/프로세스가 동시에 접근하면 안 되는 공유 자원을 다루는 코드 영역
    
- **Race Condition / Concurrency Problem**
    
    여러 스레드가 동시에 자원에 접근하면서 예상치 못한 결과가 발생하는 상황
    

---

# 🔁 Spin Lock (Busy Wait)

## ✅ Busy Wait란?

> 특정 조건이 만족될 때까지 CPU를 사용해 계속 루프를 도는 방식
> 
- `while (조건이 충족되지 않음)` 형태의 반복문으로 대기
- **잠들지 않고 깨어있는 상태로 루프를 돌기 때문에 CPU를 계속 점유**
- 간단하지만, **자원을 낭비할 수 있음**

---

## 🔒 Spin Lock이란?

> Busy Waiting의 한 형태로, **공유 자원(락)** 이 해제될 때까지 루프를 돌며 상태를 감시하는 기법
> 
- **Critical Section** 보호를 위해 사용
- **뮤텍스(Mutex), 세마포어(Semaphore)** 등의 동기화 기법이 나오기 전 or 저수준 구현에서 주로 사용

---

## ⚙️ Spin Lock의 특징

| 항목 | 설명 |
| --- | --- |
| 🔄 대기 방식 | **락이 풀릴 때까지 루프 돌면서 감시** (busy wait) |
| ⚡ 짧은 대기 시간 | 락을 짧게 잡는 상황에서는 효율적 |
| 🔥 CPU 자원 낭비 | 대기 시간이 길어지면 **Context Switch 없이 CPU 계속 사용** |
| 💡 구현이 간단함 | 락 구조 자체가 단순 |

---

## 💻 Spin Lock 예제 코드

```c
void acquire_lock() {
    while (__sync_lock_test_and_set(&lock, 1)) {
        // 🔁 이미 lock == 1이면 true 반환 → 계속 루프
        // 💡 CPU를 낭비하는 busy waiting 상태
        // lock을 획득할 때 까지 계속 while문에서 대
    }
}
```

---

## 🔒 뮤텍스 (Mutex - Mutual Exclusion)

- Locking 메커니즘을 이용하여 Critical Section 접근을 제어
- 한 번에 **하나의 스레드만** 접근 가능
- 일종의 **바이너리 세마포어**라고도 불림

![test](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdfJ75G%2FbtqZJ43DWsg%2FOUjUDPalDipT8rkuM7aks1%2Fimg.png)

---

## 🔐 Locking 메커니즘

- **lock**
    
    Critical Section에 들어갈 권한을 얻는다.
    
    이미 다른 스레드가 들어가 있다면 **끝날 때까지 대기**
    
- **unlock**
    
    Critical Section에서 일을 마치고 **다른 스레드에게 양보**
    

---

## ✅ Mutex 코드 예시

```c

#include <stdio.h>
#include <pthread.h>
#include <unistd.h>

pthread_mutex_t lock;
int shared_data = 0;

void* thread_function(void* arg) {
    pthread_mutex_lock(&lock);

    // Critical Section
    printf("Thread %ld is in the critical section.\n", (long)arg);
    int temp = shared_data;
    sleep(1);
    shared_data = temp + 1;
    printf("Thread %ld updated shared_data to %d.\n", (long)arg, shared_data);

    pthread_mutex_unlock(&lock);
    return NULL;
}

int main() {
    pthread_t t1, t2;
    pthread_mutex_init(&lock, NULL);

    pthread_create(&t1, NULL, thread_function, (void*)1);
    pthread_create(&t2, NULL, thread_function, (void*)2);

    pthread_join(t1, NULL);
    pthread_join(t2, NULL);

    printf("Final shared_data: %d\n", shared_data);
    pthread_mutex_destroy(&lock);
    return 0;
}

```

---

## ⚠️ 뮤텍스에서의 Deadlock (교착 상태) 예시

```c

#include <stdio.h>
#include <pthread.h>
#include <unistd.h>

pthread_mutex_t lockA;
pthread_mutex_t lockB;

void* thread1_func(void* arg) {
    pthread_mutex_lock(&lockA);
    printf("Thread 1 acquired lockA\n");

    sleep(1);

    pthread_mutex_lock(&lockB);
    printf("Thread 1 acquired lockB\n");

    pthread_mutex_unlock(&lockB);
    pthread_mutex_unlock(&lockA);
    return NULL;
}

void* thread2_func(void* arg) {
    pthread_mutex_lock(&lockB);
    printf("Thread 2 acquired lockB\n");

    sleep(1);

    pthread_mutex_lock(&lockA);
    printf("Thread 2 acquired lockA\n");

    pthread_mutex_unlock(&lockA);
    pthread_mutex_unlock(&lockB);
    return NULL;
}

```

### ❗ Deadlock 발생 조건:

- `Thread 1`: lockA → lockB
- `Thread 2`: lockB → lockA
    
    ⇒ 서로가 상대의 락을 기다리며 **영원히 대기**
    

---

## ✅ Deadlock 해결 방법 (trylock 사용)

```c

#include <stdio.h>
#include <pthread.h>
#include <unistd.h>

pthread_mutex_t lockA;
pthread_mutex_t lockB;

void* thread1_func(void* arg) {
    while (1) {
        pthread_mutex_lock(&lockA);
        printf("Thread 1 acquired lockA\n");

        if (pthread_mutex_trylock(&lockB) == 0) {
            printf("Thread 1 acquired lockB\n");

            printf("Thread 1 is doing work\n");

            pthread_mutex_unlock(&lockB);
            pthread_mutex_unlock(&lockA);
            break;
        } else {
            printf("Thread 1 could not acquire lockB, releasing lockA and retrying\n");
            pthread_mutex_unlock(&lockA);
            usleep(100000);
        }
    }
    return NULL;
}

void* thread2_func(void* arg) {
    while (1) {
        pthread_mutex_lock(&lockB);
        printf("Thread 2 acquired lockB\n");

        if (pthread_mutex_trylock(&lockA) == 0) {
            printf("Thread 2 acquired lockA\n");

            printf("Thread 2 is doing work\n");

            pthread_mutex_unlock(&lockA);
            pthread_mutex_unlock(&lockB);
            break;
        } else {
            printf("Thread 2 could not acquire lockA, releasing lockB and retrying\n");
            pthread_mutex_unlock(&lockB);
            usleep(100000);
        }
    }
    return NULL;
}

```

### 💡 핵심:

- `pthread_mutex_trylock()` 사용 시
    
    → 락이 이미 잡혀 있으면 **즉시 실패하고 retry**
    
    → **Deadlock 회피 가능**
    

---

# 🚦 세마포어 (Semaphore)

## ✅ 개념

- **공유 자원의 상태를 `counter`로 표현**
- `lock/unlock` 대신에 `count--` / `count++`로 접근을 제어
- **1개 이상의 스레드가 동시에 Critical Section에 접근 가능**
- 자원에 진입할 때 `sem_wait()` → `count--`
    
    자원을 반환할 때 `sem_post()` → `count++`
    

![teset](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Ft2sHF%2FbtqZJ6mQJzl%2FTcm2kfZtxXHWvnNY8t3xb1%2Fimg.png)

---

## 💻 기본 세마포어 코드 예제

```c

#include <stdio.h>
#include <pthread.h>
#include <semaphore.h>
#include <unistd.h>

#define THREAD_COUNT 5

sem_t semaphore;

void* thread_func(void* arg) {
    int id = *(int*)arg;

    printf("Thread %d waiting for access...\n", id);
    sem_wait(&semaphore);  // 세마포어 감소 (0이면 대기)

    printf("Thread %d entered critical section\n", id);
    sleep(2);  // Critical Section (2초 작업)

    printf("Thread %d leaving critical section\n", id);
    sem_post(&semaphore);  // 세마포어 증가

    return NULL;
}

int main() {
    pthread_t threads[THREAD_COUNT];
    int ids[THREAD_COUNT];

    // 초기값 3 → 동시에 최대 3개의 스레드가 접근 가능
    sem_init(&semaphore, 0, 3);

    for (int i = 0; i < THREAD_COUNT; i++) {
        ids[i] = i + 1;
        pthread_create(&threads[i], NULL, thread_func, &ids[i]);
    }

    for (int i = 0; i < THREAD_COUNT; i++) {
        pthread_join(threads[i], NULL);
    }

    sem_destroy(&semaphore);
    return 0;
}

```

### 🔑 핵심 포인트

- 초기값 `3`: 동시에 3개 스레드가 Critical Section 진입 가능
- `sem_wait()` → 자원 차감, 없으면 대기
- `sem_post()` → 자원 반환

---

## ⚠️ 세마포어의 단점: 소유권 없음

> 세마포어는 락을 가진 스레드가 아닌 다른 스레드도 sem_post() 호출이 가능
> 
> 
> → 의도치 않은 동작/버그 발생 가능
> 

---

## 🧨 문제 상황 예시 코드

```c

#include <stdio.h>
#include <pthread.h>
#include <semaphore.h>
#include <unistd.h>

sem_t semaphore;

void* worker_func(void* arg) {
    sem_wait(&semaphore);  // 자원 요청
    printf("Worker: acquired resource\n");

    sleep(2);  // 작업 중

    printf("Worker: done with resource\n");
    sem_post(&semaphore);  // 자원 반납
    return NULL;
}

void* buggy_helper_func(void* arg) {
    sleep(1);  // Worker가 자원 사용하는 도중

    // 💥 리소스를 쓰지도 않았는데 sem_post 호출!
    printf("Helper: accidentally posting semaphore!\n");
    sem_post(&semaphore);  // 💥 의도치 않게 세마포어 증가

    return NULL;
}

int main() {
    pthread_t t1, t2;

    // 초기 자원 수: 1개
    sem_init(&semaphore, 0, 1);

    pthread_create(&t1, NULL, worker_func, NULL);
    pthread_create(&t2, NULL, buggy_helper_func, NULL);

    pthread_join(t1, NULL);
    pthread_join(t2, NULL);

    sem_destroy(&semaphore);
    return 0;
}

```

---

### ❗ 문제 설명

- `worker_func`는 `sem_wait()`으로 자원을 점유
- `buggy_helper_func`는 **자원을 얻지도 않았는데 `sem_post()`를 호출**
- 결과적으로 세마포어의 **카운트가 잘못 증가됨**
- 다음에 오는 스레드가 **이미 점유 중인 자원을 다시 점유할 수 있게 됨 → 데이터 충돌 위험**

---

## 🚫 해결 방법?

- 세마포어 사용 시 `sem_post()` 호출 위치를 **엄격하게 제한**
- 혹은 **뮤텍스**처럼 소유권 있는 구조로 설계
- 고급 시스템에서는 **이중 검증 or 상태 기반 제어**로 방어

---

# 🌀 Named Semaphore vs Unnamed Semaphore

## ✅ Named Semaphore (이름 있는 세마포어)

### 📌 특징:

- **파일 시스템 상의 이름**으로 생성되며 시스템 전체에서 공유됨
- **다른 프로세스끼리 동기화**할 때 사용하기 편리함

### 💻 예제:

```c

#include <semaphore.h>

sem_t* sem = sem_open("/my_semaphore", O_CREAT, 0644, 1);
// sem_open(이름, 플래그, 퍼미션, 초기값)

// 사용 후
sem_close(sem);
sem_unlink("/my_semaphore");
```

### 📂 생성 위치:

- 리눅스 기준 `/dev/shm/sem.my_semaphore` 에 파일처럼 생성됨

---

## 🕵️‍♂️ Unnamed Semaphore (이름 없는 세마포어)

### 📌 특징:

- 그냥 **변수로 선언해서 사용하는 세마포어**
- **같은 프로세스 내 스레드** 간에 주로 사용
- 공유 메모리에 할당하면 **프로세스 간 공유도 가능**

### 💻 예제:

```c

#include <semaphore.h>

sem_t sem;
sem_init(&sem, 0, 1);  // pshared=0이면 스레드 간, 1이면 프로세스 간

// 사용 후
sem_destroy(&sem);
```

---

## 🔄 세마포어 vs 뮤텍스 비교

| 항목 | 🧱 **Mutex** | 🌀 **Semaphore** |
| --- | --- | --- |
| 🔓 제어 방식 | Lock / Unlock (Binary Lock) | Counter 기반 (0 ~ N) |
| 🧍‍♂️ 소유권 | **있음**락을 획득한 스레드만 해제 가능 | **없음**누구나 `sem_post()` 호출 가능 |
| 🔢 동시 접근 수 | **항상 1개**단일 스레드만 접근 가능 | **여러 개 설정 가능**(ex. 3개 설정 시 3개 스레드 동시 진입 가능) |
| 🔄 동작 함수 | `pthread_mutex_lock()` / `pthread_mutex_unlock()` | `sem_wait()` / `sem_post()` |
| ⚠️ 실수 가능성 | 낮음락 소유자가 아닌 스레드는 unlock 불가 | 높음다른 스레드가 의도치 않게 `sem_post()` 가능 |
| 🔁 초기값 설정 | 없음항상 1개 리소스 제어 | 있음`sem_init(&sem, 0, N)` 형태로 설정 |
| 💥 주로 사용하는 곳 | **상호 배제(Mutual Exclusion)**단일 자원 보호 | **공유 자원 제한 시**동시 접근 수 제한 (예: DB 연결 수 제한 등) |

---

### ✅ 핵심 정리

- **🔒 Mutex**
    
    → **단일 스레드/프로세스**의 Critical Section 보호
    
    → 락을 **획득한 주체만** 해제 가능 (소유권 있음)
    
- **🚦 Semaphore**
    
    → **여러 스레드/프로세스**가 공유 자원을 제한된 수만큼 접근 가능
    
    → **소유권 개념이 없음**
    
    → 즉, 다른 스레드/프로세스가 **의도치 않게 `sem_post()`를 호출해버릴 수 있음**
    
    → 이로 인해 자원의 실제 사용 여부와 무관하게 **세마포어 카운터가 증가해 버그 발생 가능**
