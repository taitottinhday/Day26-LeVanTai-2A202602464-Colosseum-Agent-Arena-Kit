# Báo cáo kiểm tra lab

## Đã hoàn thành

- Cập nhật `agent/guardrails.py` để phát hiện chỉ dẫn injection, che dữ liệu nhạy cảm và kiểm tra phép tính đơn giản trong câu trả lời.
- Cập nhật `agent/gateway.py` với routing, xác thực lease/scope/đối tượng ghi, kiểm tra A2A audience, idempotency và giới hạn chi phí.
- Hoàn thiện bộ adjudicator trong `eval/prosecute.py`; chạy trên 40 fixture gắn nhãn: 34 claim được nộp, 34 verified, 0 false, 0 unproven; precision/recall/F1 trên fixture đều 1.0. Đây chỉ là kết quả fixture, không đại diện trận đấu thật.
- Cập nhật chiến lược ngân sách, lineup, deck và README theo giới hạn của lab.

## Kiểm tra đã chạy

- Python đang dùng: 3.11.0; `pyproject.toml` yêu cầu Python `>=3.12,<3.13`.
- `python -m pytest -q`: 4,607 passed, 5 skipped, 3 failed.
  - 2 test trong `tests/test_anchor.py` giả định repo nằm dưới workspace ai20k và truy cập `_REPO_ROOT.parents[2]`; checkout hiện tại không có cấu trúc thư mục đó.
  - `tests/test_isolation.py::test_run_probe_vectors_unsandboxed_baseline` lỗi vì probe gọi `msvcrt.open`, symbol này không có trong Windows CRT.
- Demo `agent.gateway`, `agent.guardrails`, `agent.strategy`, `eval.prosecute`: đều hoàn thành với exit code 0.
- `python validate_deck.py`: 15 FAIL, 6 WARN khi chạy fixture world. Validator tự ghi nhận fixture thiếu world corpus thật; các anchor/drift và lethality chưa được kiểm chứng với release world.

## World validation and remaining environment notes

- Authenticated to the upstream private repo `VinUni-AI20k/Day26-Colosseum-Agent-Arena-Kit` and downloaded release `world-df8c55dabb35`. SHA-256 matched the published checksum: `e05327c0bd88b2b1ee9c9194a4bee5535964360d97d462144773d02686705810`. The archive had no unsafe paths or `truth.json`; it was extracted to `kit/world/df8c55dabb35/` (12,375 pages). The downloaded ZIP and checksum files were removed after verification.
- Equivalent `make doctor` checks passed individually: `kit.gate_no_key` PASS, referee 17 classes/local-only, world manifest/count/no-truth checks passed, and real-world deck validation reported 0 FAIL and 1 WARN (`R8-lethality-band`, which asks for spar evidence). GNU `make` is unavailable in this PowerShell environment, so the Make target itself was not invoked.
- `spar.py --bot rookie --as all`: YOU won 100-0. `spar.py --bot adversary --as all`: YOU won 49-0. The MISSED list still included protocol_misuse claims; the prosecutor submits one claim per rubric family, so it used the family-A slot for higher-weight enforcement_failure.
- After repairing a missing closing docstring in `agent/gateway.py`, `py_compile`, the gateway demo, and prosecution demo passed. Full pytest on the available Python 3.11: 4,607 passed, 5 skipped, 3 failed. Two failures assume the repo is nested under the instructor ai20k workspace; the third calls `msvcrt.open`, which is unavailable on Windows. `pyproject.toml` requires Python 3.12, but this environment's venv is Python 3.11.
