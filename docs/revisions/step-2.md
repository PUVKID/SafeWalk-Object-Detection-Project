

<a name="-실험-구성과-기록"></a>
# 실험 구성과 기록

<!-- reading-levels:start -->
[LEVEL 1 - Beginner](#reading-level-1) · [LEVEL 2 - Explorer](#reading-level-2) · [LEVEL 3 - Practitioner](#reading-level-3) · [LEVEL 4 - Advanced](#reading-level-4) · [LEVEL 5 - Expert](#reading-level-5)

프로젝트 전체의 설명은 [LEVEL 1 - Beginner](../%EC%9D%BD%EA%B8%B0%EC%95%88%EB%82%B4/%EB%A0%88%EB%B2%A81.md)부터 [LEVEL 5 - Expert](../%EC%9D%BD%EA%B8%B0%EC%95%88%EB%82%B4/%EB%A0%88%EB%B2%A85.md)까지 같은 범위로 연결됩니다.

<a id="reading-level-1"></a>
## LEVEL 1 - Beginner

### LEVEL 1 · 이 자료가 하는 일과 달라진 점

bicycle·kickboard·bollard·car·motorcycle·stairs·crosswalk·people을 공통 8클래스로 통합합니다.

### LEVEL 1 · 실제 동작을 따라 읽기

bicycle·kickboard·bollard·car·motorcycle·stairs·crosswalk·people을 공통 8클래스로 통합합니다.

Phase 1은 클래스별 15~40 epoch, Phase 2는 선택한 모델에서 전체 150 epoch 미세조정을 구성합니다.

데이터 분할은 강제 테스트 배정 후 남은 영상 74% 학습·21% 검증·나머지 테스트입니다. 표제의 7:2:1과 실제 배정은 다릅니다.

추론·영상 데모·내보내기 경로는 학습과 별도이며 실제 장치 환경과 가중치가 필요합니다.

CSV는 값을 쉼표로 나눈 표 파일입니다. epoch는 학습 자료를 한 차례 순회하는 단위입니다. bbox는 물체를 둘러싼 사각형 위치와 크기를 나타내는 표기입니다.

입력 — Roboflow·COCO 계열 영상·라벨과 클래스 매핑

처리 — 클래스 통합 → 데이터 분할 → 클래스별 학습 → 통합 미세조정 → 평가

출력 — 학습 설정 YAML·에폭별 CSV·평가/추론·모델 출력

### LEVEL 1 · 준비하고 확인할 것

Jupyter와 노트북 설치 셀의 패키지, GPU·CUDA 환경, 본인의 ROBOFLOW_API_KEY와 데이터셋 접근 권한을 준비합니다. 원래 PROJECT_ROOT는 /root/pbl2-main이며 전용 실험 폴더로 바꿉니다. COCO 분기는 fiftyone이 별도로 필요합니다.

최상위 safewalk_train_v8_fixed (1).ipynb 또는 이 묶음의 해당 노트북을 Jupyter에서 열고 설정·데이터 매핑·GPU 확인 셀부터 순서대로 읽습니다. 병합·분할 셀은 merged·processed·per_class 결과를 재생성하므로 이전 결과와 분리합니다.

#### LEVEL 1 · 분할·학습·평가를 읽는 순서

원본별 train·valid·test 영상을 모은 뒤 공통 8클래스로 라벨을 다시 매핑합니다. YOLO bbox의 5개 항목만 병합하고 세그멘테이션 라인은 제외합니다.

이름 매핑이 하나도 맞지 않으면 ID 정렬 순서 fallback이 있으므로 출력 매핑을 확인해야 합니다. 이 분할 코드에는 이미지 내용 중복 검출이 없습니다.

볼라드·횡단보도는 각각 `min(100, 이미지 수의15%)`를 테스트로 우선 확보합니다. 남은 영상의74%를 학습,21%를 검증,나머지를 테스트로 보냅니다. 따라서 셀 제목의7:2:1이 실제 전체 비율은 아닙니다.

Phase1은 YOLOv8x·640·AdamW로 클래스별15~40epoch를 구성합니다. Phase2는 선택한 모델에서 전체150epoch, lr0=0.0005, patience=0으로 미세조정합니다. 테스트 결과로 시작 모델을 선택하므로 같은 시험셋 최종 수치는 모델 선택과 독립된 평가가 아닙니다.

추론 설정은 conf0.4·IoU0.45·640·최대50개 탐지입니다. 속도 측정은 무작위 이미지 한 장에 준비10회와 반복100회를 적용한 장치별 측정이며 실시간 보행 안전 성능과 다릅니다. `SafeWalkDetector`의 주의 문구는 객체 종류에 따른 표시이고 거리·충돌 예측값은 아닙니다.

1. configs의 공통 8클래스·클래스별 YAML을 읽습니다.
2. models 아래 각 실험의 args.yaml과 results.csv를 같은 실행 단위로 대조합니다.
3. 현재 노트북과 이전 도커 버전 노트북을 구분해 셀·설정 차이를 읽습니다.
4. 저장된 에폭 지표를 확인하고 새로운 학습 출력과 섞지 않습니다.

### LEVEL 1 · 결과와 주의할 범위

클래스별 results.csv의 epoch·precision·recall·mAP50·mAP50-95를 args.yaml과 함께 읽습니다. 테스트값으로 Phase 2 시작 모델을 선택하므로 같은 시험값은 모델 선택과 완전히 독립된 평가가 아닙니다. 위험 문구는 객체 종류 기반 표시이며 거리·충돌 예측 결과가 아닙니다.

**상황:** 다운로드 인증 실패

**확인할 내용:** 본인 환경변수·프로젝트·버전·이용 조건을 확인합니다.

**상황:** 클래스 ID 불일치

**확인할 내용:** class_map의 이름과 실제 원본 라벨을 대조합니다. 정렬 순서 fallback의 실행 여부를 확인합니다.

**상황:** 웹캠 창 표시 실패

**확인할 내용:** 설치 셀의 headless OpenCV와 GUI 표시용 환경 차이를 확인합니다.


[자료 폴더](../../pbl2-main) · [직전 비교·자료 범위](step-2.patch) · [이 차수의 원래 전체 상세](#reading-level-5)

<a id="reading-level-2"></a>
## LEVEL 2 - Explorer

### LEVEL 2 · 목적과 구성의 연결

bicycle·kickboard·bollard·car·motorcycle·stairs·crosswalk·people을 공통 8클래스로 통합합니다.

bicycle·kickboard·bollard·car·motorcycle·stairs·crosswalk·people을 공통 8클래스로 통합합니다.

Phase 1은 클래스별 15~40 epoch, Phase 2는 선택한 모델에서 전체 150 epoch 미세조정을 구성합니다.

데이터 분할은 강제 테스트 배정 후 남은 영상 74% 학습·21% 검증·나머지 테스트입니다. 표제의 7:2:1과 실제 배정은 다릅니다.

추론·영상 데모·내보내기 경로는 학습과 별도이며 실제 장치 환경과 가중치가 필요합니다.

### LEVEL 2 · 입력에서 출력으로

**단계:** 입력

**내용:** Roboflow·COCO 계열 영상·라벨과 클래스 매핑

**단계:** 처리

**내용:** 클래스 통합 → 데이터 분할 → 클래스별 학습 → 통합 미세조정 → 평가

**단계:** 출력

**내용:** 학습 설정 YAML·에폭별 CSV·평가/추론·모델 출력


입력·처리·출력은 위의 같은 자료 흐름을 다른 단계에서 읽는 기준입니다. 처리 조건과 출력의 의미를 함께 확인합니다.

### LEVEL 2 · 설정과 처리 순서의 관계

1. configs의 공통 8클래스·클래스별 YAML을 읽습니다.
2. models 아래 각 실험의 args.yaml과 results.csv를 같은 실행 단위로 대조합니다.
3. 현재 노트북과 이전 도커 버전 노트북을 구분해 셀·설정 차이를 읽습니다.
4. 저장된 에폭 지표를 확인하고 새로운 학습 출력과 섞지 않습니다.

Jupyter와 노트북 설치 셀의 패키지, GPU·CUDA 환경, 본인의 ROBOFLOW_API_KEY와 데이터셋 접근 권한을 준비합니다. 원래 PROJECT_ROOT는 /root/pbl2-main이며 전용 실험 폴더로 바꿉니다. COCO 분기는 fiftyone이 별도로 필요합니다.

최상위 safewalk_train_v8_fixed (1).ipynb 또는 이 묶음의 해당 노트북을 Jupyter에서 열고 설정·데이터 매핑·GPU 확인 셀부터 순서대로 읽습니다. 병합·분할 셀은 merged·processed·per_class 결과를 재생성하므로 이전 결과와 분리합니다.

#### LEVEL 2 · 분할·학습·평가를 읽는 순서

원본별 train·valid·test 영상을 모은 뒤 공통 8클래스로 라벨을 다시 매핑합니다. YOLO bbox의 5개 항목만 병합하고 세그멘테이션 라인은 제외합니다.

이름 매핑이 하나도 맞지 않으면 ID 정렬 순서 fallback이 있으므로 출력 매핑을 확인해야 합니다. 이 분할 코드에는 이미지 내용 중복 검출이 없습니다.

볼라드·횡단보도는 각각 `min(100, 이미지 수의15%)`를 테스트로 우선 확보합니다. 남은 영상의74%를 학습,21%를 검증,나머지를 테스트로 보냅니다. 따라서 셀 제목의7:2:1이 실제 전체 비율은 아닙니다.

Phase1은 YOLOv8x·640·AdamW로 클래스별15~40epoch를 구성합니다. Phase2는 선택한 모델에서 전체150epoch, lr0=0.0005, patience=0으로 미세조정합니다. 테스트 결과로 시작 모델을 선택하므로 같은 시험셋 최종 수치는 모델 선택과 독립된 평가가 아닙니다.

추론 설정은 conf0.4·IoU0.45·640·최대50개 탐지입니다. 속도 측정은 무작위 이미지 한 장에 준비10회와 반복100회를 적용한 장치별 측정이며 실시간 보행 안전 성능과 다릅니다. `SafeWalkDetector`의 주의 문구는 객체 종류에 따른 표시이고 거리·충돌 예측값은 아닙니다.

### LEVEL 2 · 관측과 해석을 구분하기

클래스별 results.csv의 epoch·precision·recall·mAP50·mAP50-95를 args.yaml과 함께 읽습니다. 테스트값으로 Phase 2 시작 모델을 선택하므로 같은 시험값은 모델 선택과 완전히 독립된 평가가 아닙니다. 위험 문구는 객체 종류 기반 표시이며 거리·충돌 예측 결과가 아닙니다.

**상황:** 다운로드 인증 실패

**확인할 내용:** 본인 환경변수·프로젝트·버전·이용 조건을 확인합니다.

**상황:** 클래스 ID 불일치

**확인할 내용:** class_map의 이름과 실제 원본 라벨을 대조합니다. 정렬 순서 fallback의 실행 여부를 확인합니다.

**상황:** 웹캠 창 표시 실패

**확인할 내용:** 설치 셀의 headless OpenCV와 GUI 표시용 환경 차이를 확인합니다.


[자료 폴더](../../pbl2-main) · [직전 비교·자료 범위](step-2.patch) · [이 차수의 원래 전체 상세](#reading-level-5)

<a id="reading-level-3"></a>
## LEVEL 3 - Practitioner

### LEVEL 3 · 이 차수를 준비하는 범위

bicycle·kickboard·bollard·car·motorcycle·stairs·crosswalk·people을 공통 8클래스로 통합합니다.

Jupyter와 노트북 설치 셀의 패키지, GPU·CUDA 환경, 본인의 ROBOFLOW_API_KEY와 데이터셋 접근 권한을 준비합니다. 원래 PROJECT_ROOT는 /root/pbl2-main이며 전용 실험 폴더로 바꿉니다. COCO 분기는 fiftyone이 별도로 필요합니다.

최상위 safewalk_train_v8_fixed (1).ipynb 또는 이 묶음의 해당 노트북을 Jupyter에서 열고 설정·데이터 매핑·GPU 확인 셀부터 순서대로 읽습니다. 병합·분할 셀은 merged·processed·per_class 결과를 재생성하므로 이전 결과와 분리합니다.

#### LEVEL 3 · 분할·학습·평가를 읽는 순서

원본별 train·valid·test 영상을 모은 뒤 공통 8클래스로 라벨을 다시 매핑합니다. YOLO bbox의 5개 항목만 병합하고 세그멘테이션 라인은 제외합니다.

이름 매핑이 하나도 맞지 않으면 ID 정렬 순서 fallback이 있으므로 출력 매핑을 확인해야 합니다. 이 분할 코드에는 이미지 내용 중복 검출이 없습니다.

볼라드·횡단보도는 각각 `min(100, 이미지 수의15%)`를 테스트로 우선 확보합니다. 남은 영상의74%를 학습,21%를 검증,나머지를 테스트로 보냅니다. 따라서 셀 제목의7:2:1이 실제 전체 비율은 아닙니다.

Phase1은 YOLOv8x·640·AdamW로 클래스별15~40epoch를 구성합니다. Phase2는 선택한 모델에서 전체150epoch, lr0=0.0005, patience=0으로 미세조정합니다. 테스트 결과로 시작 모델을 선택하므로 같은 시험셋 최종 수치는 모델 선택과 독립된 평가가 아닙니다.

추론 설정은 conf0.4·IoU0.45·640·최대50개 탐지입니다. 속도 측정은 무작위 이미지 한 장에 준비10회와 반복100회를 적용한 장치별 측정이며 실시간 보행 안전 성능과 다릅니다. `SafeWalkDetector`의 주의 문구는 객체 종류에 따른 표시이고 거리·충돌 예측값은 아닙니다.

### LEVEL 3 · 실제 사용·실험 순서

1. configs의 공통 8클래스·클래스별 YAML을 읽습니다.
2. models 아래 각 실험의 args.yaml과 results.csv를 같은 실행 단위로 대조합니다.
3. 현재 노트북과 이전 도커 버전 노트북을 구분해 셀·설정 차이를 읽습니다.
4. 저장된 에폭 지표를 확인하고 새로운 학습 출력과 섞지 않습니다.

**단계:** 입력

**내용:** Roboflow·COCO 계열 영상·라벨과 클래스 매핑

**단계:** 처리

**내용:** 클래스 통합 → 데이터 분할 → 클래스별 학습 → 통합 미세조정 → 평가

**단계:** 출력

**내용:** 학습 설정 YAML·에폭별 CSV·평가/추론·모델 출력


### LEVEL 3 · 결과와 실패를 확인하기

클래스별 results.csv의 epoch·precision·recall·mAP50·mAP50-95를 args.yaml과 함께 읽습니다. 테스트값으로 Phase 2 시작 모델을 선택하므로 같은 시험값은 모델 선택과 완전히 독립된 평가가 아닙니다. 위험 문구는 객체 종류 기반 표시이며 거리·충돌 예측 결과가 아닙니다.

**상황:** 다운로드 인증 실패

**확인할 내용:** 본인 환경변수·프로젝트·버전·이용 조건을 확인합니다.

**상황:** 클래스 ID 불일치

**확인할 내용:** class_map의 이름과 실제 원본 라벨을 대조합니다. 정렬 순서 fallback의 실행 여부를 확인합니다.

**상황:** 웹캠 창 표시 실패

**확인할 내용:** 설치 셀의 headless OpenCV와 GUI 표시용 환경 차이를 확인합니다.


### LEVEL 3 · 실행 전 대조할 차수 조건

bicycle·kickboard·bollard·car·motorcycle·stairs·crosswalk·people을 공통 8클래스로 통합합니다.

Phase 1은 클래스별 15~40 epoch, Phase 2는 선택한 모델에서 전체 150 epoch 미세조정을 구성합니다.

데이터 분할은 강제 테스트 배정 후 남은 영상 74% 학습·21% 검증·나머지 테스트입니다. 표제의 7:2:1과 실제 배정은 다릅니다.

추론·영상 데모·내보내기 경로는 학습과 별도이며 실제 장치 환경과 가중치가 필요합니다.

[자료 폴더](../../pbl2-main) · [직전 비교·자료 범위](step-2.patch) · [이 차수의 원래 전체 상세](#reading-level-5)

<a id="reading-level-4"></a>
## LEVEL 4 - Advanced

### LEVEL 4 · 설계 의도와 검토 경계

bicycle·kickboard·bollard·car·motorcycle·stairs·crosswalk·people을 공통 8클래스로 통합합니다.

bicycle·kickboard·bollard·car·motorcycle·stairs·crosswalk·people을 공통 8클래스로 통합합니다.

Phase 1은 클래스별 15~40 epoch, Phase 2는 선택한 모델에서 전체 150 epoch 미세조정을 구성합니다.

데이터 분할은 강제 테스트 배정 후 남은 영상 74% 학습·21% 검증·나머지 테스트입니다. 표제의 7:2:1과 실제 배정은 다릅니다.

추론·영상 데모·내보내기 경로는 학습과 별도이며 실제 장치 환경과 가중치가 필요합니다.

### LEVEL 4 · 이 차수 구현의 실제 근거

| 내용 |
| --- |
| **파일:** [safewalk_train_v8_fixed (1).ipynb](../../pbl2-main/safewalk_train_v8_fixed%20%281%29.ipynb)<br>**원문에 정의된 역할·구성:** 노트북 23개 셀 |
| **파일:** [safewalk_train_v8_fixed (1)_이전도커버전.ipynb](../../pbl2-main/safewalk_train_v8_fixed%20%281%29_%EC%9D%B4%EC%A0%84%EB%8F%84%EC%BB%A4%EB%B2%84%EC%A0%84.ipynb)<br>**원문에 정의된 역할·구성:** 노트북 23개 셀 |

**단계:** 입력

**내용:** Roboflow·COCO 계열 영상·라벨과 클래스 매핑

**단계:** 처리

**내용:** 클래스 통합 → 데이터 분할 → 클래스별 학습 → 통합 미세조정 → 평가

**단계:** 출력

**내용:** 학습 설정 YAML·에폭별 CSV·평가/추론·모델 출력


### LEVEL 4 · 조건·변경·결과를 함께 검토하기

Jupyter와 노트북 설치 셀의 패키지, GPU·CUDA 환경, 본인의 ROBOFLOW_API_KEY와 데이터셋 접근 권한을 준비합니다. 원래 PROJECT_ROOT는 /root/pbl2-main이며 전용 실험 폴더로 바꿉니다. COCO 분기는 fiftyone이 별도로 필요합니다.

최상위 safewalk_train_v8_fixed (1).ipynb 또는 이 묶음의 해당 노트북을 Jupyter에서 열고 설정·데이터 매핑·GPU 확인 셀부터 순서대로 읽습니다. 병합·분할 셀은 merged·processed·per_class 결과를 재생성하므로 이전 결과와 분리합니다.

#### LEVEL 4 · 분할·학습·평가를 읽는 순서

원본별 train·valid·test 영상을 모은 뒤 공통 8클래스로 라벨을 다시 매핑합니다. YOLO bbox의 5개 항목만 병합하고 세그멘테이션 라인은 제외합니다.

이름 매핑이 하나도 맞지 않으면 ID 정렬 순서 fallback이 있으므로 출력 매핑을 확인해야 합니다. 이 분할 코드에는 이미지 내용 중복 검출이 없습니다.

볼라드·횡단보도는 각각 `min(100, 이미지 수의15%)`를 테스트로 우선 확보합니다. 남은 영상의74%를 학습,21%를 검증,나머지를 테스트로 보냅니다. 따라서 셀 제목의7:2:1이 실제 전체 비율은 아닙니다.

Phase1은 YOLOv8x·640·AdamW로 클래스별15~40epoch를 구성합니다. Phase2는 선택한 모델에서 전체150epoch, lr0=0.0005, patience=0으로 미세조정합니다. 테스트 결과로 시작 모델을 선택하므로 같은 시험셋 최종 수치는 모델 선택과 독립된 평가가 아닙니다.

추론 설정은 conf0.4·IoU0.45·640·최대50개 탐지입니다. 속도 측정은 무작위 이미지 한 장에 준비10회와 반복100회를 적용한 장치별 측정이며 실시간 보행 안전 성능과 다릅니다. `SafeWalkDetector`의 주의 문구는 객체 종류에 따른 표시이고 거리·충돌 예측값은 아닙니다.

클래스별 results.csv의 epoch·precision·recall·mAP50·mAP50-95를 args.yaml과 함께 읽습니다. 테스트값으로 Phase 2 시작 모델을 선택하므로 같은 시험값은 모델 선택과 완전히 독립된 평가가 아닙니다. 위험 문구는 객체 종류 기반 표시이며 거리·충돌 예측 결과가 아닙니다.

**상황:** 다운로드 인증 실패

**확인할 내용:** 본인 환경변수·프로젝트·버전·이용 조건을 확인합니다.

**상황:** 클래스 ID 불일치

**확인할 내용:** class_map의 이름과 실제 원본 라벨을 대조합니다. 정렬 순서 fallback의 실행 여부를 확인합니다.

**상황:** 웹캠 창 표시 실패

**확인할 내용:** 설치 셀의 headless OpenCV와 GUI 표시용 환경 차이를 확인합니다.


### LEVEL 4 · 재현과 전체 비교

1. configs의 공통 8클래스·클래스별 YAML을 읽습니다.
2. models 아래 각 실험의 args.yaml과 results.csv를 같은 실행 단위로 대조합니다.
3. 현재 노트북과 이전 도커 버전 노트북을 구분해 셀·설정 차이를 읽습니다.
4. 저장된 에폭 지표를 확인하고 새로운 학습 출력과 섞지 않습니다.

위 구성요소의 입력·출력과 실패 조건을 같은 차수 소스에 연결해 검토합니다. 전체 비교는 추가·수정·삭제와 원래 보관 범위를 구분하는 근거입니다.

[자료 폴더](../../pbl2-main) · [직전 비교·자료 범위](step-2.patch) · [이 차수의 원래 전체 상세](#reading-level-5)
<a id="reading-level-5"></a>
## LEVEL 5 - Expert

기존의 전체 설명·표·코드·함수 참조·파일별 비교는 아래에 그대로 이어집니다. [자료 폴더](../../pbl2-main) · [직전 비교·자료 범위](step-2.patch) · [이 차수의 원래 전체 상세](#reading-level-5)
<!-- reading-levels:end -->


8클래스 통합 실험의 구성과 기록을 정리했습니다.



**기술 분류:** Python · Jupyter · Ultralytics YOLOv8x · PyTorch · 8클래스 탐지

[핵심 기능](#features) · [사용 흐름](#usage) · [자료 구성](#structure) · [준비와 실행](#setup) · [결과 읽기](#results) · [직전 변경](#changes)



[프로젝트](../../README.md) · [전체 차수](../REVISION_INDEX.md) · [원본 자료](../../pbl2-main) · [← 이전: 기본 노트북](../materials/001-step-1/README.md)

<a name="-목적과-이-차수의-역할"></a>
## 목적과 이 차수의 역할

bicycle·kickboard·bollard·car·motorcycle·stairs·crosswalk·people을 공통 8클래스로 통합합니다.

<a name="features"></a>

<a name="-핵심-기능과-보존-범위"></a>
## 핵심 기능과 보존 범위

**항목:** 1

**이 차수에서 확인할 내용:** bicycle·kickboard·bollard·car·motorcycle·stairs·crosswalk·people을 공통 8클래스로 통합합니다.

**항목:** 2

**이 차수에서 확인할 내용:** Phase 1은 클래스별 15~40 epoch, Phase 2는 선택한 모델에서 전체 150 epoch 미세조정을 구성합니다.

**항목:** 3

**이 차수에서 확인할 내용:** 데이터 분할은 강제 테스트 배정 후 남은 영상 74% 학습·21% 검증·나머지 테스트입니다. 표제의 7:2:1과 실제 배정은 다릅니다.

**항목:** 4

**이 차수에서 확인할 내용:** 추론·영상 데모·내보내기 경로는 학습과 별도이며 실제 장치 환경과 가중치가 필요합니다.


<a name="-입력처리출력"></a>
## 입력·처리·출력

**단계:** 입력

**내용:** Roboflow·COCO 계열 영상·라벨과 클래스 매핑

**단계:** 처리

**내용:** 클래스 통합 → 데이터 분할 → 클래스별 학습 → 통합 미세조정 → 평가

**단계:** 출력

**내용:** 학습 설정 YAML·에폭별 CSV·평가/추론·모델 출력


<a name="usage"></a>

<a name="️-화면과-사용-흐름"></a>
## 화면과 사용 흐름

1. configs의 공통 8클래스·클래스별 YAML을 읽습니다.
2. models 아래 각 실험의 args.yaml과 results.csv를 같은 실행 단위로 대조합니다.
3. 현재 노트북과 이전 도커 버전 노트북을 구분해 셀·설정 차이를 읽습니다.
4. 저장된 에폭 지표를 확인하고 새로운 학습 출력과 섞지 않습니다.

<a name="structure"></a>

<a name="-실제-자료-구성"></a>
## 실제 자료 구성

| 내용 |
| --- |
| **경로:** [configs/](../../pbl2-main/configs)<br>**자료 종류:** 하위 자료 폴더 |
| **경로:** [models/](../../pbl2-main/models)<br>**자료 종류:** 하위 자료 폴더 |
| **경로:** [README.md](../../pbl2-main/README.md)<br>**자료 종류:** MD 파일 |
| **경로:** [safewalk_train_v8_fixed (1).ipynb](../../pbl2-main/safewalk_train_v8_fixed%20%281%29.ipynb)<br>**자료 종류:** IPYNB 파일 |
| **경로:** [safewalk_train_v8_fixed (1)_이전도커버전.ipynb](../../pbl2-main/safewalk_train_v8_fixed%20%281%29_%EC%9D%B4%EC%A0%84%EB%8F%84%EC%BB%A4%EB%B2%84%EC%A0%84.ipynb)<br>**자료 종류:** IPYNB 파일 |

<a name="-주요-모듈과-코드-읽기"></a>
## 주요 모듈과 코드 읽기

| 내용 |
| --- |
| **파일:** [safewalk_train_v8_fixed (1).ipynb](../../pbl2-main/safewalk_train_v8_fixed%20%281%29.ipynb)<br>**원문에 정의된 역할·구성:** 노트북 23개 셀 |
| **파일:** [safewalk_train_v8_fixed (1)_이전도커버전.ipynb](../../pbl2-main/safewalk_train_v8_fixed%20%281%29_%EC%9D%B4%EC%A0%84%EB%8F%84%EC%BB%A4%EB%B2%84%EC%A0%84.ipynb)<br>**원문에 정의된 역할·구성:** 노트북 23개 셀 |

<a name="setup"></a>

<a name="-설치-환경과-실행사용법"></a>
## 설치 환경과 실행·사용법

Jupyter와 노트북 설치 셀의 패키지, GPU·CUDA 환경, 본인의 ROBOFLOW_API_KEY와 데이터셋 접근 권한을 준비합니다. 원래 PROJECT_ROOT는 /root/pbl2-main이며 전용 실험 폴더로 바꿉니다. COCO 분기는 fiftyone이 별도로 필요합니다.

최상위 safewalk_train_v8_fixed (1).ipynb 또는 이 묶음의 해당 노트북을 Jupyter에서 열고 설정·데이터 매핑·GPU 확인 셀부터 순서대로 읽습니다. 병합·분할 셀은 merged·processed·per_class 결과를 재생성하므로 이전 결과와 분리합니다.

### 분할·학습·평가를 읽는 순서

원본별 train·valid·test 영상을 모은 뒤 공통 8클래스로 라벨을 다시 매핑합니다. YOLO bbox의 5개 항목만 병합하고 세그멘테이션 라인은 제외합니다.

이름 매핑이 하나도 맞지 않으면 ID 정렬 순서 fallback이 있으므로 출력 매핑을 확인해야 합니다. 이 분할 코드에는 이미지 내용 중복 검출이 없습니다.

볼라드·횡단보도는 각각 `min(100, 이미지 수의15%)`를 테스트로 우선 확보합니다. 남은 영상의74%를 학습,21%를 검증,나머지를 테스트로 보냅니다. 따라서 셀 제목의7:2:1이 실제 전체 비율은 아닙니다.

Phase1은 YOLOv8x·640·AdamW로 클래스별15~40epoch를 구성합니다. Phase2는 선택한 모델에서 전체150epoch, lr0=0.0005, patience=0으로 미세조정합니다. 테스트 결과로 시작 모델을 선택하므로 같은 시험셋 최종 수치는 모델 선택과 독립된 평가가 아닙니다.

추론 설정은 conf0.4·IoU0.45·640·최대50개 탐지입니다. 속도 측정은 무작위 이미지 한 장에 준비10회와 반복100회를 적용한 장치별 측정이며 실시간 보행 안전 성능과 다릅니다. `SafeWalkDetector`의 주의 문구는 객체 종류에 따른 표시이고 거리·충돌 예측값은 아닙니다.

<a name="results"></a>

<a name="-처리-결과와-출력-읽기"></a>
## 처리 결과와 출력 읽기

클래스별 results.csv의 epoch·precision·recall·mAP50·mAP50-95를 args.yaml과 함께 읽습니다. 테스트값으로 Phase 2 시작 모델을 선택하므로 같은 시험값은 모델 선택과 완전히 독립된 평가가 아닙니다. 위험 문구는 객체 종류 기반 표시이며 거리·충돌 예측 결과가 아닙니다.

<a name="️-제약과-문제-해결"></a>
## 제약과 문제 해결

**상황:** 다운로드 인증 실패

**확인할 내용:** 본인 환경변수·프로젝트·버전·이용 조건을 확인합니다.

**상황:** 클래스 ID 불일치

**확인할 내용:** class_map의 이름과 실제 원본 라벨을 대조합니다. 정렬 순서 fallback의 실행 여부를 확인합니다.

**상황:** 웹캠 창 표시 실패

**확인할 내용:** 설치 셀의 headless OpenCV와 GUI 표시용 환경 차이를 확인합니다.


<a name="changes"></a>

<a name="-직전-자료와-전체-비교"></a>
## 직전 자료와 전체 비교

기본 노트북에서 실험 구성과 기록로 이어지는 실제 파일 비교입니다.

[전체 비교·자료 범위](step-2.patch)

추가·수정·삭제, 주석·공백·개행과 바이너리 크기·해시는 연결된 원래 비교 자료에서 확인합니다.

<details>
<summary>파일별 비교·크기·해시 자세히 보기</summary>

[전체 텍스트 차이](step-2.patch) · [파일별 크기·SHA-256](step-2-files.json)

**구분:** 추가

**파일:** `configs/dataset_8class.yaml`

**구분:** 추가

**파일:** `configs/dataset_bicycle.yaml`

**구분:** 추가

**파일:** `configs/dataset_bollard.yaml`

**구분:** 추가

**파일:** `configs/dataset_car.yaml`

**구분:** 추가

**파일:** `configs/dataset_crosswalk.yaml`

**구분:** 추가

**파일:** `configs/dataset_kickboard.yaml`

**구분:** 추가

**파일:** `configs/dataset_motorcycle.yaml`

**구분:** 추가

**파일:** `configs/dataset_people.yaml`

**구분:** 추가

**파일:** `configs/dataset_stairs.yaml`

**구분:** 추가

**파일:** `models/phase1_per_class/bicycle/args.yaml`

**구분:** 추가

**파일:** `models/phase1_per_class/bicycle/results.csv`

**구분:** 추가

**파일:** `models/phase1_per_class/bollard/args.yaml`

**구분:** 추가

**파일:** `models/phase1_per_class/bollard/results.csv`

**구분:** 추가

**파일:** `models/phase1_per_class/car/args.yaml`

**구분:** 추가

**파일:** `models/phase1_per_class/car/results.csv`

**구분:** 추가

**파일:** `models/phase1_per_class/crosswalk/args.yaml`

**구분:** 추가

**파일:** `models/phase1_per_class/crosswalk/results.csv`

**구분:** 추가

**파일:** `models/phase1_per_class/kickboard/args.yaml`

**구분:** 추가

**파일:** `models/phase1_per_class/kickboard/results.csv`

**구분:** 추가

**파일:** `models/phase1_per_class/motorcycle/args.yaml`

**구분:** 추가

**파일:** `models/phase1_per_class/motorcycle/results.csv`

**구분:** 추가

**파일:** `models/phase1_per_class/people/args.yaml`

**구분:** 추가

**파일:** `models/phase1_per_class/people/results.csv`

**구분:** 추가

**파일:** `models/phase1_per_class/stairs/args.yaml`

**구분:** 추가

**파일:** `models/phase1_per_class/stairs/results.csv`

**구분:** 추가

**파일:** `models/safewalk_v8/args.yaml`

**구분:** 추가

**파일:** `models/safewalk_v8/results.csv`

**구분:** 추가

**파일:** `safewalk_train_v8_fixed (1)_이전도커버전.ipynb`


</details>

[프로젝트](../../README.md) · [전체 차수](../REVISION_INDEX.md) · [원본 자료](../../pbl2-main) · [← 이전: 기본 노트북](../materials/001-step-1/README.md)
