# pfsense-firewall

* Cài đặt pfsense-firewall
    * Cài đặt 
    * Cấu hình pfsense và Vmware network
    * Cấu hình pfsense
*  Cấu hình luật
   * Cấu hình luật
   * Web proxy
   * VPN
##
## Cài đặt pfsense 
### 1. Cài đặt
Có thể tải từ trang Web chính thức của Netgate : [link] (https://www.pfsense.org/download/) 

Nếu muốn tải các bản pfsense cũ hơn có thể truy cập link sau [link](https://repo.ialab.dsu.edu/pfsense/)

Sau khi cài đặt về thành công sẽ tiến hành cài đặt trên máy ảo VM ware : 

Trên giao diện của trang chủ của VM chọn : 
* Create new virtual machine -> Chọn mục Typical -> Chọn file iso vừa mới tả -> nhấn next cho đến hết
<div align="center">
  <img src="https://github.com/Hungblyat/pfsense-firewall/blob/main/image/img.png">
</div>
* Sau khi cài đặt, máy ảo pfsense sẽ khởi động, chọn theo các bước sau trong hình :
<div align="center">
  <img src="https://github.com/Hungblyat/pfsense-firewall/blob/main/image/pfsense_1.jpg">
</div>

<div align="center">
  <img src="https://github.com/Hungblyat/pfsense-firewall/blob/main/image/pfsense_2.jpg">
</div>

<div align="center">
  <img src="https://github.com/Hungblyat/pfsense-firewall/blob/main/image/pfsense_3.jpg">
</div>
<div align="center">
  <img src="https://github.com/Hungblyat/pfsense-firewall/blob/main/image/pfsense_4.jpg">
</div>
<div align="center">
  <img src="https://github.com/Hungblyat/pfsense-firewall/blob/main/image/pfsense_5.jpg">
</div>
<div align="center">
  <img src="https://github.com/Hungblyat/pfsense-firewall/blob/main/image/pfsense_7.jpg">
</div>

### 2. Cấu hình pfsense và Vmware network
* Mô hình mạng
<div align="center">
  <img src="https://github.com/Hungblyat/pfsense-firewall/blob/main/image/Pfsense_topo.jpg">
</div>

* Cấu hình mạng VM ware
  
  Mạng LAN : tương ứng với Card mạng Host Only của VM ware
  
  Mạng WAN : tương ứng với Card mạng NAT của VM ware

  Quản lý các card mạng của VM ware ở mục Edit -> Virtual Network Editor
<div align="center">
  <img src="https://github.com/Hungblyat/pfsense-firewall/blob/main/image/pfsense_card.png" height="450">
</div>

  Thêm các card mạng Vmet1 và Vmet8 vào máy pfsense
  
![image](https://github.com/user-attachments/assets/f7ba9b87-f2b8-44e1-937a-82211b0c3b89)

* Cấu hình pfsense
   - Trong trang bắt đầu của màn hình máy pfsense . Chọn option 1. Assign Interfaces
   - ![image](https://github.com/user-attachments/assets/886a2f15-9a88-4c73-bc33-122786e67c8a)
   - em0 tương ứng với WAN , em1 tương ứng với LAN
   - Tiếp theo chọn 2. Set Interfaces Ip address
   - Chọn Card WAN : Configure Ipv4 via dhcp : no , Nhập ipv4 static : 192.168.184.129 , subnet mask(24), upstream gateway 192.168.184.2 (gateway của card Vmet8 ), IPv6: `No`, DHCP: `No`, reverting HTTP: `No`.
   - Tiếp theo cấu hình LAN: giống card WAN , nhưng ko điền mục upstream gateway


