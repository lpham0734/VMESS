# Mux
- **Mux có nghĩa là ghép kênh.  Trong các công cụ proxy hiện tại, chỉ có V2Ray có tính năng này (2018-03-15 Lưu ý: có những phần mềm khác được triển khai tính năng tương tự).  Nó có thể kết hợp nhiều kết nối TCP thành một, tiết kiệm tài nguyên và cải thiện tính đồng thời.** 
- **Khán giả: Uh?  Cái quái gì thế?**
- **Vâng, hãy dịch sang tiếng Anh đơn giản:**
- **Ngày xưa, có một người tên là David.  Anh ấy là một tay đua xe đạp, một người điên và chơi đồ tự chế.  Vì vậy, anh có một ít tiền dư dả để mua sắm trực tuyến, và anh cũng thích mua phụ kiện để lắp ráp xe đạp.  Một lần anh ta lắp ráp một chiếc xe đạp, mua đồ trộm, găng tay và đồng hồ đo tốc độ tại một nhà đi xe đạp trực tuyến.  Anh ta mua thiết bị lái và hộp số ở cửa hàng đặc sản A, mua khung ở cửa hàng đặc biệt B, và mua phanh ở đại lý C.  Bàn đạp, đệm, bộ bánh xe, tay quay trong ...** 
```
Bốn ngày sau ...
Lúc 9 giờ, điện thoại di động của David đổ chuông. 
Đã kết nối, David:
Xin chào, xin chào.
Chuyển phát nhanh A: 
Xin chào, AA Express, đến nhận gói hàng.  
David vội vàng đến lấy người chuyển phát nhanh.  
Sau 20 phút, 
David: Xin chào, xin chào.  
Chuyển phát nhanh B: Xin chào, BB Express, đến đây để chuyển phát nhanh.  
David lại đi.  15 phút nữa, 
David: Xin chào, xin chào.  
Chuyển phát nhanh C: Xin chào, CC Express, hãy đến và nhận chuyển phát nhanh.  
David lại đi. Sau nửa giờ nữa, 
David: Chuyển phát nhanh là gì?  
Chuyển phát nhanh D: DD Express, nhanh lên.  
David nghĩ: chết tiệt!  
10 phút sau ...
```
- **Nếu bạn là David, bạn có thấy mệt mỏi không?  Máy tính cũng tương tự, nhưng công việc phải làm ít vô ích hơn nhiều:**
```
Trình duyệt: Tôi muốn xem hướng dẫn cấu hình V2Ray.  
Máy tính: OK, tôi bắt đầu kết nối TCP.  
Telegram: Tôi muốn học hỏi từ những ông lớn trong nhóm Telegram của V2Ray.  
Máy tính: OK, kết nối đã được khởi tạo.  
Trình duyệt: Tôi muốn xem hướng dẫn sử dụng cho V2Ray.  
Máy tính: OK.  
Trình duyệt: Tôi muốn tìm kiếm trên Google các hướng dẫn về V2Ray.  
Máy tính: OK.  
Trình duyệt: Tôi muốn ... 
```
- **Nếu kết nối Internet bình thường có thể sử dụng tương tự của ví dụ màu trắng ở trên, thì Mux của V2Ray là:**
- **Chris cũng lắp ráp xe đạp và mua phụ kiện trực tuyến, giống như David, nhưng anh ấy mua mọi thứ từ đại lý XX.**
```
Sau 4 ngày, Chris trả lời điện thoại: Xin chào.  
Chuyển phát nhanh: Xin chào SF Express, hãy đến nhận hàng chuyển phát nhanh.  
Nhân tiện, Chris mua một chai đồ uống: Anh bạn, thời tiết nóng quá, uống chút đi.  Hộp này nặng quá, bạn có thể giúp tôi chuyển nó đến nhà tôi được không?
```
- **Mux thực sự không thể tăng tốc độ mạng, nhưng nó hiệu quả hơn đối với các kết nối đồng thời, chẳng hạn như duyệt các trang web có nhiều hình ảnh và xem các chương trình phát sóng trực tiếp.  Từ góc độ hiệu quả sử dụng, Mux của V2Ray nên giống với TFO (TCP Fast Open) của Shadowsocks, vì mục đích của cả hai là giảm thời gian bắt tay, nhưng cách thực hiện lại khác nhau.  TFO chỉ có thể được mở bằng cách thiết lập nhân hệ thống và Mux được triển khai hoàn toàn ở cấp phần mềm.  V2Ray tốt hơn về tính dễ cấu hình.  (2018-09-19 Lưu ý: Không mất nhiều thời gian để cập nhật đoạn này, V2Ray đã thêm hỗ trợ TCP mở nhanh.)**
# Configuration
- **Mux chỉ cần được cấu hình trên máy khách, máy chủ sẽ tự động nhận dạng, do đó chỉ cần cấu hình của máy khách được cung cấp.  Tức là, chỉ cần thêm `"mux": {"enable": true}` vào outbound hoặc outboundDetour:** 
```json
{
  "inbounds": [
    {
      "port": 1080, // listening port
      "protocol": "socks", // the incoming protocol is SOCKS 5
      "sniffing": {
        "enabled": true,
        "destOverride": ["http", "tls"]
      },
      "settings": {
        "auth": "noauth" // no authenticated
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "vmess", // outcoming protocol
      "settings": {
        "vnext": [
          {
            "address": "serveraddr.com", // server address, please edit to your own server ip or domain name
            "port": 16823, // server port
            "users": [
              {
                "id": "b831381d-6324-4d53-ad4f-8cda48b30811", // The user ID must be the same as the server configuration
                "alterId": 64 // The value here should also be the same as the server
              }
            ]
          }
        ]
      },
      "mux": {"enabled": true}
    }
  ]
}
```
###### Update history
- 2018-08-30 Modify the layout and description
- 2018-11-17 Adaption for V4.0 + configuration

 





