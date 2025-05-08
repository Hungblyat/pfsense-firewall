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
## A.Cài đặt pfsense 
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

### VMWare network configurations:

<center>

| Network Card | Type | Subnet IP | Subnet mask | NAT Gateway |
| ------------ | ------- | ------| ----- | ----- |
| VMnet1 | Host-only | 192.168.31.0 | 255.255.255.0 | N/A | 
| VMnet8 | NAT | 192.168.184.0 | 255.255.255.0 | 192.168.184.2 | 

</center>

  Thêm các card mạng Vmet1 và Vmet8 vào máy pfsense
  
![image](https://github.com/user-attachments/assets/f7ba9b87-f2b8-44e1-937a-82211b0c3b89)

* Cấu hình pfsense
   - Trong trang bắt đầu của màn hình máy pfsense . Chọn option 1. Assign Interfaces
   - ![image](https://github.com/user-attachments/assets/886a2f15-9a88-4c73-bc33-122786e67c8a)
   - em0 tương ứng với WAN , em1 tương ứng với LAN
   - Tiếp theo chọn 2. Set Interfaces Ip address
   - Chọn Card WAN : Configure Ipv4 via dhcp : no , Nhập ipv4 static : 192.168.184.129 , subnet mask(24), upstream gateway 192.168.184.2 (gateway của card Vmet8 ), IPv6: `No`, DHCP: `No`, reverting HTTP: `No`.
   - Tiếp theo cấu hình LAN: giống card WAN , nhưng ko điền mục upstream gateway
   - Kết quả :
   - ![image](https://github.com/user-attachments/assets/ae27d646-8a29-4f37-832f-de879e221f31)

Truy cập vào trang Web của pfsense qua địa chỉ đã cấu hình http://192.168.31.2/ để cấu hình cơ bản cho pfSense với thông tin đăng nhập ban đầu là (user: `admin`; password `pfsense`). Các trường có thể cấu hình là :
- Hostname: `pfsense` mặc định hoặc chỉnh theo ý mình
- Domain: `home.arpa`
- Primary DNS Server: `8.8.8.8`
- Secondary DNS Server: `8.8.4.4`
- ![image](https://github.com/user-attachments/assets/17ea6fec-1c56-412a-8529-818f30b438bf)

- Timezone: `Asia/Ho Chi Minh` (Your timezone)
- In **Configure WAN interface**
  - Selected Type: `DHCP hoặc static`
  - Static IP Configuration: \<như cấu hình ở trên\>
  - RFC 1918 Private Network: bỏ các tích  **Block RFC1918 private networks** và **Block bogon networks**
- In **Configure LAN interface**: \<Giống với cấu hình LAN ở trên \>
- Có thể chọn đổi mật khẩu hoặc không điền để giữ nguyên mặc định
- Finished !

##

## B. Cấu hình luật
**1.Cấu hình các luật cơ bản**

`Các luật trong pfsense hoạt động theo mô hình xử lý các rule từ trên xuống => Tức là nó ưu tiên xử lý các luật ở trên hơn`
* Ngăn ping trong mạng nội bộ (192.168.31.0/24)

Vào mục Firewall -> Rules -> Chọn LAN -> Add

Trong phần cấu hình luật . Chọn các mục :

    Action : Block
    Interfaces : LAN
    Address Family : IPv4
    Protocol : ICMP
    Source : Single host or alias '192.168.31.10'
    Destination : This Firewall

![image](https://github.com/user-attachments/assets/e2a0f027-b89f-4330-9310-8177b8e0240e)

Kết quả : 

![image](https://github.com/user-attachments/assets/87822f15-8a2f-4965-b374-fce24a265d8e)

* Ngăn chặn truy cập các trang Web dùng cổng 80 (http)

Trong phần cấu hình luật . Chọn các mục :

    Action : Block
    Interfaces : LAN
    Address Family : IPv4
    Protocol : TCP
    Source : LAN net (tất cả các host của subnet 192.168.31.0/24)
    Destination : any
    Destination port : 80

Kết quả trước khi có luật : thử với trang http://httpforever.com/

![image](https://github.com/user-attachments/assets/7bf6a228-b1ac-4199-820d-c79c4c3ae65f)

Kết quả sau khi có luật 

![image](https://github.com/user-attachments/assets/daf3c4ae-3d76-4f7b-af30-ffaf5ca93fc8)

* Ngăn chặn truy cập ssh

Trong phần cấu hình luật . Chọn các mục :

    Action : Block
    Interfaces : LAN
    Address Family : IPv4
    Protocol : TCP
    Source : LAN net (tất cả các host của subnet 192.168.31.0/24)
    Destination : any
    Destination port : 22

Kết quả trước khi có luật (Vẫn có thể ssh đến 1 ssh server là 192.168.184.136 trên mạng WAN)

<img src="https://github.com/user-attachments/assets/8b23c352-0e89-4e9d-8e55-8db0bdbdaf0c" height="400">

Kết quả sau khi có luật :

![image](https://github.com/user-attachments/assets/8b648b04-53e3-4411-b60c-cc1077dc4e0a)

![image](https://github.com/user-attachments/assets/83e57f71-69b3-4b48-91bd-46ed118f580a)

**2.Phân chia người dùng qua alias**
* Ta sẽ chia người dùng thành 2 cấp độ :
    * Cấp 1 : Nhóm người dùng này có khả năng truy cập Internet và không giới hạn các trang Web
    * Cấp 2 : Nhóm người dùng này có khả năng truy cập Internet và nhưng bị giới hạn các trang Web truy cập như : facebook , chatgpt, qldt.ptit.edu.vn

- Chọn Mục Firewall -> Alias -> Add
![image](https://github.com/user-attachments/assets/2dd7a067-2a8e-4fef-9b87-88f3fb0ff769)

![image](https://github.com/user-attachments/assets/e6b059a7-cda1-460c-8c75-e0f6428099d9)

![image](https://github.com/user-attachments/assets/79854618-5f36-433b-8ab7-5ee93e89e27a)

Thêm alias 1 số Webpage dùng để chặn người dùng cấp 2 truy cập 

![image](https://github.com/user-attachments/assets/6a2d8191-abe8-413e-a8c6-46d304390a5a)

* Thử nghiêm :
- Với người dùng cấp 2 
![image](https://github.com/user-attachments/assets/7f48c073-90c8-4033-95e0-821bc0fddc52)

![image](https://github.com/user-attachments/assets/25ef3117-aa9e-4cea-8a84-6543036f6d5a)

=> Có thể thấy người dùng cấp 2 có ip là 192.168.31.20 ko thể kết nối tới những trang Web này nhưng vẫn có Internet nhé ở đây thử với Shoppe vẫn ok 
=> Lưu ý do facebook.com có nhiều ip nên đôi khi không ngăn chặn việc truy cập facebook nên ta nên liệt kê bằng cách chỉ rõ các ip của facebook thì sẽ chắc chắn chặn đươc hơn


