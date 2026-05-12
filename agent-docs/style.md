# 코딩 규약

> 이 파일은 `CLAUDE.md` 라우터의 일부다. 새 소스/헤더 작성, 라이선스 헤더, clang-format, copyright 스크립트가 필요할 때 Read 한다.

---

- **라이선스 헤더**: 모든 새 소스 파일에 Apache-2.0 + Copyright 헤더 필수. 기존 [`samples/api/hello_triangle/hello_triangle.cpp`](../samples/api/hello_triangle/hello_triangle.cpp) (1~17 줄) 의 보일러플레이트를 복사.
- **스타일**: Google C++ Style Guide. 저장소 루트의 `.clang-format` 으로 강제. **clang-format-15** 권장 (CI 가 이 버전을 사용).
- **경고 0**: `VKB_WARNINGS_AS_ERRORS=ON` 이 기본. `-Wall -Wextra -Werror` 통과해야 빌드 성공.
- **PR 전 헬퍼**:
  ```
  python scripts/clang_format.py main
  python scripts/copyright.py main --fix
  ```
- **파일 배치 규칙**:
  - 샘플 소스: `samples/<category>/<name>/<name>.{cpp,h}`
  - 샘플 빌드: `samples/<category>/<name>/CMakeLists.txt`
  - 샘플 문서: `samples/<category>/<name>/README.adoc`
  - 셰이더: `shaders/<name>/{glsl|hlsl|slang}/...`
- 자세한 PR/기여 규칙은 [CONTRIBUTING.adoc](../CONTRIBUTING.adoc) 참조.
