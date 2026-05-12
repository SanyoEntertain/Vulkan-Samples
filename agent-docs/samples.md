# 샘플 추가 / 수정

> 이 파일은 `CLAUDE.md` 라우터의 일부다. 새 샘플을 스캐폴딩하거나 `add_sample()` 인자·셰이더 빌드 경로를 다룰 때 Read 한다.

---

## 1. 새 샘플 스캐폴딩

```
python scripts/generate.py sample_api  --name MySample --category api
python scripts/generate.py sample      --name MySample --category extensions
```

위 명령이 `<name>.cpp`, `<name>.h`, `CMakeLists.txt`, `README.adoc` 네 파일을 만들고 [`bldsys/cmake/sample_helper.cmake`](../bldsys/cmake/sample_helper.cmake) 의 `add_sample()` 매크로로 등록까지 해 준다.

---

## 2. `add_sample()` 의 주요 인자

[samples/api/hello_triangle/CMakeLists.txt](../samples/api/hello_triangle/CMakeLists.txt) 가 가장 작은 예시.

| 인자 | 설명 |
|---|---|
| `ID` | 폴더 이름 (보통 `${FOLDER_NAME}` 으로 자동) |
| `CATEGORY` | `api` / `extensions` / `performance` / `general` / `tooling` |
| `AUTHOR`, `NAME`, `DESCRIPTION` | 메타데이터 (GUI 목록에 표시) |
| `FILES` | `<id>.cpp`/`<id>.h` 외에 추가할 소스 |
| `LIBS` | 추가 링크 라이브러리 |
| `SHADER_FILES_GLSL` / `SHADER_FILES_HLSL` / `SHADER_FILES_SLANG` / `SHADER_FILES_SPVASM` | 셰이더 경로 (모두 `shaders/` 기준 상대경로) |
| `DXC_ADDITIONAL_ARGUMENTS`, `GLSLC_ADDITIONAL_ARGUMENTS` | 컴파일러 추가 플래그 |

---

## 3. 셰이더 빌드 산출 경로

- GLSL → `<build>/.../shader-glsl-spv/<name>.<ext>.spv`
- HLSL → `<build>/.../shader-spv/<name>.<ext>.spv`
- Slang → `<build>/.../shader-slang-spv/<name>.<ext>.spv`
- SPIR-V asm → `<build>/.../shader-spvasm-spv/<name>.<ext>.spv`

빌드 후 `shaders/<name>/...` 로 복사되므로, 그곳에서 산출 `.spv` 가 보이는 게 정상이다.

---

## 4. 자동 등록

샘플 등록은 자동: [`samples/CMakeLists.txt`](../samples/CMakeLists.txt) 의 `scan_dirs()` 가 `CMakeLists.txt` 가 있는 하위 디렉터리를 재귀적으로 추가한다. 새 폴더만 만들면 별도 등록 불필요.
