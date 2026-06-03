# ⚽ AI Football Manager

> GPT-4o 기반 멀티에이전트 축구 정보 플랫폼 | SK쉴더스 루키즈 팀 프로젝트

실시간 축구 뉴스 수집부터 감정 분석, 경기 예측까지 — 여러 AI 에이전트가 협력하여 사용자 질문에 통합 답변을 제공하는 멀티에이전트 시스템입니다.

---

## 🙋 본인 기여 역할

4인 팀 프로젝트로 진행되었으며, 본인은 **뉴스 분석 파이프라인 전담** 및 **API-Football 데이터 수집 보조**를 담당했습니다.

| 담당 파일 | 역할 |
|---|---|
| `agents/news_analysis_agent.py` | 뉴스 분석 에이전트 전체 설계 및 구현 |
| `tools/news_tools.py` | News API 연동, 요약/감정분석/코멘트 함수 구현 |
| `tools/data_collector_tools.py` | API-Football 데이터 수집 보조 |

---

## 🔍 뉴스 분석 파이프라인 (담당 파트 상세)

사용자의 자연어 질문을 받아 실시간 축구 뉴스를 검색하고, 요약·감정 분석·AI 코멘트까지 자동 생성하는 end-to-end 파이프라인을 직접 설계했습니다.

```
사용자 질문 입력
    ↓
키워드 추출 (GPT-4o, temperature=0.0)
    ↓
News API 실시간 검색 (최대 5건)
    ↓
기사별 요약 생성 (GPT-4o, 축구 기사 필터링)
    ↓
감정 분석 (긍정 / 부정 / 중립)
    ↓
AI 코멘트 생성 (축구팬 시점)
    ↓
최종 종합 응답 반환 (스트리밍)
```

### 구현 포인트

- **GPT-4o 프롬프트 직접 설계**: 키워드 추출, 요약, 감정 분석, 코멘트 생성 각각 목적에 맞는 프롬프트 최적화 (temperature·max_tokens 조정)
- **토큰 비용 최적화**: `token_manager`로 대화 이력 관리, 불필요한 컨텍스트 제거
- **예외 처리 구조화**: `RequestException`, `JSONDecodeError` 분리 처리로 API 장애 시에도 서비스 안정성 확보
- **축구 관련 기사 필터링**: 비관련 기사는 요약 단계에서 자동 제외, 최소 3건 유효 기사 수집 후 처리

---

## 🛠 기술 스택

**AI / ML**
- OpenAI GPT-4o — 질문 분류, 뉴스 요약, 감정 분석, 응답 생성
- scikit-learn — 경기 예측 ML 모델 (팀원 담당)

**데이터 수집**
- News API — 실시간 뉴스 검색
- API-Football (RapidAPI) — 경기 일정 및 결과
- soccerdata — Elo 레이팅, xG 등 축구 통계

**Backend / UI**
- Python 3.9.13
- Streamlit — 대화형 웹 인터페이스
- requests, joblib, python-dateutil

---

## 🏗 프로젝트 구조

```
AI-Football-Manager/
├── ai_football_manager/
│   ├── agents/
│   │   ├── router_agent.py          # 질문 분류 및 에이전트 라우팅
│   │   ├── news_analysis_agent.py   # ✅ 뉴스 분석 에이전트 (본인 담당)
│   │   ├── prediction_agent.py      # 경기 예측 에이전트
│   │   └── team_player_agent.py     # 팀/선수 정보 에이전트
│   ├── tools/
│   │   ├── news_tools.py            # ✅ 뉴스 수집·분석 도구 (본인 담당)
│   │   ├── data_collector_tools.py  # ✅ 축구 데이터 수집 (보조)
│   │   ├── prediction_tools.py      # 예측 관련 도구
│   │   └── sports_data_api.py       # 스포츠 데이터 API 인터페이스
│   ├── utils/
│   │   ├── prompt_templates.py      # AI 프롬프트 템플릿
│   │   └── token_manager.py         # OpenAI 토큰 관리
│   └── app.py                       # Streamlit 앱 진입점
└── requirements.txt
```

---

## 🚀 실행 방법

```bash
# 1. 저장소 클론
git clone <repository-url>
cd AI-Football-Manager

# 2. 가상환경 생성 및 활성화
python -m venv .venv
source .venv/bin/activate  # macOS/Linux
# .venv\Scripts\activate   # Windows

# 3. 의존성 설치
pip install -r requirements.txt

# 4. 환경 변수 설정 (.env 파일 생성)
OPENAI_API_KEY=your_openai_api_key
NEWS_API_KEY=your_news_api_key
X_RAPIDAPI_KEY=your_rapidapi_key

# 5. 실행
streamlit run ai_football_manager/app.py
```

브라우저에서 `http://localhost:8501` 접속

---

## 💬 사용 예시

```
# 뉴스 분석 (본인 담당)
"오늘 프리미어리그 소식을 알려줘"
"손흥민 최근 뉴스를 분석해줘"

# 경기 예측
"5월 18일 아스널 경기 예측해줘"

# 복합 질문 (다중 에이전트 동시 실행)
"토트넘 경기 예측하고 상대팀 브라이턴 전적 분석해줘"
```

---

## 📚 배운 점 & 회고

- 멀티에이전트 아키텍처에서 **각 에이전트의 역할 분리**가 유지보수에 얼마나 중요한지 직접 체감
- GPT-4o 프롬프트 설계 시 **temperature와 max_tokens 튜닝**이 결과 품질에 미치는 영향을 실험적으로 학습
- 실시간 외부 API 연동 시 **예외 처리 설계**의 중요성 — 네트워크 오류, JSON 파싱 오류를 분리 처리하여 서비스 안정성 확보
- 토큰 비용 최적화를 통해 **AI 서비스의 운영 비용 관리** 개념을 처음 접함

---

## 🏫 진행 배경

**SK쉴더스 루키즈** — 생성형 AI를 활용한 정보보안 교육 프로그램 내 팀 프로젝트로 진행
