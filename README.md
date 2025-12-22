# GKey Wireless Split Keyboard - ZMK Firmware

GKey 무선 분할 키보드를 위한 ZMK 펌웨어 설정입니다.

## 🎯 주요 특징

- **완전 무선**: 양쪽 보드가 블루투스로 연결
- **5개 블루투스 프로파일**: 최대 5개 기기와 페어링 가능
- **3개 레이어**: QWERTY, FN1(기능키), FN2(숫자패드/BT 제어)
- **Nice!Nano v2** 기반 (nRF52840)

## 📐 하드웨어 사양

### 매트릭스 구조
- **왼쪽 보드**: 5행 × 7열 (ROW0~4, COL0~6)
- **오른쪽 보드**: 5행 × 9열 (ROW0~4, COL7~15)
- **다이오드 방향**: COL2ROW
- **총 키 수**: 80키 (5행 × 16열)

### GPIO 핀 배치

**양쪽 보드 공통 (Pro Micro 핀 번호 기준):**
```
Rows:
   ROW0: Pin 19
   ROW1: Pin 18
   ROW2: Pin 15
   ROW3: Pin 14
   ROW4: Pin 16

Left Columns (COL0~6):
   COL0: Pin 2
   COL1: Pin 3
   COL2: Pin 4
   COL3: Pin 5
   COL4: Pin 6
   COL5: Pin 7
   COL6: Pin 8

Right Columns (COL7~15):
   COL7: Pin 2
   COL8: Pin 3
   COL9: Pin 4
   COL10: Pin 5
   COL11: Pin 6
   COL12: Pin 7
   COL13: Pin 8
   COL14: Pin 9
   COL15: Pin 10
```

### 배선
- **전원**: 각 보드에 리튬 배터리 연결 (RAW, GND)
- **통신**: 무선 블루투스 (물리적 케이블 불필요)

## ⌨️ 키맵

### 레이어 0: QWERTY (기본)
표준 QWERTY 레이아웃

### 레이어 1: FN1 (기능키)
- F1~F12
- 화살키 네비게이션
- 미디어 컨트롤 (재생/정지, 볼륨, 이전/다음)

### 레이어 2: FN2 (숫자패드 + 블루투스)
- 숫자패드
- **블루투스 제어**:
  - `FN2 + 1`: BT 프로파일 1 선택/페어링
  - `FN2 + 2`: BT 프로파일 2
  - `FN2 + 3`: BT 프로파일 3
  - `FN2 + 4`: BT 프로파일 4
  - `FN2 + 5`: BT 프로파일 5
  - `FN2 + Backspace`: 현재 프로파일 삭제

## 🚀 빌드 방법

### GitHub Actions (권장)

1. **이 저장소를 본인 계정으로 포크**
   ```bash
   # 또는 템플릿으로 사용
   https://github.com/your-username/zmk-config
   ```

2. **코드 수정 후 푸시**
   ```bash
   git add .
   git commit -m "Update keymap"
   git push
   ```

3. **펌웨어 다운로드**
   - GitHub Actions 탭으로 이동
   - 완료된 빌드 클릭
   - Artifacts에서 `firmware.zip` 다운로드
   - 압축 해제하면 2개의 `.uf2` 파일:
     - `gkey_left-nice_nano_v2-zmk.uf2`
     - `gkey_right-nice_nano_v2-zmk.uf2`

### 로컬 빌드 (선택사항)

<details>
<summary>로컬 환경에서 빌드하기</summary>

**필요 도구:**
- Python 3
- CMake
- Ninja
- West

**빌드 실행:**
```bash
# West 초기화
west init -l config/
west update
west zephyr-export
pip3 install -r zephyr/scripts/requirements.txt

# 왼쪽 보드
west build -p -d build/left -b nice_nano_v2 -- -DSHIELD=gkey_left

# 오른쪽 보드
west build -p -d build/right -b nice_nano_v2 -- -DSHIELD=gkey_right
```

펌웨어 위치: `build/left/zephyr/zmk.uf2`, `build/right/zephyr/zmk.uf2`
</details>

## 📥 펌웨어 설치

1. **부트로더 모드 진입**
   - Nice!Nano를 USB로 PC에 연결
   - RST 핀을 GND에 2번 빠르게 터치
   - "NICENANO" 드라이브가 나타남

2. **펌웨어 업로드**
   - 해당하는 `.uf2` 파일을 드라이브에 드래그 앤 드롭
   - 자동으로 재부팅됨

3. **양쪽 보드 모두 설치**
   - 왼쪽: `gkey_left-nice_nano_v2-zmk.uf2`
   - 오른쪽: `gkey_right-nice_nano_v2-zmk.uf2`

## 🔌 페어링 방법

### 초기 설정 (한 번만)
1. 오른쪽 보드 전원 켜기
2. 왼쪽 보드 전원 켜기
3. 10초 정도 대기하면 자동 페어링

### PC/기기 연결
1. 왼쪽 보드만 PC와 블루투스 페어링
2. 오른쪽 보드는 왼쪽과 자동 연결됨

### 여러 기기 사용
- `FN2 + 1~5`: 각 프로파일로 전환
- 새 기기 페어링 시: 해당 번호 누르고 기기에서 연결

### 문제 해결
- 연결이 안 되면: `FN2 + Backspace`로 초기화 후 재페어링

## 🔋 배터리 설정

- **절전 모드**: 15분 유휴 시 자동 슬립
- **블루투스 전송**: +8dBm (안정적 연결)
- **예상 배터리 수명**: 500mAh 기준 약 1~2개월

배터리 설정 변경:
- `config/boards/shields/gkey/gkey_left.conf`
- `config/boards/shields/gkey/gkey_right.conf`

## 📁 프로젝트 구조

```
zmk-config/
├── config/
│   ├── gkey.keymap                    # 키맵 정의
│   └── boards/shields/gkey/
│       ├── gkey.dtsi                  # 공통 매트릭스 정의
│       ├── gkey_left.overlay          # 왼쪽 보드 GPIO 설정
│       ├── gkey_right.overlay         # 오른쪽 보드 GPIO 설정
│       ├── gkey_left.conf             # 왼쪽 보드 config
│       ├── gkey_right.conf            # 오른쪽 보드 config
│       ├── Kconfig.shield             # Shield 정의
│       └── Kconfig.defconfig          # 기본 config
├── build.yaml                         # GitHub Actions 빌드 설정
└── README.md
```

## 🛠️ 커스터마이징

### 키맵 수정
`config/gkey.keymap` 파일을 편집하여 키 배치 변경

**키코드 참조:**
- [ZMK 키코드 문서](https://zmk.dev/docs/codes)
- [ZMK 비헤이비어 문서](https://zmk.dev/docs/behaviors)

### GPIO 핀 변경
- `config/boards/shields/gkey/gkey_left.overlay`
- `config/boards/shields/gkey/gkey_right.overlay`

### 배터리/블루투스 설정
- `config/boards/shields/gkey/gkey_left.conf`
- `config/boards/shields/gkey/gkey_right.conf`

## 📚 참고 자료

- [ZMK 공식 문서](https://zmk.dev/)
- [ZMK Discord](https://zmk.dev/community/discord/invite)
- [Nice!Nano 문서](https://nicekeyboards.com/docs/nice-nano/)

## ⚠️ 주의사항

1. **펌웨어 업로드 순서**: 오른쪽 → 왼쪽 순서로 업로드 권장
2. **배터리 극성**: + (RAW), - (GND) 확인 필수
3. **첫 페어링**: 양쪽 보드가 모두 켜진 상태에서 진행
4. **유휴 타임아웃**: 15분 후 슬립 모드 (키 입력 시 즉시 복구)

## 📝 라이선스

이 프로젝트는 MIT 라이선스를 따릅니다.

## 🤝 기여

버그 리포트나 기능 제안은 Issues에 등록해주세요.

---

**Created with ❤️ for GKey Wireless Split Keyboard**