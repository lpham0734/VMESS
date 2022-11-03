# HTTP/2
- **HTTP / 2 là thế hệ tiếp theo của giao thức HTTP / 1.1 (mặc dù hầu hết các máy chủ HTTP vẫn chạy HTTP / 1.1).  Bạn có thể xem bản [demo](https://http2.akamai.com/demo), cho phép bạn trực tiếp thấy sự cải tiến của HTTP / 2 so với HTTP / 1.1 (không đảm bảo rằng HTTP / 2 của V2Ray có cùng hiệu suất).  Chúng tôi gọi giao thức HTTP / 2 là `h2` để đơn giản hóa.**
- **Nó thường được người dùng so sánh giữa giao thức HTTP / 2 hoặc WebSocket.  Về lý thuyết, khi HTTP / 2 được kết nối lần đầu tiên, không giống như WebSocket, nó cần phải hoàn thành yêu cầu nâng cấp.  Máy khách V2Ray và máy chủ thường giao tiếp trực tiếp và có ít proxy bậc trung hơn.  Tuy nhiên, trong kịch bản ứng dụng của CDN, Nginx / Caddy / Apache và các thành phần dịch vụ khác làm proxy phân tách trước, HTTP / 2 không linh hoạt như WebSocket.  Đối với nhiều proxy không cung cấp hỗ trợ back-end cho HTTP / 2 hoặc HTTP / 2 qua giao thức TCP.  Đối với việc sử dụng thực tế, sự khác biệt giữa WebSocket và HTTP / 2 có thể không rõ ràng.  Bạn có thể lựa chọn theo nhu cầu của bạn.**
# Configuration Example
- **Nó được cấu hình trong `streamSettings` giống như bất kỳ giao thức lớp truyền tải nào khác, nhưng lưu ý rằng việc sử dụng HTTP / 2 là bắt buộc để kích hoạt TLS.**
## Server-side Configuration
```json
{
  "inbounds": [
    {
      "port": 443,
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "b831381d-6324-4d53-ad4f-8cda48b30811",
            "alterId": 64
          }
        ]
      },
      "streamSettings": {
        "network": "h2", // h2 can write as http, they are the same
        "httpSettings": { // This term is setting of HTTP/2
          "path": "/ray"
        },
        "security": "tls", // Enable tls
        "tlsSettings": {
          "certificates": [
            {
              "certificateFile": "/etc/v2ray/v2ray.crt", // Certificate file, see in tls section
              "keyFile": "/etc/v2ray/v2ray.key" // Key file
            }
          ]
        }
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    }
  ]
}
```
## Client-side Configuration
```json
{
  "inbounds": [
    {
      "port": 1080,
      "listen": "127.0.0.1",
      "protocol": "socks",
      "sniffing": {
        "enabled": true,
        "destOverride": ["http", "tls"]
      },
      "settings": {
        "auth": "noauth",
        "udp": false
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "vmess",
      "settings": {
        "vnext": [
          {
            "address": "mydomain.me",
            "port": 443,
            "users": [
              {
                "id": "b831381d-6324-4d53-ad4f-8cda48b30811",
                "alterId": 64
              }
            ]
          }
        ]
      },
      "streamSettings": {
        "network": "h2",
        "httpSettings": { // This term is setting of HTTP/2 
          "path": "/ray"
        },
        "security": "tls"
      }
    }
  ]
}
```
###### Updates
- 2018-03-18 Initial Version
- 2018-08-30 Update
- 2018-11-17 Adapted for V4.0+
- 2019-7-11 New H2 description






 
