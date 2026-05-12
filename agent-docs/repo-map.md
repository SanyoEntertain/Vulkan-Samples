# 저장소 레이아웃 & 런타임 아키텍처

> 이 파일은 `CLAUDE.md` 라우터의 일부다. "이 저장소가 어떻게 구성돼 있고, 부팅·드로우가 어떤 흐름으로 흐르는가" 가 필요할 때 Read 한다.

---

## 1. 저장소 레이아웃

| 경로 | 역할 |
|---|---|
| [app/](../app/) | 실행 진입점. [`app/main.cpp`](../app/main.cpp) 가 플랫폼 부트스트랩 + 메인 루프 시작. |
| [app/plugins/](../app/plugins/) | 런타임 옵션 플러그인들 (`start_sample`, `batch_mode`, `benchmark_mode`, `screenshot`, `shading_language_selection`, `fps_logger`, `gpu_selection` 등). |
| [framework/](../framework/) | vkb 프레임워크. 샘플들이 공통으로 쓰는 Vulkan 추상화 + 유틸. |
| [framework/core/](../framework/core/) | Device, Instance, Swapchain, Buffer, Image, CommandBuffer, Pipeline, DescriptorSet 등 저수준 Vulkan 객체 RAII 래퍼. C 핸들 기반(`vkb::core::*`) 과 vulkan.hpp 기반(`vkb::core::HPP*`) 두 버전 공존. |
| [framework/rendering/](../framework/rendering/) | RenderContext, RenderPipeline, RenderTarget, Subpass — 프레임/명령버퍼 라이프사이클 + 드로우 오케스트레이션. |
| [framework/scene_graph/](../framework/scene_graph/) | Node, Transform, Camera, Mesh, Light, Texture 등 씬 그래프와 glTF 로더 산출물. |
| [framework/platform/](../framework/platform/) | `Platform` 추상화 + 플러그인 베이스 클래스([`framework/platform/plugins/plugin_base.h`](../framework/platform/plugins/plugin_base.h)). |
| [framework/stats/](../framework/stats/) | FPS / 메모리 / GPU 타임 통계 수집 및 표시. |
| [components/](../components/) | 플랫폼·IO 의존 코드. `windows/`, `unix/`, `android/`, `ios/`, `core/`(로깅·에러), `filesystem/`. **framework 보다 한 단계 아래** — framework 는 components 위에 얹혀 있다. |
| [samples/](../samples/) | 샘플 본체. `api/`, `extensions/`, `performance/`, `general/`, `tooling/` 카테고리. |
| [shaders/](../shaders/) | 샘플별 셰이더 소스 + 빌드 산출물(`.spv`). 샘플 별로 `glsl/`, `hlsl/`, `slang/` 세 변종을 가질 수 있음. |
| [assets/](../assets/) | glTF 씬, 텍스처, 폰트. 큰 파일은 git-lfs. |
| [bldsys/](../bldsys/) | CMake 헬퍼. `cmake/sample_helper.cmake` 에 **`add_sample()`** 매크로가 있다 (샘플 등록의 핵심). `toolchain/` 에 크로스 컴파일 툴체인. |
| [third_party/](../third_party/) | `volk`, `vma`, `glm`, `glfw`, `imgui`, `ktx`, `astc`, `tinygltf`, `spirv-cross`, `fmt`, `spdlog`, `tracy`, `stb`, `hwcpipe`, `flatbuffers`, `opencl`, `ai-ml-sdk-vgf-library`. |
| [scripts/](../scripts/) | `generate.py` (새 샘플 스캐폴딩), `clang_format.py`, `copyright.py`. |
| [docs/](../docs/) | 빌드 가이드(`docs/build.adoc`), 메모리 한계, doxygen 설정. |
| [antora/](../antora/) | docs.vulkan.org 사이트 빌드 설정. |
| build/ | CMake 산출물 (VS 솔루션·캐시). git ignore. |
| output/ | 런타임 산출물 (스크린샷·로그). |

---

## 2. 런타임 아키텍처 (요약)

부트 플로우:

1. [`app/main.cpp`](../app/main.cpp) — 플랫폼별 클래스(`WindowsPlatform` / `UnixPlatform` / `AndroidPlatform` / `IosPlatform`)를 컴파일 타임에 선택해 생성.
2. `Platform::initialize(plugins::get_all())` — CMake 가 생성한 `app/plugins/plugins.cpp` (템플릿 `plugins.cpp.in`) 의 모든 플러그인을 등록.
3. `Platform::main_loop()` → `main_loop_frame()` → 매 프레임 `Application::update(delta_time)` + `draw()` 호출. 종료 조건은 `ExitCode::Close`.

샘플 베이스 클래스:

- **`vkb::VulkanSample<BindingType>`** ([framework/vulkan_sample.h](../framework/vulkan_sample.h)) — instance/device/swapchain/RenderContext/RenderPipeline + 씬 그래프 + 프레임 루프를 모두 보유. `BindingType::C` 와 `BindingType::Cpp` 두 specialization.
- **`vkb::ApiVulkanSample`** ([framework/api_vulkan_sample.h](../framework/api_vulkan_sample.h)) — `VulkanSampleC` 를 상속. 텍스처/버텍스/셰이더 로딩 등 그래픽스 샘플에 자주 쓰이는 헬퍼 제공. `samples/api/`, `samples/extensions/` 의 대부분이 여기서 출발.
- **`vkb::HPPApiVulkanSample`** ([framework/hpp_api_vulkan_sample.h](../framework/hpp_api_vulkan_sample.h)) — 위 ApiVulkanSample 의 vulkan.hpp 버전. `hpp_<sample>` 들이 사용.
- `hello_triangle` / `hello_triangle_1_3` 처럼 *교육 목적상* 프레임워크를 거의 안 쓰고 `Application` 만 상속하는 예외도 있다.

플러그인:

- 베이스: [`framework/platform/plugins/plugin_base.h`](../framework/platform/plugins/plugin_base.h) — `vkb::PluginBase<...Tags>` 템플릿. 훅: `on_app_start`, `on_update`, `on_post_draw`, `on_app_close` 등.
- 자주 쓰는 플러그인:
  - `start_sample` — CLI `sample <name>` 으로 특정 샘플 부팅.
  - `batch_mode` — 여러 샘플을 순차 실행.
  - `benchmark_mode` — delta_time 고정 + 종료 시 통계 로그.
  - `screenshot` — 특정 프레임 캡처.
  - `shading_language_selection` — `--shading-language glsl|hlsl|slang` 으로 셰이더 변종 선택.
  - `fps_logger`, `gpu_selection`, `force_close`, `window_options`, `file_logger`, `data_path`, `stop_after`.
