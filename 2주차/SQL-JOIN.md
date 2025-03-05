# SQL - JOIN 완벽 가이드 🚀

## 🔗 관계(Relationship)의 개념

데이터베이스에서 **관계**란 엔터티(테이블) 간의 논리적인 연결을 의미합니다. 관계를 설정함으로써 여러 개의 테이블을 효과적으로 연결할 수 있습니다.

- 관계를 맺는다는 것은 **부모의 식별자를 자식에 상속하고, 이를 조인키(JOIN KEY)로 활용하여 연결하는 것**을 의미합니다.
- 예를 들어, 사원 테이블(`employees`)이 부서 테이블(`departments`)과 관계를 맺을 경우, `department_id`를 조인키로 사용하여 데이터를 연결할 수 있습니다.

💡 **예시**: 사원과 부서 간의 관계
```sql
SELECT employees.name, departments.department_name
FROM employees
JOIN departments ON employees.department_id = departments.id;
```

---

## 📌 관계의 종류

관계는 **존재 관계**와 **행위 관계**로 나눌 수 있습니다.

### ✅ 존재 관계 (Existence Relationship)
- 엔터티 간의 상태를 나타내는 관계입니다.
- 예시: **사원은 특정 부서에 소속되어 있다.**

### ✅ 행위 관계 (Action Relationship)
- 엔터티 간의 특정 행위(이벤트)가 발생하는 관계입니다.
- 예시: **고객이 상품을 주문하면 `order` 테이블이 생성된다.**

---

## 🔍 JOIN이란?

**JOIN(조인)이란 여러 개의 테이블에서 관련된 데이터를 결합하여 하나의 결과로 조회하는 SQL 연산입니다.**

- 데이터 정규화를 통해 테이블이 분리됨
- 두 개 이상의 테이블을 조인하여 데이터를 다시 결합하여 활용 가능

💡 **예시**: 계좌 테이블과 관리점 테이블을 조인하여 계좌 정보와 관리점 정보를 함께 출력하기
```sql
SELECT accounts.account_number, branches.branch_name
FROM accounts
JOIN branches ON accounts.branch_id = branches.id;
```

---

## 🔥 JOIN의 종류와 예제

### 1️⃣ INNER JOIN (내부 조인)
두 테이블에서 **일치하는 데이터만 조회**합니다.

```sql
SELECT employees.name, departments.department_name
FROM employees
INNER JOIN departments ON employees.department_id = departments.id;
```
📝 **결과:**
| name   | department_name |
|--------|---------------|
| Alice  | IT            |
| Bob    | HR            |

### 2️⃣ LEFT JOIN (왼쪽 외부 조인)
왼쪽 테이블의 모든 데이터와 **일치하는 오른쪽 테이블의 데이터**를 출력하며, 일치하지 않는 데이터는 `NULL`을 반환합니다.

```sql
SELECT employees.name, departments.department_name
FROM employees
LEFT JOIN departments ON employees.department_id = departments.id;
```
📝 **결과:**
| name   | department_name |
|--------|---------------|
| Alice  | IT            |
| Bob    | HR            |
| Charlie| NULL          |

### 3️⃣ RIGHT JOIN (오른쪽 외부 조인)
오른쪽 테이블의 모든 데이터와 **일치하는 왼쪽 테이블의 데이터**를 출력하며, 일치하지 않는 데이터는 `NULL`을 반환합니다.

```sql
SELECT employees.name, departments.department_name
FROM employees
RIGHT JOIN departments ON employees.department_id = departments.id;
```

### 4️⃣ FULL OUTER JOIN (완전 외부 조인)
양쪽 테이블의 모든 데이터를 출력하며, **일치하지 않는 경우 NULL 값**을 표시합니다.

```sql
SELECT employees.name, departments.department_name
FROM employees
FULL OUTER JOIN departments ON employees.department_id = departments.id;
```

### 5️⃣ SELF JOIN (자기 자신과 조인)
같은 테이블을 두 번 사용하여 **계층적 관계**를 표현할 때 사용됩니다.

```sql
SELECT E1.name AS 사원이름, E2.name AS 매니저이름
FROM employees E1
JOIN employees E2 ON E1.manager_id = E2.id;
```

📝 **결과:**
| 사원이름 | 매니저이름 |
|--------|---------|
| Alice  | Bob     |
| Bob    | Charlie |

### 6️⃣ CROSS JOIN (크로스 조인)
두 테이블의 **모든 가능한 조합을 생성**합니다.

```sql
SELECT employees.name, departments.department_name
FROM employees
CROSS JOIN departments;
```

---

## 🎯 JOIN 최적화 (성능 개선)

### ✅ 1. 인덱스(Index) 활용
- **조인 키에 인덱스를 추가**하면 성능이 향상됨
- `EXPLAIN` 명령어를 사용하여 실행 계획을 확인 가능

```sql
CREATE INDEX idx_department_id ON employees(department_id);
```

### ✅ 2. `ON` vs `USING` 차이점
- `ON` : 서로 다른 컬럼명을 사용할 때 적합
- `USING` : 동일한 컬럼명을 사용할 때 간결하게 표현 가능

```sql
SELECT employees.name, departments.department_name
FROM employees
JOIN departments USING (department_id);
```

### ✅ 3. WHERE 절과 JOIN 차이점
- `WHERE`에서 조건을 잘못 설정하면 불필요한 데이터까지 가져올 수 있어 성능 저하 발생
- `JOIN`을 사용하여 정확한 매칭을 수행하는 것이 중요

```sql
SELECT employees.name, departments.department_name
FROM employees
LEFT JOIN departments ON employees.department_id = departments.id
WHERE departments.department_name = 'IT';
```

---
