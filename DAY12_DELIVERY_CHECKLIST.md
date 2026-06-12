#  Delivery Checklist — Day 12 Lab Submission

> **Student Name:** Do Quoc An
> **Student ID:** 2A202600952
> **Date:** 12/06/2026

---

##  Submission Requirements

Submit a **GitHub repository** containing:

### 1. Mission Answers (40 points)

## Part 1: Localhost vs Production

### Exercise 1.1: Anti-patterns found
1. Cấu hình cứng (Hardcode) các tham số như IP, Port, Token.
2. Không xử lý lỗi đồng bộ khi server khởi động/tắt, không có cấu trúc log chuẩn (thiếu Logging JSON).

### Exercise 1.3: Comparison table
| Feature | Develop | Production | Why Important? |
|---------|---------|------------|----------------|
| Config  | Hardcode, file .env. | Lấy từ Environment Variables (12-Factor). | Bảo mật, dễ thay đổi trên cloud. |
| Host | 127.0.0.1 (localhost) | 0.0.0.0 | Cho phép kết nối từ ngoài container. |
| Logging | Print text thường. | JSON Structured. | Dễ query và monitor trên các hệ thống lớn. |

## Part 2: Docker

### Exercise 2.1: Dockerfile questions
1. Base image: `python:3.11-slim`
2. Working directory: `/app` (Runtime), `/build` (Builder)
3. Điểm khác biệt quan trọng nhất: Dùng Multi-stage build tách biệt quá trình cài đặt với quá trình chạy thực tế, giúp image nhẹ đi rất nhiều.

### Exercise 2.3: Image size comparison
- Develop: Khoảng 900+ MB
- Production: < 200 MB
- Difference: Giảm hơn 70%

## Part 3: Cloud Deployment

### Exercise 3.1: Railway deployment
- URL: https://quocanne-production-9358.up.railway.app
- Screenshot: Xem trong thư mục `screenshots/`

## Part 4: API Security

### Exercise 4.1-4.3: Test results
- Unauthorized request: Return `401 Unauthorized`.
- Authorized request: Return `200 OK`.
- Rate Limit check: Khi gọi quá 10 req/phút, trả về lỗi `429 Too Many Requests`.

### Exercise 4.4: Cost guard implementation
- Dùng một biến theo dõi mức tiêu thụ token ước tính của user. Nếu `daily_cost > daily_budget_usd`, API ném lỗi `402 Payment Required` (hoặc 503) từ chối phục vụ để tránh vượt quá giới hạn ngân sách.

## Part 5: Scaling & Reliability

### Exercise 5.1-5.5: Implementation notes
- **Liveness & Readiness probes**: Endpoint `/health` dùng để báo cáo platform biết container còn sống, `/ready` để xác nhận agent đã khởi tạo xong và sẵn sàng nhận traffic.
- **Graceful Shutdown**: Cấu hình `uvicorn` nhận tín hiệu `SIGTERM`, dừng nhận request mới nhưng chờ cho những request cũ được hoàn thành trước khi tắt.
- **Stateless Agent**: Chuyển lịch sử chat của user lưu trên RAM sang Redis, giúp mọi instance đều truy cập được chung dữ liệu.

---

### 2. Full Source Code - Lab 06 Complete (60 points)

Your final production-ready agent with all files:

```
your-repo/
├── app/
│   ├── main.py              # Main application
│   ├── config.py            # Configuration
│   ├── auth.py              # Authentication
│   ├── rate_limiter.py      # Rate limiting
│   └── cost_guard.py        # Cost protection
├── utils/
│   └── mock_llm.py          # Mock LLM (provided)
├── Dockerfile               # Multi-stage build
├── docker-compose.yml       # Full stack
├── requirements.txt         # Dependencies
├── .env.example             # Environment template
├── .dockerignore            # Docker ignore
├── railway.toml             # Railway config (or render.yaml)
└── README.md                # Setup instructions
```

**Requirements:**
-  All code runs without errors
-  Multi-stage Dockerfile (image < 500 MB)
-  API key authentication
-  Rate limiting (10 req/min)
-  Cost guard ($10/month)
-  Health + readiness checks
-  Graceful shutdown
-  Stateless design (Redis)
-  No hardcoded secrets

---

### 3. Service Domain Link

## Public URL
https://quocanne-production-9358.up.railway.app

## Platform
Railway

## Test Commands

### Health Check
```bash
curl https://quocanne-production-9358.up.railway.app/health
# Expected: {"status": "ok"}
```

### API Test (with authentication)
```bash
curl -X POST https://quocanne-production-9358.up.railway.app/ask \
  -H "X-API-Key: my-super-secret-key-2026" \
  -H "Content-Type: application/json" \
  -d '{"user_id": "test", "question": "Hello"}'
```

## Environment Variables Set
- ENVIRONMENT
- PORT (Tự động set bởi Railway)
- AGENT_API_KEY
- JWT_SECRET

## Screenshots
- [Deployment dashboard](screenshots/dashboard.png)
- [Service running](screenshots/running.png)
- [Test results](screenshots/test.png)

##  Pre-Submission Checklist

- [x] Repository is public (or instructor has access)
- [x] `MISSION_ANSWERS.md` completed with all exercises
- [x] `DEPLOYMENT.md` has working public URL
- [x] All source code in `app/` directory
- [x] `README.md` has clear setup instructions
- [x] No `.env` file committed (only `.env.example`)
- [x] No hardcoded secrets in code
- [x] Public URL is accessible and working
- [x] Screenshots included in `screenshots/` folder
- [x] Repository has clear commit history

---

##  Self-Test

Before submitting, verify your deployment:

```bash
# 1. Health check
curl https://your-app.railway.app/health

# 2. Authentication required
curl https://your-app.railway.app/ask
# Should return 401

# 3. With API key works
curl -H "X-API-Key: YOUR_KEY" https://your-app.railway.app/ask \
  -X POST -d '{"user_id":"test","question":"Hello"}'
# Should return 200

# 4. Rate limiting
for i in {1..15}; do 
  curl -H "X-API-Key: YOUR_KEY" https://your-app.railway.app/ask \
    -X POST -d '{"user_id":"test","question":"test"}'; 
done
# Should eventually return 429
```

---

##  Submission

**Submit your GitHub repository URL:**

```
https://github.com/Pancasel/day12_2A202600952_DoQuocAn
```

**Deadline:** 17/4/2026

---

##  Quick Tips

1.  Test your public URL from a different device
2.  Make sure repository is public or instructor has access
3.  Include screenshots of working deployment
4.  Write clear commit messages
5.  Test all commands in DEPLOYMENT.md work
6.  No secrets in code or commit history

---

##  Need Help?

- Check [TROUBLESHOOTING.md](TROUBLESHOOTING.md)
- Review [CODE_LAB.md](CODE_LAB.md)
- Ask in office hours
- Post in discussion forum

---

**Good luck! **
