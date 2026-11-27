# 녹화 종료 결과 조회

자동·수동 종료를 같은 확정 절차로 처리한다. 같은 세션의 종료 이벤트 또는 StopRecord 응답에서 얻은 경로를 검증한 뒤 종료 사유·상대 경로·바이트 수를 24시간, 최대 100건 보관한다. obs_record_status(session_id)와 재호출한 obs_record_stop은 같은 결과를 반환한다. 파일 확인 실패는 FILE_NOT_FINALIZED로 응답한다.
