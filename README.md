<div align="center">

<img src="https://raw.githubusercontent.com/AUTONOMOUS-PCC-Inc/.github/main/mirrors/assets/teleop-banner.svg?v=2" alt="donkeycar 웹 컨트롤러 패치 — 키를 누르고 있는 동안만 달리고, 떼면 스로틀이 0으로 감쇠하며 조향이 가운데로 돌아온다" width="100%">

### 키를 누르고 있는 동안만 달린다.

donkeycar 웹 컨트롤러에 **WASD 홀드 방식 키보드 원격조종**을 넣는 패치입니다.<br>
마우스로 조이스틱을 끌어 쓰던 조작을, 키보드로 직접 몰 수 있게 바꿉니다.

![Target](https://img.shields.io/badge/대상-donkeycar%20web%20controller-0B1B33?style=for-the-badge)
![Files](https://img.shields.io/badge/바꾸는%20파일-3개-1E6FD9?style=for-the-badge)
![Mode](https://img.shields.io/badge/조작-hold--to--drive-16A34A?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-A3A3A3?style=for-the-badge)

[**무엇을 바꾸나**](#무엇을-바꾸나) · [**적용하기**](#적용하기) · [**키 배치**](#키-배치) · [**동작 방식**](#동작-방식) · [**자주 막히는 곳**](#자주-막히는-곳) · [**라이선스**](#라이선스)

</div>

---

## 무엇을 바꾸나

> [!IMPORTANT]
> **혼자 돌아가는 프로그램이 아닙니다.** donkeycar 설치 폴더 안의 템플릿 파일
> 3개를 덮어쓰는 패치입니다. donkeycar가 먼저 깔려 있어야 합니다.

기본 웹 컨트롤러는 화면의 파란 영역을 **마우스로 끌어야** 차가 움직입니다. 끌고 있는 손을 떼면 멈추긴 하는데, 한 손으로 조향과 속도를 동시에 만들어야 해서 데이터를 모으다 보면 조향이 뭉개집니다. 이 패치는 그 자리에 키보드를 붙입니다 — **W를 누르고 있는 동안만** 앞으로 가고, 떼면 섭니다.

| 파일 | donkeycar 안의 경로 | 바뀌는 것 |
|---|---|---|
| `main.js` | `donkeycar/parts/web_controller/templates/static/main.js` | WASD 홀드 주행 로직(`keyDrive`) 추가 · 기존 `A`/`S` 모드 전환키를 `Shift`+`A`/`Shift`+`S`로 이동 · Constant 스로틀 모드 후진 버그 수정 |
| `vehicle.html` | `donkeycar/parts/web_controller/templates/vehicle.html` | 조이스틱 위 키 안내 문구 추가 · 도움말 모달의 키 목록 갱신 |
| `base.html` | `donkeycar/parts/web_controller/templates/base.html` | 스크립트 주소에 `?v=wasd3` — 브라우저가 옛 `main.js`를 계속 쓰는 것을 막습니다 |

---

## 적용하기

donkeycar를 `pip install -e .` 로 깔았다면 소스 폴더가 그대로 실행에 쓰입니다. 그 폴더 맨 위에서:

```bat
copy /Y main.js      donkeycar\parts\web_controller\templates\static\main.js
copy /Y vehicle.html donkeycar\parts\web_controller\templates\vehicle.html
copy /Y base.html    donkeycar\parts\web_controller\templates\base.html
```

바꾼 뒤 `donkey drive` 를 다시 띄우고, 브라우저에서 페이지를 새로고침하면 조이스틱 위에 키 안내가 보입니다.

- 안내 문구가 안 보이면 파일이 **다른 donkeycar**에 들어갔습니다. `python -c "import donkeycar, os; print(os.path.dirname(donkeycar.__file__))"` 로 실제로 쓰이는 경로를 확인하세요.
- 수업 저장소(`aypi-car-tutorial`)는 `donkeycar`를 서브모듈로 두고 있어서, 덮어쓸 곳은 그 **서브모듈 폴더 안**입니다.

---

## 키 배치

누르고 있는 동안만 움직입니다. 떼면 멈추고 핸들은 가운데로 돌아옵니다.

| 키 | 하는 일 |
|:--:|---|
| `W` / `↑` | 전진 |
| `S` / `↓` | 후진 (전진 최대치의 절반) |
| `A` / `←` | 좌조향 |
| `D` / `→` | 우조향 |
| `Space` | 정지 — 서버까지 0을 보냅니다 |

주행키 외의 단축키는 그대로입니다. 다만 `A`·`S`가 주행키로 갔기 때문에 **모드 전환키 두 개가 옮겨졌습니다.**

| 키 | 하는 일 | |
|:--:|---|---|
| `R` | 녹화 토글 | |
| `M` | 모드 토글 | |
| `U` | User 모드 | |
| `Shift`+`A` | Full Auto 모드 | ← 기존 `A` |
| `Shift`+`S` | Auto Steer 모드 | ← 기존 `S` |
| `I` `K` `J` `L` | 예전 단계식 조작 (한 번 누를 때마다 조금씩) | 호환용으로 남겨둠 |

---

## 동작 방식

0.1초마다 한 번씩 현재 눌린 키를 보고 조향·스로틀을 **목표치로 조금씩 밀어** 서버에 보냅니다. 키를 누른 순간 최대값을 꽂지 않는 이유는, 그렇게 모은 데이터로 학습하면 모델도 계단처럼 조향하기 때문입니다.

| 상수 (`main.js`) | 값 | 뜻 |
|---|:--:|---|
| `KEY_TICK_MS` | `100` | 한 틱 간격 |
| `KEY_STEER_STEP` | `0.15` | 틱당 조향 변화 — 풀락까지 약 0.7초 |
| `KEY_THROTTLE_STEP` | `0.10` | 키를 누르고 있을 때 스로틀 상승 |
| `KEY_THROTTLE_RELEASE_STEP` | `0.25` | 키를 뗐을 때 스로틀 감쇠 — 상승보다 빠릅니다 |
| `KEY_REVERSE_SCALE` | `0.5` | 후진은 전진 최대치의 절반 |

손이 떠난 차가 계속 달리는 상황을 막으려고 세 군데를 더 막아 두었습니다.

- **창이 포커스를 잃으면** 눌린 키를 전부 해제합니다. 키를 누른 채로 다른 창으로 넘어가도 차가 섭니다.
- **입력창·선택상자에 포커스가 있으면** 주행키로 받지 않습니다. 또 모드/스로틀 선택상자를 쓰고 나면 포커스를 놓아, 바로 다시 키보드로 몰 수 있습니다.
- **키보드로 몰면 항상 User 모드**입니다. 자율주행 중에 `W`를 누르면 사람이 운전대를 받습니다.

주행 중에는 녹화가 자동으로 켜지고 멈추면 꺼집니다 — 조이스틱·게임패드와 같은 방식입니다.

---

## 자주 막히는 곳

| 증상 | 원인과 해결 |
|---|---|
| 키를 눌러도 아무 일도 없다 | 브라우저가 옛 `main.js`를 쓰고 있습니다. `base.html`까지 같이 덮어썼는지 보고, `Ctrl`+`Shift`+`R` 로 새로고침하세요 |
| 조이스틱 위 안내 문구가 안 보인다 | `vehicle.html`이 **다른 donkeycar**에 들어갔습니다. [위](#적용하기)의 경로 확인 명령을 돌려 보세요 |
| 한글 입력 상태에서 안 된다 | **고쳐져 있습니다.** 키 글자(`w`) 대신 키 위치(`KeyW`)를 먼저 보기 때문에 IME가 켜져 있어도 동작합니다 |
| 게임패드를 꽂으면 키보드가 안 먹는다 | **정상입니다.** 실제 게임패드가 웹 조작을 덮어씁니다 (화면의 빨간 경고 문구) |
| Constant 모드에서 `S`가 전진한다 | **고쳐져 있습니다.** 그래도 그러면 `main.js`가 옛 버전입니다 — 위 첫 줄과 같습니다 |
| 조향이 너무 굼뜨다 / 너무 급하다 | `main.js`의 `KEY_STEER_STEP`을 올리거나 내리세요. [동작 방식](#동작-방식) 표 참고 |

---

## 라이선스

MIT

이 저장소의 파일 3개는 [donkeycar](https://github.com/AUTONOMOUS-PCC-Inc/donkeycar)의 웹 컨트롤러 템플릿을 고친 것입니다. 원 저작권 표기(Will Roscoe)를 그대로 보존했고, 이번 수정분에 대한 표기를 더했습니다. 전문은 [`LICENSE`](LICENSE)에 있습니다.
