## MV-Adapter 환경 설정

- ✅ **MV-Adapter 설치 및 실행 환경 구성 완료**
- ✅ **CUDA 12.3 + PyTorch ≥ 2.0** 환경에서 정상 실행
- ✅ 스크립트 기반 실행 구조 (`i2mv_sd.py`, `i2mv_sdxl.py`) 확인

---

## 💻 지원하는 모델 종류

### Task: **2D 이미지 → 다각도 Multi-view 이미지 생성**

| 모델 이름 | 기반 모델                    | 해상도       | GPU 메모리 사용량     | 비고                             |
|-----------|------------------------------|--------------|------------------------|----------------------------------|
| `i2mv_sd`   | Stable Diffusion **2.1 base** | 512 x 512    | 약 **7,740 MiB**       | 낮은 메모리, 품질 보통         |
| `i2mv_sdxl` | Stable Diffusion **XL base**  | 768 x 768    | 약 **18,500~23,230 MiB** | 고품질, GPU 자원 소모 큼     |

---

## ⚠ GPU 메모리 이슈 및 제한

### Stable Diffusion XL 기반 모델

- RTX 4090 기준으로도 **`num_views` ≈ 11** 이상 시 `Out of Memory`
- 메모리 약 **23,230 MiB**까지 사용됨(num_views=10 기준)
- SDXL은 고화질 이미지 생성이 가능하지만, 자원 소모 큼

### ✔ 권장 설정

- SDXL: `num_views ≤ 10`
- SD 2.1: `num_views ≤ 26~28`

---

## Viewpoint 설정 방법 요약

| 스크립트        | 사용 변수                     | 설명                            |
|----------------|------------------------------|---------------------------------|
| `i2mv_sd.py`    | `azimuth_deg = np.linspace(...)` | ✅ 다양한 각도 자동 생성 가능     |
| `i2mv_sdxl.py`  | `--azimuth_deg` CLI에서 직접 입력 | ❗ `--num_views`는 사용되지 않음 |

---

## ⚠ Viewpoint 생성의 한계점

- MV-Adapter는 **6가지 고정 ViewPoint** 기반으로 학습된 것으로 추정됨
- `num_views` 수를 늘려도 실제로는 **6개 skeleton 위치 중심**으로 생성됨
- `Plücker Embedding`으로 viewpoint는 다양하지만 **이미지 출력은 반복적인 경향**
- 논문에서는 제한이 없다고 하나, 실험상 6방향 기반으로 작동

---

## 실험 결과 요약

- `i2mv_sd`: `num_views = 26~28`까지 생성 가능 (RTX 4090 기준)
- `i2mv_sdxl`: `num_views = 10`에서 메모리 한계 도달
- `azimuth_deg` 조정으로 각도 다양성 확보 가능
- 다만 출력은 **6방향 중심**으로 수렴하는 경향

---

## 후속 아이디어

- **PaC-NeRF** 등 multi-view → 다른 관점 변환 모델과의 결합 가능성
- Viewpoint 증강을 위한 후처리 또는 NeRF 기반 정렬 보정 기법 제안 가능

---

## 실행 예시

```bash
# SDXL 기반 inference 실행 예시
python scripts/inference_i2mv_sdxl.py \
--image assets/demo/input.png \
--azimuth_deg 0 60 120 180 240 300 \
--output output.png \
--remove_bg
# --text는 default로 "high qaulity"로 되어 있음`
