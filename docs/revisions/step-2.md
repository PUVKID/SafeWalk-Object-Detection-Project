<div align="center">

# 🚶 실험 구성과 기록

bicycle·kickboard·bollard·car·motorcycle·stairs·crosswalk·people을 공통 8클래스로 통합합니다.

**기술 분류:** Python · Jupyter · Ultralytics YOLOv8x · PyTorch · 8클래스 탐지

[핵심 기능](#features) · [사용 흐름](#usage) · [자료 구성](#structure) · [준비와 실행](#setup) · [결과 읽기](#results) · [직전 변경](#changes)

</div>

[🏠 프로젝트](../../README.md) · [🗂️ 전체 차수](../REVISION_INDEX.md) · [📁 원본 자료](../../pbl2-main) · [← 이전: 기본 노트북](../materials/001-step-1/README.md)

## 🎯 목적과 이 차수의 역할

bicycle·kickboard·bollard·car·motorcycle·stairs·crosswalk·people을 공통 8클래스로 통합합니다.

<a name="features"></a>

## ✨ 핵심 기능과 보존 범위

| 항목 | 이 차수에서 확인할 내용 |
|---|---|
| 1 | bicycle·kickboard·bollard·car·motorcycle·stairs·crosswalk·people을 공통 8클래스로 통합합니다. |
| 2 | Phase 1은 클래스별 15~40 epoch, Phase 2는 선택한 모델에서 전체 150 epoch 미세조정을 구성합니다. |
| 3 | 데이터 분할은 강제 테스트 배정 후 남은 영상 74% 학습·21% 검증·나머지 테스트입니다. 표제의 7:2:1과 실제 배정은 다릅니다. |
| 4 | 추론·영상 데모·내보내기 경로는 학습과 별도이며 실제 장치 환경과 가중치가 필요합니다. |

## 🔄 입력·처리·출력

| 단계 | 내용 |
|---|---|
| 📥 입력 | Roboflow·COCO 계열 영상·라벨과 클래스 매핑 |
| ⚙️ 처리 | 클래스 통합 → 데이터 분할 → 클래스별 학습 → 통합 미세조정 → 평가 |
| 📤 출력 | 학습 설정 YAML·에폭별 CSV·평가/추론·모델 출력 |

<a name="usage"></a>

## 🖐️ 화면과 사용 흐름

1. configs의 공통 8클래스·클래스별 YAML을 읽습니다.
2. models 아래 각 실험의 args.yaml과 results.csv를 같은 실행 단위로 대조합니다.
3. 현재 노트북과 이전 도커 버전 노트북을 구분해 셀·설정 차이를 읽습니다.
4. 저장된 에폭 지표를 확인하고 새로운 학습 출력과 섞지 않습니다.

<a name="structure"></a>

## 📁 실제 자료 구성

| 경로 | 자료 종류 |
|---|---|
| [configs/](../../pbl2-main/configs) | 하위 자료 폴더 |
| [models/](../../pbl2-main/models) | 하위 자료 폴더 |
| [README.md](../../pbl2-main/README.md) | MD 파일 |
| [safewalk_train_v8_fixed (1).ipynb](../../pbl2-main/safewalk_train_v8_fixed%20%281%29.ipynb) | IPYNB 파일 |
| [safewalk_train_v8_fixed (1)_이전도커버전.ipynb](../../pbl2-main/safewalk_train_v8_fixed%20%281%29_%EC%9D%B4%EC%A0%84%EB%8F%84%EC%BB%A4%EB%B2%84%EC%A0%84.ipynb) | IPYNB 파일 |

## 🧩 주요 모듈과 코드 읽기

| 파일 | 원문에 정의된 역할·구성 |
|---|---|
| [safewalk_train_v8_fixed (1).ipynb](../../pbl2-main/safewalk_train_v8_fixed%20%281%29.ipynb) | 노트북 23개 셀 |
| [safewalk_train_v8_fixed (1)_이전도커버전.ipynb](../../pbl2-main/safewalk_train_v8_fixed%20%281%29_%EC%9D%B4%EC%A0%84%EB%8F%84%EC%BB%A4%EB%B2%84%EC%A0%84.ipynb) | 노트북 23개 셀 |

<a name="setup"></a>

## 🚀 설치 환경과 실행·사용법

Jupyter와 노트북 설치 셀의 패키지, GPU·CUDA 환경, 본인의 ROBOFLOW_API_KEY와 데이터셋 접근 권한을 준비합니다. 원래 PROJECT_ROOT는 /root/pbl2-main이며 전용 실험 폴더로 바꿉니다. COCO 분기는 fiftyone이 별도로 필요합니다.

최상위 safewalk_train_v8_fixed (1).ipynb 또는 이 묶음의 해당 노트북을 Jupyter에서 열고 설정·데이터 매핑·GPU 확인 셀부터 순서대로 읽습니다. 병합·분할 셀은 merged·processed·per_class 결과를 재생성하므로 이전 결과와 분리합니다.

### 분할·학습·평가를 읽는 순서

원본별 train·valid·test 영상을 모은 뒤 공통8클래스로 라벨을 다시 매핑합니다. YOLO bbox의5개 항목만 병합하고 세그멘테이션 라인은 제외합니다. 이름 매핑이 하나도 맞지 않으면 ID 정렬 순서 fallback이 있으므로 출력 매핑을 확인해야 합니다. 이 분할 코드에는 이미지 내용 중복 검출이 없습니다.

볼라드·횡단보도는 각각 `min(100, 이미지 수의15%)`를 테스트로 우선 확보합니다. 남은 영상의74%를 학습,21%를 검증,나머지를 테스트로 보냅니다. 따라서 셀 제목의7:2:1이 실제 전체 비율은 아닙니다.

Phase1은 YOLOv8x·640·AdamW로 클래스별15~40epoch를 구성합니다. Phase2는 선택한 모델에서 전체150epoch, lr0=0.0005, patience=0으로 미세조정합니다. 테스트 결과로 시작 모델을 선택하므로 같은 시험셋 최종 수치는 모델 선택과 독립된 평가가 아닙니다.

추론 설정은 conf0.4·IoU0.45·640·최대50개 탐지입니다. 속도 측정은 무작위 이미지 한 장에 준비10회와 반복100회를 적용한 장치별 측정이며 실시간 보행 안전 성능과 다릅니다. `SafeWalkDetector`의 주의 문구는 객체 종류에 따른 표시이고 거리·충돌 예측값은 아닙니다.

<a name="results"></a>

## 📊 처리 결과와 출력 읽기

클래스별 results.csv의 epoch·precision·recall·mAP50·mAP50-95를 args.yaml과 함께 읽습니다. 테스트값으로 Phase 2 시작 모델을 선택하므로 같은 시험값은 모델 선택과 완전히 독립된 평가가 아닙니다. 위험 문구는 객체 종류 기반 표시이며 거리·충돌 예측 결과가 아닙니다.

## 🛠️ 제약과 문제 해결

| 상황 | 확인할 내용 |
|---|---|
| 다운로드 인증 실패 | 본인 환경변수·프로젝트·버전·이용 조건을 확인합니다. |
| 클래스 ID 불일치 | class_map의 이름과 실제 원본 라벨을 대조합니다. 정렬 순서 fallback의 실행 여부를 확인합니다. |
| 웹캠 창 표시 실패 | 설치 셀의 headless OpenCV와 GUI 표시용 환경 차이를 확인합니다. |

<a name="changes"></a>

## 🔍 직전 자료와 전체 비교

기본 노트북에서 실험 구성과 기록로 이어지는 실제 파일 비교입니다.

[📄 전체 비교·자료 범위](step-2.patch)

추가·수정·삭제, 주석·공백·개행과 바이너리 크기·해시는 연결된 원래 비교 자료에서 확인합니다.

<details>
<summary>📚 파일별 비교·크기·해시 자세히 보기</summary>

[전체 텍스트 차이](step-2.patch) · [파일별 크기·SHA-256](step-2-files.json)

| 구분 | 파일 |
| :--- | :--- |
| 추가 | `configs/dataset_8class.yaml` |
| 추가 | `configs/dataset_bicycle.yaml` |
| 추가 | `configs/dataset_bollard.yaml` |
| 추가 | `configs/dataset_car.yaml` |
| 추가 | `configs/dataset_crosswalk.yaml` |
| 추가 | `configs/dataset_kickboard.yaml` |
| 추가 | `configs/dataset_motorcycle.yaml` |
| 추가 | `configs/dataset_people.yaml` |
| 추가 | `configs/dataset_stairs.yaml` |
| 추가 | `models/phase1_per_class/bicycle/args.yaml` |
| 추가 | `models/phase1_per_class/bicycle/results.csv` |
| 추가 | `models/phase1_per_class/bollard/args.yaml` |
| 추가 | `models/phase1_per_class/bollard/results.csv` |
| 추가 | `models/phase1_per_class/car/args.yaml` |
| 추가 | `models/phase1_per_class/car/results.csv` |
| 추가 | `models/phase1_per_class/crosswalk/args.yaml` |
| 추가 | `models/phase1_per_class/crosswalk/results.csv` |
| 추가 | `models/phase1_per_class/kickboard/args.yaml` |
| 추가 | `models/phase1_per_class/kickboard/results.csv` |
| 추가 | `models/phase1_per_class/motorcycle/args.yaml` |
| 추가 | `models/phase1_per_class/motorcycle/results.csv` |
| 추가 | `models/phase1_per_class/people/args.yaml` |
| 추가 | `models/phase1_per_class/people/results.csv` |
| 추가 | `models/phase1_per_class/stairs/args.yaml` |
| 추가 | `models/phase1_per_class/stairs/results.csv` |
| 추가 | `models/safewalk_v8/args.yaml` |
| 추가 | `models/safewalk_v8/results.csv` |
| 추가 | `safewalk_train_v8_fixed (1)_이전도커버전.ipynb` |

</details>

[🏠 프로젝트](../../README.md) · [🗂️ 전체 차수](../REVISION_INDEX.md) · [📁 원본 자료](../../pbl2-main) · [← 이전: 기본 노트북](../materials/001-step-1/README.md)
