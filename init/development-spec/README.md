# Secure OBS MCP 개발 명세 v1.1

이 문서는 [문서 이슈 #1](https://github.com/Nobamb/secure-obs-mcp/issues/1)의 산출물이다. 2026-11-27자 개발 명세 v1.1을 주제별로 나눴다. 설계에 적힌 v1.0은 필수 기능의 목표 범위다. 첫 제품 배포 후보인 0.1.0-alpha.1은 그 범위 중 구현·검증하고 릴리스 노트에 공개한 기능부터 제공한다. 공통 보안 조건은 alpha에도 동일하게 적용한다.

현재 작업 트리에 구현 코드와 실행 가능한 테스트가 없어 모든 제품 동작은 설계이며 실기 시험 상태는 미실행이다. OBS 버전별 프로필 키와 창 식별 방법의 실제 신뢰성, SDK·클라이언트 확정 버전 및 배포 방식은 구현·검증 때 확정한다. 제품의 배포·검증 현황은 [호환성 정책](../../docs/COMPATIBILITY.md)에서 관리한다.

| 읽는 순서 | 문서 | 요구사항 및 검증 |
|---|---|---|
| 1 | [아키텍처와 개발 범위](architecture.md) | stdio, 사용자 승인, OBS 루프백, 로컬 파일의 신뢰 경계 |
| 2 | [보안 요구사항](security-requirements.md) | SEC-01–SEC-12 및 인증·승인·경로 정책 |
| 3 | [OBS RPC와 MCP 도구 초기 계약](tool-contract.md) | 공개 도구, RPC 기본 거부, 결과 메타데이터 |
| 4 | [캡처·녹화 기능 설계](feature-design.md) | 장면·창 재검증, 녹화 상태 머신, 감시·장애 복구 |
| 5 | [설정·설치와 권장 코드 구조](setup-and-structure.md) | 전용 환경, 격리 실행 및 클라이언트 등록 |
| 6 | [인수 기준과 구현 단계](acceptance.md) | T01–T27, 성능 측정, 제출물 및 공식 사양 |

## 원본과 개정 기록

- [개발 명세 원본](../spec/Secure_OBS_MCP_Development_Spec.md)
- [종료 결과](../spec/spec_docs/01-recording-result.md), [녹화 소유권](../spec/spec_docs/02-recording-ownership.md), [창 식별](../spec/spec_docs/03-window-identity.md)
- [장면 전환](../spec/spec_docs/04-scene-transition.md), [녹화 출력](../spec/spec_docs/05-recording-profile.md), [녹화 한도](../spec/spec_docs/06-recording-limits.md)
- [실행 격리](../spec/spec_docs/07-isolated-python.md), [PNG 전송](../spec/spec_docs/08-png-transport.md), [승인 동시성](../spec/spec_docs/09-approval-concurrency.md)
- [성능 측정](../spec/spec_docs/10-obs-performance.md), [우선 인수 시험](../spec/spec_docs/11-priority-acceptance.md)
