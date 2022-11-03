# mKCP
- **V2Ray đã giới thiệu giao thức truyền tải [KCP](https://github.com/skywind3000/kcp) và thực hiện một số tối ưu hóa khác nhau được gọi là KCP sửa đổi (mKCP).  Nếu bạn thấy rằng môi trường mạng của mình bị mất nhiều gói, hãy cân nhắc sử dụng mKCP.  Do cơ chế truyền lại nhanh, mKCP có lợi thế lớn hơn TCP thông thường trong mạng có môi trường mất gói cao.  Do đó, mKCP tiêu thụ nhiều lưu lượng hơn TCP, vì vậy hãy sử dụng nó một cách cẩn thận.  Một điều cần hiểu là mKCP là giao thức KCP giống như KCPTUN, nhưng cả hai không tương thích.**
- **Tôi muốn sửa lại một khái niệm ở đây.  Chỉ cần bạn đề cập đến KCP hoặc UDP, bạn sẽ luôn nói "dễ dàng trở thành QoS", nhưng QoS là một cụm danh từ.  Nó là một lối tắt cho Chất lượng dịch vụ.  Hãy tưởng tượng rằng bạn đã nói điều gì đó với tôi, "mạng của tôi đang được bảo dưỡng trở lại."  Thứ hai, ngay cả khi danh từ có thể được động từ, nó không thích hợp để sử dụng.  Vì QoS phân biệt mức độ ưu tiên của giao thông mạng, nó giống như việc phân chia vỉa hè, làn đường không dành cho xe cơ giới, làn đường nhanh và làn đường chậm trên đường, ngay cả khi bạn có mức độ ưu tiên cao nhất của dịch vụ internet, đó là làn đường nhanh trong  làn nhanh.  Đây cũng là kết quả của QoS.**
# Configuration Example
- **Cấu hình của mKCP tương đối đơn giản.  Chỉ cần thêm một StreamSettings vào phần đầu vào của máy chủ và phần đầu ra của máy khách và đặt nó thành mkcp.**
## Server-side Configuration
```json
{
  "inbounds": [
    {
      "port": 16823,
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
        "network": "mkcp", // Alternatively, you can write kcp, they are the same
        "kcpSettings": {
          "uplinkCapacity": 5,
          "downlinkCapacity": 100,
          "congestion": true,
          "header": {
            "type": "none"
          }
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
}{
  "inbounds": [
    {
      "port": 16823,
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
        "network": "mkcp", // Alternatively, you can write kcp, they are the same
        "kcpSettings": {
          "uplinkCapacity": 5,
          "downlinkCapacity": 100,
          "congestion": true,
          "header": {
            "type": "none"
          }
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
      "protocol": "socks",
      "sniffing": {
        "enabled": true,
        "destOverride": ["http", "tls"]
    },
      "settings": {
        "auth": "noauth"
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "vmess",
      "settings": {
        "vnext": [
          {
            "address": "serveraddr.com",
            "port": 16823,
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
        "network": "mkcp",
        "kcpSettings": {
          "uplinkCapacity": 5,
          "downlinkCapacity": 100,
          "congestion": true,
          "header": {
            "type": "none"
          }
        }
      }
    }
  ]
}
```
## Explanation
- **Trong cấu hình trên, thay đổi chính so với trước là có thêm một streamSettings, chứa nhiều tham số:**
- *`network`: Lựa chọn mạng, như cấu hình ở trên được viết là kcp hoặc mkcp sẽ bật mKCP*
- *`kcpSettings`: chứa một số thông số về cài đặt mKCP, có*
> `uplinkCapacity`: Dung lượng đường lên, xác định tốc độ V2Ray gửi gói tin.  Đơn vị là megabyte (MB)

> `downlinkCapacity`: Dung lượng đường xuống sẽ xác định tốc độ V2Ray nhận các gói.  Đơn vị cũng là MB

> `header`：Làm xáo trộn các gói tin

> `type`: Loại rối loạn

- **Đường lên của máy khách là đường xuống của máy chủ.  Tương tự, đường xuống của máy khách là đường lên của máy chủ.  Trong cài đặt mKCP, cả máy chủ và máy khách đều có uplinkCapacity và downlinkCapacity, do đó, tỷ lệ tải lên của máy khách được xác định bởi LinkCapacity của máy chủ và uplinkCapacity của máy khách.  Nó được quyết định rằng tỷ lệ tải xuống của khách hàng là như nhau.  Do đó, chúng tôi khuyên bạn nên đặt DownlinkCapacity của máy chủ và máy khách thành một giá trị lớn, sau đó sửa đổi UplinkCapacity của cả hai đầu để điều chỉnh tỷ lệ đường lên và đường xuống.**
- **Ngoài ra còn có một tham số tiêu đề có thể ngụy trang mKCP, đây là một lợi thế của mKCP.  Loại giả mạo cụ thể được đặt trong tham số loại, loại có thể được đặt thành utp, srtp, wechat-video, dtls, wireguard hoặc không có, tương ứng ngụy trang dữ liệu mKCP thành tải xuống BT, cuộc gọi video, cuộc gọi video WeChat, dtls, wireguard  (Một loại VPN mới) và không có ngụy trang. `Tham số kiểu ở đây giống với máy khách và máy chủ.  Cũng nên nhớ luôn nhớ rằng ngụy trang chỉ là ngụy trang`.** 
- **Còn các thông số trong cấu hình trên mà mình chưa giải thích thì nó là giá trị mặc định của V2Ray.  Khuyến nghị cá nhân của tôi là giữ nguyên giá trị mặc định.  Nếu bạn cần hiểu hoặc sửa đổi, vui lòng tham khảo sách hướng dẫn.**
###### Updates
- 2018-03-17 Update
- 2018-08-30 Update
- 2018-11-17 Adapted for V4.0+


 



