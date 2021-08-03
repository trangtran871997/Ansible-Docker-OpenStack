## Các action có thể thực hiện với VM trên Horizon:

[action_1.png]

### 1. Associate Floating IP

- Khi 1 VM được tạo ra tronng OpenStack, nó sẽ được gán 1 địa chỉ IP cố định trong dải mạng được chỉ định, địa chỉ IP tồn tại đến khi VM bị xóa (IP DCN)
- Floating IP là địa chỉ nổi, nói chung là địa chỉ thứ hai để truy cập vào VM từ vùng mạng ngoài, địa chỉ này có thể thay đổi được.

[action_2.png]

### 2. Attach & Detach Interface

- Thêm 1 Interface mới cho VM: dải public, dải mạng di động (MPBN) tương tự IP DCN

[attach_interface.png]

- Interface detach từ VM sẽ được xóa từ database port Neutron.

[detach_interface.png]


### 3. Attach & Detach Volume

- Hành động attach volume: khi muốn gán thêm phân vùng mới, hoặc mở rộng phân vùng cũ

[AttachVolumes.png]

- Hành động detach volume: khi muốn unmount phân vùng cũ đi, không sử dụng nữa phục vụ mục đích thu hồi tài nguyên, hoặc detach để extend volume (nâng dung lượng volume)

[DetachVolumes.png]


### 4. Edit Instance

- Có thể sửa tên VM, Security Group, port Security Group của VM đó


[edit_vm1.png] 

- Security group:

[edit_vm2.png]

+ Là một tập hợp các traffic rule được áp dụng cho các VM trong 1 project và kiểm soát truy cập giưuã các VM và giưuã các VM với tài nguyên mạng bên ngoài OpenStack.

+ Một security khi được gán cho interface(IP DCN, IP public) sẽ được dịch sang iptable rules.

+ Mặc định, 1 security group: default được gán cho tất cả các instance, default group chặn tất cả các traffic tới VM và chỉ cho traffic từ VM gửi đi.


[security_group1.png]

- Edit port securrity group : chỉ áp dụng cho port đó.

### 5. Console
- Cho phép truy cập vào VM thông qua Terminal

[console.png]

### 6. View Log

- Xem log của VM từ khi Click `Launch Instance`: xem được VM boot như nào, log network, cloud-init, ...

[viewlog.png]


### 7. Rescue, Pause, Suppend, Shelve Instance

- Rescue Instance cung cấp cơ chế truy cập ngay cả khi Image render của Instance không thể truy cập được.

- Pause Instance:

+ Khi pause 1 VM, toàn bộ trạng thái của VM được lưu trữ trong RAM. Hành động này tạm thời vô hiệu hóa quyền truy cập vào VM, nhưng không giải phóng bất kỳ tài nguyên nào.

+ Một VM ở trạng thái `Pause` sẽ trở về trạng thái `Running` khi thực hiện action `Resume Instance`

[pause_instance.png]

- Suppend Instance:

+ Giống như Pause, như dữ liệu được ghi vào disk. Trong trạng thái này, RAM & CPU sẽ được giải phóng để sẵn sàng cho các  VM khác. 1 VM lâu không sử dụng sẽ sử dụng trạng thái này để tiết kiệm tài nguyên máy chủ.

- Shelve Instance:

+ Shelving 1 server cho biết server không cần thiết trong một khoảng thời gian và có thể tạm thời xóa khỏi trình giám sát. Điều này cho phép tài nguyên của nó được giải phóng, phân bổ để người dùng sử dụng 



+ Để bỏ chế độ `Shelve Instance` về `Active` thực hiện hành động `Unshelve Instance`. Nó sẽ thực hiện khởi động lại máy chủ được lên lịch trên 1 host mới nếu bị offloaded.

[shelve_instance.png]

### 8. Resize (Confirm resize, Revert resize)

- Sử dụng chức năng này để chuyển đổi một máy chủ hiện có sang một Flavor khác, về bản chất, nhân rộng tài nguyên máy chủ lên hoặc xuống. (tăng giảm RAM, CPU)

- Hành động `Confirm Resize` sẽ xóa máy chủ cũ trong lớp virt. Máy chủ sinh sản trong lớp virt sẽ được sử dụng từ đó về sau.
 
- Ngược lại, hành động thay đổi kích thước Revert sẽ xóa máy chủ mới sinh ra trong lớp virt và hoàn nguyên tất cả các thay đổi. Máy chủ ban đầu sẽ được sử dụng từ đó về sau.

[resize_instance.png]

### 9. Lock Instance

- Chặn hành động vào VM từ non-admin.

- Để bỏ lock 1 instance chọn `unlock instance` trong action menu.

[lock_instance.png]


### 10. Reboot Instance (soft reboot & hard reboot)

- Sử dụng chức năng này để thực hiện khởi động lại mềm hoặc cứng của máy chủ. 
    + Với khởi động lại phần mềm, hệ điều hành được báo hiệu khởi động lại, cho phép tắt tất cả các quy trình. Soft reboot khởi động lại VM mà không tắt nguồn điện (chế độ mặc định khi thực hiện bằng dòng lệnh)
 
    + Khởi động lại phần cứng: phải đảm bảo rằng hành động khởi động lại sẽ thành công ngay cả trong trường hợp VM bị tạm dừng. Hard reboot: tắt và khởi động lại nguồn điện (thêm –hard khi thực hiện reboot bằng dòng lệnh)

### 11. Rebuild Instance

- Sử dụng chức năng này để xóa tất cả dữ liệu trên máy chủ và thay thế nó bằng image được chỉ định. ID máy chủ, flavor và địa chỉ IP vẫn giữ nguyên.


### 12. Ecuvate Instance

- “Evacuate”(di tản) là một công việc xoay quanh việc này cho phép người quản trị buộc phải xây dựng lại các máy chủ này trên một compute khác. 
- Nó không đảm bảo rằng máy chủ đã thực sự ngừng hoạt động, có thể nó sẽ hoạt động cả trên 2 compute, nên phải kiểm tra và reboot lại.
- Khi thực hiện evacuate cần set down compute mà máy ảo cũ tồn tại trên đó, sau khi evacuate xong mới up compute lên.



### 13. Snapshot

Có thể lưu trữ trạng thái hiện tại root disk serrvẻ sẽ được lưu và uploaded lại vào repo image. Sau đó, một máy chủ có thể được khởi động lại bằng image đã lưu này.

20. Backup

Sử dụng phương pháp sao lưu để lưu trữ trạng thái hiện tại của máy chủ trong kho lưu trữ, trong thời gian đó, các snapshot cũ sẽ bị xóa dựa trên loại ‘daily” hoặc “weekly”.

21. Delete, Restore Instance

- Tắt nguồn máy chủ đã cho trước sau đó tách tất cả các tài nguyên được liên kết với máy chủ như mạng và ổ đĩa, sau đó xóa máy chủ.
- Tùy chọn cấu hình ‘reclaim_instance_interval, (tính bằng giây) quyết định xem máy chủ cần xóa có còn trong hệ thống hay không. 
    • Nếu giá trị này lớn hơn 0, máy chủ bị xóa sẽ không bị xóa ngay lập tức, thay vào đó, nó sẽ được đưa vào hàng đợi cho đến khi nó quá cũ (thời gian bị xóa lớn hơn giá trị của reclaim_instance_interval). Quản trị viên có thể sử dụng hành động “Restore” để khôi phục máy chủ từ hàng đợi xóa. 
    • Nếu máy chủ bị xóa vẫn còn lâu hơn giá trị của reclaim_instance_interval, nó sẽ tự động bị xóa bởi dịch vụ tính toán.



23. Set admintrator password

Set password root/admin cho server. Nó sử dụng cài đặt lựa chọn để set mật khẩu cho quản trị viên.

24. Migrate, Live migrate

- Migrate thường được sử dụng bởi quản trị viên, nó sẽ di chuyển một máy chủ sang máy chủ khác; nó sử dụng hành động ‘thay đổi kích thước, nhưng với cùng một flavor, vì vậy trong quá trình di chuyển, máy chủ sẽ bị tắt và được xây dựng lại trên một máy chủ khác.

- Live migrate cũng di chuyển một máy chủ từ máy chủ này sang máy chủ khác, nhưng nói chung, nó đã giành được quyền tắt máy chủ vì vậy máy chủ sẽ không gặp sự cố. Quản trị viên có thể sử dụng điều này để sơ tán máy chủ khỏi máy chủ cần trải qua các nhiệm vụ bảo trì.


Link tham khảo : https://docs.openstack.org/api-guide/compute/server_concepts.html

