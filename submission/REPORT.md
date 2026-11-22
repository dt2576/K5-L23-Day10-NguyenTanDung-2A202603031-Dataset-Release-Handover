# Báo cáo nhóm 7 — Dataset Release & Handover

- **Seed:** `7007`
- **Repo:** [https://github.com/dt2576/K5-L23-Day10-NguyenTanDung-2A202603031-Dataset-Release-Handover](https://github.com/dt2576/K5-L23-Day10-NguyenTanDung-2A202603031-Dataset-Release-Handover)
- **Thành viên và phân công:** xem `TEAM.md`
- **Release v1:** `lab250_42b020af0ffa`
- **Release v2:** `lab250_42b020af0ffa_v2`

## 1. Số liệu luyện tập

- **Bộ v1:** 250 ảnh.
- **Kết quả QC tổng hợp:** `errors.json` có 34 lỗi; tính theo đối tượng ảnh/cảnh phân biệt có **32/250 trường hợp**, tương đương **12,8%**.
- **Review:** 59 dòng trong `review_log.csv`; quá trình review trước đó ghi nhận 22 trường hợp xác nhận và 37 trường hợp bác bỏ.
- **Bộ v2:** 228 ảnh và 228 file nhãn Detection; 22 ảnh được chuyển sang `rework_queue.csv`; manifest gồm 456 file, tổng 1.444 nhãn.
- **F1 theo seed (rule / cross_check / overall):** *Chưa có số liệu đo được trong các tệp đã cung cấp; cần bổ sung từ kết quả chấm luyện tập*.
- **Số ứng viên trên lô sạch (`--no-faults`):** *Chưa có kết quả xác minh*. Hàng đợi review trên seed 7007 có 59 mục; **không đồng nhất 59 mục với 59 ảnh khác nhau**.
- **Ngưỡng lựa chọn ứng viên cross-check:** *Cần trích từ cấu hình/chạy thử thực tế, không suy đoán từ `_private`*.

## 2. Vì sao báo hết ứng viên làm F1 giảm?

Cross-check tạo **ứng viên nghi vấn**, không phải tất cả đều là lỗi thật. Trong 59 mục review đã ghi nhận, có 22 trường hợp được xác nhận và 37 trường hợp bị bác bỏ. Nếu coi toàn bộ ứng viên là lỗi, số trường hợp báo sai sẽ tăng, làm Precision giảm và có thể kéo F1 xuống. Muốn kết luận F1 giảm **bao nhiêu** cần có kết quả so sánh F1 từ lần chạy thực tế; hiện chưa có bảng số đó trong tài liệu được cung cấp.

## 3. Quyết định và lineage

Tỷ lệ ảnh/cảnh bị ảnh hưởng trong v1 là **32/250 = 12,8%**, cao hơn ngưỡng **2%** của bài. Vì vậy quyết định đối với v1 là **HOLD**, không cho qua như một lô sạch. Sự kiện `decision` HOLD cho `lab250_42b020af0ffa` và sự kiện `release` cho `lab250_42b020af0ffa_v2` với `parent` là v1 đã được ghi nhận để đáp ứng kiểm tra lineage.

Run huấn luyện **R-021** dùng dữ liệu thuộc lineage của v1 nên được đánh dấu cần **xem xét ảnh hưởng, đánh giá lại hoặc huấn luyện lại** trước khi dựa vào kết quả. Điều này không có nghĩa đã thực hiện huấn luyện lại R-021.

## 4. Hash chứng minh gì, không chứng minh gì?

SHA-256 cho phép xác minh file có thay đổi so với mã hash đã ghi nhận hay không. Trong v2, `manifest.csv` kê khai 456 file ảnh/nhãn và checker xác nhận hash tương ứng khớp nội dung thực tế. Hash **không chứng minh nhãn đúng về mặt ngữ nghĩa**: một bounding box đặt sai đối tượng vẫn có thể có mã hash hợp lệ. Vì vậy phải kết hợp kiểm tra luật, kiểm tra trực quan và review log.

## 5. Truy vết

Bộ bàn giao có `truyvet.csv`, `trace.json`, `jobs.csv` và lineage để truy ngược nguồn gốc, người gán nhãn, người kiểm tra, phiên bản guideline và các nhóm ảnh cần thông báo. `trace.json` có 2 ảnh `tier_a`, 5 ảnh `tier_b` và 5 mã người được thông báo; các giá trị đã vượt qua điều kiện kiểm tra tính hợp lệ của checker.

Nguyên tắc phân tầng cần bám theo kết quả đánh giá tác động/ưu tiên xử lý thực tế; **không suy diễn ý nghĩa cụ thể của tier_a và tier_b nếu chưa có quy tắc phân tầng trong repo**. Khi kiểm tra `guideline_mismatch`, dùng phiên bản guideline gắn với **job sở hữu ảnh** trong `jobs.csv`, không dùng một phiên bản mặc định cho toàn lô.

## 6. Bug hoặc điểm chưa rõ trong lab

- Validator Detection từng dùng dung sai bbox quá nhỏ, dẫn tới nhiều cảnh báo tọa độ sát mép. Sau khi điều chỉnh và thêm hồi quy, bộ test riêng của Detection đạt 43 PASS theo nhật ký sử dụng AI.
- Logic decode ID trong mask Panoptic phải dùng `R + G × 256 + B × 65536`; dùng `B × 256` có thể khiến thống kê pixel sai.
- Bản ZIP handover nháp từng không khớp cấu trúc/cột CSV mà checker yêu cầu. Bản v2 sau khi đồng bộ về đường dẫn làm việc và ghi đủ lineage đã đạt **17 PASS, 0 FAIL** với `scripts/check_handover.py`.
- **Giới hạn:** PASS của checker chứng minh tính hợp lệ về định dạng và tính nhất quán, không thay cho ground truth hay kiểm định chất lượng nhãn cuối cùng. Các chỉ số F1 và số ứng viên `--no-faults` chưa được cung cấp để xác nhận.

## 7. Kết quả bàn giao và liên kết thành viên

| Thành viên | GitHub | Phần đảm nhận |
|---|---|---|
| Nguyễn Tấn Dũng | [dt2576](https://github.com/dt2576) | Tích hợp, quản lý nhóm, QC và quyết định HOLD |
| Nguyễn Thị Hương Giang | [Giangnth](https://github.com/Giangnth) | Cross-check và review ảnh |
| Bùi Kim Trại | [KtraiVla](https://github.com/KtraiVla) | Panoptic và Metadata |
| Ngô Thị Minh Quang | [NgoThiMinhQuang](https://github.com/NgoThiMinhQuang) | Detection |
| Nguyễn Trọng Hùng | [hungcuday](https://github.com/hungcuday) | Handover v2, truy vết và bảo mật |

> Kết quả tổng thể: **17 PASS, 0 FAIL** theo lần chạy checker mà nhóm cung cấp. Cần đối chiếu lại trạng thái Git và các file nộp cuối cùng trước khi kết luận đã nộp lên hệ thống.
