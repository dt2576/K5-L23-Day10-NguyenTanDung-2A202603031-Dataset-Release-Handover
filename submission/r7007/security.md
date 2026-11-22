# Bảo mật khi làm việc

## 1. Ca USB: hết giờ, muốn chép lô về nhà làm tiếp
Không được tự ý chép lô dữ liệu lab250_42b020af0ffa ra USB cá nhân hoặc mang dữ liệu về nhà làm tiếp. Dữ liệu dự án chưa qua kiểm duyệt cuối cùng và có thể chứa hình ảnh nhạy cảm hoặc thông tin định danh cá nhân (PII); việc chép ra thiết bị ngoại vi sẽ làm mất kiểm soát truy cập và vi phạm quy định bảo mật. Việc gửi qua email cá nhân, Google Drive cá nhân hoặc nhờ người khác làm hộ cũng sai vì dữ liệu bị đưa ra khỏi môi trường được cấp phép và làm mất tính toàn vẹn của nhật ký truy vết (jobs.csv, truyvet.csv). Cách xử lý đúng là lưu tiến độ trên máy công ty, khóa máy và báo cáo quản lý/team lead để xin gia hạn hoặc phân bổ lại ca làm việc trên hệ thống nội bộ được phê duyệt.

## 2. Năm việc: được hay không
- Mở job được giao trên máy công ty: Được phép, vì thao tác nằm trong đúng phạm vi công việc, đúng tài khoản định danh đã phân công trong jobs.csv và trên thiết bị nằm trong vùng kiểm soát bảo mật của tổ chức.
- Chụp màn hình gửi người quen: Không được phép, vì hành vi này làm rò rỉ hình ảnh, cấu trúc nhãn và thông tin nội bộ của dự án ra bên ngoài cho bên thứ ba chưa ký cam kết bảo mật (NDA).
- Chép lô ra USB: Không được phép, vì USB cá nhân dễ thất lạc, nhiễm mã độc và làm mất khả năng thu hồi hoặc kiểm soát bản sao dữ liệu của lô bàn giao.
- Dán nhãn thật lên chatbot: Không được phép, vì dữ liệu thật của dự án khi gửi lên dịch vụ AI công cộng bên ngoài có nguy cơ bị lưu trữ trên máy chủ bên thứ ba hoặc dùng để huấn luyện lại mô hình ngoài tầm kiểm soát.
- Đưa tài khoản CVAT cho đồng nghiệp: Không được phép, vì mỗi tài khoản gán nhãn (A01-A05) và kiểm duyệt (Q01-Q03) gắn với trách nhiệm cá nhân; dùng chung tài khoản sẽ làm sai lệch hoàn toàn dữ liệu truy vết khi phát sinh sự cố chất lượng.

## 3. Ca khuôn mặt: thấy mặt người rõ trong lô, guideline yêu cầu làm mờ
Khi phát hiện ảnh trong lô có khuôn mặt người thật nhìn rõ (ví dụ các ảnh 000000001000, 000000004134, 000000009378, 000000024021 trong lô r7007 có nhiều khuôn mặt người ở cự ly gần chưa được làm mờ) trong khi guideline yêu cầu phải làm mờ (anonymize/blur PII), quyết định bắt buộc đối với trạng thái lô là Hold + báo người phụ trách (Data Lead / QC Lead / bộ phận bảo mật dữ liệu). Tuyệt đối không tự ý dùng công cụ sửa ảnh để làm mờ hoặc tự ý xóa ảnh khỏi lô gốc delivery/, vì thao tác sửa trực tiếp sẽ làm thay đổi mã băm SHA256 trong manifest.json, phá vỡ tính bất biến của lô v1 và có thể làm lệch tọa độ nhãn. Cần giữ nguyên hiện trạng, ghi nhận danh sách ảnh vi phạm PII và chờ quy trình xử lý ẩn danh hóa chính thức ở phiên bản kế tiếp.
