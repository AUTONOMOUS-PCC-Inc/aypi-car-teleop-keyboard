| 저장소 | 경로 | 내용 |
|---|---|---|
| donkeycar | `donkeycar/parts/web_controller/templates/static/main.js` | WASD 누르는-동안 운전 로직 추가. 기존 A/S 모드 전환키는 Shift+A, Shift+S로 이동. `limitedThrottle()`에서 Constant 스로틀 모드가 부호를 버려 후진(S)이 전진하던 문제 수정 |
| donkeycar | `donkeycar/parts/web_controller/templates/vehicle.html` | 조이스틱 위 키 안내 문구, 도움말 키 목록 갱신 |
| donkeycar | `donkeycar/parts/web_controller/templates/base.html` | 스크립트 주소에 `?v=wasd3` 캐시 무효화 |
