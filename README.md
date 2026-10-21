# my-service

공개 API와 공공데이터로 만든 작은 생활 서비스 모음이에요. 서비스마다 HTML 파일이 하나씩 있어요.

| 서비스 | 파일 | 한 줄 소개 |
|---|---|---|
| 출근 도우미 | `commute.html` | 역을 고르면 지금 이후 열차 3대와 서울 날씨·미세먼지를 한 화면에서 보여 줘요 |

## 출근 도우미 기능
- 호선과 역 이름을 넣으면 상행·하행(2호선은 내선·외선) 시간표가 나오고, 지금 이후 열차 3대가 맨 위에 보인다
- 서울 현재 기온·날씨와 미세먼지·초미세먼지 등급(환경부 기준)이 기준 시각과 함께 보인다
- 비 올 확률이 50% 이상이거나 미세먼지가 나쁨 이상이면 "우산", "마스크" 안내가 보인다

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
| ③ 옵션 | 필수 `serviceKey`, `tmprTmtblYn`(Y/N), `upbdnbSe`(상행/하행/내선/외선), `wkndSe`(평일/주말/공휴일), `lineNm`(예: 2호선) · 선택 `stnNm`, `dataType=JSON`, `numOfRows` |
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
