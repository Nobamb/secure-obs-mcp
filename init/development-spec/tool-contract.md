# OBS RPC와 MCP 도구 초기 계약

[개발 명세 목차](README.md) · 설계 명세 v1.1 · 구현 및 실기 검증 전

## 5. OBS RPC 및 MCP 도구 계약

### 5.1 OBS RPC 허용 목록

| 기능 | 허용 요청 |
|---|---|
| 연결 및 상태 | `GetVersion`, `GetRecordStatus`, `GetStreamStatus`, `GetVirtualCamStatus` |
| 전용 환경 검증 | `GetProfileList`, `GetSceneCollectionList`, `GetRecordDirectory`, `GetVideoSettings` |
| 장면 및 소스 검증 | `GetSceneList`, `GetCurrentProgramScene`, `GetSceneItemList`, `GetInputList`, `GetInputSettings`, `GetInputDefaultSettings`, `GetSourceFilterList`, `GetSpecialInputs`, `GetInputMute`, `GetInputAudioTracks` |
| 장면 선택 | `GetCurrentSceneTransition`, `GetSceneSceneTransitionOverride`, `GetCurrentSceneTransitionCursor`, `SetCurrentProgramScene` (허용 장면에 한정, 녹화/방송 중 금지) |
| 스크린샷 | `GetSourceScreenshot` |
| 녹화 | `StartRecord`, `PauseRecord`, `ResumeRecord`, `StopRecord` |
| 녹화 프로필 검사 | `GetProfileParameter` (검증한 OBS 버전별 녹화 section/key만 사용, 임의 인자 전달 금지) |

OBS 공식 사양 및 `GetVersion.availableRequests`에서 실제 지원 여부를 확인한다. 필요한 요청이 없으면 해당 기능을 비활성화하거나 안전하게 거부한다. `GetInputSettings` 등은 내부에서 검증할 때만 쓰고 원본 JSON을 모델에 반환하지 않는다. 허용 목록은 필요 최소치로 유지하며 테스트에 사용되지 않는 요청은 최종 구현에서 제거한다. 녹화 제어 연결은 Outputs 구독을 사용해 `RecordStateChanged`를 수신한다. PNG 전용 연결은 이벤트를 구독하지 않고 `GetSourceScreenshot`만 요청한다.

`StartStream`, `StartVirtualCam`, `SetStreamServiceSettings`, `GetStreamServiceSettings`, `CreateInput`, `SetInputSettings`, `SetRecordDirectory`, `SaveSourceScreenshot`, `CallVendorRequest`, `BroadcastCustomEvent`, 임의 `RequestBatch`, `TriggerHotkey*`, `SetProfileParameter`, 소스 삭제·생성, 플러그인·외부 프로그램 실행 등은 기본적으로 금지한다. 단순히 MCP 도구를 숨기는 것만으로는 부족하며 내부 RPC 송신 계층에서도 차단한다. [선택 확장](acceptance.md#9-선택-확장-v1-합격-후에만) 확장 시 별도 검토 후 필드 수준 허용 목록을 추가한다.

### 5.2 공개 MCP 도구

| 도구 | 인자 | 반환/제한 |
|---|---|---|
| `obs_status` | 없음 | 연결, 버전, 사용 가능 여부, 녹화/일시정지/방송 상태, 안전 정책 상태 |
| `obs_list_targets` | 없음 | 사용자 허용 대상 ID와 표시명만. 전체 창·장면 목록 반환 금지 |
| `obs_select_target` | `target_id` | 해당 허용 장면 선택. 녹화/방송 중 금지, 사용자 승인 필요 |
| `obs_screenshot` | `target_id`, `width?` | PNG 상대 경로, 이미지 크기, 바이트 수. 기본 원본 크기(설정 한도 내), 축소 폭 320~1920 등 검증된 범위 |
| `obs_record_start` | `target_id`, `duration_seconds?` | 세션 ID, 최대 종료 시각, 녹화 상태. 사용자 승인 필수 |
| `obs_record_status` | `session_id?` | 생략 시 현재 OBS 상태. 지정 시 해당 MCP 세션의 상태, 종료 사유 및 검증된 종료 결과 |
| `obs_record_pause` | `session_id` | MCP가 시작한 녹화만, 지원되는 출력 형식일 때 |
| `obs_record_resume` | `session_id` | 해당 세션만, 최초 최대 종료 시각 유지 |
| `obs_record_stop` | `session_id` | 소유 세션의 중지 및 결과 조회. 이미 종료했으면 관찰된 확정 결과 재반환. 검증 중에는 `FINALIZING` |

종료 결과는 `session_id`, `state`, `end_reason`, `relative_path`, `bytes`, `limit_exceeded`, `playback_verified`를 반환한다. 가능한 종료 사유는 `USER_STOP`, `TIME_LIMIT`, `SIZE_LIMIT`, `DISK_LOW`, `SAFETY_CHANGE`, `OBS_STOPPED`, `SERVER_SHUTDOWN`이다. `FINALIZING`은 파일 검증 진행 중이며 최종 경로를 반환하지 않는다. 종료를 확인했으나 유효한 파일을 확인하지 못하면 `FILE_NOT_FINALIZED`를 반환한다. 없거나 만료된 ID는 `SESSION_NOT_FOUND`, 소유권을 확정하지 못한 활성 세션은 `STATE_UNKNOWN`으로 응답한다. 종료 결과 조회에는 OBS 재연결을 요구하지 않는다.

타입 검증은 예상하지 못한 필드를 거부하는 것부터 시작한다. 문자열은 길이·형식 제한, 정수는 범위 검증(불리언·NaN·무한대 불허), 대상 ID는 설정에서 정확 일치해야 한다. 원시 RPC명·파일 경로·URL·비밀번호·쉘 문자열을 받는 도구는 만들지 않는다. MCP 메타데이터의 readOnly/destructive 힌트는 보조 설명일 뿐 보안 장벽이 아니다.

공통 반환 예시(실제 성공을 확인한 경우에만 `ok=true`):

```json
{
  "ok": true,
  "code": "SCREENSHOT_SAVED",
  "target_id": "unity_game",
  "relative_path": "screenshots/20261127T134700Z_8f9c.png",
  "width": 1920,
  "height": 1080,
  "bytes": 1458200
}
```

오류 코드 예: `OBS_UNAVAILABLE`, `AUTH_REQUIRED`, `AUTH_FAILED`, `UNSUPPORTED_RPC`, `TARGET_NOT_ALLOWED`, `TARGET_CHANGED`, `USER_DENIED`, `APPROVAL_TIMEOUT`, `STREAM_ACTIVE`, `RECORDING_NOT_OWNED`, `UNSAFE_PATH`, `DISK_LIMIT`, `BUSY`, `STATE_UNKNOWN`, `FILE_NOT_FINALIZED`. 예외 스택이나 원시 인증·설정 데이터를 반환하지 않는다. 파일 경로가 필요한 사용자는 로컬 루트와 상대 경로를 결합한다.
