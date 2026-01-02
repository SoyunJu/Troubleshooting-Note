#  Troubleshooting Note
개인 프로젝트를 진행하며 겪은 기술적 난제와 해결 과정을 기록합니다.

---

## 260101 Docker Desktop Kubernetes Cluster 기동 실패 및 가상화 인식 오류

### 1. 현상 (Symptom)
* **Docker Desktop**에서 Kubernetes 클러스터 활성화 시 'Starting' 상태에서 무한 멈춤 현상 발생.
* "Virtualization support not detected" 에러 메시지 출력 및 **Resources** 설정(Apply) 버튼 비활성화.

### 2. 원인 분석 및 진단 (Diagnosis)
* **OS 레벨:** Windows 작업 관리자 및 `msinfo32` 확인 결과, CPU 가상화 지원(SVM)이 '사용 안 함'으로 표시됨.
* **HW 레벨:** BIOS(MSI Click BIOS 5) 진입 결과 `SVM Mode`가 **Enabled** 상태였으나 OS에서 하드웨어를 인식하지 못함.
* **추정 원인:** 2018년도 BIOS 버전(`.280`)과 최신 Windows 하이퍼바이저/WSL2 간의 호환성 문제로 판단.

### 3. 해결 조치 (Action Taken)
* **BIOS 업데이트:** MSI 공식 홈페이지에서 최신 AGESA 코드가 포함된 바이오스(`7B38v2I`) 다운로드 후 **M-Flash**를 통해 업데이트 진행.
* **환경 재설정:** 업데이트 후 초기화된 BIOS 환경에서 `SVM Mode`를 다시 **Enabled**로 재활성화.
* **리소스 조정:** `%UserProfile%\.wslconfig` 파일을 생성하여 WSL2 할당 메모리를 **8GB**로 제한하여 시스템 안정성 확보.

### 4. 결과 (Result)
* Kubernetes 클러스터가 정상적으로 **Running** 상태로 진입하고 `Pod running` 상태 확인 완료.
* **학습 내용:** 하드웨어 펌웨어와 최신 소프트웨어(WSL2) 간의 버전 매칭이 가상화 안정성에 미치는 영향을 학습함. (호환성 검증의 중요성 재확인)
