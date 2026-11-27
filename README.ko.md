# Secure OBS MCP

[English](README.md)

Secure OBS MCP는 로컬 Codex 또는 Claude Code 세션에서 승인된 프로그램 창을 OBS Studio로 캡처하기 위한 Windows 우선 프로젝트입니다. Unity UI 변경을 캡처하거나 짧은 게임 플레이 테스트를 녹화하는 데 사용할 계획입니다.

**상태:** 설계 및 문서화 단계입니다. 현재 실행 가능한 MCP 서버, 설치 가능한 릴리스, 검증된 OBS·클라이언트 조합은 없습니다. 예시는 검증된 빌드가 나온 뒤의 사용 흐름을 설명합니다.

## 동작 구조

로컬 MCP 클라이언트 → stdio → 정책 검사와 로컬 승인 → 127.0.0.1:4455의 인증된 OBS WebSocket → 로컬 캡처 폴더

OBS 상태 조회, 사전 승인 대상 목록 조회 및 선택, PNG 저장, 소유 녹화 시작·일시정지·재개·정지·결과 조회를 제공할 계획입니다. 캡처 응답에는 파일 메타데이터가 포함됩니다. 방송, 카메라·마이크 활성화, 전체 화면 캡처, 원격 OBS 연결, 임의 OBS 명령, 파일 업로드는 초기 범위에서 제외합니다.

## 시작하기

1. [호환성 표](docs/COMPATIBILITY.md)에서 빌드와 함께 검증된 Windows, OBS, Python, 로컬 클라이언트 버전을 확인합니다.
2. 검증된 빌드가 나오면 [설치 가이드](docs/installation/README.md)에 따라 격리된 Python 환경과 인증된 OBS WebSocket, 무음 캡처 전용 프로필을 설정합니다.
3. 로컬 MCP 클라이언트 하나에 빌드를 등록하고 상태를 확인합니다. 캡처를 승인하기 전에 OBS에서 실제 창을 확인합니다.

검증된 빌드에서 사용할 요청 예시입니다.

> 승인 대상인 Unity Game의 상태를 확인해 줘. 내가 각 작업을 로컬에서 승인하면 PNG 한 장을 저장하고 같은 창을 10초간 무음 녹화해 줘. 녹화 결과를 조회해서 파일들의 상대 경로를 알려 줘.

MCP 정책은 이 서버를 통과하는 요청에 적용됩니다. 동일한 Windows 계정으로 실행되는 다른 도구는 OBS와 저장 파일에 별도로 접근할 수 있습니다. OBS 네트워크 노출 범위는 방화벽과 라우터 설정에 따라 달라집니다. [위협 모델](docs/security/THREAT_MODEL.md) · [개인정보 및 로컬 데이터](docs/PRIVACY.md)

## 프로젝트 문서

- [전체 문서](docs/README.md)와 [API 레퍼런스](docs/reference/API.md)
- [개발 명세](init/development-spec/README.md)와 [기여 가이드](CONTRIBUTING.md)
- [MIT 라이선스](LICENSE)와 [외부 의존성 고지](THIRD_PARTY_NOTICES.md)
- [보안 정책과 취약점 제보](SECURITY.md)
