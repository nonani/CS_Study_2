
# 📌 SQL Injection이란?
SQL Injection은 공격자가 웹 애플리케이션의 데이터베이스 쿼리를 조작하여 데이터 탈취, 변경, 삭제 등을 수행하는 해킹 기법입니다. 잘못된 입력 검증으로 인해 발생하며, 해커가 원하는 SQL 문을 실행할 수 있도록 만듭니다.

## 🚨 SQL Injection 공격 방법
SQL Injection은 주로 사용자 입력을 그대로 SQL 쿼리에 포함할 때 발생합니다. 

### ❌ 취약한 코드 예시 (Java + MySQL)
```
String userInput = request.getParameter("username");
String query = "SELECT * FROM users WHERE username = '" + userInput + "'";
Statement stmt = connection.createStatement();
ResultSet rs = stmt.executeQuery(query);

```

위 코드는 사용자의 입력값을 직접 SQL 문에 삽입합니다. 만약 사용자가 아래와 같이 입력한다면?

' OR '1'='1

해당 입력을 SQL 문에 삽입하면 다음과 같은 쿼리가 됩니다.

SELECT * FROM users WHERE username = '' OR '1'='1';

'1'='1'는 항상 참(True)이므로, 모든 사용자 데이터를 반환하게 됩니다. 즉, 로그인 차단을 우회하거나, 관리자 계정에 접근할 수도 있습니다.

## 🔥 SQL Injection 공격 예시

### 1️⃣ 인증 우회
```
입력값: admin' --

SELECT * FROM users WHERE username = 'admin' --' AND password = 'password';

결과: --는 주석 처리 기능을 하므로, 비밀번호 확인 로직이 무시되어 비밀번호 없이 로그인 가능

```

### 2️⃣ 데이터베이스 정보 탈취
```
입력값: ' UNION SELECT null, database(), null --

SELECT id, username, password FROM users WHERE username = '' UNION SELECT null, database(), null --';

결과: 현재 데이터베이스 이름이 반환됨
```


### 3️⃣ 데이터 삭제 (위험!)

```
입력값: '; DROP TABLE users; --

SELECT * FROM users WHERE username = ''; DROP TABLE users; --';

결과: users 테이블이 삭제됨 😱
```


## 🛡️ SQL Injection 방어 방법

SQL Injection을 막기 위해 다음 방법을 사용해야 합니다.

### ✅ 1. Prepared Statement (권장!)

SQL을 미리 컴파일하여 입력값을 별도로 처리하는 방법입니다.
``` 
String query = "SELECT * FROM users WHERE username = ?";
PreparedStatement pstmt = connection.prepareStatement(query);
pstmt.setString(1, userInput);
ResultSet rs = pstmt.executeQuery();
```

✔️ 입력값이 자동으로 이스케이프 처리되므로 SQL Injection 공격을 방어할 수 있습니다.

### ✅ 2. 입력값 검증 및 필터링

사용자 입력값에 특수문자(', ", ;, --)가 포함되지 않도록 검증

예) ^[a-zA-Z0-9_]+$ (알파벳, 숫자, _만 허용)

### ✅ 3. 최소 권한 원칙 적용

DB 계정에 필요한 권한만 부여 (예: SELECT만 허용, DROP, DELETE 제한)

루트 계정 사용 금지

### ✅ 4. 웹 방화벽 (WAF) 사용

SQLi 패턴을 탐지하고 차단하는 보안 솔루션 활용

예) AWS WAF, ModSecurity
