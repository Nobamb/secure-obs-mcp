# Codex·Claude 실행 경로 격리

전용 가상환경에 검증한 빌드와 해시 고정 의존성을 설치한다. Codex와 Claude Code는 해당 python.exe의 절대 경로와 인자 -I -m secure_obs_mcp로 로컬 stdio 서버를 실행한다. 작업 폴더와 PYTHONPATH에 동명 모듈을 둔 시험에서 설치본만 실행되어야 한다. 두 클라이언트는 순차 검증하고 동시 실행은 BUSY로 응답한다.
