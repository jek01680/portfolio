# Freepik 이미지 검색 및 다운로드 기능 개발 (Flextudio)

🔥 **기간**: 2023/02/27 → 2023/03/08  
🛠 **기술 스택**: Node.js, AWS S3, JavaScript, jQuery  
🏢 **회사**: Flextudio  

---

## 📝 프로젝트 개요
Freepik API를 활용하여 **이미지를 검색하고, 다운로드하여 AWS S3에 저장하는 기능**을 개발했습니다.  
사용자는 UI에서 원하는 이미지를 검색하여 배경 이미지로 적용할 수 있습니다.  

✅ **기여한 부분**  
- **검색 API 연동**: Freepik API를 활용하여 이미지 검색 기능 개발  
- **이미지 다운로드 최적화**: AWS S3에 저장 후 클라이언트에 최적화된 URL 제공  
- **검색 속도 개선**: 불필요한 API 호출 제거 및 응답 속도 개선  
- **UX 개선**: 다운로드 시 로딩 UI 추가  

---

## 🚀 주요 기능
### 1. 이미지 검색 (Freepik API)
- Freepik API를 사용하여 **실시간 이미지 검색 결과 반환**
- 필터(색상, 카테고리) 적용 가능

### 2. 이미지 다운로드 (AWS S3 저장)
- 선택한 이미지를 **AWS S3에 저장하고, URL을 반환**하여 지속적으로 사용 가능
- 다운로드 속도를 최적화하여 UX 개선  

### 3. 이미지 적용 (UI 컨트롤 연동)
- UI 컨트롤에서 검색한 이미지를 **즉시 배경 이미지로 적용 가능**
- UX 향상을 위해 **로딩 UI 추가**

---

## 🛠 사용 기술
| 기술 | 설명 |
|------|------|
| **Node.js** | 백엔드 API 개발 |
| **AWS S3** | 이미지 저장 및 URL 제공 |
| **JavaScript (jQuery)** | 프론트엔드 연동 |
| **Freepik API** | 이미지 검색 및 다운로드 |

---

## 🔍 시스템 아키텍처
```plaintext
사용자 → Freepik API 검색 → Node.js 서버 → 이미지 다운로드 → AWS S3 저장 → URL 반환
```

## ⚡ 성능 개선 사항

### **🔹 개선된 기능**
1️⃣ **다운로드 API 업데이트 및 속도 개선**  
   - 기존보다 빠른 이미지 다운로드를 위해 API 로직을 최적화  
   - **검색 API 응답 속도가 약 36% ~ 60% 개선됨**  
   - **특정 이미지 다운로드 속도는 최대 50% 단축**  

2️⃣ **다운로드 시 로딩 UI 추가**  
   - 사용자가 다운로드 상태를 알 수 있도록 **로딩창 추가**  

3️⃣ **검색 속도 개선** (체감이 어려울 수도 있음)  
   - API 요청 최적화 및 쿼리 필터링 개선  

---

### 🖥️ 속도 개선 비교 결과

| 개선 전 (검색 API) | 개선 후 (검색 API) | 개선율 |
|--------------------|--------------------|--------|
| 1.21초 ~ 1.95초    | 690ms ~ 786ms      | 36% ~ 60% 개선 |

| 개선 전 (이미지 다운로드) | 개선 후 (이미지 다운로드) | 개선율 |
|------------------------|------------------------|--------|
| 2~3초                  | 1초대 이하             | 최대 50% |

➡ **특정 요청에서는 다운로드 속도가 50% 이상 단축되었으며, 전반적인 API 응답 속도도 향상됨**  

![네트워크 화면](./images/network.png)

![네트워크 화면](./images/network2.png)

---

## 📸 프로젝트 UI 화면
![검색 화면](./images/freepik_image.png)

![적용 화면](./images/freepik_image2.gif)

---

## 🔍 개선된 코드 샘플

### **🔹 검색 기능 (`searchFreePik`)**
```javascript
function searchFreePik(wsName, term, page, filters) {
    $.ajax({
        "type": "post",
        "url": "/studio/searchFreePik",
        "async": false,
        "data": {
            "wsName": wsName || undefined,
            "term": term || "",
            "page": page || 1,
            "filters" : filters || ""
        },
        "dataType": "json"
    })
    .done(function (res) {
        console.log(res)
        renderFreepikResult(res)
    })
    .fail(function (res, status, err) {
        console.log(status, err, res);
    })
}
```
➡ **개선 포인트:**
- API 호출 최적화 (불필요한 요청 최소화)
- `async: false` 옵션 제거 가능성 검토


### **🔹 이미지 적용 기능 (`processFreePikImage`)**
```javascript
function processFreePikImage(id, user) {
    showCommonLoadingView(true); // 로딩 UI 추가
    $.ajax({
        "type": "post",
        "url": "/studio/processFreePikImage",
        "data": {
            "id": id || 0,
            "user": user || 'default-user'
        },
        "dataType": "json"
    })
    .done(function (rtn) {
        // 메타데이터 업데이트 로직 개선
        updateMultiPropertyMeta(...changes);
        reloadProperty();
    })
    .fail(function (res, status, err) {
        console.error(status, err, res);
    })
    .always(function (res, status) {
        showCommonLoadingView(false); // 로딩 UI 제거
    })
}
```

➡ **개선 포인트:**
- **로딩 UI 추가** → 다운로드 중 사용자 피드백 제공  
- **네트워크 요청 최적화** → 응답 속도 단축  


✅ **배운 점**:
- API 최적화 및 성능 튜닝 경험  
- AWS S3를 활용한 이미지 스토리지 설계  
- UX 개선을 위한 로딩 UI 적용  

🔧 **개선할 점**:
- 이미지 다운로드 속도 추가 최적화 가능  
- 캐싱 기법을 활용한 추가 개선 고려  
