## 1. Tổng quan về OpenStack

- OpenStack là một dự án mã nguồn mở, dùng cho triển khai điện toán đám mây, do các công ty, tổ chức,lập trình viên tự nguyện xây dựng và phát triển, hỗ trợ quản trị các tài nguyên, tính toán lưu trữ, kết nối mạng trong toàn bộ trung tâm dữ liệu thông qua API và xác thực phổ biến.

- Hiện nay OpenStack đang được đánh giá là một trong những phần mềm mã nguồn mở mạnh nhất dùng trong xây dựng điện toán đám mây, với sự hổ trợ của nhiều hãng máy tính lớn trên thế giới như HP, Canonical, IBM, Cisco, Microsoft…

- Các khối cơ bản của OpenStack:

+ OpenStack Controller : Glance, Keystone, Horizon

+ OpenStack Compute - Nova

+ OpenStack Networking - Neutron

+ OpenStack BlockStorage - Cinder

+ OpenStack Object Storage - Swift


### 2. Tìm hiểu các microservice trong 6 core service của OpenStack version Train


- Các dịch vụ bên trong OpenStack bao gồm 1 số quy trình. Tất cả các dịch vụ đều có ít nhất một quy trình API để lắng nghe các yêu cầu, xử lý và chuyển tiếp. 

- Để giao tiếp giữa các quá trình của 1 dịch vụ, bản tin AMQP (Advanced Message Queuing Protocol) được sử dụng để lưu trữ trạng thái trong cơ sở dữ liệu. 

- Hiện đã có OpenStack Train v20 – bản release mới nhất của OpenStack-Ansible. OpenStack – Ansible là một trong số các công cụ để triển khai OpenStack.

- OpenStack Train v20 mang tới cải tiến sau: tăng cường bảo mật dữ liệu, hỗ trợ AI, machine learning và nâng cao khả năng theo dõi cũng như là quản lý tài nguyên.

**1.Keystone**

- Cải thiện tính năng tích hợp với WebSSo

- Keystone policy không còn được khuyến khích


**2.Glance**

- Nâng cao khả năng tích hợp với Cinder

**3.Cinder**

- Thêm tùy chọn giúp kiểm soát và cải thiện tính năng nén image

- Cải thiện các driver dành cho Dell EMC, và NetAPP

**4.Nova**

- Bổ sung tính năng live-migrate dành cho các host
- Cung cấp data persistence cho RAM máy ảo trong quá trình reboot

**5.Neutron**

- Hỗ trợ SmartNIC cho driver
- Cải thiện khả năng thông báo với Baremetal service(Ironic: hỗ trợ HTTP Proxy, hỗ trợ BIOS interface cho ilo, ilo5,...)

**6.Heat**

- Cải thiện khả năng hỗ trợ CoreOS Ignition thay vì cloud-init

- Cải thiện khả năng hỗ trợ QOS Rules: đảm bảo các yêu cầu mạng: băng thông, độ trễ, độ tin cậy để đáp ứng Thỏa thuận mức dịch vụ (SLA) giữa nhà cung cấp ứng dụng và người dùng cuối.

**7.Horizon**

- Cho phép người dùng thay đổi passwd ở lần đăng nhập đầu tiên

- Sử dụng Cinder backup cho Snapshot, multi – attachment cho volume


#### 2.1. OpenStack Compute (Nova)

- OpenStack Compute đóng vai trò cốt lõi trong OpenStack bằng cách cung cấp máy ảo theo yêu cầu.

- Tất cả tính năng của người dùng cuối (tính năng quản trị) được hiển thị thông qua REST API.

- Các thành phần của Nova sử dụng `Remote Procedure Calls (RPC)` để giao tiếp với nhau. RPC : yêu cầu và phản hồi (rpc.call và rpc.cast) qua AMQP bằng cách sắp xếp và quản lý các thông báo thành các lệnh gọi hàm.

**Bao gồm các thành phần sau:**

`Nova-api service`:
-  Tiếp nhận và phản hồi các lệnh call API từ người dùng, xử lý và thực thi chính sách như : booting an instance.

`nova-api-metadata service`:
-  Tiếp nhận các metadata request từ instance, thường được sử dụng khi chạy ở chế độ multi-host với nova-networking.

`nova-compute service`
- Quản lý giao tiếp giữa Hypervisor và VM: libvirt cho KVM or QEMU, VMwareAPI cho Vmware.

Về cơ bản, là 1 daemon chờ các action từ hàng đợi và thực hiện 1 loạt các lệnh hệ thống như: launch instance, update state VM vào database.

`nova-placement-api service`:
- Theo dõi và kiểm tra việc sử dụng tài nguyên, cung cấp khả năng xác định tài nguyên tùy chỉnh khi cần thiết.

- Ví dụ,  1 instance được tạo trên 1 compute node: nơi các tài nguyên như : RAM, CPU, Disk được lưu trữ để chia sẻ. Các loại tài nguyên được theo dõi dưới dạng các lớp tiêu chuẩn: DISK_GB, RAM_MB, VCPU

`nova-scheduler service:`
- Nhận yêu cầu từ hàng đợi, xác định xem máy ảo sẽ nằm trên compute host nào.

`nova-conductor module:`
- Tạo sự tương tác giữa nova-compute và database. Cho phép loại bỏ truy cập trực tiếp vào Cloud database cho các compute node để giảm rủi ro bảo mật.

`nova-consoleauth daemon:`
- Dành riêng cho XenAPI: SPICE console và VNC console proxy

- Quản lý xác thực ủy quyền cho người dùng từ xa. SPICE console hỗ trợ những điểm hạn chế của VNC: hỗ trợ nhiều màn hình, âm thanh 2 chiều, hỗ trợ từ xa.

`Queue:`
- Nơi truyền thông diệp giữa các daemon, thường được triển khai với AMQP: RabbitMQ (hàng đợi)

`SQL database:`	
- Lưu trữ trạng thái build-time và run-time cho Cloud infrastructure: instance type, instance in use, network, ... Cơ sử dữ liệu phổ biến là SQLite3, MySQL, MariaDB.

`Nova:`
- Dòng lệnh để truy cập Compute API.	
	
#### 2.2. OpenStack Image (Glance) 

-  Là nơi có thể upload & download, lưu trữ image để sử dụng, có thể bổ sung thêm metadata cho image.

-  Glance sử dụng API RESPful cho phép truy vấn image metadata.

**OpenStack Image Service gồm các thành phần:**

`glance-api:`
- Tương tác với storage back end để xử lý các yêu cầu truy xuất và lưu trữ images.

- API sử dụng Openstack-glance-registry để truy xuất thông tin image mà không được phép truy cập trực tiếp vào service.

`openstack-glance-registry:`
-  Quản lý tất cả metadata cho các images: size, type,...

`glance:`
 - Command-line để truy cập đến API image

`Database:`
-  Lưu trữ image metadata. Được triển khai bởi: MySQL, SQLite.


#### 2.3. OpenStack Network Service (Neutron)

- Tạo và quản lý hạ tầng mạng ảo trong OpenStack Cloud. Các yếu tố cơ sở hạ tầng gồm: network, subnet, router, có thể triển khai service: firewall, VPN (virtual private network).
 
- OpenStack Networking chủ yếu tương tác với OpenStack Compute để cung cấp mạng và kết nối cho các instances.

**Ưu điểm:**

- Người dùng có thể tạo network, kiểm soát lưu lượng mạng, kết nối máy chủ và thiết bị với 1 or nhiều mạng.

- Địa chỉ IP có thể dedicated hoặc floating, nếu sử dụng mạng VLAN, có thể sử dụng tối đa 4094 VLAN (12 bit), nếu sử dụng VXLAN (24 bit) cho phép khoảng 16 triệu mạng địa chỉ. = 2^24-2

**Thành phần:**

`Network agent:`
- Là 1 microservice chạy trên mỗi node của OpenStack để thực hiện cấu hình mạng cục bộ cho máy ảo.

`neutron-dhcp-agent:`
-  Là agent cung cấp dịch vụ DHCP (dynamic host IP addressing) cho network.

`neutron-ml2:`
-  Plugin quản lý network driver, routing, switching (định tuyến, chuyển mạch) cho các dịch vụ mạng như OpenStack vSwitch, Cisco.

`neutron-server:`
-  Daemon quản lý yêu cầu của người dùng: accept và route (định tuyến) các yêu cầu API đến các plugin của OpenStack Networking.. 1 số plugin : openvswich, linuxbrigde sử dụng cơ chế mạng Linux.

`Neutron:`
- Command-line để người dùng truy cập API


#### 2.4. OpenStack Identity (Keystone)

- OpenStack Identity cung cấp xác thực người dùng và ủy quyền cho tất cả các thành phần của OpenStack. 

- Identity hỗ trợ nhiều cơ chế xác thực: thông tin đăng nhập, tên người dùng, passwd, quyền người dùng: member, admin,...

`Openstack-keystone:`
- Cung cấp service Identity, cùng các API quản trị và public.

- Có 2 version Identity API: v2 & v3. Hiện version 3 là tập hợp tất cả các chức năng có sẵn trong version 2 và 1 số phần mở rộng: không gian tên cho người dùng sử dụng “domains” và khắc phục sự cố bảo mật trong API v2.0.

`keystone:`
- Command-line để truy cập API.

**Thành phần:**

`server:`
- Máy chủ cung cấp dịch vụ xác thực và ủy quyền bằng RESTful API.

`drivers:`
- Các trình điều khiển hoặc service back end được tích hợp vào máy chủ tập trung.

- Được sử dụng để truy cập thông tin xác thực trong kho lưu trữ của OpenStack (cơ sở dữ liệu SQL: cho phép gán quyền cho project, máy chủ LDAP: cung cấp xác thực người dùng )

`modules:`
- Các module sẽ chặn các request service, trích xuất thông tin, đăng nhập của người dùng, và gửi chúng đến máy chủ tập trung để ủy quyền.

- Sự tích hợp giữa các module phần mềm trung gian với các thành phần của OpenStack sử dụng Python Web Server gateway Interface (WSGI) – để máy chủ trung t âm chuyển tiếp các yêu cầu tới các thành phần khác trong OpenStack.

`Identity API` với role-based access control (RBAC)
- Identity hỗ trợ bảo vệ các API bằng cách xác định các quy tắc dựa trên RBAC. 

- Identity lưu trữ tham chiếu đến file cấu hình: `/etc/keystone/keystone.conf`. Tệp này chứa các rulé mà roles có quyền truy cập nhất định trong các services.

- Mỗi lệnh call API v3 cho biết mức độ quản trị quyền truy cập nào được áp dụng.


#### 2.5. OpenStack Block Storage (Cinder)

- OpenStack Block Storage cung cấp khả năng quản lý lưu trữ khối cho các ổ cứng ảo, cho phép người dùng tạo xóa block device cũng như attach vào server.

- Việc attach & detach volume được xử lý thông qua tích hợp với Compute service.

**Block Storage (Volumes):**

- Lưu trữ cơ sở dữ liệu hoặc hệ thống tệp có thể mở rộng
- Snapshot volume để khôi phục dữ liệu hoặc tạo volume mới.
- Tất cả các tính năng của Cinder được hiển thị thông qua API REST.

**Các micro service:**

`openstack-cinder-api:`
- Phản hồi các yêu cầu và thêm chúng vào queue. Khi nhận được yêu cầu, API xác minh các request được đáp ứng và chuyển thành thông báo de lap lich.

`openstack-cinder-backup:`
- Sao lưu volume vào repo. Sử dụng Ceph hoặc NFS backend làm kho lưu trữ để sao lưu.

`openstack-cinder-scheduler:`
- Giao nhiệm vụ cho hàng đợi (queue) và xác định volume server. 
- Cinder-scheduler  lập lịch để đọc yêu cầu từ hàng đợi và xác định server nào sẽ thực hiện hành động được yêu cầu.
- Sau đó lập lich giao tiếp với cinder-volume trên server đã chọn để xử lý yêu cầu.

`openstack-cinder-volume:`
- Chỉ định bộ nhớ cho máy ảo. Khi có yêu cầu từ scheduler, cindẻ-volume sẽ tạo, sửa, xóa volume.

`cinder:`
- Command-line cho phép người dùng truy cập Block Storage API.

### 2.6. OpenStack Dashboard (Horizon)

- Cung cấp giao diện người dùng và quản trị viên để thực hiện các hoạt động như tạo và khởi chạy các instance, quản lý mạng, kiểm soát truy cập,...

- Cung cấp cái nhìn tổng quan về Project, setting, Hypervisor, Compute,...



**Microservice:**

`openstack-dashboard:`
- Ứng dụng web Django cxng cấp quyền truy cập vào dashboard bằng bất cứ trình duyệt nào.

`Apache HTTP server (httpd service):`
- Lưu trữ ứng dụng

**Sự tương tác giữa các service:**
- `OpenStack Identity service:`  xác thực và ủy quyền cho người dùng.
- `Sesion backend`: cung cấp csdl
- `httpd service:` lưu trữ ứng dụng Web và tất cả các dịch vụ của OpenStack khác cho các lệnh gọi API.





## 3.  Các endpoint trong OpenStack

- OpenStack gồm các thành phần khác nhau cung cấp nhiều services, database, queue, memcached,... Đảm bảo cũng cấp một cơ chế nhất quán để xác định các endpoints.

- Endpoint (là địa chỉ có khả năng truy cập mạng như URL, RESTful API, etc.)

- Keystone được tổ chức theo nhóm các nội dịch vụ (internal services)tương tác với một hoặc nhiều endpoints. Các nội dịch vụ đó là:

+ Identity : Cung cấp dịch vụ chứng thực và dữ liệu về Users, Groups, Projects, ...

+ Token: Xác nhận và quản lý các tokens được sử dụng để xác thực yêu cầu khi thông tin của người dùng đã được xác minh

+ Catalog: chứa danh sách các endpoint mà user có thể sử dụng.

+ Policy: cung cấp engine để ủy quyền dựa trên rule và kết nối với giao diện quản lý rule

- Mỗi services trong OpenStack Cloud sẽ chạy trên 1 URL:1 port cụ thể - đây là các endpoint cho các services.

- Khi giao tiếp với OpenStack thì OpenStack Identity Service sẽ tiến hành trả về các endpoints URL cần để thao tác trong OpenStack.



- Dịch vụ catalog chia thành danh sách các endpoint, mỗi endpoint chia thành các admin URL, internal URL, public URL

+ public_url: Tham số này là URL mà người dùng cuối sẽ sử dụng. Trong public Cloud, đây sẽ là một URL phân giải địc chỉ IP public.

```
nova             | compute                 | True    | public    | http://10.255.77.1:8774/v2.1/%(tenant_id)s |
```

+ admin_url: URL này được sử dụng bởi những người dùng cần quyền truy cập của quản trị viên vào một dịch vụ và. Các API quản trị viên chỉ có sẵn tại URL này.

```
 nova             | compute                 | True    | admin     | http://10.6.1.1:8774/v2.1/%(tenant_id)s    |
```
+ internal_url: sử dụng để liên lạc giữa các dịch vụ OpenStack

```
nova             | compute                 | True    | internal  | http://10.6.1.1:8774/v2.1/%(tenant_id)s    |
```

- Để tạo URL endpoint cho service keystone:

```
$ openstack endpoint create identity --region RegionOne internal http://10.6.1.1:5000 
```

## 4. Iptables sinh ra bởi neutron trên openstack compute

- Neutron sử dụng iptables để tạo nên các traffic rules nhằm thiết lập chức năng cho security group, từ đó quy định cách thức các máy ảo truyền thông trên mạng nội bộ cũng như ra ngoài mạng internet.

- Security group:

+ Là 1 tập hợp các traffic rule được áp dụng cho các VM trong 1 project, kiểm soát truy cập giữa các VM và giữa VM với tài nguyên mạng bên ngoài môi trường openstack.

+ 1 security khi đc gán cho 1 port hoặc VM sẽ được dịch sang các iptables rules.

+ Mặc định, 1 security group được tạo ra có tên default group được gán cho tất cả các instance. default group chặn tất cả traffic tới VM và chỉ cho traffic từ VM gửi đi.

```
Security Groups

default

        ALLOW IPv4 to 0.0.0.0/0
        ALLOW IPv6 from default
        ALLOW IPv4 from 0.0.0.0/0
        ALLOW IPv4 from default
        ALLOW IPv6 to ::/0

```

- Trước đây, openstack sử dụng nova firewall driver để triển khai các security rules, nhưng giờ đã được thay thế bởi neutron: OVSHybridIptablesFirewallDriver là driver dành cho openvswitch plugin

- Việc sử dụng iptables có thể gặp 1 số vấn đề:

+ Khi 1 security group có 1 hay nhiều rules liên quan đến 1 security group khác, hiệu năng của security group sẽ bị giảm.

+ Khi update 1 port vào 1 security group, thì các chain liên quan đến port này sẽ bị xóa đi và build lại, điều này ảnh hưởng đến hiệu năng của L2 agent (VMs sẽ nằm trên một network được kết nối tới một router. Router này được triển khai trên neutron bằng cách sử dụng công nghệ Network Namspace. Sẽ có một agent cụ thể được chỉ định để xử lý các Routers đó là L2_agent)

- Từ phiên bản openstack Juno, người ta đã thêm vào tính năng ipset nhằm hỗ trợ cho iptables và tối ưu các rule chain.

**Tìm hiểu về ipset**

- `ipset` là một "match extension" cho iptables. 

Khi iptables có những rules giống nhau đối với 1 tập các địa chỉ xác định, khi đó ipset lập ra 1 danh sách gồm các địa chỉ ip đó, iptables chỉ việc gọi tới danh sách này bằng cách chỉ định tên của nó mà không cần khai báo nhiều rules với từng địa chỉ ip khác nhau.

- Giả sử muốn chặn truy cập từ 2 địa chỉ 10.1.1.1 và 172.1.1.2, với iptables:

```
iptables -A INPUT -s 10.1.1.1 -j DROP
iptables -A INPUT -s 172.1.1.2 -j DROP
```

- Sử dụng ipset kết hợp cùng iptables:

```
ipset -N myset iphash 
ipset -A myset 10.1.1.1 
ipset -A myset 172.1.1.2 
iptables -A INPUT -m set --set myset src -j DROP 
```

=> Tạo ra 1 danh sách có tên myset bằng ipset, danh sách này bao gồm 2 địa chỉ ip. Khi muốn chặn truy cập từ 2 địa chỉ này, iptables chỉ việc gọi tới myset bằng cách chỉ định -m set --set myset. Cờ src: địa chỉ nguồn xuất hiện trong myset sẽ bị drop ở rule này.



## 5.  Ý nghĩa các bước trong quy trình tích hợp compute



- Bước 1: Tiếp nhận server trên NIAM, IIM và đổi mật khẩu, gửi cập nhật NIAM 

- Bước 2: Bonding & Hardening:

+ `vim harderning/bond.yml` : cấu hình interface: external && internal

+ update repo: pip, local.repo, Disable selinux, install packages: yum install, nable & start Docker, limit RAM for rsyslog: MemoryLimit=7408818590, tuning sysctl: cấu hình linux kernel tuning cho RAM và CPU, tunning SSH: disable UseDNS & Generic Security Services API = No

+ copy iptables: chuẩn của Cloud.

- Bước 3: Check CPU Model, HBA và chỉnh sửa inventory/multinode_train

- Bước 4: Gather facts để genconfig cho `/root/inventory/multinode_train`

- Bước 5: Chỉnh sửa /etc/kolla/config/nova/.../nova.conf : ratio của ram & cpu. OpenStack cho phép overcommit CPU and RAM trên node compute, cho phép tăng số instance chạy trên cloud.

```
[DEFAULT]
cpu_allocation_ratio = 1.5
ram_allocation_ratio = 1.0
[libvirt]
volume_use_multipath=True

```

- Bước 6: Triển khai compute và kiểm tra đủ các container: Generate hosts file && copy hosts to all containers -> Deploy

- Bước 7: Enable giám sát dịch vụ: 

+ Genconfig prometheus cho node controller 

+ Trên các node Controller: Vào container `prometheus_server` để copy `config_files/prometheus.yml` -> Reload 

- Bước 8: Tạo issue để giám sát vật lý (đầu mối: ducnv41)

- Bước 9: Fix lvm: configuration for the LVM2 system

- Bước 10: Kiểm tra compute up, thêm compute vào HA, tạo máy ảo và kiểm tra lại


## 6. Hiện trạng Cloud hiện tại, các tối ưu, cấu hình đặc thù


- Giờ có mấy cloud ?

Hiện đang có 4 hạ tầng Cloud: Huawei.HLC.T5, Product2019, Cisco2020, ViettelPay

- Các version hiện tại là gì?

+ Cloud Huawei: 

openstack_release: "queens"
kolla_external_vip_address: "10.240.201.100"

+ Cloud Product2019: 

openstack_release: "3.17.0"
docker_namespace: "rockey"
kolla_external_vip_address: "10.255.77.1"

+ Cloud Cisco 2020:

openstack_release: "20.0.1"
docker_namespace: "train"
kolla_external_vip_address: "10.254.170.1"


## 7.  Tìm hiểu hệ thống prom, giám sát từ dịch vụ tới vật lý , switch, san...

### 7.1. Prometheus

- Prometheus là bộ công cụ giám sát và cảnh báo hệ thống mã nguồn mở

- Prometheus có khả năng thu thâp số liệu (metric) từ các mục tiêu được cấu hình theo các khoảng thời gian nhất định, hiển thị kết quả và có thể kích hoạt cảnh báo nếu một số điều kiện được thỏa mãn yêu cầu.
- Cung cấp một giao diện web cho phép người sử dụng: truy vấn số liệu thu thập, target, rules, trạng thái cảnh báo.

- Prometheus lưu trữ dữ liệu dạng time-series. Mỗi time-series được định nghĩa bởi tên số liệu (metric name) và một tập các cặp key-value.

**Biểu thức truy vấn:**

- Prometheus cung cấp một ngôn ngữ truy vấn gọi là PromQL (Prometheus Query Language) cho phép người dùng chọn và tổng hợp dữ liệu time series theo thời gian thực. 
- Kết quả của một biểu thức có thể được hiển thị dưới dạng biểu đồ, dữ liệu dạng bảng trong trình duyệt biểu thức của Prometheus.
- Để xem các đối tượng mà Prometheus giám sát, người dùng truy cập Status → Targets
- Để xem các quy tắc cảnh báo được thiết lập trong Prometheus, người dùng truy cập Status → Rules
- Người dùng có thể theo dõi các cảnh  báo của Promethes bằng cách chọn “Alerts”
### 7.2. Faythe


- Faythe là hệ thống mã nguồn mở được thiết kế, xây dựng và phát triển để tương tác, kết nối giữa các hệ thống Prometheus và OpenStack.
- Các tính năng cơ bản của Faythe:
+ Clustering: mỗi khi có node join/leave cluster thì các node trong cluster đều có thể nhận biết để điều chỉnh lại.

   + Autoscaling: tự động co giãn tài nguyên ảo hóa khi phát hiện có sự thay đổi về tải của hệ thống.
   
+ Autohealing: khi phát hiện sự cố liên quan đến Compute của hệ thống OpenStack, Faythe sẽ tự động thực hiện các action phù hợp đã được cấu hình (Thực hiện migrate máy ảo, tắt dịch vụ trên compute bị down nhằm tránh tạo máy ảo trên Compute lỗi, gửi mail thông báo...). Cho phép thực hiện “silence” Compute đã được xác định bị lỗi: hỏng ổ, RAM,...

**Component:**

   - Cloud provider: đối tượng chính của Faythe, đại diện cho một hệ thống Cloud với các thông tin liên quan đến Auth URL, tên user sẽ được sử dụng... Hiện tại, OpenStack là provider duy nhất được hỗ trợ.

- Scaler: định nghĩa hành động scaling. Scaler sẽ bao gồm một câu truy vấn PromQL được sử dụng để đánh giá khi nào nên thực hiện hành động scale in/scale out. 

- Healer: thể hiện hành động healing. Healer đánh giá câu truy vấn PromQL do người dùng cấu hình sẵn để biết khi nào thực hiện hành động healing. Một healer có thể có nhiều hành động healing (gửi mail, gửi HTTP request, thực hiện workflow...). Một Cloud provider chỉ có một healer. 

- Silencer: bỏ qua hành động healing của healer. Trong trường hợp đã xác định lỗi của Compute, không muốn thực hiện hành động healing đối với Compute, người dùng có thể sử dụng Silencer. Một Cloud provider có thể nhiều hơn một Silencer.

- Name Resolver (NResolver): thực hiện mapping giữa địa chỉ IP với hostname của Compute. Vì Prometheus chỉ sử dụng địa chỉ IP, còn bên OpenStack thì lại làm việc với hostname. Một Cloud provider chỉ có một Nresolver.

- MonitorEngine: Mỗi Cloud provider luôn đi cùng với một Monitor Engine khi khởi tạo. Monitor Engine đại diện cho thông tin của hệ thống giám sát cho Cloud provider. Hiện tại, Faythe chỉ hỗ trợ duy nhất hệ thống giám sát Prometheus.

### 7.3. Garafana

- Grafana là phần mềm cho phép truy vấn, sinh cảnh báo dựa trên metrics từ các nguồn dữ liệu khác nhau (datasource) (Time series databases: Prometheus; Document database: Elasticsearch; SQL: mysql).
- Để giám sát các thông tin về OS, thông tin tài nguyên sử dụng của các service chạy trên Cloud các dashboard trong Folder: Cloud-2020

+ Basic Physical Monitoring: chứa thông tin cơ bản overview về Memory, CPU, Disk, Network của các server đang chạy trên hệ thống Cloud

+ Node Exporter: giám sát chi tiết về Memory, CPU, Disk, Network của các server đang chạy trên hệ thống Cloud

+ Libvirt exporter: giám sát thông tin cơ bản về Memory, CPU, Disk, Network trên các máy ảo đang chạy trên node compute host.
		+ Docker Container Dashboard: giám sát các service chạy trên hệ thống
+ Openstack clouds: giám sát, thống kê chi tiết về tài nguyên đã sử dụng, số lượng máy ảo đang chạy, data storage: used, free... của hệ thống Cloud
		+ ElasticSearch Prometheus: giám sát chi tiết về ElasticSearch
		+ HAproxy Monitoring: giám sát chi tiết về HAproxy
		+ Memcached Monitoring: giám sát chi tiết về Memcached
		+ RabbitMQ Monitoring: giám sát chi tiết về RabbitMQ
		+ Mysql Monitoring: giám sát chi tiết về Mysql
		+ Faythe Autoscaling - Selfhealing instrumentation: giám sát chi tiết về các service trong Faythe - hệ thống autoscaling và autohealing.

- Để giám sát về các thiết bị phần cứng như: DELLServer, Cisco Switch, San Switch ...Các thông tin này được lấy thông qua SNMP, các dashboard triển khai trong Folder Hardware

+ SNMP - DELL server: chứa thông tin chi tiết về CPU, Memory, Fan, Power, Battery, RAID Disk của server DELL.
		+ SNMP - SAN Switch: chứa thông tin chi tiết về thiết bị SAN Switch
		+ SNMP - Cisco Switch: chứa thông tin chi tiết về thiết bị Cisco Switch

## 8. DCIM, vportal kết nối đến Cloud


