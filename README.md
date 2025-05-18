# Project: Intra-Domain Routing Algorithms - DISTANCE VECTOR


## Giới thiệu

Đây là dự án mô phỏng thuật toán định tuyến sử dụng Distance Vector được xây dựng bằng Python. Mỗi router (self) là một node trong mạng, có các thuộc tính lưu trữ thông tin: `distance` (chi phí tốt nhất để đi tới router khác), `forwarding-table` (bảng lưu tuyến đường tốt nhất tới router khác), `neighbors` (các router có kết nối trực tiếp). Định kỳ hoặc khi có sự thay đổi, router sẽ gửi vector của nó tới hàng xóm. Giao thức này sử dụng thuật toán ***Bellman-Ford*** để cập nhật bảng định tuyến dựa trên thông tin từ hàng xóm gửi tới.

## Mô tả chi tiết:

Dự án này là bài tập thực hành cuối kì môn học Mạng máy tính tại Trường Đại học Công nghệ - ĐHQGHN, yêu cầu sinh viên cài đặt lớp `DVrouter` kế thừa từ lớp `Router`, xử lý các chức năng sau:

* Tự động cập nhật bảng định tuyến khi có sự thay đổi từ hàng xóm: `handle_packet`.
* Xử lý thêm và xóa liên kết: `handle_new_link`, `handle_remove_link`.
* Gửi vector định tuyến định kỳ: `handle_time`.

## Distance-Vector Routing

### Nguyên lý hoạt động:

* Mỗi router duy trì vector khoảng cách riêng, ghi lại chi phí tốt nhất đến mọi đích.
* Router cập nhật vector khi nhận thông tin từ hàng xóm, dựa theo công thức:
  ```
  cost_to_dest = min { cost_to_dest, cost_to_neighbor + neighbor.cost_to_dest }
  ```
* Khi vector có sự thay đổi, router sẽ gửi lại vector của nó đến các hàng xóm.
* Định kỳ, ngay cả khi không thay đổi, router vẫn broadcast lại để đảm bảo đồng bộ và giữ liên kết sống.
* Các router không phát vectơ khoảng cách đã nhận đến các hàng xóm của mình. Nó chỉ phát vectơ khoảng cách của bản thân đến các hàng xóm của mình.

### Quy trình: 

* Khởi tạo: Router khởi tạo bảng khoảng cách, khoảng cách đến chính nó bằng 0, các đích khác là vô hạn.
* Gửi cập nhật: Router gửi bảng khoảng cách hiện tại đến neighbors theo chu kỳ heartbeat.
* Xử lý gói tin nhận được:
    * Router nhận bảng khoảng cách từ các láng giềng.
    * Dựa trên bảng khoảng cách nhận được, router tính toán lại chi phí đến các đích, cập nhật bảng định tuyến nếu có đường đi tốt hơn.
    * Nếu có thay đổi bảng định tuyến, router gửi thông tin cập nhật đến các láng giềng.
* Xử lý thay đổi liên kết: Khi có thêm hoặc mất đi một liên kết mạng, router cập nhật bảng và thông báo cho neighbors.
      
## Python code implementation:

### Thuộc tính:

* `distance (dict[str, float])`: chi phí tốt nhất để đi tới router khác.
* `forwarding-table (dict[str, int])`: bảng lưu tuyến đường tốt nhất tới router khác.
* `neighbors (dict[int, tuple[str, float]])`: các router có kết nối trực tiếp với router hiện tại.
* `heartbeat_time`: thời gian cố định định kì sẽ gửi cập nhật tới hàng xóm.
* `last_time`: thời gian lần cuối gửi cập nhật.

### Phương thức:

* `__init__(addr, heartbeat_time)`: Khởi tạo router với địa chỉ addr và thời gian heartbeat. Khởi tạo các bảng distance, forwarding_table, neighbors.
* `broadcast()`: Định kỳ hoặc khi có thay đổi, gửi bảng distance vector hiện tại đến tất cả neighbor.
* `handle_packet(port, packet)`: Xử lý gói tin đến từ cổng port.
    * Nếu là gói dữ liệu thông thường (traceroute), chuyển tiếp dựa trên bảng định tuyến.
    * Nếu là gói routing (bảng khoảng cách từ neighbor), cập nhật lại bảng khoảng cách và bảng định tuyến nếu phát hiện đường đi tốt hơn, sau đó phát lại cập nhật.
* `handle_new_link(port, endpoint, cost)`: Xử lý khi có liên kết mới. Cập nhật thông tin neighbor, bảng khoảng cách, bảng định tuyến nếu cần thiết và phát lại cập nhật .
* `handle_remove_link(port)`: Xử lý khi một liên kết bị ngắt. Xóa thông tin liên quan đến liên kết, cập nhật lại bảng, phát lại cập nhật nếu cần.
* `handle_time(time_ms)`: Xử lý thời gian hiện tại. Nếu đã đến thời điểm gửi heartbeat, gọi broadcast() để gửi bảng khoảng cách định kỳ.
* `__repr__()`: Hàm hiển thị đối tượng cho mục đích debug, giúp quan sát trạng thái router dễ dàng.
  
## Running and Testing

Kiểm tra hoạt động của `DVrouter` bằng mô phỏng mạng được cung cấp sẵn trong các file JSON.
Chạy lệnh sau trong terminal: 

Với mô phỏng trực quan: 
```
python visualize_network.py <net_json_path> DV
```

Với mô phỏng trong command line:
```
python network.py <net_json_path> DV
```

## Tác giả
Dự án gồm sự đóng góp của hai sinh viên:
* Nguyễn Phương Linh - 23021609
* Đào Minh Đức - 23020598
