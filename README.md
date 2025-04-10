## 면접? 기술?
- 면접 스터디라는 느낌보다는 CS의 각자 맡은 파트를 공부하고 발표하며 지식을 공유하고 서로 궁금한 것을 계속 질문하고 이것들을 전부 기록으로 남기는 느낌의 스터디라고 생각해주시면 편할 것 같습니다.
- Deep Dive하게 할 것이냐, 기본에 충실할 것이냐는 스터디원과 함께 만들어가는 것이라고 생각합니다.

## 멤버
| <a href="https://github.com/HeoJamong"><img src="https://avatars.githubusercontent.com/u/84281455?v=4" width="90" height="90"></a> | <a href="https://github.com/sssssungjun"><img src="https://avatars.githubusercontent.com/u/108875242?v=4" width="90" height="90"></a> | <a href="https://github.com/MinwooPyeon"><img src="https://avatars.githubusercontent.com/u/153968515?v=4" width="90" height="90"></a> | <a href="https://github.com/hyeriimm"><img src="https://avatars.githubusercontent.com/u/94698088?v=4" width="90" height="90"></a> | <a href="https://github.com/rhflffkaksl"><img src="https://avatars.githubusercontent.com/u/195999066?v=4" width="90" height="90"></a> | <a href="https://github.com/rhflffkaksl"><img src="https://avatars.githubusercontent.com/u/30969164?v=4" width="90" height="90"></a> |
| ----- | ----- | ----- | ----- | ----- | ----- |
| [허지명](https://github.com/HeoJamong) | [윤성준](https://github.com/sssssungjun) | [편민우](https://github.com/MinwooPyeon) | [최혜림](https://github.com/hyeriimm) | [김수현](https://github.com/rhflffkaksl) | [최진우](https://github.com/nonani) |

## 진행 방식
- 주 1회 2시간
- 매주 각자 인원에게 할당된 주제를 공부 (아래 예상 진행도 참고)
    - 기술 개념(최대한 많이 그리고 깊게)
- 공부를 해서 정리한 내용을 스터디원에게 발표
- 한명의 발표가 끝나고 면접 느낌으로 스터디원과 Q&A형식으로 질의 응답
- 정리한 내용을 매주 Github ReadMe 에 업로드
    - 이에 대한 내용은 본인 블로그에 다 가져가셔도 됩니다. (타 스터디원 내용까지도 허용)
 
## 
```kotlin
import kotlin.random.Random

fun main() {
    val names = listOf("최혜림", "김수현", "윤성준", "편민우", "허지명", "최진우")
    val numbers = (0..5).shuffled().toMutableList() // 1~5를 랜덤하게 섞음
    
    val assignments = names.mapIndexed { index, name -> name to numbers[index] } // 이름과 숫자를 매칭
    
    // 0은 면제
    assignments.forEach { (name, number) ->
        println("${name} -> ${number}")
    }
}
```

## 예상 진행도
### **📌 1주차: 컴퓨터 구조 기초**
1. 컴퓨터의 구성 : 최진우
2. 중앙처리장치(CPU) 작동 원리 : 윤성준
3. 캐시 메모리 : 편민우
4. 고정 소수점 & 부동 소수점 : 최혜림
5. 패리티 비트 & 해밍 코드 : 김수현
6. 프런트맨 : 허지명 

### **📌 2주차: 운영체제 개념 ①**
1. 운영체제란? : 김수현
2. 프로세스 vs 스레드 : 편민우
3. 프로세스 주소 공간 : 허지명
4. 인터럽트(Interrupt) : 윤성준
5. 시스템 콜(System Call) : 최혜림
6. 프런트맨 : 최진우

### **📌 3주차: 운영체제 개념 ②**
1. PCB와 Context Switching : 윤성준
2. IPC(Inter Process Communication) : 김수현
3. CPU 스케줄링 : 허지명
4. 데드락(DeadLock) : 최혜림
5. Race Condition : 최진우
6. 프런트맨 : 편민우

### **📌 4주차: 운영체제 메모리 관리**
1. 세마포어(Semaphore) & 뮤텍스(Mutex) : 윤성준
2. 페이징 & 세그먼테이션 : 최혜림
3. 파일 시스템 : 편민우
4. 대용량 저장장치 구조 : 최진우
5. 입출력 시스템 : 허지명
6. 프런트맨 : 김수현

### **📌 5주차: 데이터베이스 기초**

1. 키(Key) 정리 : 최혜림
2. SQL - JOIN : 허지명
3. SQL Injection : 최진우
4. SQL vs NoSQL : 김수현
5. 정규화(Normalization) : 편민우
6. 프런트맨 : 윤성준

### **📌 6주차: 네트워크 기초**
1. OSI 7 계층 : 편민우
2. TCP 3 way handshake & 4 way handshake : 최진우
3. TCP/IP 흐름제어 & 혼잡제어 : 김수현
4. UDP : 윤성준
5. 대칭키 & 공개키 : 허지명
6. 프런트맨 : 최혜림

### **📌 7주차: 네트워크 심화 & 보안**
1. HTTP & HTTPS : 윤성준 
2. TLS/SSL handshake : 최혜림
3. 로드 밸런싱(Load Balancing) : 허지명
4. Blocking,Non-blocking & Synchronous,Asynchronous : 김수현
5. RSA : 최진우
6. 프런트맨 : 편민우

### **📌 8주차: 소프트웨어 공학 개념**
1. 클린코드 & 리팩토링
2. TDD(Test Driven Development)
3. 애자일(Agile)
4. 객체 지향 프로그래밍(Object-Oriented Programming)
5. 함수형 프로그래밍(Fuctional Programming)

### **📌 9주차: 소프트웨어 개발 & 배포**
1. 데브옵스(DevOps)
2. 서드 파티(3rd party)란?
3. 마이크로서비스 아키텍처(MSA)
4. CI/CD 개념
5. 서버리스(Serverless)
