# 4. SQL vs NoSQL
## 어떤 데이터베이스를 선택할까?
==================================

웹 개발에서 데이터베이스 선택은 중요한 결정입니다.  
**SQL -** MySQL, 오라클  
**NoSQL -** MongoDB  
프로젝트에 적합한 데이터베이스를 선택하기 위해 그 차이를 알아봅시다!

* * *
![image](https://github.com/user-attachments/assets/40e90946-7f65-439b-bb08-25b5af68f619)




1️⃣ SQL이란?
----------

✅ **SQL (Structured Query Language)**은 관계형 데이터베이스(RDBMS)에서 데이터를 저장, 수정, 삭제, 검색하는 데 사용됩니다.

### 📌 SQL의 특징

| 특징 | 설명 |
| --- | --- |
| **정형화된 스키마** | 테이블마다 정해진 엄격한 구조(컬럼, 데이터 타입)를 가짐 |
| **관계형 모델** | 데이터를 여러 테이블로 나누고 관계(Foreign Key)로 연결 |
| **ACID 보장** | 트랜잭션의 무결성 유지 (원자성, 일관성, 고립성, 지속성) |
| **JOIN 지원** | 여러 테이블 간 관계를 이용해 원하는 데이터를 조합 |

  ACID : 데이터베이스 트랜잭션의 신뢰성을 보장하는 4가지 원칙

  ✅ 원자성 (Atomicity): 트랜잭션이 모두 성공하거나 모두 실패해야 함

  ✅ 일관성 (Consistency): 데이터베이스가 무결성을 유지해야 함

  ✅ 고립성 (Isolation): 동시에 실행되는 트랜잭션이 서로 영향을 주지 않아야 함

  ✅ 지속성 (Durability): 트랜잭션이 완료되면 데이터가 영구적으로 저장되어야 함

SQL 예제

    CREATE TABLE Users (
        id INT PRIMARY KEY,
        name VARCHAR(100),
        email VARCHAR(100) UNIQUE
    );
    
    INSERT INTO Users (id, name, email) VALUES (1, 'Alice', 'alice@example.com');
    SELECT * FROM Users WHERE name = 'Alice';
    

* * *

2️⃣ NoSQL이란?
------------

✅ **NoSQL (Not Only SQL)** 은 비관계형 데이터베이스로, 정형화된 스키마 없이 유연한 데이터 저장을 제공합니다.

### 📌 NoSQL의 특징

| 특징 | 설명 |
| --- | --- |
| **정해진 스키마 없음 (데이터 구조를 정의하는 설계도)** | 데이터를 자유로운 형태(JSON, Key-Value, 그래프 등)로 저장 가능 |
| **수평적 확장** | 여러 노드(서버)로 데이터를 분산하여 확장 가능 |
| **JOIN 없음**  | 데이터를 한 문서/컬렉션에 중복 저장하여 성능 최적화 |
| **다양한 데이터 모델** | Key-Value, Document, Column-Family, Graph 등 여러 모델 지원 |

### NoSQL 예제 (MongoDB)

    {
        "id": 1,
        "name": "Alice",
        "email": "alice@example.com"
    }
    

    // MongoDB에서 데이터 추가
     db.users.insertOne({ id: 1, name: "Alice", email: "alice@example.com" });
     db.users.find({ name: "Alice" });
    

* * *
![image](https://github.com/user-attachments/assets/8191ff52-ccc4-429b-9f14-ee1ef17bd70e)
3️⃣ SQL vs NoSQL 차이점 정리
-----------------------

### 🔍 왜 NoSQL에는 조인이 없을까?

SQL에서는 여러 테이블의 데이터를 조합하기 위해 **JOIN**을 사용합니다. 하지만 NoSQL에서는 조인을 지원하지 않거나 성능이 저하되기 때문에 **데이터를 한 문서 안에 중복 저장**하는 방식으로 해결합니다.

예를 들어, SQL에서는 `Orders` 테이블이 `Users`와 `Products` 테이블을 참조하고, 조인을 통해 데이터를 가져옵니다. 반면, NoSQL에서는 `Orders` 문서 안에 `User`와 `Product` 정보를 그대로 포함하여 저장합니다. 이렇게 하면 **한 번의 조회로 필요한 데이터를 모두 가져올 수 있어 성능이 향상**되지만, 같은 데이터가 여러 문서에 중복될 수 있습니다. 즉, NoSQL에서는 조인을 하지 않는 대신 데이터 중복을 감수하는 방식으로 설계됩니다.

| 비교 항목 | SQL | NoSQL |
| --- | --- | --- |
| **데이터 구조** | 정해진 테이블 스키마 | 자유로운 구조(JSON 등) |
| **관계형 모델** | 여러 테이블로 나눠 저장 | 한 컬렉션/문서에 통합 저장 |
| **확장 방식** | 수직적 확장 (서버 업그레이드) | 수평적 확장 (노드 추가) |
| **트랜잭션** | ACID 지원 | 일반적으로 성능 위주 (일부 NoSQL은 ACID 지원) |
| **사용 사례** | 금융, ERP, 전통적인 웹 애플리케이션 | 빅데이터, 소셜미디어, IoT |

* * *

4️⃣ 언제 SQL을, 언제 NoSQL을 사용할까?
----------------------------

### ✅ SQL을 선택해야 하는 경우

✔️ **관계형 데이터가 많고, 정규화가 필요한 경우**  
✔️ **데이터 무결성이 중요할 때 (금융, 회계 등)**  
✔️ **복잡한 쿼리와 트랜잭션이 필요한 경우**

### ✅ NoSQL을 선택해야 하는 경우

✔️ **빠른 개발과 유연성이 중요한 경우(여러 유형의 데이터를 저장해야 할)**  
✔️ **대량의 데이터, 높은 트래픽(빅데이터, IoT, 로그 등)을 처리해야 할 때**  
✔️ **수평적 확장이 필요한 경우 (SNS, 스트리밍 서비스 등)**

* * *

5️⃣ 추가 설명
---------

*   **하이브리드 모델**: 한 프로젝트에서 SQL과 NoSQL을 조합하여 사용할 수도 있음. 예를 들어, 사용자 데이터는 SQL에, 로그 데이터는 NoSQL에 저장 가능.(여러 개의 DBMS를 조합)
    
*   **다중 모델 데이터베이스:**
    
    하나의 데이터베이스 시스템에서 여러 데이터 모델을 지원
    
    예: ArangoDB, CosmosDB, PostgreSQL (문서형, 그래프형, 키-값 저장소 등을 동시에 지원)
    
    하나의 DBMS에서 여러 데이터 모델을 제공하므로 단일 시스템 내에서 다양한 데이터 구조 사용 가능
    
*   **다중 문서 트랜잭션(Multi-Document Transaction)**:  
    여러 개의 문서(document)를 하나의 트랜잭션으로 처리하는 기능  
    MongoDB 4.0 이상에서 지원됨
    
    SQL처럼 ACID 보장 가능
    

* * *

[📝 참고자료](https://www.mongodb.com/)
-----------------------------------

*   ["Designing Data-Intensi](https://www.mongodb.com/)[v](https://dev.mysql.com/)[e Applications"](https://www.mongodb.com/) [b](https://dev.mysql.com/)y [Martin Kleppma](https://www.mongodb.com/)[nn](https://dev.mysql.com/)
*   (https://www.mongodb.com/) [MyS](https://www.mongodb.com/)[QL](https://dev.mysql.com/), [](https://www.mongodb.com/)[MongoDB](https://www.mongodb.com/)
*   PostgreSQL vs MongoDB 성능 비교: [링크](https://www.enterprisedb.com/blog/postgresql-vs-mongodb-performance)
