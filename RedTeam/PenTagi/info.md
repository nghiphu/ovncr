# Đánh giá việc tối ưu chi phí AI cho PenTagi
- Thực hiện: tuannm
- Thời gian: 24/6/2026
- Đánh giá: Đợi thêm ý kiến nhóm để chốt

## Các mô hình glm 4.5, gemma 3,.... free: 
- mặc dù miễn phí nhưng sẽ giới hạn số lượng token/ 1 ngày
- Hết token nhanh, gọi 1 lệnh pentest đã hết rồi
- Nếu nhiều requets 1 lần quá cũng sẽ bị limit (1 lần pentest gọi rất nhiều prompt song song nhau nên bị limit luôn)

Kiến trúc của PentAGI hoạt động theo kiểu multi-agent orchestration, tức là mỗi agent có vai trò riêng (pentest, coding, reporting, summarizing, searching...), và mỗi vai trò được dẫn hướng bằng prompt riêng
## Prompt caching
Tùy mô hình nhưng đa số đều đã tự động prompt caching bao gồm gpt, anthropic, gemini, deepseek, glm, qwen
## Batch API
Là cơ chế gửi nhiều request AI cùng lúc dưới dạng một “lô” (batch) thay vì gọi từng request realtime qua API thông thường (chỉ có trong gemini, tự động áp dụng).
Cài đặt riêng tham số cho từng AI agent: có khoảng 15 agent và mỗi agent có thể sử dụng các model khác nhau tùy thuộc vào mình cấu hình

## Kết luận
- Không thể sử dụng model free có sẵn vì chỉ cần 1-2 lệnh promt pentest là chạm limit của model free đó
- Đối với các yêu cầu về promt caching, batchAPI để giảm chi phí thì đều đã được tích hợp tự động trong các lệnh promt khi pentagi gửi đi nên đã giảm được chi phí tương đối
- Đối với việc cài đặt riêng mô hình cho từng AI agent thì mình có thể chọn mô hình từng agent phù hợp để giảm chi phí ạ (ví dụ dùng model mạnh cho các agent quan trọng, dùng model rẻ cho các agent phụ)
