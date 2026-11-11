# Ghi chú phân tích · Day 04

Họ và tên:Phạm Thanh Tùng
MSSV:2A202603695

## 1. Case được chọn

- ID case: H1
- Câu trả lời của agent: Agent có nêu incident GitHub `h02n9572mx9y` chuyển hướng
  tới incident tổng hợp `xr3myzj7ms51`, nhưng chưa đọc chi tiết incident được dẫn tới
  để trả lời đầy đủ phần nguyên nhân và ảnh hưởng.
- Điểm ở lần chạy 1: 60/100

## 2. Bằng chứng trong trace

Dán đoạn trace chứng minh lỗi và nêu nguyên nhân có thể xảy ra.

```text
step 1: get_incident_details(github, h02n9572mx9y)
        -> update có URL tới xr3myzj7ms51
step 1: get_incident_details(cloudflare, yaa0witbl2me)
step 2: model stop
```

Nguyên nhân: model dừng sau khi nhận URL liên kết, thay vì tiếp tục dùng tool để đọc
incident tổng hợp được nhắc trong update. Judge trừ điểm vì thiếu bằng chứng về nguyên
nhân và ảnh hưởng của incident được dẫn tới.

## 3. Thay đổi đã thực hiện

- Sửa gì, ở đâu: Bổ sung `SYSTEM_PROMPT` trong `agent.py`: phân biệt snapshot hiện
  tại với incident lịch sử, bắt buộc dùng `get_status` khi xác nhận trạng thái hiện
  tại, và chỉ kết luận theo bằng chứng tool trả về. Khi tool lỗi/timeout phải nêu rõ
  giới hạn thay vì suy đoán.
- Vì sao chọn thay đổi này: Tăng tính kiểm chứng của agent trong các câu hỏi nhiều
  incident/nguồn, đồng thời xử lý lỗi H2 và H7 đã phát hiện ở các lần chạy trước.

## 4. So sánh hai lần chạy

| Chỉ số | `eval_before.json` | `eval_after.json` |
|---|---|---|
| Điểm tổng | 97.82 | 99.64 |
| Điểm case đã chọn | 60 | 100 |
| Số model calls | 3 | 4 |
| Token, chi phí nếu có | Xem `eval_before.json` | Xem `eval_after.json` |

- Lỗi đã giải quyết: H1 đã đọc incident liên kết và đạt 100/100; tổng điểm tăng từ
  97.82 lên 99.64, với cả 22 case được chấm.
- Lỗi còn lại hoặc mới xuất hiện: Kết quả judge có tính không tất định; A1 giảm nhẹ
  từ 100 xuống 92 giữa hai lần chạy dù cùng cấu hình.

## 5. Giới hạn

Điểm judge có thể thay đổi nhẹ giữa các lần chạy. Snapshot status/incident là dữ liệu
cố định của bài lab, không phản ánh hệ thống thời gian thực. Một lần chạy cho mỗi
phiên bản chỉ là một mẫu đánh giá; vẫn cần kiểm tra trace của case đã chọn.
