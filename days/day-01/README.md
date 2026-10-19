## Day 1 (Thứ Hai 19/10): Global infra, Free plan, Billing an toàn
**Lý thuyết (45'):**
1. Đọc *Exam Guide DVA-C03*, ghi lại các domain và tỉ trọng.
2. Region / AZ / Edge location; mô hình Shared Responsibility.

**Lab (60'):**
1. ✅ Đã có: account dùng **Free plan**, $100 credit, hết hạn 09/03/2027, region `ap-southeast-1` (Singapore). Dùng cố định region này.
2. Root user: bật **MFA** (dùng app Authenticator), không tạo access key cho root.
3. Billing → Budgets → **Create budget → template "Zero spend budget"** để nhận email ngay khi phát sinh $0.01 (dùng thay alert $5).
4. Billing preferences: bật *Free Tier usage alerts*.
5. IAM Identity Center (hoặc IAM) → tạo user `admin-phuc` có `AdministratorAccess` + MFA. Từ giờ đăng nhập bằng user này, không dùng root nữa.
6. Cài AWS CLI v2, chạy `aws configure sso` (hoặc `aws configure`), rồi chạy `aws sts get-caller-identity` để kiểm tra.
7. Hoàn thành các *Explore AWS* activity trên Console Home để nhận thêm credit.

**Xong khi:** `get-caller-identity` trả về user admin (không phải root), có email xác nhận Budget, và thấy số credit ở Billing → Credits.

**Ghi chú:** trả lời 3 câu: *Region khác AZ thế nào? Vì sao không dùng root? Free plan khác Paid plan ra sao?*


## Checklist
- [ ] Lý thuyết
- [ ] Lab
- [ ] Teardown + xem Billing

## Notes

