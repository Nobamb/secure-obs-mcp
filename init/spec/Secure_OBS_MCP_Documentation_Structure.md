# Secure OBS MCP — 문서 및 폴더 구성 가이드

- 대상 저장소: https://github.com/Nobamb/secure-obs-mcp
- 관리 대상: 문서 이슈 #1~#14, 이후 추가되는 개발·운영 문서
- 목적: 초기 개발 자료와 공식 문서를 분리하고, 긴 문서를 주제별로 나누어 관리한다.
- 상태: 권장 구성안. 실제 파일 생성, 이동 및 이슈 수정은 별도 작업으로 진행한다.
- 이 문서의 권장 저장 위치: `init/DOCUMENTATION_STRUCTURE.md`

## 1. 기본 관리 원칙

- 최상위: GitHub에서 인식하거나 사용자가 가장 먼저 찾는 대표 문서만 배치한다.
- `init/`: 초기 개발 명세, 설계 검토, 구현 계획 및 초기 설정 체크리스트를 관리한다.
- `docs/`: 현재 제품의 사용법, 기술 계약, 보안 정책의 상세 설명 및 검증 기준을 관리한다.
- `.github/`: GitHub가 사용하는 이슈·PR 템플릿, CI 및 저장소 자동화 설정을 배치한다.
- `src/`, `tests/`: 구현 코드와 실행 가능한 테스트를 관리한다. 문서의 테스트 계획과 구분한다.
- `init/`은 개발 완료 후에도 설계 근거를 보존하되, 현재 동작을 설명하는 공식 기준으로 사용하지 않는다.
- Public 저장소이므로 `init/`을 포함한 모든 추적 파일은 공개 대상이다. 비밀·개인 자료는 커밋하지 않는다.

## 2. 권장 디렉터리 구조

아래 구조는 최종 목표 예시이며, 실제 내용이 필요한 시점에 폴더와 파일을 생성한다.

```text
secure-obs-mcp/
├── README.md                       # 영문 프로젝트 소개
├── README.ko.md                    # 한국어 프로젝트 소개
├── LICENSE                         # MIT 라이선스 원문
├── SECURITY.md                     # 보안 제보 및 지원 버전 안내
├── CONTRIBUTING.md                 # 기여 절차 요약
├── CHANGELOG.md                    # 버전별 변경 이력
├── THIRD_PARTY_NOTICES.md           # 외부 라이선스 고지
├── AGENTS.md                       # 공통 AI 개발 지침 (권장)
├── CLAUDE.md                       # 공통 지침 참조 (권장)
├── .gitignore
├── init/
│   ├── README.md                   # 초기 개발 자료 목차
│   ├── DOCUMENTATION_STRUCTURE.md  # 본 가이드
│   ├── development-spec/
│   │   ├── README.md               # 개발 명세 목차 및 요구사항 개요
│   │   ├── architecture.md         # 목표, 범위, 기술 및 구조
│   │   ├── security-requirements.md # 보안 설계 요구사항
│   │   ├── tool-contract.md        # OBS RPC 및 MCP 계약의 초기 설계
│   │   ├── feature-design.md       # 캡처, 녹화, 상태 관리 설계
│   │   ├── setup-and-structure.md  # 설정, 등록 및 코드 구성
│   │   └── acceptance.md           # 인수 기준, 단계 및 참고 자료
│   ├── REPOSITORY_SECURITY.md      # 초기 GitHub 보안 설정 계획
│   └── decisions/                 # 기술 선택 기록 (필요 시)
│       └── README.md
├── docs/
│   ├── README.md                   # 공식 문서 전체 목차
│   ├── COMPATIBILITY.md            # 지원 버전 및 검증 현황
│   ├── PRIVACY.md                  # 로컬 데이터와 정보 전송 정책
│   ├── security/
│   │   ├── README.md
│   │   ├── THREAT_MODEL.md
│   │   └── threat-scenarios.md     # 위협 모델이 길어질 경우 분리
│   ├── installation/
│   │   ├── README.md
│   │   ├── obs-setup.md
│   │   └── mcp-clients.md
│   ├── reference/
│   │   ├── README.md
│   │   ├── API.md
│   │   ├── CONFIGURATION.md
│   │   └── error-codes.md           # 오류 명세 확장 시
│   ├── testing/
│   │   ├── README.md
│   │   ├── TESTING.md              # 테스트 전략 및 결과 기록 양식
│   │   ├── security-tests.md
│   │   └── functional-performance-tests.md
│   ├── TROUBLESHOOTING.md
│   ├── RELEASE.md
│   └── contributing/              # 기여 지침 확장 시
│       └── README.md
├── .github/
│   ├── ISSUE_TEMPLATE/
│   ├── PULL_REQUEST_TEMPLATE.md
│   ├── workflows/
│   └── dependabot.yml
├── src/
└── tests/
```

`src/`, 빌드 설정 및 패키지 잠금 파일의 실제 구성은 구현 언어와 빌드 도구 확정 후 결정한다.
빈 폴더나 내용 없는 문서를 단순히 구조를 맞추기 위해 만들지 않는다.

## 3. GitHub 이슈별 문서 배치

| 이슈 | 권장 저장 위치 | 관리 기준 |
|---|---|---|
| [#1](https://github.com/Nobamb/secure-obs-mcp/issues/1) 개발 명세 | `init/development-spec/README.md` 및 하위 문서 | 기존 명세를 주제별로 분할 |
| [#2](https://github.com/Nobamb/secure-obs-mcp/issues/2) 프로젝트 소개 | `README.md`, `README.ko.md` | 영문 기본, 한글 동기화 |
| [#3](https://github.com/Nobamb/secure-obs-mcp/issues/3) 보안 정책 | `SECURITY.md` | 제보 경로, 지원 버전, 상세 문서 링크 |
| [#4](https://github.com/Nobamb/secure-obs-mcp/issues/4) 위협 모델 | `docs/security/THREAT_MODEL.md` | 길어지면 위협별 하위 문서 추가 |
| [#5](https://github.com/Nobamb/secure-obs-mcp/issues/5) 호환성 | `docs/COMPATIBILITY.md` | 실제 검증 버전, 검증일, 미지원 구분 |
| [#6](https://github.com/Nobamb/secure-obs-mcp/issues/6) 라이선스 | `LICENSE`, `THIRD_PARTY_NOTICES.md` | 원문 및 필수 고지 보존 |
| [#7](https://github.com/Nobamb/secure-obs-mcp/issues/7) 설치 | `docs/installation/README.md` 및 하위 문서 | OBS 설정과 에이전트 등록 분리 |
| [#8](https://github.com/Nobamb/secure-obs-mcp/issues/8) API·설정 | `docs/reference/API.md`, `docs/reference/CONFIGURATION.md` | 실제 스키마·오류·승인 조건과 일치 |
| [#9](https://github.com/Nobamb/secure-obs-mcp/issues/9) 개인정보 | `docs/PRIVACY.md` | 수집·저장·전송·삭제 범위 |
| [#10](https://github.com/Nobamb/secure-obs-mcp/issues/10) 테스트 | `docs/testing/TESTING.md` 및 하위 문서 | 기능·보안·성능 기준을 분리 |
| [#11](https://github.com/Nobamb/secure-obs-mcp/issues/11) 문제 해결 | `docs/TROUBLESHOOTING.md` | 장애 유형별 분할 가능 |
| [#12](https://github.com/Nobamb/secure-obs-mcp/issues/12) 기여 | `CONTRIBUTING.md`, `.github/ISSUE_TEMPLATE/`, `.github/PULL_REQUEST_TEMPLATE.md` | 상세 가이드는 `docs/contributing/` |
| [#13](https://github.com/Nobamb/secure-obs-mcp/issues/13) 릴리스 | `CHANGELOG.md`, `docs/RELEASE.md` | 이력과 배포 절차 분리 |
| [#14](https://github.com/Nobamb/secure-obs-mcp/issues/14) 저장소 보안 | `init/REPOSITORY_SECURITY.md` | 초기 계획; 지속 운영 기준은 필요 시 `docs/security/`로 승격 |

기존 이슈에 기재된 경로와 다를 경우 이 가이드 확정 후 이슈 본문을 갱신한다.

## 4. 문서 분량 및 분할 기준

- 직접 작성하는 Markdown 문서는 **파일당 약 200줄 이내**를 목표로 한다.
- 권장 분량은 100~200줄이며, 짧은 문서를 일부러 늘리지 않는다.
- 빈 줄, 표, 코드 블록, 주석을 포함한 실제 파일의 줄 수를 기준으로 한다.
- 200줄을 넘을 것으로 예상되는 문서는 작성 전에 주제별 하위 파일을 계획한다.
- 소폭 초과는 가독성이나 예제의 완결성을 위해 허용하되, 250줄 이상이면 분할을 우선 검토한다.
- 줄 수를 줄이기 위해 문장을 한 줄에 과도하게 몰아넣거나 요구사항·보안 설명을 생략하지 않는다.
- LICENSE 원문, 외부 의무 고지, 생성된 자료, CHANGELOG 누적 기록 등은 예외로 둔다. 변경 이력은 필요 시 버전군별 보관 파일로 분리하고 최상위 목차를 유지한다.
- YAML/JSON 설정과 소스코드는 이 문서 줄 수 기준의 적용 대상이 아니다.

### 긴 문서 분할 방법

1. 문서의 주제를 먼저 구분하고 관련 폴더를 만든다.
2. 폴더의 `README.md`에 목적, 적용 범위, 하위 문서 링크 및 읽는 순서를 작성한다.
3. 각 하위 문서는 하나의 책임을 가지도록 구성한다. 예: 인증, 파일 저장, 녹화 상태 관리.
4. 동일한 요구사항은 한 문서에만 상세히 작성하고 다른 문서에서는 상대 경로로 참조한다.
5. 기존 경로가 공개된 경우 이동 전 링크를 갱신하고 필요 시 짧은 안내 문서를 유지한다.
6. 분할 이후에도 요구사항 ID, 체크리스트, 코드 예제, 인수 조건의 추적 가능성을 유지한다.

### 기존 개발 명세서(354줄) 분할 권장안

| 기존 절 | 이동할 파일 (`init/development-spec/` 기준) |
|---|---|
| 1~3장: 목표, 구조, 구현 기술 | `architecture.md` |
| 4장: 보안 정책 | `security-requirements.md` |
| 5장: OBS RPC 및 MCP 도구 | `tool-contract.md` |
| 6장: 구현 상세 | `feature-design.md` |
| 7~8장: 설정·설치·코드 구조 | `setup-and-structure.md` |
| 9~13장: 확장, 테스트, 구현 단계, 지시, 출처 | `acceptance.md` (길어지면 별도 분할) |

기존 명세의 원문을 삭제하기 전에 모든 요구사항과 참고 자료가 새 파일로 옮겨졌는지 검토한다.
이 가이드는 명세의 보안 정책이나 제품 요구사항 자체를 수정하는 문서가 아니다.

## 5. 작성 및 유지보수 규칙

- 문서 목차는 `init/README.md`, `docs/README.md`와 각 하위 폴더의 README로 연결한다.
- 최상위 README에는 핵심 소개와 설치·호환성·보안·API 문서 링크를 배치한다.
- 일반 파일명은 영문 kebab-case를 사용하고, 이미 정한 대표 문서명은 대문자 관례를 유지한다.
- 문서 링크는 가능한 상대 경로를 사용한다. 이동·분할 시 깨진 링크를 검사한다.
- 기술 문서에는 적용 제품 버전, 검증 환경, 검증일과 미검증 항목을 필요한 범위에서 표시한다.
- 초기 설계와 실제 구현이 다르면 `docs/`를 현재 구현의 설명으로 유지하고 `init/`에는 변경 근거를 기록한다. 구현과 보안 요구사항의 충돌은 별도 검토한다.
- 영문 공개 문서를 기본으로 하고 한국어판을 함께 제공하는 경우 `.ko.md`로 구분한다. 번역은 의미와 지원 범위를 일치시킨다.
- 비밀번호, 토큰, 실제 로컬 경로, 캡처 파일, 원본 로그는 예시에 넣지 않는다. 설정 예시는 가상 값만 사용한다.
- AI 작업 지침은 `AGENTS.md`에 공통 기준을 두고 `CLAUDE.md`에서 참조한다. 지침만으로 보안 통제가 구현됐다고 간주하지 않는다.
- 기능·API·지원 버전·보안 정책이 바뀌는 PR에는 관련 공식 문서 수정과 검증 근거를 포함한다.

## 6. 문서 작업 완료 체크리스트

- [ ] 이슈에 지정된 파일 경로와 실제 저장 위치가 일치한다.
- [ ] 200줄 목표를 검토했고 긴 문서는 주제별로 분할했다.
- [ ] 폴더 README 및 상위 문서에서 모든 필수 문서에 접근할 수 있다.
- [ ] 기존 요구사항, 보안 조건, 테스트 기준 및 공식 출처가 누락되지 않았다.
- [ ] 문서의 구현 완료·계획·미검증 상태가 명확히 구분되어 있다.
- [ ] 중복 설명, 깨진 링크, 서로 충돌하는 정책을 확인했다.
- [ ] 공개해서는 안 되는 인증 정보, 개인 정보, 로컬 자료가 포함되지 않았다.
- [ ] 관련 이슈의 완료 조건 및 문서 변경 내역을 확인했다.
