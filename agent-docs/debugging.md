# 디버깅 & 프로파일링 팁

> 이 파일은 `CLAUDE.md` 라우터의 일부다. Validation/RenderDoc/Nsight/Tracy/shader printf/timestamp 가 필요할 때 Read 한다.

---

- **Validation layer**: `VKB_VALIDATION_LAYERS=ON` (Debug 빌드 기본). GPU-assisted / best-practices / sync 는 별도 스위치 ([agent-docs/build.md](build.md) 의 CMake 옵션 표 참조).
- **객체 이름 / 라벨**: [samples/extensions/debug_utils/](../samples/extensions/debug_utils/) — RenderDoc · Nsight 캡처에서 객체 이름과 마커가 보이게 하는 표준 방법.
- **셰이더 printf**: [samples/extensions/shader_debugprintf/](../samples/extensions/shader_debugprintf/) — `printf` in shader.
- **GPU 시간 측정**: [samples/api/timestamp_queries/](../samples/api/timestamp_queries/) — `vkCmdWriteTimestamp` 의 깔끔한 레퍼런스.
- **러닝 자동화**: `app/plugins/screenshot/`, `app/plugins/fps_logger/`, `app/plugins/benchmark_mode/`, `app/plugins/batch_mode/` 는 CLI 로 켤 수 있는 표준 도구.
- **Tracy**: `VKB_PROFILING=ON` 으로 빌드하면 Tracy 프로파일러가 붙는다.
