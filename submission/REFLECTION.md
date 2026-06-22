# Reflection

**Anti-pattern dễ vướng nhất:** "Small File Problem" (Vấn đề tệp nhỏ).

**Lý do:**
Trong quá trình xử lý dữ liệu thực tế, đặc biệt là với dữ liệu streaming hoặc ghi các batch nhỏ liên tục (ví dụ: log observability), hệ thống rất dễ sinh ra hàng ngàn file nhỏ dưới định dạng parquet/delta. Nếu đội ngũ không thiết lập tự động chạy `OPTIMIZE` và `Z-ORDER`, hiệu suất query sẽ bị giảm sút nghiêm trọng do chi phí overhead mở/đóng file.

Thực tế từ bài lab cho thấy, việc áp dụng `OPTIMIZE + Z-ORDER` đã giúp tăng tốc độ truy vấn lên tới **15.0x** (như kết quả ở NB2) và giảm số lượng file từ 200 xuống còn 1. Điều này chứng minh rằng việc thiếu bảo trì định kỳ (anti-pattern) sẽ gây lãng phí tài nguyên cực lớn. Đồng thời, không dọn dẹp bằng `VACUUM` cũng làm phình to dung lượng lưu trữ. Do đó, đây là lỗi rất dễ mắc phải nếu chỉ tập trung đẩy dữ liệu (ingest) mà quên mất khâu tối ưu Lakehouse.
