# 서명 키 관리 (HDnotes Android)

저장소에는 키스토어·비밀번호가 없습니다(공개 저장소이므로 넣으면 누구나 같은 서명으로 가짜 업데이트를 만들 수 있습니다). 정식 릴리스 APK는 GitHub Actions 비밀값으로만 서명됩니다.

## 구조

| 단계 | 하는 일 | 비밀값 접근 |
| --- | --- | --- |
| `build` 잡 | 테스트, 디버그 APK, **서명 안 된** 릴리스 APK 빌드 | 없음 (PR·포크 포함) |
| `release` 잡 | 태그 `v*` 또는 main의 `[release]` 푸시에서만 실행. zipalign → `apksigner sign`(v1·v2·v3) → 검증 → GitHub 릴리스 게시(+`.sha256`) | 저장소 Secrets |

필요한 Secrets: `KEYSTORE_BASE64`(키스토어 파일을 base64로 바꾼 한 줄), `KEYSTORE_PASSWORD`, `KEY_ALIAS`, `KEY_PASSWORD`.
선택 Variables: `EXPECTED_CERT_SHA256` — 설정하면 다른 키로 서명된 APK는 게시되지 않습니다.

현재 인증서(별칭 `hdnotes`, CN=HDnotes, O=Lee Hyunduk) SHA-256:
`22:CF:5B:18:BC:1F:6E:FF:FB:F0:27:AF:3A:DB:A8:DC:E8:05:52:49:85:09:EA:23:EE:39:5F:46:2B:13:30:6D`
앱의 「앱 정보」에 표시되는 서명 지문과 비교할 수 있습니다.

## 등록 방법
1. https://github.com/hdlee73/HDnotes/settings/secrets/actions → **New repository secret**
2. 위 네 이름으로 값을 하나씩 넣습니다. `KEYSTORE_BASE64`는 `base64 -w0 hdnotes-release.jks`의 출력입니다.

## 주의
- 키스토어(`hdnotes-release.jks`)와 비밀번호는 개인 보관함에 따로 백업하세요. 잃어버리면 설치된 앱을 업데이트할 수 없습니다.
- 디버그 APK는 기본 디버그 키라 정식 앱 위에 설치되지 않습니다.
- HDnotes는 Everynote와 다른 앱(applicationId `com.hdlee.hdnotes`)이므로 Everynote의 키·lineage와 무관합니다.
