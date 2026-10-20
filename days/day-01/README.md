## Day 1 (Thứ Ba 20/10): Global infra, Free plan, Billing an toàn
**Lý thuyết (45'):**
1. Đọc *Exam Guide DVA-C03* (~15'), ghi lại các domain và tỉ trọng.
   - Format đề: số câu, thời gian, điểm đậu, dạng câu hỏi.
   - 4 domain + tỉ trọng; so với DVA-C02 thì domain nào tăng/giảm.
   - Nội dung mới (Bedrock, Amazon Q, bảo mật AI agent) và những gì *không* thi.
   - Lướt danh sách in-scope services ở cuối guide, đánh dấu service chưa từng dùng.
2. Global infrastructure (~15'): Region / AZ / Edge location.
   - Region gồm những gì, 4 tiêu chí chọn Region.
   - AZ: cô lập lỗi, multi-AZ; khác nhau giữa AZ name và AZ ID.
   - Edge location dùng bởi service nào (CloudFront, Route 53, Global Accelerator, Lambda@Edge…).
   - Phân loại service: global / regional / zonal.
3. Shared Responsibility (~15').
   - *Security OF the cloud* (AWS) vs *IN the cloud* (customer).
   - Ranh giới thay đổi theo EC2 → Lambda → DynamoDB/S3 (ai patch OS, ai lo runtime, ai lo data/IAM/encryption).
   - Shared controls: patch, configuration, training.

> Ghi chép chi tiết ở mục **Notes** bên dưới.

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

### 1. Exam Guide DVA-C03
- **Thời điểm:** DVA-C03 mở đăng ký **27/10/2026**; DVA-C02 thi được đến **30/11/2026** (theo trang AWS Certification).
- **Format (giữ như C02):** 65 câu (50 tính điểm + 15 không tính), 130 phút, pass **720/1000**, multiple choice / multiple response.
- **Domain & tỉ trọng** (⚠️ số C03 lấy từ Tutorials Dojo, chưa đối chiếu được exam guide chính thức — check lại khi AWS publish PDF ngày 27/10):

| # | Domain | DVA-C02 | DVA-C03 |
|---|--------|--------:|--------:|
| 1 | Development with AWS Services | 32% | 30% |
| 2 | Security | 26% | 26% |
| 3 | Deployment → *Testing and Deployment* | 24% | 22% |
| 4 | Troubleshooting and Optimization | 18% | 22% |

- **Điểm mới ở C03:**
  - Task 2.3 mới: bảo vệ AI services / AI agents.
  - AI-assisted development rải khắp các domain: **Amazon Bedrock**, **Amazon Q Developer**.
  - DynamoDB: 4 skill cũ gộp thành 1 skill tổng hợp (vẫn phải nắm chắc).
  - **Không** thi: prompt engineering, thiết kế RAG, chọn model, SageMaker AI, AI governance.
- **Hệ quả cho lộ trình:** Troubleshooting tăng lên 22% → để ý CloudWatch, X-Ray, logging, tối ưu (caching, concurrency).

### 2. Global infrastructure
| Khái niệm | Là gì | Ghi nhớ cho đề |
|---|---|---|
| **Region** | Khu vực địa lý độc lập, gồm nhiều AZ (thường ≥3). VD `ap-southeast-1` | Dữ liệu **không tự rời Region**. Chọn Region theo: compliance/chủ quyền dữ liệu → latency tới user → service có sẵn chưa → giá |
| **Availability Zone (AZ)** | 1+ data center riêng biệt (điện, mạng, làm mát độc lập), nối nhau bằng mạng latency thấp trong cùng Region | Deploy **multi-AZ** = high availability. Tên `ap-southeast-1a` map khác nhau giữa các account → dùng **AZ ID** (`apse1-az1`) khi cần khớp |
| **Edge location / PoP** | Điểm cache/mạng gần user, nhiều hơn Region rất nhiều | Dùng bởi **CloudFront**, **Route 53**, **Global Accelerator**, **Lambda@Edge / CloudFront Functions**, Shield, S3 Transfer Acceleration |
| Local Zone / Wavelength / Outposts | Mở rộng Region ra gần user / mạng 5G / on-prem | Ít gặp ở DVA, chỉ cần nhận diện |

- **Service theo phạm vi:**
  - Global: IAM, Route 53, CloudFront, Organizations.
  - Regional: Lambda, DynamoDB, SQS, API Gateway… (S3 bucket nằm ở 1 Region dù namespace tên là global).
  - Zonal: EC2 instance, EBS volume, subnet.

### 3. Shared Responsibility Model
- **AWS — security *OF* the cloud:** phần cứng, data center, mạng vật lý, hypervisor, phần mềm của managed service.
- **Customer — security *IN* the cloud:** data, IAM (user/role/policy), cấu hình, mã hoá (bật KMS, TLS), code của mình.
- **Ranh giới dịch chuyển theo loại dịch vụ:**

| | EC2 (IaaS) | Lambda / Fargate | DynamoDB / S3 (managed) |
|---|---|---|---|
| Patch OS | **Customer** | AWS | AWS |
| Runtime / code | Customer | Customer (code + dependency; runtime do AWS patch) | — |
| Security group / network config | Customer | Customer (nếu gắn VPC) | — |
| Data, IAM, encryption setting | Customer | Customer | Customer |

- **Shared controls** (cả hai cùng làm, mỗi bên ở tầng của mình): patch management, configuration management, awareness & training.
- **Bẫy đề hay gặp:** "Ai chịu trách nhiệm patch guest OS trên EC2?" → customer. "Ai đảm bảo DynamoDB được patch?" → AWS. "Ai quyết định mã hoá data trong S3?" → customer.

### 4. Trả lời 3 câu ghi chú
1. **Region khác AZ thế nào?** Region là vùng địa lý chứa nhiều AZ; AZ là cụm data center cô lập lỗi bên trong một Region. Multi-AZ để chống sập data center; multi-Region để chống sập cả vùng / phục vụ user ở xa / đáp ứng compliance.
2. **Vì sao không dùng root?** Root có toàn quyền và không bị IAM policy giới hạn (chỉ SCP chặn được), lộ là mất account. Chỉ dùng cho vài tác vụ bắt buộc (đổi account settings, đóng account, một số thao tác billing). Bật MFA, không tạo access key.
3. **Free plan khác Paid plan?** Free plan: được credit (tối đa $200, gồm $100 lúc đăng ký + tối đa $100 khi làm các activity), dùng trong 6 tháng hoặc đến khi hết credit; không bị tính tiền, nhưng một số service bị giới hạn; hết hạn mà không nâng cấp thì account bị đóng. Paid plan: dùng mọi service, phần vượt credit bị tính tiền pay-as-you-go; credit còn lại vẫn được giữ khi nâng cấp.

Nguồn: [AWS Certified Developer – Associate](https://aws.amazon.com/certification/certified-developer-associate/), [DVA-C02 Exam Guide (PDF)](https://d1.awsstatic.com/training-and-certification/docs-dev-associate/AWS-Certified-Developer-Associate_Exam-Guide.pdf), [Tutorials Dojo – What's new DVA-C03](https://tutorialsdojo.com/whats-new-with-the-aws-certified-developer-associate-dva-c03/).

