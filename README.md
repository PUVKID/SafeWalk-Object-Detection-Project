<div align="center">

# 🚶 SafeWalk Project

여러 데이터셋을 통합해 보행환경의 8개 클래스를 탐지합니다.

**기술 분류:** 컴퓨터 비전 › 딥러닝 객체 검출 › 다중 데이터셋·8클래스 통합

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![YOLO](https://img.shields.io/badge/Detection-YOLOv8-7C3AED?style=flat-square)
![Jupyter](https://img.shields.io/badge/Jupyter-F37626?style=flat-square&logo=jupyter&logoColor=white)

[빠른 시작](#quick-start) · [저장소 구성](#repository-structure) · [클래스 구성](#data-settings)

[예시 이미지](#preview)

</div>

<a name="overview"></a>

## 🗺️ 한눈에 보는 구성

<p align="center"><img src="assets/overview.png" width="960" alt="프로젝트의 입력·처리·출력을 정리한 설명도" /></p>
보관한 프로젝트 자료의 흐름을 정리한 설명도입니다.

## ✨ 최신 차수에서 달라진 점

보행 안전 관련 8개 객체의 학습·평가 노트북과 실행 자료를 보관합니다.

테스트 결과로 시작 모델을 선택한 경로가 있어 같은 테스트 수치를 독립 시험 성능으로 해석하지 않습니다. 숫자 차수는 확인되지 않습니다.

- YOLO 학습·평가 경로를 담은 노트북입니다. 클래스 객체 검출과 실제 보행 위험 판정 성능을 구분합니다.
- 학습 데이터와 평가 설정을 노트북 셀에서 확인해야 하며, 테스트 결과를 이용한 시작 모델 선택 때문에 같은 시험 수치를 완전히 독립된 평가로 볼 수 없습니다.

**자료별 안내:** [기본](docs/versions/stage-1.md) · [전체 기록](docs/VERSIONS.md)


<a name="preview"></a>

## 🖼️ 예시로 보기

<p align="center"><img src="assets/previews/code-explainer-v2.png" width="900" alt="코드 기반 설명도 · SafeWalk-Object-Detection-Project" /></p>

**코드 기반 설명도** — 코드에 정의된 8개 탐지 클래스 목록입니다. 클래스 사이의 순차 관계나 성능 측정값을 뜻하지 않습니다.

[근거 파일 보기](safewalk_train_v8_fixed%20%281%29.ipynb)



<a name="input-processing-output"></a>

## 🔄 입력·처리·출력

| 단계 | 이 프로젝트에서 다루는 내용 |
| :--- | :--- |
| 입력 | Roboflow·COCO 등 객체 영상과 라벨 |
| 처리 | 클래스 ID 통합, YOLOv8x 클래스별 학습과 전체 미세조정 |
| 출력 | 8클래스 탐지·평가·영상 데모 |



---

<a name="introduction"></a>

## ✨ 프로젝트 소개

보행환경의 여러 객체를 하나의 분류 체계로 묶어 탐지 모델을 학습하는 프로젝트입니다.

SafeWalk는 보행환경을 구성하는 여덟 종류의 객체를 YOLO로 탐지하기 위한 학습 노트북입니다. Roboflow와 COCO 등 서로 다른 데이터의 클래스 표기를 공통 ID로 바꿔 통합하는 부분을 포함합니다.

| 기능 | 구현 내용 |
| :--- | :--- |
| 데이터 준비 | 여러 탐지 데이터셋의 다운로드·정리 |
| 클래스 통합 | 원본 라벨 이름을 공통 8클래스 ID에 매핑 |
| 학습 단계 | 클래스별 학습과 통합 미세조정 구성 |
| 장치 설정 | GPU 확인 및 메모리에 따른 배치 크기 선택 |
| 결과 확인 | 학습 기록·그래프·모델 출력 |
| 변환 경로 | 노트북의 추론·내보내기 실험 구성 |
| 영상 데모 | `SafeWalkDetector`의 웹캠·동영상·이미지 폴더 추론 |

```mermaid
flowchart LR
    A[Roboflow 등 탐지 데이터] --> C[클래스 이름 통합]
    B[COCO 데이터] --> C
    C --> D[공통 8클래스 데이터]
    D --> E[클래스별 학습]
    E --> F[통합 미세조정]
    F --> G[평가와 모델 출력]
```

<a name="repository-structure"></a>

## 📁 저장소 구성

```text
.
├── safewalk_train_v8_fixed (1).ipynb
├── pbl2-main/
│   ├── safewalk_train_v8_fixed (1).ipynb
│   └── safewalk_train_v8_fixed (1)_이전도커버전.ipynb
├── docs/VERSIONS.md
└── CHANGELOG.md
```

- [최상위 학습 노트북](safewalk_train_v8_fixed%20%281%29.ipynb).
- [보존된 실험 폴더](pbl2-main/).
- [버전 기록](docs/VERSIONS.md).

## 🧩 주요 기술

데이터를 준비하고 모델을 학습하는 데 Ultralytics YOLO, PyTorch, OpenCV, Roboflow, pandas, Matplotlib, PyYAML을 사용합니다. 노트북의 기본 모델은 YOLOv8x이며, GPU 메모리에 따라 배치 크기를 정하는 분기가 있습니다.

데이터셋별 `class_map`에서 비슷한 이름의 라벨을 공통 클래스로 매핑합니다. 예를 들어 보행자 표기나 계단·횡단보도의 여러 명칭을 통합하므로, 실제 다운로드한 버전의 라벨과 매핑이 일치하는지 확인해야 합니다.

<a name="quick-start"></a>

## 🚀 빠른 시작

```powershell
python -m pip install jupyterlab
python -m jupyter lab "safewalk_train_v8_fixed (1).ipynb"
```

1. 자신의 Roboflow 접근정보를 실행 환경의 `ROBOFLOW_API_KEY`에 준비합니다.
2. `PROJECT_ROOT`를 실제 실험 폴더에 맞춥니다. 원래 노트북은 `/root/pbl2-main` 경로를 사용합니다.
3. 패키지 설치·GPU 확인 셀을 읽고 실행 환경에 맞는 구성을 확인합니다.
4. 데이터셋 목록과 버전·이용 조건을 검토한 뒤 데이터 준비부터 실행합니다.
5. 학습 결과와 클래스별 지표를 확인하고 필요한 모델 출력 셀을 실행합니다.

CUDA 라이브러리 경로를 다루는 Linux 전용 셀이 있으므로 Windows 로컬 실행에서는 해당 환경 차이를 확인해야 합니다. 데이터셋과 완성 가중치는 별도로 준비합니다.

COCO 다운로드 분기는 `fiftyone.zoo`를 사용하지만 첫 설치 셀의 목록에는 `fiftyone`이 없습니다. 이 경로를 사용할 때는 별도 의존성을 준비해야 합니다. 데이터 병합·분할 셀은 기존 `merged`, `processed`, `per_class` 하위 결과를 지우고 다시 만드므로, `PROJECT_ROOT`를 전용 실험 폴더로 지정하고 이전 결과를 분리해 두세요.

<a name="data-settings"></a>

## 📊 데이터 구성과 클래스

| ID | 클래스 | 의미 |
| :--- | :--- | :--- |
| 0 | `bicycle` | 자전거 |
| 1 | `kickboard` | 킥보드 |
| 2 | `bollard` | 볼라드 |
| 3 | `car` | 자동차 |
| 4 | `motorcycle` | 오토바이 |
| 5 | `stairs` | 계단 |
| 6 | `crosswalk` | 횡단보도 |
| 7 | `people` | 보행자 |

### ⚙️ 실제 학습 설정

| 단계 | 코드 설정 |
| :--- | :--- |
| 병합 | YOLO bbox의 5개 항목 형식을 유지하고 세그멘테이션 라인을 제외 |
| 테스트 확보 | 볼라드·횡단보도 각각 `min(100, 해당 이미지 수의 15%)`를 우선 배정 |
| 나머지 분할 | 강제 테스트 배정 후 남은 이미지의 74% 학습·21% 검증·나머지 테스트 |
| Phase 1 | 클래스별 15~40 epoch, YOLOv8x, 이미지 640, AdamW |
| Phase 2 | 선택된 Phase 1 모델을 시작점으로 전체 클래스 150 epoch, `lr0=0.0005`, `patience=0` |
| 최종 평가 | `split="test"`, mAP50·mAP50-95·precision·recall |
| 추론 설정 | `conf=0.4`, `iou=0.45`, 이미지 640, 최대 탐지 50개 |
| 속도 측정 | 무작위 이미지 한 장으로 준비 추론 10회 뒤 반복 추론 100회 |

분할 셀의 제목은 7:2:1이지만 실제 비율은 위의 강제 테스트 배정과 74:21 분할에 따라 달라집니다. 테스트 이미지를 항상 100장 확보하는 것도 아닙니다. 또한 Phase 1의 테스트 평가값으로 Phase 2 시작 모델을 선택하므로, 같은 테스트셋의 최종 값을 모델 선택과 독립된 시험 성능으로 해석하지 마세요.

데이터 이름 매핑에 일치 항목이 하나도 없을 때는 원본 ID와 대상 ID의 정렬 순서로 연결하는 fallback이 있습니다. 해당 경로가 쓰였는지 출력된 ID 매핑을 확인해야 합니다. 데이터 원본별 train·valid·test는 한데 모아 재분할하며, 이미지 내용의 중복 검출은 이 분할 코드에 없습니다.

`SafeWalkDetector`는 자전거·킥보드·자동차·오토바이 탐지에 주의 문구를 표시합니다. 거리를 계산하거나 충돌 위험을 예측하는 로직은 아닙니다. TensorRT 변환은 CUDA 환경에서 FP16 엔진을 생성하는 별도 단계이며, 속도 비교는 자신이 실행한 장치의 결과를 사용합니다.

노트북의 목표 정확도와 처리속도 서술은 실제 재현된 결과와 구분해야 합니다. 이 소개에서는 확인하지 않은 FPS나 클래스별 목표 달성을 성과로 표시하지 않습니다. 탐지 모델은 보행환경 연구의 구성 요소이며 실제 보행 안전을 보장하는 완성 시스템을 의미하지 않습니다.

<a name="troubleshooting"></a>

## 🛠️ 문제 해결

| 증상 | 확인할 내용 |
| :--- | :--- |
| Roboflow 인증 오류 | 본인의 환경변수와 해당 데이터셋 접근 권한 |
| 데이터 다운로드 실패 | 프로젝트·버전의 존재와 다운로드 조건 |
| GPU 메모리 부족 | 모델 크기·배치 크기·장치 메모리 |
| 클래스 수가 다름 | 원본 라벨과 공통 `class_map` 매핑 |
| Linux 명령 실패 | CUDA 경로 수정 셀의 운영체제 전제 |
| COCO 분기를 건너뜀 | `fiftyone` 설치와 다운로드 오류 로그 |
| 웹캠 창을 열 수 없음 | 설치 셀은 `opencv-python-headless`를 사용하므로 GUI 표시용 OpenCV 환경 필요 |
| 영상의 한글 표시가 깨짐 | `cv2.putText`의 기본 Hershey 글꼴은 한글용 Matplotlib 설정과 별개 |
| 목표보다 성능이 낮음 | 클래스 분포·분할·실제 학습 로그를 함께 확인 |

---

<div align="center">

**보행환경의 다양한 객체를 같은 기준으로 학습합니다.**

</div>
