# 꿈씨라운지 | 2026 시민 아이디어 공모전

## 🔐 보안 설정 가이드

### 파일 구조
```
index.html          ← 프론트엔드 (깃허브 커밋 OK)
config.example.js   ← 설정 예시 파일 (깃허브 커밋 OK)
config.js           ← 실제 비밀 정보 ⚠️ 절대 커밋 금지
Code.gs             ← Google Apps Script 서버 코드 (깃허브 커밋 OK)
.gitignore          ← config.js 제외 설정
```

---

### 1. 처음 설정하는 경우

**① config.js 만들기**
```bash
cp config.example.js config.js
```
`config.js` 를 열어서 아래 3가지를 실제 값으로 채워주세요:
- `scriptUrl` : Google Apps Script 웹앱 배포 URL
- `adminPassword` : 관리자 로그인에 사용할 비밀번호
- `adminToken` : 서버 검증용 랜덤 문자열 (예: `k8#mP2!qZ9rL` 처럼 긴 것 권장)

> ⚠️ **config.js 는 .gitignore 에 등록되어 있어 깃허브에 올라가지 않습니다.**
> 배포 시 이 파일만 서버(호스팅 폴더)에 직접 업로드하세요.

---

**② Google Apps Script 설정**
1. [script.google.com](https://script.google.com) 접속
2. 새 프로젝트 만들기 → `Code.gs` 내용 붙여넣기
3. `CONFIG` 블록에서 값 설정:
   - `ADMIN_TOKEN` → config.js 의 `adminToken` 과 **동일하게**
   - `ALLOWED_ORIGIN` → 실제 배포 도메인 (예: `https://ggumssee.or.kr`)
4. 배포 → 웹앱으로 배포 → 실행 권한: "모든 사용자"
5. 발급된 URL → config.js 의 `scriptUrl` 에 입력

---

### 2. 보안 조치 요약

| 항목 | 조치 내용 |
|------|----------|
| Apps Script URL | `config.js` 분리 — 깃허브 미포함 |
| 관리자 비밀번호 | `config.js` 분리 — 깃허브 미포함 |
| 관리자 인증 | 클라이언트 비교 제거 → 서버 토큰 검증으로 변경 |
| `__admin__` 토큰 | 하드코딩 제거 → `adminToken` 으로 대체 |
| 스팸 방지 | Apps Script에 IP 기반 레이트 리밋 추가 |
| 입력 새니타이즈 | 서버에서 모든 입력값 sanitize() 처리 |
| 어드민 전용 액션 | 서버에서 토큰 검증 후에만 실행 |

---

### 3. 추가 권장 사항 (선택)

- **HTTPS 배포 필수** — HTTP 사이트에서는 폼 데이터가 평문으로 전송됩니다.
- **비밀번호 해시 강화** — 현재 `btoa(pw + '_djto2026')` 방식보다 서버에서 SHA-256 처리 권장.
- **Google reCAPTCHA 추가** — 폼 스팸 방지를 위해 [reCAPTCHA v3](https://developers.google.com/recaptcha) 연동 고려.
- **관리자 IP 제한** — Apps Script에서 관리자 요청 시 특정 IP만 허용하도록 추가 가능.
