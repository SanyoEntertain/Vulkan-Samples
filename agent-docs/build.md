# 빌드 & 실행 (Windows)

> 이 파일은 `CLAUDE.md` 라우터의 일부다. 빌드·실행·CMake 옵션·Windows 함정·셰이더 컴파일러 경로가 필요할 때 Read 한다.

기타 플랫폼(Linux/Android/iOS/macOS) 빌드는 [docs/build.adoc](../docs/build.adoc) 참조.

---

## 1. 설정

설정(이미 한 번 수행됨, build/ 디렉터리 존재):

```
cmake -G "Visual Studio 17 2022" -A x64 -S . -B build
```

> 공식 문서([docs/build.adoc:189](../docs/build.adoc)) 는 `-Bbuild/windows` 를 권장하지만, 이 저장소의 현재 사용자는 `-Bbuild` 로 설정해 두었다. 두 경로 중 하나를 골라 일관성 있게 유지할 것.

---

## 2. 빌드

```
cmake --build build --config Release --target vulkan_samples
```

---

## 3. 실행

```
build\app\bin\Release\AMD64\vulkan_samples.exe sample hello_triangle
```

또는 인자 없이 실행하면 GUI 에서 카테고리별로 샘플을 고를 수 있다.

---

## 4. 자주 만지는 CMake 옵션

| 옵션 | 기본 | 설명 |
|---|---|---|
| `VKB_BUILD_SAMPLES` | ON | 샘플 바이너리 빌드 여부 |
| `VKB_VALIDATION_LAYERS` | ON(Debug) | Vulkan validation layer |
| `VKB_VALIDATION_LAYERS_GPU_ASSISTED` | OFF | GPU-assisted validation |
| `VKB_VALIDATION_LAYERS_BEST_PRACTICES` | OFF | best-practices layer |
| `VKB_VALIDATION_LAYERS_SYNCHRONIZATION` | OFF | sync validation |
| `VKB_VULKAN_DEBUG` | ON | `VK_EXT_debug_utils` 활성화 |
| `VKB_WARNINGS_AS_ERRORS` | ON | 경고도 오류로 — **새 코드 경고 0 필수** |
| `VKB_WSI_SELECTION` | (Win 무관) | Linux 창 시스템 선택 (XCB/XLIB/WAYLAND/D2D) |
| `VKB_PROFILING` | OFF | Tracy 프로파일러 |
| `VKB_SKIP_SLANG_SHADER_COMPILATION` | OFF | slangc 부재 시 회피용 |

---

## 5. 셰이더 컴파일러 탐색

`$VULKAN_SDK/Bin` 에서 `dxc`, `glslc`, `slangc`, `spirv-as` 를 찾는다. 현재 환경 기준 `C:/VulkanSDK/1.4.341.1/Bin/`.

---

## 6. 이 사용자 환경의 알려진 함정

- VS Code "CMake Tools" 확장에 stale kit 등록 경고가 반복된다 (`Visual Studio Community 2022 Release - amd64 - (ab5ef3a7)`). 빌드 시 컴파일러 검출이 실패하면 명령 팔레트의 **CMake: Scan for Kits** 를 먼저 실행할 것.
- `git status` 에 `shaders/**/*.spv` 다수가 `M` 으로 잡혀 있는 경우가 잦다. 보통은 사용자가 다른 SDK 버전으로 셰이더를 재컴파일해서 바이트만 달라진 것이다. 의미 있는 변경인지 확인 전엔 스테이지하지 말 것.
