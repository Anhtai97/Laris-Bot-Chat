# CẤU HÌNH ROUTING VÀ INTENT SMAX — LARIS

> Tệp dành cho quản trị viên, không tải vào Knowledge.

## Quyết định hiện tại

Dùng `Messenger Default → AI_NHAN_TIN` làm router tổng. Không bật Trigger GenAI/Other song song.

Không cần tạo Intent riêng cho giá, ưu đãi, địa chỉ, xin ảnh hoặc đặt lịch. Tách các câu này thành Intent có sender riêng dễ làm một tin đi qua hai đường và tạo phản hồi trùng.

## Quy tắc routing

- Tin tự do, câu hỏi giá, yêu cầu đặt lịch và xin ảnh: đều chuyển về `AI_NHAN_TIN`.
- Keyword nếu còn giữ chỉ được chuyển block, không được trả lời trực tiếp và không gọi GenAI riêng.
- Click To Message: tách metadata quảng cáo; chỉ phần chữ khách tự nhập mới vào buffer.
- Yêu cầu gặp nhân viên/khiếu nại: có thể chuyển người thật, nhưng phải dừng luồng AI hiện tại trước khi gửi phản hồi khác.

## Tiếp nhận lịch

Không dùng Intent để thu field hoặc chạy automation. Bot AI hỏi đúng dịch vụ, thời gian hoặc SĐT còn thiếu rồi để nhân viên note thủ công.

## Xin ảnh

Không dùng Intent để gửi ảnh. Bot AI gửi một câu chờ tự nhiên, sau đó nhân viên gửi hình thủ công.

## Checklist chống lặp

- Chỉ một router tổng quát đang bật.
- Không có Keyword chung trả lời trực tiếp.
- Không có GenAI trực tiếp cạnh tranh với buffer 15 giây.
- Không có sender thứ hai trong hoặc sau `AI_TRA_LOI`.
- Một message ID chỉ xuất hiện trong một lượt xử lý ở logs.
