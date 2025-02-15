# MSSQL 연동 기능 개발 (Flextudio)

🔥 **기간**: 2023/03/10 → 2023/03/17  
🛠 **기술 스택**: Node.js, DynamoDB, MSSQL, JavaScript  
🏢 **회사**: Flextudio  

---

## 📝 프로젝트 개요
MSSQL과의 연동을 통해 **사용자가 DB 정보를 입력받아 저장하고, 해당 정보를 바탕으로 데이터베이스와 상호작용할 수 있도록 하는 기능**을 개발했습니다.  

✅ **기여한 부분**  
- **DB 정보 입력 화면 개발**: 사용자에게 DB 정보를 입력받고 저장  
- **MSSQL 연동**: 입력된 정보를 바탕으로 MSSQL과 상호작용  
- **DynamoDB 저장 및 관리**: DB 정보를 DynamoDB 테이블에 저장 및 삭제 기능 추가  
- **MSSQL 쿼리 실행 기능 추가**: 사용자가 SQL을 직접 실행할 수 있도록 구현  

---

## 🚀 주요 기능

### ✅ 1. DB 정보 입력
- **사용자가 DB 정보를 입력하는 UI 개발**  
- **입력된 정보가 DynamoDB에 저장되도록 연동**  
- **DB 타입(MySQL, MSSQL 등)에 따라 입력 필드가 동적으로 변경됨**  

### ✅ 2. DB 정보 저장
- **입력받은 DB 정보를 DynamoDB `_fFlexSQLTenant` 테이블에 저장**  
- **기존에 저장된 DB 정보가 있으면 덮어쓰기 가능**  

### ✅ 3. DB 정보 삭제
- **사용자에게 확인 메시지를 표시한 후 DynamoDB에서 삭제**  
- **삭제 완료 후 초기 화면으로 리다이렉트**  

### ✅ 4. MSSQL 쿼리 실행
- **MSSQL에 직접 SQL 쿼리를 실행하고 결과를 반환**  
- **실행된 쿼리는 `_fFlexSQLService` 테이블의 `fScript_mssql` 필드에 저장**  

---

## 🛠 사용 기술
| 기술 | 설명 |
|------|------|
| **Node.js** | 백엔드 API 개발 |
| **MSSQL** | 데이터 저장 및 조회 |
| **DynamoDB** | 사용자 DB 정보 저장 |
| **JavaScript (Express.js)** | 서버 개발 |
| **SQL (MSSQL 쿼리 실행)** | 직접 SQL 실행 가능 |

---

## 🔍 시스템 아키텍처
```plaintext
사용자 → DB 정보 입력 → DynamoDB 저장 → MSSQL 연동 → SQL 실행 및 응답 반환
```

---

## ⚡ 성능 개선 사항
1️⃣ **DB 연결 속도 최적화**  
   - 기존 DB 연결 프로세스를 개선하여 **연결 속도 단축**  
   - **MSSQL 연결 최적화**를 통해 불필요한 요청 감소  

2️⃣ **DynamoDB 저장 구조 개선**  
   - 기존보다 더 빠르게 저장 및 조회 가능하도록 테이블 구조 최적화  

---

## 📸 프로젝트 UI 화면
🔗 **[DB 정보 입력 화면](https://github.com/jek01680/portfolio/issues/5)**  
🔗 **[DB 정보 저장 및 삭제 화면](https://github.com/jek01680/portfolio/issues/6)**  
🔗 **[MSSQL 쿼리 실행 화면](https://github.com/jek01680/portfolio/issues/7)**  

➡ **GitHub Issues를 통해 프로젝트 UI 화면을 확인할 수 있습니다.**  

---

## 🔍 개선된 코드 샘플

### **🔹 DB 정보 입력 및 저장**
```javascript
// DB 정보 입력 폼 처리 및 저장 로직
router.post('/saveDBInfo', async (req, res) => {
    const { dbType, dbName, hostname, port, username, password, adminUsername, adminPassword } = req.body;

    const params = {
        TableName: '_fFlexSQLTenant',
        Item: {
            fBaseCompanyID: req.session.companyID,
            fDBKey: `${dbType}#${dbName}`,
            fDBType: dbType,
            fDBName: dbName,
            hostname: hostname,
            port: port,
            username: username,
            password: password,
            adminUsername: adminUsername,
            adminPassword: adminPassword
        }
    };

    try {
        await docClient.put(params).promise();
        res.send({ result: 'Success' });
    } catch (err) {
        res.status(500).send({ result: 'Failed', error: err });
    }
});
```

---

### **🔹 DB 정보 삭제**
```javascript
// 특정 DB 정보를 DynamoDB에서 삭제하는 코드
router.post('/deleteDBInfo', async (req, res) => {
    const { dbKey } = req.body;
    const params = {
        TableName: '_fFlexSQLTenant',
        Key: {
            fBaseCompanyID: req.session.companyID,
            fDBKey: dbKey
        }
    };

    try {
        await docClient.delete(params).promise();
        res.send({ result: 'Success' });
    } catch (err) {
        res.status(500).send({ result: 'Failed', error: err });
    }
});
```

---

### **🔹 MSSQL 쿼리 실행**
```javascript
// MSSQL 쿼리를 실행하고 결과를 반환하는 함수
const sql = require('mssql');

async function executeMSSQLQuery(query, dbConfig) {
    try {
        const pool = await sql.connect(dbConfig);
        const result = await pool.request().query(query);
        return result.recordset;
    } catch (err) {
        throw new Error(`MSSQL Query Execution Failed: ${err.message}`);
    }
}

// DB 연결 설정 예시
const dbConfig = {
    user: 'username',
    password: 'password',
    server: 'hostname',
    database: 'dbName',
    port: 1433,
    options: {
        encrypt: true, // Use encryption
        enableArithAbort: true // Enable arith abort
    }
};

// 쿼리 실행 예시
const query = 'SELECT * FROM TableName';
executeMSSQLQuery(query, dbConfig).then(result => {
    console.log(result);
}).catch(err => {
    console.error(err);
});
```

---

## 🚀 배운 점 & 개선할 점
✅ **배운 점**:
- **MSSQL과 Node.js 연동 경험**  
- **DynamoDB를 활용한 데이터 저장 및 삭제 로직 구현**  
- **실제 SQL 실행 환경 구축 경험**  

🔧 **개선할 점**:
- **DB 연결 속도 추가 최적화 가능**  
- **쿼리 실행 중 발생할 수 있는 오류 처리 로직 추가 필요**  
- **사용자 친화적인 UI 개선 가능**  

---