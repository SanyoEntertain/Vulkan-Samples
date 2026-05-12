# CLAUDE.md

이 파일은 이 저장소에서 작업하는 Claude Code 세션을 위한 **라우터** 다. 매 턴 컨텍스트에 로드되므로 짧게 유지한다. 상세 정보는 작업 유형에 맞춰 `agent-docs/*.md` 를 Read 해서 가져온다.

---

## 프로젝트 한 줄

Khronos Group / Arm 의 공식 **Vulkan-Samples** — Vulkan API 학습용 예제와 모바일 GPU(특히 Arm Mali) best-practice 데모를 하나의 멀티플랫폼 코드베이스로 모은 컬렉션. Apache-2.0. 모든 샘플은 단일 실행 파일 `vulkan_samples` 로 합쳐진다.

이 사용자 환경: **Windows 11 + VS 2022 + VulkanSDK 1.4.341.1**, C++20, `VKB_WARNINGS_AS_ERRORS=ON` (새 코드 경고 0 필수). 대부분 샘플은 `<sample>` 과 `hpp_<sample>` (vulkan.hpp 버전) 짝으로 존재.

---

## 작업 유형별 참조 가이드

아래 작업을 시작하기 전에 해당 `agent-docs/*.md` 를 Read 하라. 한 작업이 여러 행에 걸칠 수 있으니 필요한 파일은 모두 읽어도 된다. 표 밖의 단순 질문에는 이 파일만으로 충분하다.

| 작업 유형 | 먼저 읽을 파일 |
|---|---|
| 빌드 / 실행 / CMake 옵션 / Windows 함정 / 셰이더 컴파일러 | [agent-docs/build.md](agent-docs/build.md) |
| 새 샘플 추가 / `add_sample()` / 셰이더 빌드 경로 | [agent-docs/samples.md](agent-docs/samples.md) |
| 코드 스타일 / clang-format / 라이선스 헤더 / copyright | [agent-docs/style.md](agent-docs/style.md) |
| 저장소 구조 / 프레임워크 클래스 위치 / 부트 플로우 / 플러그인 | [agent-docs/repo-map.md](agent-docs/repo-map.md) |
| 디버깅 / Validation / RenderDoc / Tracy / shader printf | [agent-docs/debugging.md](agent-docs/debugging.md) |
| 사용자가 학습 순서·로드맵을 물을 때 | [agent-docs/learning-path.md](agent-docs/learning-path.md) |
| 외부 공식 자료 링크가 필요할 때 | [agent-docs/references.md](agent-docs/references.md) |

---

## 항상 적용되는 규칙 (Claude 용 체크리스트)

아래 7개 규칙은 매 작업 턴에 그대로 적용된다. agent-docs/ 로 옮기지 않고 여기에 둔다.

1. 이 저장소의 문서 규약은 **AsciiDoc(.adoc)** 이다. 새 문서를 작성하라는 요청이 오면 별도 지시가 없는 한 `.adoc` 으로 만들고 AsciiDoc 문법을 따른다. **예외**: `CLAUDE.md` 와 `agent-docs/*.md` 는 의도된 Markdown.
2. `framework/` 안에는 **C 핸들 기반** (`vkb::core::*`) 과 **vulkan.hpp 기반** (`vkb::core::HPP*`, 또는 `BindingType::Cpp`) 의 두 평행 API 가 공존한다. 하나의 샘플 안에서 섞어 쓰지 말 것.
3. `<sample>` 과 `hpp_<sample>` 짝은 *기능적으로 동일* 해야 하는 것이 원칙이지만 항상 1:1 동기화돼 있지는 않다. 한 쪽 동작을 바꿀 때 다른 쪽도 손볼지 명시적으로 결정하고, 결정한 이유를 PR 설명에 남길 것.
4. `.spv` 변경은 보통 의미가 없다 (SDK 버전 차이). 의미 있는 셰이더 수정이 아니면 스테이지에서 빼라.
5. 이 저장소는 git 서브모듈을 다수 사용한다 (third_party). 서브모듈 갱신은 *별도 PR*. 일반 작업에서 임의로 갱신하지 말 것.
6. 새 외부 의존성 추가는 사실상 금지 — 필요한 거의 모든 라이브러리가 이미 `third_party/` 에 있다.
7. Windows 외 플랫폼에서 빌드가 깨지지 않는지 한 번씩 의식할 것 (특히 path separator, `#include` 대소문자, 플랫폼별 `#ifdef`).
