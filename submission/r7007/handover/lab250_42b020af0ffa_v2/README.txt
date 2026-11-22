Release: lab250_42b020af0ffa_v2
Parent: lab250_42b020af0ffa
Số ảnh: 228
Số nhãn: 1444
Guideline: v1.2 (J01-J04), v1.3 (J05-J10)
QC v1: 32/250 = 12.8% (34 lỗi phát hiện trên 31 ảnh và 1 cảnh scene_593)
Kết luận v1: Hold — Tỷ lệ lỗi 32/250 = 12.8% vượt ngưỡng 2% theo quy định Day 7 nên không Release lô v1.
Kết luận v2: Release — Đã tách 22 ảnh lỗi nhãn detection/thiếu file sang rework_queue.csv và sửa trực tiếp wrong_hash, guideline_mismatch, scene_leak trong v2; còn lại 228 ảnh sạch nhãn detection đủ điều kiện bàn giao.
Ghi chú: Đã sửa trực tiếp trong v2: (1) scene_leak tại scene_593 (đưa ảnh 000000020333 từ val về train để toàn bộ cảnh thống nhất ở split train); (2) wrong_hash tại 000000016010 (đã mở ảnh kiểm tra thấy ảnh đàn ngựa vằn nguyên vẹn, cập nhật lại mã sha256 thực tế vào manifest.csv); (3) guideline_mismatch tại 000000011615 và 000000024567 (chuẩn hóa cột guideline trong truyvet.csv theo đúng phiên bản của job trong jobs.csv). 22 ảnh lỗi detection/thiếu ảnh (bao gồm 000000005992 missing_image) đã tách vào rework_queue.csv giao đúng annotator và reviewer theo jobs.csv. Các ảnh chỉ có lỗi ở nhánh panoptic (invalid_category_id: 000000018491, 000000018833; thing_overlap: 000000011511; category_swap: 000000006471; instance_merge: 000000008277, 000000009400) có nhãn detection đúng nên giữ lại trong v2 và báo riêng cho team panoptic xử lý (riêng ảnh 000000004134 vừa có instance_merge vừa có class_swap nên đã tách vào rework_queue.csv).
