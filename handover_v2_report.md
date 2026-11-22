# Báo cáo hoàn thiện Dataset Release v2 (`handover_v2_report.md`)

- **Run:** `runs/r7007` (Seed `7007`)
- **Parent Release (v1):** `lab250_42b020af0ffa`
- **Handover Release (v2):** `lab250_42b020af0ffa_v2`
- **Đường dẫn làm việc:** `runs/r7007/handover/lab250_42b020af0ffa_v2/`
- **Đường dẫn nộp Git:** `submission/r7007/handover/lab250_42b020af0ffa_v2/` và `submission/r7007/handover/lab250_42b020af0ffa_v2.sha256`

---

## 1. Những phần kế thừa từ gói ZIP cũ (`handover_v2_review.zip`)
- Kế thừa nguồn dữ liệu ảnh và nhãn gốc từ lô v1 (`runs/r7007/delivery/images/` và `runs/r7007/delivery/labels/`) mà không sửa tay nội dung file nhãn hay ghi đè lên thư mục `delivery/`.
- Kế thừa nội dung nền tảng của 3 mục bảo mật trong `security.md` (ca USB, 5 tình huống được/không được phép, quy trình xử lý ảnh có khuôn mặt người thật).
- Kế thừa danh sách kết quả QC chính thức từ nhánh `main` (`submission/r7007/errors.json` gồm 34 lỗi trên 32 mục tiêu và `submission/r7007/review_log.csv` gồm 59 dòng review).

---

## 2. Những phần đã sửa toàn diện
1. **Đồng bộ Release ID chuẩn (`lab250_42b020af0ffa`):**
   - Trong bản ZIP cũ tạo trên Windows, ký tự xuống dòng `CRLF` trong `manifest.json` và `metadata.json` làm mã băm v1 bị lệch thành `lab250_20c5f74a77e2`. Đã chuẩn hóa về `LF` (`\n`), thống nhất `Parent Release = lab250_42b020af0ffa` và đổi thư mục bàn giao thành `runs/r7007/handover/lab250_42b020af0ffa_v2/`.
2. **Loại bỏ hoàn toàn UTF-8 BOM (`0xEF,0xBB,0xBF`):**
   - Bản cũ ghi bằng PowerShell mặc định có BOM khiến `check_handover.py` và `score_findings.py` lỗi `KeyError: 'file'` và `JSONDecodeError`. Toàn bộ các file `.csv`, `.json`, `.txt`, `.md`, `.jsonl`, `.sha256` đã được ghi lại bằng `UTF-8 without BOM` và dòng kết thúc `LF`.
3. **Sửa `rework_queue.csv` và lọc lại `images/`, `labels/` trong v2:**
   - Bản cũ để trống `rework_queue.csv` (chỉ có dòng tiêu đề) và giữ 249 ảnh trong v2.
   - Bản mới đã đối chiếu đầy đủ 34 lỗi trong `errors.json`, tách đúng **22 ảnh** có lỗi Detection hoặc thiếu file sang `rework_queue.csv` kèm đúng `annotator`, `reviewer` theo `jobs.csv`, đồng thời loại 22 ảnh/nhãn này khỏi `v2/images/` và `v2/labels/` (còn lại đúng **228 ảnh** và **228 file nhãn** khớp tên 1-1).
4. **Xử lý riêng ảnh `000000005992` (`missing_image`):**
   - Trong gói ZIP cũ, ảnh `000000005992` bị thiếu file ảnh nhưng vẫn còn dòng trong `truyvet.csv` (250 dòng) và còn file nhãn gây lệch cấu trúc. Bản mới đã xóa hoàn toàn `000000005992` khỏi `v2/images/`, `v2/labels/`, `v2/truyvet.csv`, `v2/manifest.csv` và đưa vào `rework_queue.csv` (`000000005992,missing_image,A03,Q02,bo_sung_file_anh_va_kiem_tra_nhan`).
5. **Sửa cấu trúc và dữ liệu `truyvet.csv`:**
   - Bản cũ chỉ có 5 cột (`image,scene_id,split,job_id,guideline_version`), thiếu `annotator`, `reviewer` và chưa sửa lỗi `scene_leak` cũng như `guideline_mismatch`.
   - Bản mới dùng đúng 7 cột chuẩn `image,scene_id,split,job_id,annotator,reviewer,guideline` cho 228 ảnh trong v2; lấy `guideline` theo `jobs.csv` (sửa `guideline_mismatch` cho `000000011615` và `000000024567`) và đưa ảnh `000000020333` thuộc `scene_593` từ `val` về `train` (sửa triệt để `scene_leak`).
6. **Sửa cấu trúc `label_summary.csv`:**
   - Bản cũ dùng 3 cột `"class_id","class_name","count"` và đếm trên 249 ảnh (1.725 nhãn).
   - Bản mới dùng đúng 2 cột `class,count` theo tên lớp trong `classes.txt`, chỉ thống kê trên 228 file nhãn thực tế có trong v2 và kết thúc bằng dòng `TOTAL,1444`.
7. **Sửa `manifest.csv`:**
   - Liệt kê đúng 456 file (`228 ảnh` trong `images/` + `228 nhãn` trong `labels/`) với cột `file,sha256` và mã băm SHA256 thực tế từ byte của từng file (sửa trực tiếp lỗi `wrong_hash` của ảnh `000000016010`).
8. **Viết lại `README.txt` theo đúng `templates/README.txt`:**
   - Cập nhật đầy đủ các khóa `Release: lab250_42b020af0ffa_v2`, `Parent: lab250_42b020af0ffa`, `Số ảnh: 228`, `Số nhãn: 1444`, `QC v1: 32/250 = 12.8%`, `Kết luận v1: Hold`, `Kết luận v2: Release` và `Ghi chú` chi tiết.
9. **Hoàn thiện `trace.json`, `security.md`, `lineage.jsonl` và `report.md`:**
   - `trace.json`: Truy vết chính xác `tier_a` (2 ảnh), `tier_b` (5 ảnh), `notify` (5 mã nhân sự), đạt F1 truy vết = `1.0`.
   - `security.md`: Đặt đúng tại `runs/r7007/security.md` (và `submission/r7007/security.md`), xóa bản đặt nhầm trong thư mục con `v2/`, bổ sung kết luận `Hold` và dẫn chứng mã ảnh có khuôn mặt người rõ trong lô (`000000001000`, `000000004134`, `000000009378`, `000000024021`).
   - `lineage.jsonl`: Ghi đầy đủ 4 sự kiện `inject`, `train` (`R-021`), `decision` (`HOLD`) cho `lab250_42b020af0ffa` và `release` cho `lab250_42b020af0ffa_v2` (`parent: lab250_42b020af0ffa`). Kiểm tra `lineage.py impact --release-id lab250_42b020af0ffa` trả về đúng `R-021: affected=True action=review_or_retrain`.

---

## 3. Danh sách 22 ảnh phải Rework (loại khỏi v2, đưa vào `rework_queue.csv`)

Các ảnh có lỗi thuộc nhóm `DET_REWORK` (`missing_image`, `invalid_class`, `bbox_out_of_bounds`, `duplicate_box`, `class_swap`, `bbox_drift`, `missing_box`):

| # | Ảnh (`image`) | Loại lỗi (`type`) | Job | Annotator | Reviewer | Hành động (`action`) | Ghi chú thêm |
|---:|---|---|---|---|---|---|---|
| 1 | `000000000785` | `missing_box` | J01 | A04 | Q01 | `bo_sung_box_detection_con_thieu` | Thiếu box skis |
| 2 | `000000001268` | `class_swap` | J01 | A04 | Q01 | `gan_lai_dung_class_detection_theo_anh` | |
| 3 | `000000001353` | `bbox_drift` | J01 | A04 | Q01 | `can_chinh_lai_khung_bao_detection_khop_vat_the` | |
| 4 | `000000002685` | `class_swap` | J02 | A01 | Q01 | `gan_lai_dung_class_detection_theo_anh` | |
| 5 | `000000003845` | `class_swap` | J02 | A01 | Q01 | `gan_lai_dung_class_detection_theo_anh` | Kèm `invalid_class` trong `errors.json` |
| 6 | `000000004134` | `class_swap` | J02 | A01 | Q01 | `gan_lai_dung_class_detection_theo_anh` | Kèm `instance_merge` trong `errors.json` |
| 7 | `000000004765` | `invalid_class` | J02 | A01 | Q01 | `sua_class_id_ngoai_pham_vi_0_79` | |
| 8 | `000000005992` | `missing_image` | J03 | A03 | Q02 | `bo_sung_file_anh_va_kiem_tra_nhan` | Đã xóa khỏi `truyvet.csv` và `labels/` của v2 |
| 9 | `000000008211` | `class_swap` | J04 | A04 | Q03 | `gan_lai_dung_class_detection_theo_anh` | |
| 10 | `000000009590` | `class_swap` | J04 | A04 | Q03 | `gan_lai_dung_class_detection_theo_anh` | |
| 11 | `000000009914` | `class_swap` | J04 | A04 | Q03 | `gan_lai_dung_class_detection_theo_anh` | |
| 12 | `000000013177` | `class_swap` | J06 | A04 | Q02 | `gan_lai_dung_class_detection_theo_anh` | |
| 13 | `000000014439` | `bbox_drift` | J06 | A04 | Q02 | `can_chinh_lai_khung_bao_detection_khop_vat_the` | |
| 14 | `000000015517` | `bbox_drift` | J07 | A05 | Q01 | `can_chinh_lai_khung_bao_detection_khop_vat_the` | |
| 15 | `000000016249` | `class_swap` | J07 | A05 | Q01 | `gan_lai_dung_class_detection_theo_anh` | |
| 16 | `000000016451` | `bbox_out_of_bounds` | J07 | A05 | Q01 | `cat_chinh_box_vuot_bien_anh` | |
| 17 | `000000017182` | `bbox_drift` | J07 | A05 | Q01 | `can_chinh_lai_khung_bao_detection_khop_vat_the` | |
| 18 | `000000018380` | `bbox_drift` | J08 | A04 | Q01 | `can_chinh_lai_khung_bao_detection_khop_vat_the` | |
| 19 | `000000019109` | `bbox_drift` | J08 | A04 | Q01 | `can_chinh_lai_khung_bao_detection_khop_vat_the` | |
| 20 | `000000020247` | `duplicate_box` | J09 | A03 | Q01 | `xoa_box_trung_lap_1_2_pixel` | |
| 21 | `000000022755` | `bbox_drift` | J09 | A03 | Q01 | `can_chinh_lai_khung_bao_detection_khop_vat_the` | |
| 22 | `000000023899` | `bbox_drift` | J10 | A05 | Q03 | `can_chinh_lai_khung_bao_detection_khop_vat_the` | |

---

## 4. Những ảnh có trong `errors.json` nhưng được giữ lại trong v2 (9 mục tiêu)

Theo đúng quy tắc bàn giao sản phẩm Detection:
1. **Nhóm lỗi sửa trực tiếp được ngay trong v2 (3 ảnh + 1 cảnh):**
   - `000000016010` (`wrong_hash`): File ảnh JPEG gốc nguyên vẹn, chỉ sai hash trong manifest v1 -> giữ lại trong v2 và ghi mã SHA256 thật (`6c78137345b25b5bf983dd8f3b2ee1e9268da478a472f9282267ed90878a0a10`) vào `manifest.csv`.
   - `000000011615` (`guideline_mismatch`, thuộc `J05`): Giữ lại trong v2, cập nhật cột `guideline` trong `truyvet.csv` theo đúng `J05` là `v1.3`.
   - `000000024567` (`guideline_mismatch`, thuộc `J10`): Giữ lại trong v2, cập nhật cột `guideline` trong `truyvet.csv` theo đúng `J10` là `v1.3`.
   - `scene_593` (`scene_leak`): Cảnh gồm 5 ảnh (`000000020333`, `000000020553`, `000000020571`, `000000020992`, `000000021167`), trong đó `000000020333` bị đặt ở `val` còn 4 ảnh kia ở `train` -> giữ cả 5 ảnh trong v2 và chuẩn hóa split của `000000020333` về `train` trong `truyvet.csv`.
2. **Nhóm lỗi chỉ thuộc nhánh Panoptic, nhãn Detection vẫn đúng (6 ảnh):**
   - `000000006471` (`category_swap`)
   - `000000008277` (`instance_merge`)
   - `000000009400` (`instance_merge`)
   - `000000011511` (`thing_overlap`)
   - `000000018491` (`invalid_category_id`)
   - `000000018833` (`invalid_category_id`)
   *(6 ảnh này giữ nguyên trong v2 và được ghi chú rõ trong `README.txt` để thông báo cho team Panoptic).*

> **Ghi chú báo lại Trưởng nhóm về bằng chứng trong `review_log.csv` / `errors.json` (không tự ý sửa file của nhóm):**
> - Tại dòng 6 của `review_log.csv` (`"000000008277","instance_merge","confirmed","Các box sandwich không bị lệch khỏi đối tượng; bác bỏ nghi vấn."`), nội dung `note` đang bị trùng với dòng 9 (`000000015079`) và mang nội dung bác bỏ trong khi `verdict` là `confirmed`.
> - Hai ảnh `000000003845` (`invalid_class` + `class_swap`) và `000000004134` (`class_swap` + `instance_merge`) đang xuất hiện 2 lần trong `errors.json` (theo `GUIDE.md` mỗi ảnh mang nhiều nhất 1 lỗi gài). Theo đúng yêu cầu mục 4, mình giữ nguyên không tự sửa `review_log.csv` và `errors.json`.

---

## 5. Kết quả kiểm tra Hash và Thống kê lô v2
- **Tổng số ảnh v1:** `250`
- **Số ảnh chuyển sang `rework_queue.csv`:** `22`
- **Số ảnh giữ lại trong `v2/images/`:** `228`
- **Số file nhãn trong `v2/labels/`:** `228` (khớp 100% tên file và SHA256 gốc trong `delivery/manifest.json`)
- **Tổng số file trong `v2/manifest.csv`:** `456` file (`228` ảnh + `228` nhãn)
- **Tổng số bounding box trong `v2/label_summary.csv`:** `1444` nhãn (`TOTAL,1444`)
- **Đóng gói `pack_release.py`:**
  - File gói chuẩn: `runs/r7007/handover/lab250_42b020af0ffa_v2.zip` (`461 file`)
  - Mã SHA256 gói (`lab250_42b020af0ffa_v2.sha256`):
    `a5e92fcc86d33c2e24f4930d320e9782eec18450c57752f1b92db84a5929da8a  lab250_42b020af0ffa_v2.zip`
  - Bản sao `runs/r7007/handover_v2_review.zip` có cùng nội dung và cùng mã SHA256 `a5e92fcc86d33c2e24f4930d320e9782eec18450c57752f1b92db84a5929da8a`.

---

## 6. Kết quả chạy kiểm thử bắt buộc (`check_handover.py`, `lineage.py`, `score_findings.py`)

### Các lệnh đã chạy:
```bash
python scripts/pack_release.py runs/r7007/handover/lab250_42b020af0ffa_v2
python scripts/lineage.py impact --release-id lab250_42b020af0ffa
python scripts/score_findings.py --run runs/r7007
python scripts/check_handover.py --run runs/r7007
python scripts/export_submission.py --run runs/r7007
```

### Kết quả `lineage.py impact`:
```text
R-021: affected=True action=review_or_retrain
```

### Kết quả `score_findings.py`:
```text
# Tự đo — seed 7007

- Số lỗi nộp: 34
- F1 tổng ≈ 0.6 · lỗi luật ≈ 1.0 · lỗi gán nhãn ≈ 0.2
- Truy vết F1 ≈ tier_a 1.0 · tier_b 1.0 · notify 1.0
```

### Kết quả `check_handover.py --run runs/r7007` (17/17 PASS, 0 FAIL):
```text
  [PASS] H1   v1 (lab250_42b020af0ffa) giữ nguyên
  [PASS] H2   errors.json có 34 lỗi, gồm đủ lỗi luật mức file
  [PASS] H3   review_log.csv: 59 dòng, mọi lỗi gán nhãn đã được xem ảnh và xác nhận, ghi chú cụ thể
  [PASS] H4   lab250_42b020af0ffa_v2/ đủ 7 phần
  [PASS] H5   v2 có 228 ảnh; 22 ảnh lỗi chuyển sang rework_queue, không nằm trong v2
  [PASS] H6   rework_queue ghi đúng người gán/người kiểm theo jobs.csv
  [PASS] H7   nhãn trong v2 giữ nguyên như đã gán (không sửa tay)
  [PASS] H8   manifest.csv: 456 file, sha256 đúng
  [PASS] H9   label_summary.csv khớp (1444 nhãn)
  [PASS] H10  truyvet.csv: mỗi ảnh một dòng, job/người/guideline theo jobs.csv
  [PASS] H11  v2 không còn cảnh nằm ở cả train và val
  [PASS] H12  README.txt: tên lô, lô cha, số ảnh, số nhãn khớp
  [PASS] H13  QC v1 32/250 = 12.8% ⇒ Hold (ngưỡng 2%); có kết luận v2
  [PASS] H14  lineage: HOLD cho v1 và release v2 có parent v1
  [PASS] H15  đóng gói lại v2 ra đúng mã trong .sha256
  [PASS] H16  trace.json: tier_a 2 ảnh, tier_b 5 ảnh, notify 5 người
  [PASS] H17  security.md đủ 3 mục, ca khuôn mặt kết luận Hold và nêu ảnh cụ thể trong lô

Tổng: 17 PASS, 0 FAIL
=> ĐỦ ĐIỀU KIỆN ĐỊNH DẠNG (PASS không có nghĩa nhãn đúng).
```

- **Các lỗi còn tồn tại trong checker:** Không còn (`0 FAIL`).

---

## 7. Danh sách File thay đổi, Commit và Pull Request
- **Nhánh làm việc:** `feature/hung-handover-v2-fix` (tạo trực tiếp từ `origin/main` commit `9bed0b5fddc5e5dbff1243f8212293056b887504`).
- **Các Commit đã thực hiện (chia 2 commit):**
  - **Commit 1 (`58ef773`):** `feat(handover): complete dataset release v2 package and lineage for r7007` (`submission/r7007/handover/lab250_42b020af0ffa_v2/`, `submission/r7007/handover/lab250_42b020af0ffa_v2.sha256`, `submission/lineage.jsonl`)
  - **Commit 2:** `feat(trace-security): add trace.json, security.md and handover_v2_report.md for r7007` (`submission/r7007/trace.json`, `submission/r7007/security.md`, `handover_v2_report.md`)
- **Đường dẫn tạo / xem Pull Request:** `https://github.com/dt2576/K5-L23-Day10-NguyenTanDung-2A202603031-Dataset-Release-Handover/pull/new/feature/hung-handover-v2-fix`
- **Danh sách các file thuộc phạm vi bàn giao được commit:**
  - `submission/lineage.jsonl`
  - `submission/r7007/trace.json`
  - `submission/r7007/security.md`
  - `submission/r7007/handover/lab250_42b020af0ffa_v2.sha256`
  - `submission/r7007/handover/lab250_42b020af0ffa_v2/README.txt`
  - `submission/r7007/handover/lab250_42b020af0ffa_v2/label_summary.csv`
  - `submission/r7007/handover/lab250_42b020af0ffa_v2/manifest.csv`
  - `submission/r7007/handover/lab250_42b020af0ffa_v2/rework_queue.csv`
  - `submission/r7007/handover/lab250_42b020af0ffa_v2/truyvet.csv`
  - `submission/r7007/handover/lab250_42b020af0ffa_v2/images/*.jpg` (228 ảnh)
  - `submission/r7007/handover/lab250_42b020af0ffa_v2/labels/*.txt` (228 nhãn)
  - `handover_v2_report.md`
- **Không chạm tới bất kỳ file nào của các thành viên khác** (`validator/`, `scripts/`, `tools/`, `tests/`, `submission/r7007/errors.json`, `submission/r7007/review_log.csv`, `submission/r7007/candidates.json`).
