# Quản lý tiến trình nền

## 1. Mục tiêu

- Chạy shell script dưới nền bằng `nohup` và `&`.
- Đảm bảo tiến trình tiếp tục chạy khi ngắt kết nối SSH.
- Kiểm tra tiến trình bằng `ps` và `pgrep`.
- Theo dõi file log bằng `tail`.
- Dừng tiến trình bằng `SIGTERM` và sử dụng `SIGKILL` khi cần thiết.

## 2. Tạo script

File: `loop-monitor.sh`

```bash
#!/bin/bash

while true; do
    echo "System time: $(date)" >> /tmp/monitor.log
    sleep 5
done
```

Cấp quyền thực thi:

```bash
chmod +x loop-monitor.sh
```

## 3. Khởi chạy tiến trình nền

Lệnh sử dụng:

```bash
nohup ./loop-monitor.sh > /dev/null 2>&1 &
```

Kết quả:

```text
[1] 12345
```

PID của tiến trình là `12345`.

## 4. Kiểm tra tiến trình

```bash
pgrep -f loop-monitor.sh
```

Kết quả:

```text
12345
```

Hoặc:

```bash
ps aux | grep loop-monitor.sh
```

## 5. Kiểm tra file log

```bash
tail -n 10 /tmp/monitor.log
```

Kết quả:

```text
System time: Tue Oct  6 18:28:01 +07 2026
System time: Tue Oct  6 18:28:06 +07 2026
System time: Tue Oct  6 18:28:11 +07 2026
System time: Tue Oct  6 18:28:16 +07 2026
```

Các bản ghi được tạo cách nhau khoảng 5 giây.

## 6. Dừng tiến trình

Sử dụng SIGTERM:

```bash
kill -15 12345
```

Kiểm tra:

```bash
pgrep -f loop-monitor.sh
```

Không còn PID được trả về, chứng tỏ tiến trình đã được dừng thành công.

Trong trường hợp SIGTERM không thể dừng tiến trình, có thể sử dụng SIGKILL:

```bash
kill -9 12345
```

## 7. Kết luận

Script `loop-monitor.sh` đã được chạy dưới nền bằng `nohup`, tiếp tục hoạt động độc lập với phiên Terminal/SSH và ghi thời gian hiện tại vào `/tmp/monitor.log` mỗi 5 giây.

Tiến trình được xác định bằng PID và dừng an toàn trước bằng `SIGTERM (15)`. `SIGKILL (9)` chỉ được sử dụng khi `SIGTERM` không thể kết thúc tiến trình.