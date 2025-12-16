# Chatterbox TTS Evaluation

본 모듈은 파인튜닝된 Chatterbox TTS 모델로 생성된 음성의 품질을 정량적으로 평가하기 위한 도구이다.  
다음 두 가지 지표를 사용한다.

- MOS (Mean Opinion Score) 예측
- Whisper ASR 기반 WER (Word Error Rate)

본 evaluation 코드는 학습 및 추론 파트와 독립적으로 동작하며,
TTS 출력 품질 검증 및 실험 결과 정리에 사용된다.

---

## What is included

- 음성 폴더 단위 자동 평가
- WV-MOS 기반 MOS 예측
- Whisper ASR 기반 WER 계산
- 파일별 결과 및 전체 평균 통계 출력
- 평가 결과를 CSV 파일로 저장

본 모듈은 standalone evaluation 도구로 설계되어 있으며,
training / inference 코드 없이도 단독 실행 가능하다.

---

## My Environment

- Kubernetes Pod 기반 실행 환경
- NVIDIA CUDA 12.x + Ubuntu 22.04
- 단일 GPU 환경 (A100 40GB)
- Persistent Volume 기반 작업 디렉토리
- Whisper large-v2 ASR 모델
- WV-MOS pretrained 모델 사용

---

## Environment Setup

### 1. Python 가상환경 생성 및 활성화

```bash
python -m venv .env
source .env/bin/activate
```

### 2. 시스템 의존성 설치

```
apt install ffmpeg
```

### 3. Python 패키지 설치

```
pip install -r requirements.txt
```

---

## Usage

각 음성 파일은 동일한 이름의 .txt 참조 텍스트 파일을 가져야 한다.  
예시 디렉토리 구조:  

tts_audios/  
├── utt_01.wav  
├── utt_01.txt  
├── utt_02.wav  
├── utt_02.txt  

---

## Evaluation 실행

```
cd ~/tts_eval
source .env/bin/activate

python eval_tts_folder.py \
  --audio_dir "/mnt/c/Users/user/tts_audios" \
  --whisper_model base \
  --language Korean \
  --cpu_mos
```

옵션 설명:

- audio_dir : 평가할 음성 및 참조 텍스트가 포함된 폴더
- whisper_model : Whisper ASR 모델 크기 (tiny, base, small, medium, large-v2 등)
- language : Whisper 언어 힌트 (예: Korean)
- cpu_mos : MOS 계산을 CPU에서 수행 (GPU 미사용 시)

---

## Output

- 파일별 MOS 및 WER 결과 출력
- 전체 평균 MOS / WER 요약 통계 출력
- CSV 파일 생성 위치:
/path/to/audio_dir/tts_eval_results.csv

CSV 파일에는 다음 정보가 포함된다:  
- 파일명
- 참조 텍스트
- Whisper 전사 결과
- MOS 점수
- WER 점수 (참조 텍스트가 존재하는 경우)
