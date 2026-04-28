# 제주 렌터카 통합 가격 비교기

제주도 렌터카 4개 플랫폼의 가격을 동시에 수집하여 비교하는 크롤러 + GUI 프로그램입니다.

## 지원 플랫폼

| 플랫폼 | 방식 |
|--------|------|
| [제주패스](https://jejupass.com) | REST API |
| [카모아](https://carmore.kr) | REST API (세션 기반) |
| [돌하루팡](https://dolharupang.com) | REST API |
| [찜카](https://zzimcar.com) | REST API |

## 주요 기능

- **통합 가격 수집** — 4개 플랫폼을 동시 조회해 차종별 가격을 하나의 표로 정리
- **가격 비교 피벗** — 동일 차종의 플랫폼별 가격을 나란히 비교
- **단일 검색 / 자동 모드** — 특정 날짜 1회 검색 또는 날짜 범위 반복 수집
- **결과 저장** — 원본 데이터(xlsx) + 가격 비교표(xlsx) 저장
- **차량명 정규화** — 플랫폼마다 다른 차량명을 동일 기준으로 통일
- **업체명 정리** — 중복·불필요 키워드 제거로 업체명 표준화

## 화면

tkinter 기반 GUI로 실행됩니다.

```
┌─────────────────────────────────────────┐
│  🚗 제주 렌터카 가격 통합 비교 시스템      │
├─────────────────────────────────────────┤
│ 검색 옵션: ● 단일 검색  ○ 자동 모드       │
│ 대여 시작일: 2026-04-29  시작 시간: 09:00 │
│ 반납 종료일: 2026-05-01  종료 시간: 09:00 │
├─────────────────────────────────────────┤
│ 수집 플랫폼: ✅제주패스 ✅찜카 ✅카모아 ✅돌하루팡 │
├─────────────────────────────────────────┤
│  [🔍 검색 시작]  [💾 결과 저장]           │
│  결과 표시 영역...                        │
└─────────────────────────────────────────┘
```

## 설치

```bash
pip install requests pandas openpyxl
```

> tkinter는 Python 표준 라이브러리에 포함되어 있습니다.  
> macOS에서 tkinter가 없는 경우: `brew install python-tk`

## 실행

```bash
python jeju_rental_car_price_comparison.py
```

콘솔 모드로 실행하려면:

```bash
python jeju_rental_car_price_comparison.py --console
```

## 출력 파일

수집 결과는 선택한 폴더에 저장됩니다.

| 파일명 | 내용 |
|--------|------|
| `렌터카수집_YYYYMMDD_HHMMSS.xlsx` | 플랫폼별 전체 수집 데이터 |
| `가격비교_YYYYMMDD_HHMMSS.xlsx` | 차종별 플랫폼 가격 비교 피벗 |

## 코드 구조

```
jeju_rental_car_price_comparison.py
│
├── 데이터 수집
│   ├── fetch_jeju_pass_filtered()   # 제주패스 API
│   ├── fetch_carmore_data()         # 카모아 API (CarmoreDataCollector 클래스)
│   ├── fetch_dolharupang_data()     # 돌하루팡 API
│   └── fetch_zzimcar_data()         # 찜카 API
│
├── 데이터 처리
│   ├── clean_vendor_name()          # 업체명 정규화
│   ├── parse_car_name()             # 차량명 파싱 (제조사/모델/등급 분리)
│   ├── normalize_car_names()        # 플랫폼 간 차량명 통일
│   └── create_price_comparison_pivot() # 가격 비교 피벗 생성
│
└── 실행
    ├── RentalCarGUI                 # tkinter GUI (기본)
    ├── run_single_search()          # 콘솔 단일 검색
    └── run_auto_mode()              # 콘솔 자동 모드
```

## 설정

파일 상단에서 기본값을 변경할 수 있습니다.

```python
SAVE_DIR = os.path.join(os.path.expanduser("~"), "Downloads")  # 기본 저장 경로
TIMEOUT = 60.0       # API 요청 타임아웃 (초)
TRAVEL_TERMS = [2]   # 자동 모드 여행 기간 (박수)
```

## 주의사항

- 각 플랫폼의 API 구조 변경 시 수집이 중단될 수 있습니다.
- 수집 데이터는 개인 이용 목적으로만 사용하세요.
- 과도한 요청은 IP 차단으로 이어질 수 있으니 주의하세요.
