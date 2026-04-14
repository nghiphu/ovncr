Các máy mục tiêu thường được cài đặtt trên máy Vmware sau đó chuyển vào Eve hay các hệ thống khác. 
Hướng dẫn khuyến nghị cách khởi tạo và chuyển đổi định dạng sang Eve.
## 1. Tạo máy ảo
Khuyến nghị: Chọn “Store virtual disk as a single file”
Lý do:
•	Dễ convert sang QCOW2 bằng qemu-img (chỉ 1 file .vmdk) 
•	Tránh lỗi khi thiếu file (vì dạng split sẽ có nhiều file .vmdk) 
•	Copy/upload lên EVE nhanh và đơn giản hơn 
•	Ít rủi ro sai đường dẫn khi xử lý 
Nếu chọn “Split into multiple files”:
•	Phải merge hoặc xử lý nhiều file trước khi convert 
•	Dễ lỗi khi convert (qemu-img không nhận đủ chain) 

<Ảnh 1>

Sau khi tạo xong:
•	Cài OS + dịch vụ cần thiết
•	Shutdown máy ảo
•	Lấy file .vmdk trong thư mục VM

<Ảnh 2>

# 2. Chuẩn bị công cụ
Trên máy chạy EVE (Ubuntu), đảm bảo đã có:
apt update 
apt install qemu-utils -y
# 3. Convert VMDK → QCOW2
Giả sử bạn có file disk.vmdk, chạy:
qemu-img convert -f vmdk -O qcow2 disk.vmdk disk.qcow2
Trong đó:
•	-f vmdk: định dạng đầu vào
•	-O qcow2: định dạng đầu ra
•	disk.qcow2: file dùng cho EVE
# 4. Đặt đúng thư mục trong EVE
Copy file .qcow2 vào đúng path:
/opt/unetlab/addons/qemu/<ten-image>/
Ví dụ:
mkdir -p /opt/unetlab/addons/qemu/cisco-test
mv disk.qcow2 /opt/unetlab/addons/qemu/cisco-test/
# 5. Rename chuẩn EVE
Tùy loại image:
•	Linux, appliance → virtioa.qcow2
•	Một số image đặc biệt → hda.qcow2
mv disk.qcow2 virtioa.qcow2
# 6. Fix permission
/opt/unetlab/wrappers/unl_wrapper -a fixpermissions
