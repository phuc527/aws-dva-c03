# Roadmap AWS Developer Associate (DVA-C03): 60 ngày, ngày nào cũng có lab

**Lịch học:** học **Thứ Hai – Thứ Sáu**, nghỉ cuối tuần, tổng cộng 12 tuần. Day 1 = **Thứ Hai 19/10/2026** → Day 60 = **Thứ Sáu 08/01/2027**. Credit Free plan còn đến 09/03/2027 nên vẫn đủ.
Từ ngày 1/1 trở đi là năm 2027. Ngày 1/1 nếu nghỉ Tết thì dời các day phía sau lùi lại 1 ngày.

> **Lưu ý:** Đối chiếu lại với *Exam Guide* chính thức của DVA-C03 trên aws.amazon.com/certification. Roadmap này bám theo 4 domain của dòng DVA: (1) Development with AWS Services, (2) Security, (3) Deployment, (4) Troubleshooting & Optimization.

**Thời lượng mỗi ngày (~2–2.5h):** 45' lý thuyết → 60–90' lab → 15' ghi chú vào `notes/` + flashcard.

---

## Học miễn phí được không? Được, gần như $0

**Cách 1: Tài khoản mới với Free plan (khuyên dùng).**
Từ 15/7/2025, tài khoản AWS mới được chọn *Free plan*. Gói này cho tối đa $200 credit (nhận $100 khi đăng ký, thêm tối đa $100 khi làm các activity hướng dẫn) và dùng được trong 6 tháng.
- Khi dùng Free plan, AWS **không trừ tiền vào thẻ**. Hết credit hoặc hết 6 tháng thì tài khoản bị tạm dừng, trừ khi m tự nâng cấp lên Paid plan.
- 60 ngày lab này ước tính tốn **dưới $20 credit**.
- ⚠️ Kiểm tra lại chính sách hiện tại trên trang AWS Free Tier lúc đăng ký. Đừng bấm "Upgrade to paid plan".

**Cách 2: Tài khoản cũ (Free Tier 12 tháng hoặc đã hết hạn).**
- Làm theo nhãn chi phí trong bảng:
  - 🟢 miễn phí: nằm trong Always Free / Free Tier
  - 🟡 tốn vài cent: chỉ tính tiền khi tài nguyên đang chạy, làm xong **xoá ngay**
  - 🔵 làm local: dùng Docker / LocalStack / `sam local`, $0
- Nếu cẩn thận, tổng chi phí khoảng **< $2**.

**Các dịch vụ always-free dùng nhiều nhất:**
- Lambda: 1M request/tháng
- DynamoDB: 25GB
- SQS, SNS: 1M request
- CloudWatch: metric và alarm cơ bản
- X-Ray: 100k trace
- Cognito: 10k MAU
- CloudFront: 1TB
- CodeBuild: 100 phút build
- Step Functions: 4k state transitions

**Dịch vụ tính tiền theo giờ, dễ bị "đốt" tiền:**
- NAT Gateway, ALB, RDS Proxy, ElastiCache, DAX, Kinesis, Fargate, EC2 không phải dòng micro
- KMS customer key: $1/tháng, tính theo giờ dùng

**Không cần lab trên AWS:** DAX, RDS Proxy, NAT. Học lý thuyết là đủ vì đề chỉ hỏi khái niệm.

**Phương án miễn phí thay thế:**
- [LocalStack](https://localstack.cloud) (Docker) giả lập S3/SQS/SNS/DynamoDB/Lambda/Kinesis
- `sam local`, DynamoDB Local
- Redis chạy trong Docker (thay ElastiCache)

---

## Day 1 (Thứ Hai 19/10): Global infra, Free plan, Billing an toàn
**Lý thuyết (45'):**
1. Đọc *Exam Guide DVA-C03*, ghi lại các domain và tỉ trọng.
2. Region / AZ / Edge location; mô hình Shared Responsibility.

**Lab (60'):**
1. Tạo AWS account mới, chọn **Free plan**, chọn region gần như `ap-southeast-1` (Singapore) và dùng cố định region này.
2. Root user: bật **MFA** (dùng app Authenticator), không tạo access key cho root.
3. Billing → Budgets → **Create budget → template "Zero spend budget"** để nhận email ngay khi phát sinh $0.01 (dùng thay alert $5).
4. Billing preferences: bật *Free Tier usage alerts*.
5. IAM Identity Center (hoặc IAM) → tạo user `admin-phuc` có `AdministratorAccess` + MFA. Từ giờ đăng nhập bằng user này, không dùng root nữa.
6. Cài AWS CLI v2, chạy `aws configure sso` (hoặc `aws configure`), rồi chạy `aws sts get-caller-identity` để kiểm tra.
7. Hoàn thành các *Explore AWS* activity trên Console Home để nhận thêm credit.

**Xong khi:** `get-caller-identity` trả về user admin (không phải root), có email xác nhận Budget, và thấy số credit ở Billing → Credits.

**Ghi chú:** trả lời 3 câu: *Region khác AZ thế nào? Vì sao không dùng root? Free plan khác Paid plan ra sao?*

---

## Phần 1 (19/10 – 27/10): Nền tảng (IAM, CLI, SDK)
| Day | Ngày | Chủ đề | Lab | Chi phí |
|---|---|---|---|---|
| 1 | T2 19/10 | Global infra, Billing | Xem chi tiết ở phần trên | 🟢 |
| 2 | T3 20/10 | IAM users/groups/policies | Viết custom policy chỉ cho đọc 1 bucket S3, test bằng IAM Policy Simulator | 🟢 |
| 3 | T4 21/10 | IAM Roles, trust policy, STS | Tạo role, `aws sts assume-role` từ CLI, dùng credential tạm thời | 🟢 |
| 4 | T5 22/10 | CLI profiles, credential provider chain | Cấu hình nhiều profile, `--profile`, env vars, xem thứ tự ưu tiên của chain | 🟢 |
| 5 | T6 23/10 | SDK (boto3 / JS v3), pagination, retry/backoff | Script liệt kê S3 object có paginator + retry mode `adaptive` | 🟢 |
| 6 | T2 26/10 | Permission boundary, condition keys, ABAC | Policy theo tag `aws:ResourceTag`, test allow/deny | 🟢 |
| 7 | T3 27/10 | **Ôn tập** | Quiz 20 câu IAM + dựng lại lab Day 3 mà không xem hướng dẫn | 🟢 |

## Phần 2 (28/10 – 5/11): Compute & Storage
| Day | Ngày | Chủ đề | Lab | Chi phí |
|---|---|---|---|---|
| 8 | T4 28/10 | EC2, IMDSv2, user data | Launch **t3.micro** với user data cài web server, gọi IMDSv2 lấy credential của role. **Terminate** sau khi xong | 🟢 |
| 9 | T5 29/10 | S3: storage class, versioning, lifecycle | Bật versioning, lifecycle chuyển IA sau 30 ngày, khôi phục object đã xoá | 🟢 |
| 10 | T6 30/10 | S3 security: bucket policy, SSE-S3/KMS/C, CORS | Bucket policy bắt buộc SSE-KMS (dùng key `aws/s3`, free), CORS cho web tĩnh | 🟢 |
| 11 | T2 2/11 | Presigned URL, multipart upload, S3 events | Presigned URL bằng SDK, upload file 200MB bằng multipart | 🟢 |
| 12 | T3 3/11 | S3 static website + CloudFront OAC | Host SPA trên S3 + CloudFront OAC, invalidate cache | 🟢 |
| 13 | T4 4/11 | Elastic Beanstalk | Deploy app với preset **Single instance** (không load balancer), thử các deployment policy, xem `.ebextensions`. **Terminate env** sau khi xong | 🟡 |
| 14 | T5 5/11 | **Ôn tập** | Practice 30 câu (S3/EC2/EB), ghi câu sai | 🟢 |

## Phần 3 (6/11 – 16/11): Lambda, API Gateway
| Day | Ngày | Chủ đề | Lab | Chi phí |
|---|---|---|---|---|
| 15 | T6 6/11 | Lambda: handler, runtime, execution role | Lambda xử lý S3 event, tạo thumbnail | 🟢 |
| 16 | T2 9/11 | Memory/timeout, env vars, layers | Tạo Layer dùng chung, mã hoá env var bằng key mặc định `aws/lambda` | 🟢 |
| 17 | T3 10/11 | Versions, aliases, weighted traffic | Alias `prod` chia 90/10 giữa 2 version | 🟢 |
| 18 | T4 11/11 | Concurrency, cold start, async, DLQ/destinations | Async invocation + on-failure destination về SQS | 🟢 |
| 19 | T5 12/11 | Lambda trong VPC (lý thuyết), container image | Lambda dạng container image, push lên ECR (500MB free). **Không tạo NAT** | 🟢 |
| 20 | T6 13/11 | API GW REST: stage, integration, mapping template | REST API → Lambda proxy, stage `dev`/`prod` + stage variables | 🟢 |
| 21 | T2 16/11 | Throttling, usage plan, API key, caching, HTTP API | Usage plan + API key, so sánh với HTTP API. **Cache:** chỉ đọc lý thuyết (tính tiền theo giờ) | 🟢 |

## Phần 4 (17/11 – 25/11): Dữ liệu
| Day | Ngày | Chủ đề | Lab | Chi phí |
|---|---|---|---|---|
| 22 | T3 17/11 | DynamoDB: PK/SK, RCU/WCU, on-demand vs provisioned | Tạo bảng, tính RCU/WCU theo ví dụ đề thi, CRUD bằng SDK | 🟢 |
| 23 | T4 18/11 | Query vs Scan, LSI/GSI, projection | Thiết kế GSI cho access pattern mới | 🟢 |
| 24 | T5 19/11 | Conditional writes, optimistic locking, transactions | Version attribute + `TransactWriteItems` | 🟢 |
| 25 | T6 20/11 | Streams, TTL, DAX (lý thuyết) | Stream → Lambda ghi audit log; bật TTL | 🟢 |
| 26 | T2 23/11 | ElastiCache, caching strategies | Lazy loading và write-through bằng **Redis chạy Docker local** | 🔵 |
| 27 | T3 24/11 | RDS, RDS Proxy (lý thuyết), Secrets Manager | RDS **db.t4g.micro** Single-AZ + Lambda đọc password từ Secrets Manager (trial 30 ngày). **Xoá DB** sau khi xong | 🟡 |
| 28 | T4 25/11 | **Mini project** | REST API CRUD: API GW → Lambda → DynamoDB | 🟢 |

## Phần 5 (26/11 – 4/12): Messaging & Integration
| Day | Ngày | Chủ đề | Lab | Chi phí |
|---|---|---|---|---|
| 29 | T5 26/11 | SQS standard/FIFO, visibility timeout, long polling, DLQ | Producer/consumer bằng SDK, cố tình làm lỗi để message vào DLQ | 🟢 |
| 30 | T6 27/11 | SNS fan-out, filter policy | SNS → nhiều SQS kèm filter policy | 🟢 |
| 31 | T2 30/11 | EventBridge rules, bus, scheduler | Rule bắt event, scheduler gọi Lambda theo cron | 🟢 |
| 32 | T3 1/12 | Step Functions Standard/Express | State machine có Retry, Catch, Parallel | 🟢 |
| 33 | T4 2/12 | Kinesis Data Streams / Firehose | Chạy trên **LocalStack**, hoặc 1 shard provisioned trên AWS trong 1h rồi xoá | 🔵/🟡 |
| 34 | T5 3/12 | AppSync / GraphQL | AppSync API với DynamoDB resolver | 🟢 |
| 35 | T6 4/12 | **Ôn tập** | Practice 40 câu, vẽ sơ đồ khi nào dùng SQS / SNS / EventBridge / Kinesis | 🟢 |

## Phần 6 (7/12 – 15/12): Security
| Day | Ngày | Chủ đề | Lab | Chi phí |
|---|---|---|---|---|
| 36 | T2 7/12 | Cognito User Pools, JWT, hosted UI | Tạo user pool, đăng nhập, decode ID/access token | 🟢 |
| 37 | T3 8/12 | Identity Pools, API GW authorizers | Bảo vệ API bằng Cognito authorizer + Lambda authorizer | 🟢 |
| 38 | T4 9/12 | KMS, envelope encryption | Tạo customer key → `GenerateDataKey` bằng SDK → **schedule deletion 7 ngày** | 🟡 |
| 39 | T5 10/12 | Secrets Manager vs SSM Parameter Store | Secret có rotation; SecureString parameter (Standard tier free) | 🟢 |
| 40 | T6 11/12 | Encryption in transit, ACM, cross-account | Giả lập cross-account bằng 2 role trong cùng account | 🟢 |
| 41 | T2 14/12 | Security best practices trong code | Audit lại các lab trước bằng IAM Access Analyzer | 🟢 |
| 42 | T3 15/12 | **Ôn tập** | Practice 30 câu Security | 🟢 |

## Phần 7 (16/12 – 24/12): Deployment, CI/CD, IaC
| Day | Ngày | Chủ đề | Lab | Chi phí |
|---|---|---|---|---|
| 43 | T4 16/12 | CloudFormation: params, outputs, intrinsic functions | Stack S3 + Lambda, dùng `!Ref`, `!GetAtt`, `!Sub` | 🟢 |
| 44 | T5 17/12 | Change sets, nested stacks, exports, drift | Change set + Export/ImportValue | 🟢 |
| 45 | T6 18/12 | AWS SAM | `sam init` → `sam local invoke` → `sam deploy --guided` | 🟢 |
| 46 | T2 21/12 | AWS CDK | Dựng lại mini project Day 28 bằng CDK | 🟢 |
| 47 | T3 22/12 | Source + CodeBuild (buildspec.yml) | **GitHub** (CodeCommit không còn nhận khách hàng mới) + CodeBuild chạy unit test | 🟢 |
| 48 | T4 23/12 | CodeDeploy: appspec, hooks, blue/green, canary | Lambda canary `Canary10Percent5Minutes` qua SAM | 🟢 |
| 49 | T5 24/12 | CodePipeline, AppConfig | Pipeline Source → Build → Deploy + manual approval | 🟢 |

## Phần 8 (25/12 – 1/1): Containers, Monitoring, Troubleshooting
| Day | Ngày | Chủ đề | Lab | Chi phí |
|---|---|---|---|---|
| 50 | T6 25/12 | ECR, ECS/Fargate, task role vs execution role | Fargate + ALB, làm trong ~1h rồi **xoá service và ALB** (tốn khoảng $0.1) | 🟡 |
| 51 | T2 28/12 | CloudWatch Logs, custom metrics, EMF, alarms | Custom metric bằng EMF, alarm gửi qua SNS | 🟢 |
| 52 | T3 29/12 | Logs Insights, metric filters | Query lỗi trong log Lambda, metric filter đếm ERROR | 🟢 |
| 53 | T4 30/12 | X-Ray: segments, annotations, sampling | Instrument Lambda + API GW, xem service map | 🟢 |
| 54 | T5 31/12 | CloudTrail, lỗi hay gặp (throttling, 4xx/5xx, timeout) | Tái hiện `ThrottlingException`, `AccessDenied`, timeout rồi sửa | 🟢 |
| 55 | T6 1/1 | Tối ưu: Power Tuning, hot partition | Chạy Lambda Power Tuning, so sánh chi phí và độ trễ | 🟢 |

## Về đích (4/1 – 8/1)
| Day | Ngày | Chủ đề | Lab | Chi phí |
|---|---|---|---|---|
| 56 | T2 4/1 | **Mock exam #1** (65 câu, 130') | Làm lại lab của domain có điểm thấp nhất | 🟢 |
| 57 | T3 5/1 | Chữa đề | **Capstone:** Cognito + API GW + Lambda + DynamoDB + SQS, deploy bằng SAM qua CodePipeline | 🟢 |
| 58 | T4 6/1 | **Mock exam #2** | Thêm X-Ray + alarm cho capstone, debug 1 lỗi cố tình gây ra | 🟢 |
| 59 | T5 7/1 | Flashcard số liệu và giới hạn (Lambda 15', SQS 256KB, DynamoDB item 400KB, API GW timeout...) | Teardown toàn bộ, kiểm tra Billing / Cost Explorer | 🟢 |
| 60 | T6 8/1 | Nghỉ nhẹ, đọc lại câu sai | Dựng lại mini project Day 28 bằng CLI trong 30 phút | 🟢 |

---

## Thói quen cuối mỗi ngày (5')
1. Vào Billing → *Free tier* / *Credits* xem đã dùng bao nhiêu.
2. Xoá các tài nguyên 🟡 (EC2, RDS, ALB, Fargate, Kinesis).
3. Dùng **Tag Editor**, lọc tag `project=dva`, để tìm tài nguyên còn sót. Mọi lab đều gắn tag này.

## Tài nguyên miễn phí
- Exam guide + sample questions chính thức của AWS
- **AWS Skill Builder (free tier):** Official Practice Question Set (20 câu), Exam Prep Standard Course
- **workshops.aws:** Serverless, DynamoDB, CDK, Observability (tự deploy vào account của m)
- **YouTube:** freeCodeCamp *AWS Developer Associate* (Andrew Brown)
- **Bản trả phí (nếu muốn):** Udemy Stephane Maarek, Tutorials Dojo practice exams (~$15)

## Theo dõi tiến độ
- Mục tiêu: ≥ 80% ở mỗi practice set trước khi sang tuần mới, ≥ 85% ở mock exam trước khi đặt lịch thi.
- Ghi mỗi câu sai vào 1 file: *câu hỏi, đáp án đúng, vì sao mình sai*.
