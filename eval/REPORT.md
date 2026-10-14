# Báo cáo hoàn thành lab

**Học viên:** Lê Văn Tài · **Mã:** 2A202602464

## Công việc đã hoàn thành

- `agent/gateway.py`: successor routing, chặn route selector trong body, kiểm tra lease và điều kiện ghi, scope, learner đích, A2A audience, khóa idempotency và ngân sách mỗi vòng. `progress.record_mastery` kiểm tra cả learner truyền qua `anchor`; wildcard danh mục được viết lại thành mask rẻ.
- `agent/guardrails.py`: kiểm tra citation/grounding, quét dấu hiệu prompt injection, che các mẫu dữ liệu nhạy cảm và nhận diện phương trình số học tường minh. Các helper này không tự được arena gọi và không thể chứng minh nội dung đúng chỉ nhờ citation.
- `eval/prosecute.py`: các detector cho 17 lớp. Trên bộ fixture công khai, 34 claim được adjudicate và verified, không có false/unproven/rejected; precision, recall, F1 = 1.000 trên fixture đó.
- Đã cá nhân hóa deck/lineup và cập nhật tài liệu trong các thư mục được giao.

## World và kiểm tra

- Tải release `world-df8c55dabb35` từ repo riêng tư chính thức. SHA-256 khớp checksum: `e05327c0bd88b2b1ee9c9194a4bee5535964360d97d462144773d02686705810`. Đã kiểm tra archive không có đường dẫn nguy hiểm hoặc `truth.json`; world có 12.375 trang. ZIP/checksum không được giữ lại trong repo; world nằm trong thư mục bị Git ignore.
- `make doctor` chạy thành công trong WSL Ubuntu với Python 3.12.15 và GNU Make tạm thời: `ready to spar.` Validator báo 0 FAIL, 1 WARN (`R8-lethality-band`, cần bằng chứng spar). Dòng tổng số trang của Makefile hiện in 24.750 vì cộng cả `__total__` lẫn các bộ đếm loại trang; manifest và tổng các bộ đếm đều xác nhận 12.375. Không sửa thư mục `kit/`.
- `validate_deck.py` với world thật: 0 FAIL, 1 WARN.
- Spar toàn trận: thắng `rookie` 100–0 và `adversary` 49–0. Một số tình huống `protocol_misuse` còn trong MISSED do giới hạn chỉ một claim mỗi nhóm; prosecutor dùng slot nhóm A cho `enforcement_failure`.
- Gateway focused checks: foreign Learner bị deny; Learner khớp `ctx.act` được admit; wildcard `registry.list_servers` và `glossary.list_terms` được rewrite lần lượt thành `name` và `term`.
- Bộ test đầy đủ trên WSL/Python 3.12.15: **4.608 passed, 7 skipped, 0 failed**. Các skip được pytest báo cáo; kiểm thử có corpus `.tex` không chạy vì các file nguồn đó không có trong gói world.
- Demo `agent.gateway`, `agent.guardrails`, `agent.strategy`, `eval.prosecute` và `git diff --check` đều thành công.

## Còn cần lưu ý

- `R8-lethality-band` là cảnh báo validator về proxy tĩnh; kết quả spar ở trên là bằng chứng chạy thực tế với hai bot, không chứng minh chiến thắng với mọi đối thủ.
- Guardrail helpers cần được tích hợp vào wrapper tạo ANSWER nếu muốn áp dụng tự động; harness hiện không gọi chúng.
- `make` không có sẵn trong PowerShell trên máy này. Doctor được chạy trong WSL; Python Windows mặc định là 3.11, vì vậy dùng Python 3.12 cho kiểm tra theo yêu cầu dự án.
