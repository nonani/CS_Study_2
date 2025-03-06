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
![join](https://github.com/user-attachments/assets/779b7459-9ae3-4266-b6ad-59957d6d6446)

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

SQL에서 JOIN은 강력한 기능이지만, 잘못 사용하면 성능 저하가 발생할 수 있습니다. 따라서 **최적화 방법**을 이해하고 적용하는 것이 중요합니다.

### ✅ 1. 인덱스(Index) 활용하기

**왜 인덱스가 필요할까?**
- 테이블의 크기가 커지면 조인 연산에 시간이 오래 걸릴 수 있음.
- `ON` 절에서 사용되는 컬럼에 **인덱스**를 추가하면 검색 속도를 크게 향상시킬 수 있음.
- 특히, `JOIN KEY`로 사용되는 컬럼에 인덱스를 설정하면 테이블 탐색 속도가 향상됨.

```sql
CREATE INDEX idx_department_id ON employees(department_id);
```

| employees 테이블 (인덱스 적용 전) |
|---------------|
| id | name  | department_id |
|----|------|--------------|
| 1  | Alice  | 101 |
| 2  | Bob  | 102 |
| 3  | Charlie | NULL |

✅ **인덱스를 사용하면 검색 속도가 더 빠르게 동작할 수 있습니다.**

### ✅ 2. `ON` vs `USING` 차이점

- `ON`: 조인 조건을 명확하게 설정할 때 사용 (컬럼명이 다를 때 유용)
- `USING`: 두 테이블에서 **같은 이름을 가진 컬럼**을 조인할 때 사용

```sql
-- ON 사용 예시
SELECT employees.name, departments.department_name
FROM employees
JOIN departments ON employees.department_id = departments.id;

-- USING 사용 예시 (department_id가 두 테이블에서 동일한 이름일 경우)
SELECT employees.name, departments.department_name
FROM employees
JOIN departments USING (department_id);
```

✅ `USING`을 사용하면 SQL이 자동으로 컬럼을 매칭하여 가독성이 좋아집니다.

### ✅ 3. WHERE 절을 활용한 조인 성능 향상

조인 후 `WHERE` 절을 적절히 사용하여 **불필요한 데이터 조회를 줄이면 성능이 향상**됩니다.

❌ **잘못된 예시** (불필요한 데이터가 조인됨)
```sql
SELECT employees.name, departments.department_name
FROM employees
LEFT JOIN departments ON employees.department_id = departments.id;
```

✅ **올바른 예시** (`WHERE` 절을 활용하여 불필요한 데이터 제거)
```sql
SELECT employees.name, departments.department_name
FROM employees
LEFT JOIN departments ON employees.department_id = departments.id
WHERE departments.department_name = 'IT';
```

✅ **불필요한 데이터가 조인되지 않도록 `WHERE` 조건을 추가하면 성능이 향상됩니다.**

### ✅ 4. 서브쿼리 대신 조인 사용하기

**서브쿼리는 경우에 따라 성능이 느려질 수 있음. 조인을 사용하면 최적화가 가능함.**

❌ **비효율적인 서브쿼리 사용 예**
```sql
SELECT name, (SELECT department_name FROM departments WHERE departments.id = employees.department_id)
FROM employees;
```

✅ **효율적인 JOIN 사용 예**
```sql
SELECT employees.name, departments.department_name
FROM employees
JOIN departments ON employees.department_id = departments.id;
```

✅ **JOIN을 사용하면 불필요한 서브쿼리 실행을 줄일 수 있어 성능이 향상됩니다.**

### ✅ 5. 조인 순서 최적화

SQL은 조인을 실행할 때 **테이블의 크기에 따라 실행 순서를 자동으로 최적화**하지만, 개발자가 직접 최적화할 수도 있습니다.

예를 들어, 다음 두 개의 테이블이 있다고 가정하겠습니다.
- `orders`: 주문 정보 (10,000개 행)
- `customers`: 고객 정보 (1,000개 행)

❌ **비효율적인 조인 순서** (큰 테이블이 먼저 조인됨)
```sql
SELECT orders.order_id, customers.name
FROM orders
JOIN customers ON orders.customer_id = customers.id;
```

✅ **효율적인 조인 순서** (작은 테이블을 먼저 조인하여 최적화)
```sql
SELECT customers.name, orders.order_id
FROM customers
JOIN orders ON customers.id = orders.customer_id;
```

✅ **작은 테이블을 먼저 조회하면 조인 성능이 향상됩니다.**

---
