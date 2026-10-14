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

## Còn cần môi trường/tài khoản bên ngoài

Cần tải `world-df8c55dabb35` cùng checksum từ private GitHub release, xác minh SHA-256, rồi chạy validator với world đó và sửa deck theo kết quả thật. GitHub CLI đã cài nhưng phiên hiện tại chưa đăng nhập (`gh auth status` báo chưa có tài khoản). Sau khi đăng nhập, cần commit/push và xác minh trạng thái remote.
