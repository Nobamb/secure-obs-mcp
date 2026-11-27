# 설정·설치와 권장 코드 구조

[개발 명세 목차](README.md) · 설계 명세 v1.1 · 구현 및 실기 검증 전

## 7. 설정 및 설치 절차

### 7.1 사용자 전용 설정 예시 (실제 비밀번호 미포함)

아래는 구현해야 할 설정 스키마의 예시이며 현재 작동하는 프로그램 설정 파일이 아니다. 경로·입력 종류·창 식별자는 실제 환경에서 사용자가 확인 후 등록한다.

```toml
[storage]
root = 'D:\OBS-Captures'
min_free_bytes = 1073741824
max_record_bytes = 2147483648

[policy]
profile = 'AI Capture'
scene_collection = 'AI Capture'
max_record_seconds = 120
require_local_approval = true
allow_audio = false

[[targets]]
id = 'unity_game'
scene = 'Unity Game'
source = 'Unity Window'
input_kind = 'window_capture'
# 실제 OBS 버전에서 읽은 창 매칭 설정은 별도 등록/검증한다.
```

설정은 에이전트가 자동 생성·변경하지 않고 사용자가 별도 설정 절차를 통해 승인한다. 필수 설정이 누락되거나 정책이 변조되면 기본 거부 상태로 시작한다. 실제 설정에 `allow_audio=true`, `require_local_approval=false` 등을 넣어도 v1 안전 정책을 우회할 수 없도록 한다.

### 7.2 OBS 설정 (사용자 수행)

1. OBS Studio 실행 → 도구 → WebSocket 서버 설정 → 서버 활성화, 포트 4455, 인증 활성화 및 강한 전용 비밀번호 지정.
2. LAN·인터넷에서 해당 포트로 접근할 수 없도록 Windows 방화벽 및 라우터 설정을 확인한다. 루프백 연결이 작동하는지 검증한다. MCP는 방화벽을 자동 수정하지 않는다.
3. `AI Capture` 전용 프로필과 장면 컬렉션을 생성하고 별도 녹화 폴더, 고급 출력의 표준 MKV, 고유 파일명 규칙, 덮어쓰기·자동 분할·자동 리먹스·오디오 비활성화를 설정한다. Cut을 선택하고 장면별 전환 재정의를 확인한다. 기존 일반 OBS 환경은 보존한다.
4. 허용 대상 창을 등록하고 OBS 미리보기로 실제 창과 캡처 내용이 일치하는지 확인한다.
5. MCP의 로컬 설정 도구로 대상·경로·비밀번호를 등록한다. 사용자 비밀번호 입력은 에이전트 대화창이 아닌 로컬 자격증명 등록 UI에서 직접 수행한다.

### 7.3 빌드와 MCP 등록 (구현 완료 후)

설치 예시는 구현자가 제공할 패키지 구조와 로컬 경로를 전제로 한다. 아래 `C:\Tools\secure-obs-mcp`는 예시다. 실제 배포 명령은 잠금 파일 및 테스트 결과와 함께 제공한다.

- 프로젝트를 Unity 저장소와 별개로 배치한다. `.venv` 생성 후 검토된 잠금 파일로 해시 검증 설치한다. 코드 및 테스트가 통과하기 전 MCP를 등록하지 않는다.
- 실행 진입점은 전용 가상환경 Python의 절대 경로와 `-I -m secure_obs_mcp`로 통일한다. `--self-check` 같은 관리 모드는 별도 진입점에서 실행하고 MCP로 노출하지 않는다.
- Codex 설정 예시 (`~/.codex/config.toml`):

```toml
[mcp_servers.secure_obs]
command = 'C:\Tools\secure-obs-mcp\.venv\Scripts\python.exe'
args = ['-I', '-m', 'secure_obs_mcp']
tool_timeout_sec = 60
cwd = 'C:\Tools\secure-obs-mcp'
```

- Claude Code PowerShell 등록 예시 (로컬 프로젝트 범위):

```powershell
claude mcp add --transport stdio --scope local secure-obs -- 'C:\Tools\secure-obs-mcp\.venv\Scripts\python.exe' -I -m secure_obs_mcp
```

검증된 빌드를 전용 가상환경에 설치한다. 동명 모듈을 작업 폴더와 `PYTHONPATH`에 둔 시험에서 설치된 모듈만 불러오는지 확인한다. 두 클라이언트의 설치 버전에서 등록 옵션을 확인하고 기존 설정을 덮어쓰지 않는다. 환경변수 대체 인증을 선택했다면 클라이언트별 안전한 환경변수 전달 설정을 검증하되 실제 비밀번호는 설정 파일에 넣지 않는다. 로컬 MCP 도구 목록과 `obs_status` 결과로 연결을 확인한다. 기존 승인 정책은 유지하며 무조건 허용 모드는 설정하지 않는다. 원격/클라우드 에이전트에서 사용자의 로컬 OBS에 직접 연결되는 것으로 가정하지 않는다.

## 8. 권장 코드 구조

```text
secure-obs-mcp/
  pyproject.toml
  requirements.lock       # 또는 해시가 있는 동등 잠금 파일
  README.md
  SECURITY.md
  config.example.toml
  .gitignore
  src/secure_obs_mcp/
    __main__.py
    server.py             # 공개 MCP 도구 및 입력 스키마
    config.py             # 사용자 정책, 설정 로딩
    policy.py             # 대상/기능/상태 승인
    consent.py            # 로컬 사용자 승인 UI
    credentials.py        # 로컬 비밀번호 조회
    obs_client.py         # 연결, 인증, RPC 허용 목록
    targets.py            # 장면/소스 검증
    capture.py            # PNG 검사 및 신규 저장
    recorder.py           # 상태 머신, 감시, 종료
    storage.py            # 경로 및 파일 검증
    audit.py              # 제한된 로컬 로그
    lock.py               # Windows 단일 서버 제어권
  tests/
    unit/
    integration/          # 가짜 OBS WebSocket 및 MCP stdio 테스트
    manual/               # 실제 OBS 검증 체크리스트
```

구조는 제안이며 동등한 모듈 분리가 가능하다. 관리 UI 및 설치 스크립트가 필요하면 별도로 포함하고 동작을 검토한다. `eval`, `exec`, `shell=True`, 사용자 입력 기반 프로세스 실행, 임의 파일 읽기·쓰기, HTTP 클라이언트 등을 런타임에 추가하지 않는다. 테스트 도구나 설치 시 패키지 관리 명령은 런타임과 분리한다.
