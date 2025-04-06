# ✅ 2025년 4월 6일 진행 상황 보고

## 🔧 MV-Adapter 환경 설정
- ✅ **MV-Adapter 설치 및 실행 환경 구성 완료**
- ✅ 의존성 문제 해결 및 실행 테스트 완료

---

## 🧠 지원하는 모델 종류

### 🎯 Task: **Image to Multi-view Generation**

| 모델 이름 | 기반 모델 | 비고 |
|-----------|-----------|------|
| **모델 1** | Stable Diffusion **2.1 base** | 일반적인 multi-view 용도로 적합 |
| **모델 2** | Stable Diffusion **XL (SDXL) base** | 고품질 결과 생성 가능, **자원 소모 큼** |

---

## ⚠️ GPU 메모리 부족 (Out of Memory) 이슈

> **Stable Diffusion XL (SDXL)** 기반 모델은 다음과 같은 문제가 있음:

- ❗ **GPU 메모리 부족**으로 `out of memory` 오류 발생 (예: RTX 4080에서도)
- ❗ 기본 설정으로는 실행 어려움

### ✔️ 해결 방안

- `batch_size` 줄이기 (예: `1`)
- `fp16` (half precision) 설정 사용
- 필요 시 `gradient checkpointing`, `memory-efficient attention` 사용 고려

```bash
# 실행 예시
python -m scripts.inference_ig2mv_sdxl \
--image assets/demo/ig2mv/1ccd5c1563ea4f5fb8152eac59dabd5c.jpeg \
--mesh assets/demo/ig2mv/1ccd5c1563ea4f5fb8152eac59dabd5c.glb \
--output output.png --remove_bg
