# TÀI LIỆU GIỚI THIỆU: THAO TRƯỜNG IOT HYBRID OVNCR
## 1. Giới thiệu tổng quan về thao trường
Thao trường IoT Hybrid OVNCR là một nền tảng diễn tập an toàn thông tin không gian mạng tiên tiến, được thiết kế dựa trên mô hình **kết hợp (Hybrid)** giữa môi trường mạng ảo hóa truyền thống và hệ thống thiết bị vật lý thực tế (Smart Home/SOHO).
Mục tiêu cốt lõi của hệ thống là xóa nhòa ranh giới giữa các cuộc tấn công mạng thuần túy và hệ quả vật lý. Thay vì chỉ chiếm quyền máy chủ hay đánh cắp dữ liệu trên màn hình, các chuyên gia an ninh mạng (Red Team) và lực lượng phòng thủ (Blue Team) sẽ được trải nghiệm các kịch bản thực chiến: nơi một dòng lệnh trên không gian ảo có thể trực tiếp làm tắt/bật đèn, mở khóa cửa, hoặc can thiệp vào camera an ninh ngay trong phòng làm việc thực tế. Đây là mô hình kiểm thử rủi ro IoT toàn diện, đáp ứng nhu cầu đánh giá bảo mật trong kỷ nguyên vạn vật kết nối.
## 2. Phân tích các thành phần trong thao trường
Kiến trúc hệ thống được chia thành ba phân hệ chính, hoạt động đồng bộ để tạo thành một bề mặt tấn công đa dạng:
### 2.1. Phân hệ Ảo hóa (Virtual Environment Zone)
Được triển khai trên nền tảng OVNCR, đóng vai trò tạo ra các môi trường làm việc giả lập và cung cấp các "mồi nhử" (Honeypot) chứa lỗ hổng bảo mật:
 * **Máy trạm người dùng (Windows/Linux VMs):** Đóng vai trò là máy tính cá nhân của chủ nhà/nhân viên, chứa các tài liệu giả lập. Đây thường là điểm bắt đầu (Initial Access) qua các hình thức lừa đảo (Phishing).
 * **Smart TV giả lập (Android-x86):** Mô phỏng TV thông minh với giao diện đầy đủ, chứa lỗ hổng ADB mở mặc định hoặc các ứng dụng bên thứ ba thiếu bản vá.
 * **Điện thoại Android giả lập (Legacy OS):** Mô phỏng các thiết bị di động đời cũ, không còn được hỗ trợ cập nhật bảo mật, dễ bị khai thác qua mạng.
 * **Camera IP giả lập:** Các node camera ảo hóa (chạy firmware cũ) sử dụng mật khẩu yếu hoặc mở các cổng stream video (RTSP) không xác thực để tạo thêm mục tiêu phong phú.
### 2.2. Phân hệ Vật lý (Physical Smart Home Zone)
Là một căn phòng làm việc thông minh thực tế, chịu tác động trực tiếp từ các cuộc tấn công:
 * **Công tắc thông minh (Tuya Wi-Fi):** Điều khiển hệ thống chiếu sáng, rèm cửa, ổ cắm.
 * **Hub Hồng ngoại (IR Hub):** "Cầu nối" điều khiển các thiết bị ngoại vi không có kết nối mạng (như máy lạnh, tivi vật lý cũ, quạt).
 * **Hệ thống An ninh vật lý:** Khóa cửa thông minh (Smart Door Lock) và Camera IP thực tế.
 * **Thiết bị cá nhân (BYOD):** Đồng hồ thông minh (Smartwatch), điện thoại Android và iPhone vật lý kết nối vào mạng LAN để làm phong phú dữ liệu mạng (Network traffic) và mô phỏng sự hiện diện của con người.
### 2.3. Phân hệ Mạng cốt lõi & Cửa ngõ kết nối (Core Network & Gateway)
 * **Bộ phát Wi-Fi (Physical Router):** Trái tim của phòng vật lý, cấp phát IP và định tuyến nội bộ.
 * **Raspberry Pi Gateway:** Thiết bị nhúng đóng vai trò là cầu nối VPN, giúp liên thông hai thế giới ảo và thực.
## 3. Phân tích cơ chế kết nối thực - ảo (Hybrid Connectivity)
Điểm đột phá của thao trường OVNCR là việc tích hợp giải pháp **Tailscale Gateway (định tuyến mạng Subnet Routing)** cài đặt trên Raspberry Pi. Cơ chế hoạt động diễn ra như sau:
 1. **Thiết lập Đường hầm (Encrypted Tunnel):** Các máy ảo trên thao trường OVNCR và Raspberry Pi tại phòng vật lý cùng được cài đặt Tailscale Client, tạo ra một mạng riêng ảo được mã hóa (VPN) qua Internet (hoặc mạng Intranet dùng chung).
 2. **Đồng bộ dải mạng (Subnet Routing):** Raspberry Pi được cấu hình với cờ lệnh --advertise-routes, có nhiệm vụ "phát thanh" dải địa chỉ IP nội bộ của phòng vật lý (ví dụ: 192.168.1.0/24) vào mạng lưới ảo hóa.
 3. **Hợp nhất không gian mạng:** Nhờ cơ chế này, một máy ảo tấn công (Kali Linux) nằm trên hệ thống OVNCR có thể ping, rà quét (Nmap) và giao tiếp trực tiếp với IP của chiếc Công tắc Tuya hay Camera đang đặt tại phòng vật lý mà không cần thông qua các cơ chế NAT port phức tạp. Mạng lưới được làm phẳng hoàn toàn, mang lại cảm giác chân thực như Hacker đang cắm cáp trực tiếp vào Switch của căn phòng.
## 4. Đề xuất 10 kịch bản tấn công, khai thác phổ biến
Dưới đây là 10 kịch bản tấn công theo trình tự chuỗi tiêu diệt mạng (Kill Chain), từ xâm nhập ban đầu đến tác động vật lý:
 1. **Phishing & Initial Access trên máy ảo Windows:** Kẻ tấn công gửi email lừa đảo chứa mã độc vào máy ảo Windows. Khi người dùng (ảo) mở file, mã độc thực thi, thiết lập kết nối C&C (Command & Control) và cấp cho hacker một chỗ đứng ban đầu trong mạng lưới.
 2. **Khai thác lỗ hổng ADB trên Smart TV giả lập:** Quét mạng nội bộ, phát hiện Smart TV giả lập đang mở cổng 5555 (ADB) không xác thực. Hacker kết nối, thả (drop) shell độc hại để biến TV thành máy trạm trung gian (Pivot) sâu hơn vào hệ thống.
 3. **Rò rỉ hình ảnh từ Camera IP (Thực & Ảo):** Quét các cổng 554 (RTSP) hoặc 80 (HTTP) trên các Camera. Khai thác lỗi mật khẩu mặc định (admin/admin) hoặc lỗi bypass xác thực để xem trộm luồng video trực tiếp của phòng thao trường.
 4. **Tấn công đánh chặn (Man-in-the-Middle - MitM) trong mạng LAN:** Sử dụng máy trung gian (như điện thoại Android cũ bị hack) để thực hiện ARP Spoofing. Bắt các gói tin HTTP/UDP trao đổi giữa Hub Hồng ngoại và ứng dụng điều khiển để phân tích cấu trúc lệnh.
 5. **Tấn công phát lại (Replay Attack) vào Hub Hồng ngoại:** Sau khi bắt được gói tin lệnh bật/tắt máy lạnh, hacker dùng script tự động phát lại (replay) gói tin này liên tục vào địa chỉ IP của IR Hub, khiến máy lạnh trong phòng thực thay đổi nhiệt độ liên tục gây gián đoạn môi trường làm việc.
 6. **Khai thác API cục bộ (Local API) của công tắc Tuya:** Bypass quá trình giao tiếp qua Cloud, gửi trực tiếp các payload JSON đã được chế tạo (crafted) vào cổng mạng nội bộ của công tắc Tuya Wi-Fi để thao túng việc bật/tắt đèn phòng vật lý.
 7. **Từ chối dịch vụ (DoS/UDP Flood) nhắm vào Router:** Dùng các máy ảo trong mạng tạo ra lượng lớn lưu lượng rác (UDP/SYN Flood) bắn vào Router vật lý. Hậu quả: Toàn bộ thiết bị Smart Home (Tuya, Camera) mất kết nối Cloud và tê liệt hoàn toàn, người dùng không thể điều khiển bằng điện thoại.
 8. **Vét cạn (Brute-force) mã PIN Khóa cửa thông minh:** Xác định địa chỉ IP hoặc cổng giao tiếp Bluetooth (thông qua Gateway) của hệ thống khóa cửa. Sử dụng từ điển mã PIN phổ biến để dò mật khẩu, nhằm kích hoạt chốt mở cửa vật lý.
 9. **Leo thang đặc quyền trên thiết bị di động đời cũ:** Nhắm mục tiêu vào các điện thoại Android/iPhone đời cũ (thiết bị BYOD) đang kết nối chung mạng Wi-Fi. Khai thác các lỗ hổng hệ điều hành cũ đã biết (CVEs) để chiếm quyền điều khiển, đọc lén tin nhắn OTP hoặc dữ liệu cá nhân giả lập.
 10. **Tấn công Ransomware vào Home Server (Máy ảo Linux):** Từ các bước di chuyển ngang, hacker xâm nhập thành công vào máy chủ Linux đóng vai trò lưu trữ dữ liệu (NAS). Triển khai mã hóa toàn bộ tài liệu cấu hình, camera log và yêu cầu tiền chuộc, gây sức ép toàn diện lên kịch bản diễn tập.
