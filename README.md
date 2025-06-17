# Lịch Cắt Điện - Power Outage Tracker

Ứng dụng theo dõi lịch cắt điện tại các tỉnh thành Việt Nam.

## Tính năng

- Cập nhật tự động thông tin cắt điện từ các nguồn chính thức
- Tìm kiếm theo tỉnh/thành, quận/huyện, phường/xã
- Thông báo qua Telegram khi có lịch cắt điện mới
- Giao diện thân thiện, dễ sử dụng

## Yêu cầu hệ thống

- Node.js 14.x trở lên
- MongoDB 4.x trở lên
- Telegram Bot Token (cho tính năng thông báo)

## Cài đặt

1. Clone repository:
```bash
git clone <repository-url>
cd power-outage-tracker
```

2. Cài đặt dependencies:
```bash
npm install
```

3. Tạo file .env dựa trên file mẫu:
```bash
cp env.example .env
```

Sau đó chỉnh sửa file `.env` với thông tin thực tế của bạn:
```
PORT=3000
MONGODB_URI=mongodb://localhost:27017/power-outage-tracker
TELEGRAM_BOT_TOKEN=your_telegram_bot_token
TELEGRAM_CHAT_ID=your_chat_id
```

4. Khởi động ứng dụng:
```bash
npm start
```

## Cấu hình Telegram Bot

1. Tạo bot mới thông qua BotFather trên Telegram
2. Lấy token bot và thêm vào file .env
3. Lấy chat ID của bạn và thêm vào file .env

## Sử dụng

1. Truy cập http://localhost:3000 để xem trang chủ
2. Sử dụng form tìm kiếm để lọc thông tin theo khu vực
3. Đăng ký nhận thông báo qua Telegram

## Đóng góp

Mọi đóng góp đều được hoan nghênh. Vui lòng tạo issue hoặc pull request để đóng góp.

## Giấy phép

MIT License

## Hướng dẫn cài đặt & deploy trên VPS

### 1. Cài đặt Node.js

**Ubuntu/Debian:**
```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs
node -v
npm -v
```

### 2. Cài đặt MongoDB

**Ubuntu/Debian:**
```bash
# Cài đặt MongoDB Community Edition
wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add -
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu $(lsb_release -cs)/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
sudo apt-get update
sudo apt-get install -y mongodb-org
sudo systemctl start mongod
sudo systemctl enable mongod
```
- Kiểm tra MongoDB đã chạy: `systemctl status mongod`
- Mặc định MongoDB sẽ chạy ở cổng 27017.

### 3. Cấu hình biến môi trường

Sao chép file mẫu và cấu hình:
```bash
cp env.example .env
```

Chỉnh sửa file `.env` với nội dung:
```
TELEGRAM_BOT_TOKEN=your_telegram_bot_token
TELEGRAM_CHAT_ID=your_telegram_chat_id
MONGODB_URI=mongodb://localhost:27017/dienluc
NODE_ENV=production
PORT=3000
```
- Thay các giá trị bằng thông tin thực tế của bạn.

### 4. Cài đặt & chạy ứng dụng

```bash
# Clone code hoặc upload code lên VPS
cd <thư-mục-dự-án>
npm install

# Chạy thử
node app.js
# Hoặc dùng npm start nếu có script
npm start
```

#### (Khuyên dùng) Chạy nền với PM2:
```bash
npm install -g pm2
pm2 start app.js --name dienluc
pm2 save
pm2 startup
```

### 5. Cấu hình domain & nginx (reverse proxy)

**Cài nginx:**
```bash
sudo apt-get install nginx
```

**Cấu hình nginx:**
Tạo file `/etc/nginx/sites-available/dienluc` với nội dung:
```
server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

```bash
sudo ln -s /etc/nginx/sites-available/dienluc /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```
- Đổi `your-domain.com` thành domain của bạn.
- Trỏ domain về IP VPS.

### 6. Mở port tường lửa (nếu cần)
```bash
sudo ufw allow 80
sudo ufw allow 443
sudo ufw allow 3000
sudo ufw reload
```

---

## Liên hệ & hỗ trợ
- Nếu gặp lỗi, kiểm tra log bằng `pm2 logs dienluc` hoặc `node app.js`.
- Đảm bảo MongoDB và Node.js đều đang chạy.
- Nếu cần hỗ trợ thêm, hãy liên hệ admin. 