# 추천 학습 순서 (Suggested Learning Path)

> 이 파일은 `CLAUDE.md` 라우터의 일부다. 사용자가 명시적으로 학습 순서·로드맵을 물을 때 Read 한다. 일반 작업에는 거의 필요 없다.
>
> 이 절은 사용자가 이 저장소를 이용해 Vulkan 을 학습하기 위해 받은 가이드를 그대로 보존한 것이다.

Vulkan은 학습 곡선이 가파른 API라, 이 저장소를 처음부터 끝까지 순서대로 도는 것보다 **"단계별로 묶어서 한 단계의 개념을 머릿속에 박은 뒤 다음으로"** 가는 방식이 효율적입니다. 아래는 이 저장소의 샘플들을 기반으로 한 단계별 로드맵입니다.

---

## 시작 전 준비

1. **Vulkan을 처음 본다면** 이 저장소 샘플로 바로 들어가지 마시고, [vulkan-tutorial.com](https://vulkan-tutorial.com) 또는 README가 가리키는 [docs.vulkan.org/tutorial](https://docs.vulkan.org/tutorial/latest/index.html) 을 한 번 끝까지 보세요. instance→device→swapchain→pipeline→draw 의 큰 흐름이 머리에 들어와야 이 샘플들이 의미 있습니다.
2. **Vulkan 1.1 vs 1.3 차이를 미리 알아두세요.** 1.3에서 RenderPass/Framebuffer가 dynamic rendering으로 대체되고 sync도 sync2로 단순해졌습니다. 두 스타일이 샘플에 섞여 있습니다.
3. **`hpp_` 접두사가 붙은 샘플은 같은 내용을 vulkan.hpp(C++ 래퍼)로 다시 쓴 것** 입니다. **처음엔 무시하세요.** C 핸들 기반 코드로 먼저 익히고, 마지막에 한 쌍만 비교해 보면 충분합니다.

---

## 1단계 — Hello 단계 (1~2일)

목표: "draw 한 번 어떻게 일어나는가"를 손으로 따라간다.

| 순서 | 샘플 | 보는 포인트 |
|---|---|---|
| 1 | [samples/api/hello_triangle](../samples/api/hello_triangle/) | Vulkan 1.1 기준. instance, physical/logical device, swapchain, render pass, framebuffer, graphics pipeline, command buffer, semaphore/fence — **모든 핵심 객체가 한 파일에 다 있음.** 프레임워크를 거의 안 씀. |
| 2 | [samples/api/hello_triangle_1_3](../samples/api/hello_triangle_1_3/) | 같은 결과를 Vulkan 1.3 (`VK_KHR_dynamic_rendering`, `VK_KHR_synchronization2`)로. 1번과 diff로 비교하면서 "1.3에서 사라진 것/단순해진 것"을 체감. |

이 두 개를 **이해**(베껴 쓸 수 있을 정도로)할 때까지 다음으로 넘어가지 마세요. 여기서 막히면 뒤가 다 막힙니다.

---

## 2단계 — 기본 API 사용법 (3~7일)

목표: 자주 쓰는 리소스 타입과 패턴을 익힌다. 여기서부터는 vkb 프레임워크 기반이라 코드가 짧아지는 대신 *프레임워크가 뭘 감춰주는지* 를 의식하면서 보세요.

순서대로:

1. [samples/api/texture_loading](../samples/api/texture_loading/) — image, image view, sampler, staging buffer, layout transition.
2. [samples/api/texture_mipmap_generation](../samples/api/texture_mipmap_generation/) — `vkCmdBlitImage` 로 mip chain 만들기. barrier 연쇄에 익숙해지는 단계.
3. [samples/api/separate_image_sampler](../samples/api/separate_image_sampler/) — descriptor set에서 image와 sampler를 분리. descriptor 개념 굳히기.
4. [samples/api/dynamic_uniform_buffers](../samples/api/dynamic_uniform_buffers/) — UBO offset/alignment, descriptor dynamic offset.
5. [samples/api/instancing](../samples/api/instancing/) — per-instance vertex attribute, 한 draw call로 다수 객체.
6. [samples/api/compute_nbody](../samples/api/compute_nbody/) — compute pipeline, storage buffer, compute→graphics 동기화. **그래픽스만 보다가 처음 만나는 compute** 라 시야가 넓어집니다.
7. [samples/api/timestamp_queries](../samples/api/timestamp_queries/) — `vkCmdWriteTimestamp` 로 GPU 시간 재기. 이후 성능 단계에서 계속 쓰게 됨.
8. [samples/api/swapchain_recreation](../samples/api/swapchain_recreation/) — 창 크기 변경/minimization 처리. 의외로 어렵고, 실전에서 반드시 한 번은 디버깅함.

선택(시간 되면): `hdr`, `terrain_tessellation`, `oit_depth_peeling`, `oit_linked_lists` — 알고리즘 자체가 흥미로운 예제들.

---

## 3단계 — 모던 Vulkan 확장 (1~2주)

목표: 최신 KHR/EXT 확장을 어떻게 켜고, 코드가 어떻게 단순/유연해지는지 본다. **순서가 중요한 단계** 입니다.

**3-1. Dynamic & 단순화 계열** — 일단 이걸 먼저:
- [samples/extensions/dynamic_rendering](../samples/extensions/dynamic_rendering/) — RenderPass/Framebuffer 객체 없이 그리기. 이미 1단계의 `hello_triangle_1_3` 에서 맛본 것의 본격판.
- [samples/extensions/dynamic_rendering_local_read](../samples/extensions/dynamic_rendering_local_read/) — subpass 없이 attachment를 입력으로 다시 읽기 (deferred 렌더에 사용).
- [samples/extensions/synchronization_2](../samples/extensions/synchronization_2/) — 단일화된 sync2 API. **이후 모든 코드를 sync2로 쓰는 습관이 권장됨.**
- [samples/extensions/push_descriptors](../samples/extensions/push_descriptors/) — descriptor set 객체 없이 push로 바인딩.
- [samples/extensions/buffer_device_address](../samples/extensions/buffer_device_address/) — 버퍼를 포인터처럼 다루기. 이후 ray tracing의 전제.

**3-2. Pipeline 유연성 계열**:
- [samples/extensions/extended_dynamic_state2](../samples/extensions/extended_dynamic_state2/), [samples/extensions/dynamic_blending](../samples/extensions/dynamic_blending/), [samples/extensions/logic_op_dynamic_state](../samples/extensions/logic_op_dynamic_state/), [samples/extensions/patch_control_points](../samples/extensions/patch_control_points/) — 파이프라인 객체를 새로 안 만들고 상태를 바꿔가며 그리기.
- [samples/extensions/graphics_pipeline_library](../samples/extensions/graphics_pipeline_library/) — 파이프라인 객체 부분 캐싱.
- [samples/extensions/shader_object](../samples/extensions/shader_object/) — 파이프라인 객체를 통째로 없애는 EXT.
- [samples/extensions/descriptor_buffer_basic](../samples/extensions/descriptor_buffer_basic/), [samples/extensions/descriptor_indexing](../samples/extensions/descriptor_indexing/) — bindless 흐름의 토대.

**3-3. 디버깅/툴링** (실전에서 가장 자주 씀):
- [samples/extensions/debug_utils](../samples/extensions/debug_utils/) — RenderDoc/Nsight에서 객체 이름/라벨 보이게 하기. **무조건 일찍 보세요. 시간 절약됨.**
- [samples/extensions/shader_debugprintf](../samples/extensions/shader_debugprintf/) — 셰이더 안에서 `printf`.

**3-4. 알고리즘적 확장** (관심사에 따라 선택):
- Ray tracing: `ray_tracing_basic` → `ray_tracing_extended` → `ray_queries` → `ray_tracing_reflection`
- Mesh shading: `mesh_shading` → `mesh_shader_culling` → `gshader_to_mshader`
- Fragment 처리: `fragment_shading_rate` → `fragment_density_map` → `fragment_shader_barycentric`
- 상호운용: `open_gl_interop`, `open_cl_interop`

---

## 4단계 — 성능/모바일 best practice (1주)

목표: "돌아가는 코드"에서 "빠른 코드"로. 이 폴더 샘플들은 *나쁜 방법 vs 좋은 방법* 을 토글로 비교하도록 짜여 있어서 학습 효과가 큽니다.

- [samples/performance/pipeline_barriers](../samples/performance/pipeline_barriers/) — barrier 과하면 어떻게 느려지는지. **가장 추천.**
- [samples/performance/render_passes](../samples/performance/render_passes/) / [samples/performance/subpasses](../samples/performance/subpasses/) — 모바일 TBDR에서 subpass 의 효과.
- [samples/performance/msaa](../samples/performance/msaa/) — MSAA를 모바일에서 "공짜에 가깝게" 쓰는 방법(`resolve` 시점이 핵심).
- [samples/performance/descriptor_management](../samples/performance/descriptor_management/), [samples/performance/constant_data](../samples/performance/constant_data/) — UBO/PushConstant/SSBO 의 적절한 선택.
- [samples/performance/multi_draw_indirect](../samples/performance/multi_draw_indirect/), [samples/performance/async_compute](../samples/performance/async_compute/) — GPU 활용도 끌어올리기.
- [samples/performance/pipeline_cache](../samples/performance/pipeline_cache/), [samples/performance/specialization_constants](../samples/performance/specialization_constants/) — 파이프라인 생성 비용 다루기.
- [samples/performance/swapchain_images](../samples/performance/swapchain_images/), [samples/performance/wait_idle](../samples/performance/wait_idle/), [samples/performance/surface_rotation](../samples/performance/surface_rotation/) — 프레젠테이션 흐름의 함정들.
- [samples/performance/texture_compression_comparison](../samples/performance/texture_compression_comparison/), [samples/performance/afbc](../samples/performance/afbc/), [samples/performance/image_compression_control](../samples/performance/image_compression_control/) — 텍스처/프레임버퍼 압축.

각 샘플의 README가 *왜 이렇게 하면 빠른가* 를 그래프와 함께 설명합니다. 코드보다 README를 먼저 보세요.

---

## 5단계 — 마무리

- 관심 있는 1단계/2단계 샘플 중 하나의 **`hpp_` 버전**을 원본과 diff로 비교 → vulkan.hpp 가 뭘 줄여주는지 익히기. 모던 C++ 코드를 쓸 거면 hpp 버전을 기본으로 쓰는 게 좋습니다.
- [framework/](../framework/) 안의 `core/`, `rendering/`, `scene_graph/` 를 읽으면서 "이 프로젝트가 raw Vulkan을 어떻게 추상화해뒀나" 를 읽어보기. 본인의 미니 엔진을 짤 때 좋은 출발점.
- 자기만의 토이 프로젝트로 마무리: 예) glTF 모델 하나 띄우고, deferred shading + dynamic_rendering_local_read + sync2 + bindless descriptors 조합 — 위에서 본 것들이 한꺼번에 들어갑니다.

---

## 실행/관찰 팁

- 빌드한 `vulkan_samples.exe` 를 그냥 실행하면 GUI에서 카테고리별로 샘플을 고를 수 있고, 명령행으로 `vulkan_samples sample <이름>` 도 가능합니다 (`start_sample` 플러그인).
- **RenderDoc 으로 한 프레임을 캡처해서 보면 학습 속도가 두세 배** 입니다. 1단계부터 습관 들이세요. `debug_utils` 샘플을 빨리 보라고 한 이유.
- 각 샘플 폴더의 `README.adoc` 가 코드보다 먼저 읽기 좋은 설명입니다. 절대 건너뛰지 마세요.

전체 일정은 *주말 시간 기준* 으로 1~2달 잡으면 적당합니다. 1·2단계까지만 끝나도 일반 그래픽스 코드는 읽고 쓸 수 있고, 3단계 이후는 본인 관심사(레이트레이싱/모바일 최적화/엔진 개발 등)에 따라 가지치기 하시면 됩니다.
