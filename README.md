# 스케줄러 벤치마크 프로젝트

Linux 커널 스케줄러 성능을 측정하고 분석하는 종합 벤치마크 도구 입니다.

## 프로젝트 목표

- **CFS vs EEVDF** 스케줄러 성능 비교
- **컴파일러별 성능** 분석 (GCC, Clang, ICC)
- **하드웨어 레벨 성능** 카운터 측정
- **시스템 상호작용성** 평가

## 프로젝트 구조

```
scheduler-benchmark/
├── scripts/                      # 핵심: Bash로 모든 실행
│   ├── 00_setup.sh              # 환경 검증 및 도구 설치
│   ├── 01_system_info.sh        # 시스템 정보 수집
│   ├── 02_latency_bench.sh      # Latency 벤치마크 (논문 방법론)
│   ├── 03_compilation.sh        # Turn-around time
│   ├── 04_interbench.sh         # Interactivity
│   ├── 05_hardware_perf.sh      # PMU/perf 측정
│   └── run_all.sh               # 전체 자동 실행
│
├── analysis/                     # Python은 여기만
│   ├── parse_json.py            # JSON 파싱
│   ├── calculate_metrics.py     # 메트릭 계산
│   ├── plot_graphs.py           # matplotlib 시각화
│   └── generate_report.py       # 보고서 생성
│
├── results/                      # 결과 저장
│   ├── cfs/
│   │   ├── system-info.txt
│   │   ├── latency.json
│   │   ├── compile.json
│   │   └── interbench.txt
│   └── eevdf/
│       └── (동일 구조)
│
├── plots/                        # 생성된 그래프
│   ├── latency-comparison.png
│   ├── turnaround-time.png
│   └── interactivity-*.png
│
├── report.md                     # 최종 보고서
└── README.md
```

## 빠른 시작

### 1. 전체 벤치마크 실행
```bash
# 모든 벤치마크를 자동으로 실행
./scripts/run_all.sh results/cfs

# 또는 개별 스크립트 실행
./scripts/00_setup.sh
./scripts/01_system_info.sh results/cfs
./scripts/02_hackbench.sh results/cfs
./scripts/03_compilation.sh results/cfs
./scripts/04_interbench.sh results/cfs
./scripts/05_hardware_perf.sh results/cfs
```

### 2. 결과 분석
```bash
# JSON 데이터 파싱
cd analysis
python3 parse_json.py ../results/cfs

# 그래프 생성
python3 plot_graphs.py ../results/cfs

# 보고서 생성
python3 generate_report.py ../results/cfs
```

## 벤치마크 종류

### 1. **스케줄러 지연시간 벤치마크** - 논문 방법론 준수
- `perf bench sched messaging -p -t -g 20 -l 2000` 사용
- 논문 표준 방법론으로 스케줄러 지연시간 측정
- hyperfine 50회 반복으로 정밀 측정

### 2. **컴파일 벤치마크** - Turn-around Time
- GCC, Clang, Intel C Compiler 비교
- 컴파일 시간, 바이너리 크기, 메모리 사용량 측정
- 다양한 복잡도의 C 소스 코드로 테스트

### 3. **Interbench** - 상호작용성
- 시스템의 상호작용성 점수 측정
- 백그라운드 워크로드와 함께 실행
- 지연 점수, 처리량 점수, 최종 점수 제공

### 4. **하드웨어 성능 카운터** - PMU/perf
- CPU 사이클, 캐시 미스, 분기 예측 실패 측정
- perf stat, perf top, perf record 사용
- 시스템 전체 성능 모니터링

## 설치된 도구들

- **hyperfine**: 빠른 벤치마킹 도구
- **perf**: Linux 성능 분석 도구
- **hackbench**: 스케줄러 테스트 도구
- **interbench**: 상호작용성 테스트 도구
- **libpfm4**: 성능 이벤트 라이브러리

## 결과 분석

### JSON 데이터
- 모든 벤치마크 결과가 JSON 형태로 저장
- 타임스탬프, 메트릭, 원시 데이터 포함

### 시각화
- matplotlib를 사용한 그래프 생성
- 각 벤치마크별 성능 비교 차트
- 상관관계 분석 히트맵

### 보고서
- 마크다운 형태의 종합 보고서
- 통계 요약 및 권장사항 포함
- 생성된 그래프 자동 포함

## 시스템 요구사항

- **OS**: Ubuntu/Debian, Fedora, Arch Linux
- **커널**: 5.15+ (perf 지원)
- **Python**: 3.6+ (matplotlib, pandas, numpy)
- **권한**: sudo 권한 (도구 설치용)

## 사용 예시

```bash
# CFS 스케줄러로 벤치마크 실행
./scripts/run_all.sh results/cfs

# EEVDF 스케줄러로 벤치마크 실행 (6.6+ 커널)
./scripts/run_all.sh results/eevdf

# 결과 비교 분석
cd analysis
python3 parse_json.py ../results/cfs
python3 parse_json.py ../results/eevdf
python3 plot_graphs.py ../results/cfs
python3 generate_report.py ../results/cfs
```

## 학부 연구 활용

이 프로젝트는 다음과 같은 연구에 활용할 수 있습니다:

- **커널 스케줄러 성능 분석**
- **컴파일러 최적화 효과 측정**
- **시스템 성능 튜닝**
- **하드웨어 성능 카운터 활용**

## 문의

프로젝트 관련 문의사항이 있으시면 이슈를 등록해 주세요.