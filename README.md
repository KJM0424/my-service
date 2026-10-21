# my-service

공개 API와 공공데이터로 만든 작은 생활 서비스 모음이에요. 서비스마다 HTML 파일이 하나씩 있어요.

| 서비스 | 파일 | 한 줄 소개 |
|---|---|---|
| 출근길 | `commute.html` | 역을 고르면 다음 열차까지 남은 시간, 지금 나가야 할 시각, 서울 날씨·미세먼지를 한 화면에서 보여 줘요 |

## 출근 도우미(출근길) 기능
- 호선과 역 이름을 넣으면 방향별로 다음 열차까지 남은 시간이 크게 보이고, 30초마다 다시 계산된다
- 역까지 걸리는 시간을 넣으면 "몇 시 열차를 탈 수 있고 몇 분 안에 나가야 하는지" 알려 준다
- 행선지 필터(예: 1호선 천안행만), 첫차·막차, 시간대별 전체 시간표(지난 열차는 흐리게)
- 즐겨찾기 역과 최근 검색 칩, 공유 링크(`?line=2호선&stn=강남`). 즐겨찾기 첫 역은 열자마자 자동 검색
- 오늘이 공휴일이면 주말 시간표를 자동 선택하고 다음 공휴일 D-day를 보여 준다 (Nager.Date)
- 서울 날씨·12시간 예보·미세먼지(환경부 기준)를 보고 우산·마스크·겉옷 안내를 띄운다
- 같은 역 시간표는 하루 한 번만 부르고 브라우저에 저장해 API 호출을 아낀다
- **목적지까지 다시 찾기**: 목적지를 저장해 두고, 열차가 늦거나 길을 바꿔야 할 때 버튼을 누르면 지금 위치(브라우저 위치 기능)를 출발지로 네이버 지도 앱·카카오맵 대중교통 길찾기를 바로 열어 준다. 지도에 지금 위치와 목적지를 표시한다

## 실행 방법
1. 공공데이터포털에서 [서울교통공사_열차시간표](https://www.data.go.kr/data/15143847/openapi.do) 활용신청 → 마이페이지에서 **일반 인증키(Decoding)** 복사
2. 키를 넣는 방법은 둘 중 하나예요
   - 로컬: `config.example.js`를 복사해 `config.js`로 만들고 키를 붙여넣기 (`config.js`는 git에 올라가지 않아요)
   - GitHub Pages: 화면의 인증키 칸에 붙여넣기 (이 브라우저의 localStorage에만 저장돼요)
3. `commute.html`을 Live Server로 열기

## API 문서 6칸

### 서울교통공사_열차시간표 (공공데이터포털)
| 칸 | 내용 |
|---|---|
| ① 기본 주소 | `https://apis.data.go.kr/B553766/schedule` |
| ② 메뉴 | `getTrainSch` (열차시간표 조회) |
| ③ 옵션 | 필수 `serviceKey`, `tmprTmtblYn`(Y/N), `upbdnbSe`(상행/하행, 2호선은 내선/외선), `wkndSe`(실제로는 평일/주말만 데이터가 옴), `lineNm`(예: 2호선) · 선택 `stnNm`(포함 검색), `searchDt`(지금 유효한 시간표만), `dataType=JSON`, `numOfRows` |
| ④ 응답 모양 | `{ response: { header: { resultCode }, body: { items: { item: [ { stnNm, trainDptreTm, arvlStnNm, etrnYn, crtrYmd } ] }, totalCount } } }` |
| ⑤ 키 | 필요 (공공데이터포털 활용신청 후 일반 인증키) |
| ⑥ 규칙 | 무료, 이용허락범위 제한 없음. 서울교통공사 운영 구간(1~9호선)만 제공 |

### Open-Meteo 날씨 예보
| 칸 | 내용 |
|---|---|
| ① 기본 주소 | `https://api.open-meteo.com/v1/` |
| ② 메뉴 | `forecast` |
| ③ 옵션 | `latitude`, `longitude`, `current=temperature_2m,apparent_temperature,weather_code`, `hourly=precipitation_probability`, `forecast_hours=12`, `timezone=Asia/Seoul` |
| ④ 응답 모양 | `{ current: { time, temperature_2m, weather_code }, hourly: { precipitation_probability: [] } }` |
| ⑤ 키 | 필요 없음 |
| ⑥ 규칙 | 비상업적 용도 무료, 하루 1만 회 이하 권장, 출처 표기 "Weather data by Open-Meteo.com" |

### Open-Meteo 대기질
| 칸 | 내용 |
|---|---|
| ① 기본 주소 | `https://air-quality-api.open-meteo.com/v1/` |
| ② 메뉴 | `air-quality` |
| ③ 옵션 | `latitude`, `longitude`, `current=pm10,pm2_5`, `timezone=Asia/Seoul` |
| ④ 응답 모양 | `{ current: { time, pm10, pm2_5 } }` |
| ⑤ 키 | 필요 없음 |
| ⑥ 규칙 | 날씨 예보와 같음 |

### Nager.Date 공휴일
| 칸 | 내용 |
|---|---|
| ① 기본 주소 | `https://date.nager.at/api/v3/` |
| ② 메뉴 | `PublicHolidays/{연도}/KR` |
| ③ 옵션 | 주소 안의 연도, 나라 코드 |
| ④ 응답 모양 | `[ { date, localName, name } ]` |
| ⑤ 키 | 필요 없음 |
| ⑥ 규칙 | 무료, 한국어 이름은 `localName` |

### Nominatim (OpenStreetMap 장소 검색·역지오코딩)
| 칸 | 내용 |
|---|---|
| ① 기본 주소 | `https://nominatim.openstreetmap.org/` |
| ② 메뉴 | `search` (이름 → 좌표), `reverse` (좌표 → 주소) |
| ③ 옵션 | `q` 또는 `lat`·`lon`, `format=jsonv2`, `countrycodes=kr`, `accept-language=ko` |
| ④ 응답 모양 | `[ { name, lat, lon, address: { city, borough, suburb } } ]` |
| ⑤ 키 | 필요 없음 |
| ⑥ 규칙 | 초당 1회 이하, 출처 "© OpenStreetMap contributors" 표기. 지도 타일(`tile.openstreetmap.org`)도 같은 출처 표기 |

### 길찾기 링크 (API 호출 아님, 주소 형식)
| 대상 | 형식 |
|---|---|
| 네이버 지도 앱 | `nmap://route/public?slat=&slng=&sname=&dlat=&dlng=&dname=&appname=` |
| 카카오맵 | `https://map.kakao.com/link/by/traffic/{이름},{위도},{경도}/{이름},{위도},{경도}` |
