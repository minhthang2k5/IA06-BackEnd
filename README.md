# Backend - User Registration API

NestJS API cho hệ thống đăng ký user với MongoDB.

---

## 🚀 Quick Start (Development)

### 1. Cài đặt dependencies
```bash
npm install
```

### 2. Cấu hình environment variables
```bash
# Copy file .env.example
copy .env.example .env

# Chỉnh sửa .env với thông tin thực tế
```

### 3. Chạy development server
```bash
npm run start:dev
```

Server sẽ chạy tại: `http://localhost:3000`

---

## 📦 Available Scripts

| Script | Mô tả |
|--------|-------|
| `npm run start:dev` | Chạy development server với hot-reload |
| `npm run build` | Build production code vào thư mục `dist/` |
| `npm start` | Chạy production server từ `dist/` |
| `npm run lint` | Kiểm tra code style với ESLint |
| `npm run format` | Format code với Prettier |

---

## 🌐 API Endpoints

### Health Check
```bash
GET /health
Response: { status: 'ok' }
```

### User Registration
```bash
POST /user/register
Content-Type: application/json

Body:
{
  "email": "user@example.com",
  "password": "YourPassword123"
}

Success Response (201):
{
  "message": "User registered successfully",
  "data": {
    "id": "507f1f77bcf86cd799439011",
    "email": "user@example.com",
    "createdAt": "2025-12-03T10:30:00.000Z"
  }
}

Error Responses:
- 400 Bad Request: Validation errors
- 409 Conflict: Email already exists
```

---

## 🔧 Environment Variables

Tạo file `.env` trong thư mục `backend/` với các biến sau:

```env
# Port cho server (development: 3000, production Render: 10000)
PORT=3000

# MongoDB Connection String
# Development (local):
MONGODB_URI=mongodb://127.0.0.1:27017/userdb

# Production (MongoDB Atlas):
# MONGODB_URI=mongodb+srv://appuser:password@cluster.mongodb.net/userdb?retryWrites=true&w=majority

# CORS - Frontend URLs được phép truy cập
CLIENT_URL=http://localhost:5173

# Node environment
NODE_ENV=development
```

---

## 🚢 Deploy lên Render.com

### Bước 1: Chuẩn bị Code

**✅ Đã có sẵn:**
- `package.json` với scripts `build` và `start`
- `.env.example` template
- `.gitignore` để không commit `.env`

**📋 Checklist trước khi deploy:**
- [ ] Code đã push lên GitHub/GitLab
- [ ] Đã có MongoDB Atlas connection string
- [ ] File `.env` KHÔNG được commit (check `.gitignore`)

### Bước 2: Tạo Web Service trên Render

1. **Đăng nhập Render.com**
   - Truy cập: https://render.com
   - Đăng ký/Đăng nhập bằng GitHub

2. **Tạo Web Service mới**
   - Click **Dashboard** → **New +** → **Web Service**
   - Chọn repository chứa code này
   - Click **Connect**

3. **Cấu hình Service**
   
   **Basic Settings:**
   - **Name**: `user-registration-api` (hoặc tên bạn muốn)
   - **Region**: Singapore (gần Việt Nam nhất)
   - **Branch**: `main` (hoặc branch bạn muốn deploy)
   - **Root Directory**: `backend`
   - **Runtime**: `Node`

   **Build & Deploy:**
   - **Build Command**: 
     ```bash
     npm install && npm run build
     ```
   - **Start Command**: 
     ```bash
     npm start
     ```

   **Instance Type:**
   - **Plan**: `Free` (hoặc upgrade nếu cần)

4. **Environment Variables**
   
   Click **Advanced** → **Add Environment Variable**:

   | Key | Value | Ghi chú |
   |-----|-------|---------|
   | `PORT` | `10000` | Render yêu cầu port này |
   | `MONGODB_URI` | `mongodb+srv://appuser:yourpassword@cluster.mongodb.net/userdb?retryWrites=true&w=majority` | Từ MongoDB Atlas |
   | `CLIENT_URL` | `https://your-frontend.vercel.app` | Cập nhật sau khi deploy frontend |
   | `NODE_ENV` | `production` | Environment |

   **⚠️ LƯU Ý:**
   - Thay `yourpassword` bằng password thực tế của MongoDB user
   - Thay `cluster` bằng tên cluster thực tế
   - `CLIENT_URL` cập nhật sau khi có URL frontend

5. **Deploy**
   - Click **Create Web Service**
   - Render sẽ tự động:
     - Clone code từ GitHub
     - Chạy `npm install && npm run build`
     - Chạy `npm start`
   - Chờ 5-10 phút

6. **Lấy URL Backend**
   
   Sau khi deploy thành công, bạn sẽ có URL:
   ```
   https://user-registration-api.onrender.com
   ```

### Bước 3: Kiểm Tra Deploy

**Test Health Endpoint:**
```bash
curl https://user-registration-api.onrender.com/health
```

Expected response:
```json
{"status":"ok"}
```

**Test Registration Endpoint:**
```bash
curl -X POST https://user-registration-api.onrender.com/user/register \
  -H "Content-Type: application/json" \
  -d "{\"email\":\"test@example.com\",\"password\":\"Test123456\"}"
```

Expected response (201):
```json
{
  "message": "User registered successfully",
  "data": {
    "id": "...",
    "email": "test@example.com",
    "createdAt": "..."
  }
}
```

### Bước 4: Cập nhật CORS sau khi deploy Frontend

Sau khi deploy frontend lên Vercel, cập nhật `CLIENT_URL`:

1. Vào Render Dashboard → Service → **Environment**
2. Edit `CLIENT_URL`:
   ```
   https://your-app.vercel.app
   ```
3. Click **Save Changes**
4. Service sẽ tự động redeploy

---

## 🔍 Xem Logs

**Render Dashboard:**
1. Vào service của bạn
2. Tab **Logs**
3. Xem realtime logs để debug

**Các logs quan trọng:**
- ✅ `🚀 Application is running on: http://localhost:10000`
- ✅ `Successfully connected to MongoDB`
- ❌ `MongooseServerSelectionError` → Check MONGODB_URI
- ❌ `CORS error` → Check CLIENT_URL

---

## 🐛 Troubleshooting

### Lỗi: "Module not found"
**Nguyên nhân:** Dependencies không được cài đặt

**Giải pháp:**
```bash
# Xóa node_modules và reinstall
rm -rf node_modules package-lock.json
npm install
```

### Lỗi: "Cannot connect to MongoDB"
**Nguyên nhân:** Connection string sai hoặc Network Access chưa cấu hình

**Giải pháp:**
1. Kiểm tra MongoDB Atlas → Network Access
2. Thêm IP: `0.0.0.0/0` (allow all)
3. Kiểm tra username/password trong connection string
4. Test connection locally:
   ```bash
   mongosh "mongodb+srv://user:pass@cluster.mongodb.net/userdb"
   ```

### Lỗi: "Build failed"
**Nguyên nhân:** TypeScript compilation errors

**Giải pháp:**
```bash
# Chạy build locally để xem lỗi chi tiết
npm run build
```

### Lỗi: CORS blocked
**Nguyên nhân:** `CLIENT_URL` không khớp với frontend URL

**Giải pháp:**
- Đảm bảo `CLIENT_URL` không có trailing slash
- Ví dụ: ✅ `https://app.vercel.app` ❌ `https://app.vercel.app/`

---

## 📊 Render Free Tier Limitations

- **Sleep after inactivity**: Service ngủ sau 15 phút không dùng
- **First request slow**: Request đầu tiên mất ~30-60s (wake up time)
- **750 hours/month**: Đủ cho 1 tháng nếu chỉ chạy 1 service

**Giải pháp cho sleep:**
- Dùng [UptimeRobot](https://uptimerobot.com) ping mỗi 10 phút
- Hoặc upgrade lên Paid Plan ($7/month) → không sleep

---

## 🔐 Security Best Practices

- ✅ `.env` file KHÔNG được commit vào Git
- ✅ Dùng strong password cho MongoDB
- ✅ Enable CORS chỉ cho trusted domains
- ✅ Passwords được hash với bcryptjs
- ✅ Input validation với class-validator
- ⚠️ Nên thêm rate limiting cho production

---

## 📚 Tech Stack

- **Framework**: NestJS 10.x
- **Database**: MongoDB + Mongoose
- **Validation**: class-validator, class-transformer
- **Security**: bcryptjs (password hashing)
- **Config**: @nestjs/config (environment variables)

---

## 🆘 Support

**Nếu gặp vấn đề:**
1. Kiểm tra logs trên Render Dashboard
2. Test locally với production environment variables
3. Verify MongoDB connection string
4. Check CORS configuration

**Useful Commands:**
```bash
# Test locally với production settings
NODE_ENV=production PORT=10000 npm start

# Check if MongoDB is accessible
mongosh "your-connection-string"

# Test API endpoints
curl -X POST http://localhost:3000/user/register \
  -H "Content-Type: application/json" \
  -d '{"email":"test@test.com","password":"Password123"}'
```

---

## 📝 Notes

- Backend này được thiết kế để deploy trên Render.com
- Tương thích với MongoDB Atlas free tier (M0)
- CORS được cấu hình động qua environment variable
- Port được config qua env (development: 3000, production: 10000)

Good luck! 🚀
