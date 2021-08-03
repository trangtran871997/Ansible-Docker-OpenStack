# Docker Container

## 1.Tìm hiểu chung về Docker Container

### a.Các khái niệm cơ bản trong docker


**Quá trình hình thành Container**

1.mô hình máy chủ thường là máy chủ vật lý + hệ điều hành(OS) + application. (Pysical server +  Operating system +Appication) 

lãng phí tài nguyên, một máy chủ chỉ cài được một OS

2.Sau đó ra đời công nghệ ảo hóa virtualization (VitualBox, VMWare)

Với công nghệ này, trên một máy chủ vật lý mình có thể tạo được nhiều OS, tận dụng tài nguyên đã tốt hơn nhưng lại nảy sinh vấn đề tiếp.

*Về tài nguyên:* Khi bạn chạy máy ảo, bạn phải cung cấp "cứng" dung lượng ổ cứng cũng như ram cho máy ảo đó, bật máy ảo lên để đó không làm gì thì máy thật cũng phải phân phát tài nguyên.

*Về thời gian:* Việc khởi động, shutdown khá lâu, có thể lên tới hàng phút.

3.Ở bước tiến hóa tiếp theo, người ta sinh ra công nghệ containerlization

trên một máy chủ vật lý, ta sẽ sinh ra được nhiều máy con (giống với công nghệ ảo hóa virtualization), nhưng tốt hơn ở chỗ là các máy con này (Guess OS) đều dùng chung phần nhân của máy mẹ (Host OS) và chia sẻ với nhau tài nguyên máy mẹ.

khi nào cần tài nguyên thì được cấp, cần bao nhiêu thì cấp bấy nhiêu, như vậy việc tận dụng tài nguyên đã tối ưu hơn. Điểm nổi bật nhất của containerlization là nó sử dụng các container

Container là gì?

Các phần mềm, chương trình sẽ được Container Engine ( là một công cụ ảo hóa tinh gọn được cài đặt trên host OS) đóng gói thành các container.

Container là giải pháp để chuyển giao phần mềm một cách đáng tin cậy giữa các môi trường máy tính khác nhau bằng cách:

Tạo ra một môi trường chứa mọi thứ mà phần mềm cần để có thể chạy được.

Không bị các yếu tố liên quan đến môi trường hệ thống làm ảnh hưởng tới.

Cũng như không làm ảnh hưởng tới các phần còn lại của hệ thống.

Giả sử : Một project cần phải cài nhiều thứ mới có thể built được. Bạn có thể hiểu là ruby, rails, mysql ... kia được bỏ gọn vào một hoặc nhiều cái thùng (container), ứng dụng của bạn chạy trong những chiếc thùng đó, đã có sẵn mọi thứ cần thiết để hoạt động, không bị ảnh hưởng từ bên ngoài và cũng không gây ảnh hưởng ra ngoài.

Các tiến trình (process) trong một container bị cô lập với các tiến trình của các container khác trong cùng hệ thống tuy nhiên tất cả các container này đều chia sẻ kernel của host OS (dùng chung host OS).


**b.Ưu điểm của container:**

Linh động: Triển khai ở bất kỳ nơi đâu do sự phụ thuộc của ứng dụng vào tầng OS cũng như cơ sở hạ tầng 

Nhanh: Do chia sẻ host OS nên container có thể được tạo gần như một cách tức thì. 

Nhẹ: Container cũng sử dụng chung các images nên cũng không tốn nhiều disks.

Đồng nhất :Khi nhiều người cùng phát triển trong cùng một dự án sẽ không bị sự sai khác về mặt môi trường.

Đóng gói: Có thể ẩn môi trường bao gồm cả app vào trong một gói được gọi là container. Có thể test được các container. Việc bỏ hay tạo lại container rất dễ dàng.


**c.Nhược điểm:**

Tính an toàn: 

Do dùng chung OS nên nếu có lỗ hổng nào đấy ở kernel của host OS thì nó sẽ ảnh hưởng tới toàn bộ container có trong host OS đấy.

Nếu host OS là Linux, nếu trong trường hợp ai đấy hoặc một ứng dụng nào đấy có trong container chiếm được quyền superuser,thì tầng OS sẽ bị crack và ảnh hưởng trực tiếp tới máy host bị hack cũng như các container khác trong máy đó



Công nghệ ảo hóa (virtualization) thì ta có thể dùng công cụ Virtualbox hay VMware thế còn đối với containerlization thì dùng Docker.


### Docker là gì?

Docker cũng cấp môi trường ảo hóa chứa dầy đủ những cài đặt cần thiết cho project.

Là nền tảng mở, tạo môi trường cho ứng dụng và đóng gói ứng dụng.

Ban đầu được viết bằng Python sau đó chuyển sang Go-lang

(Go-lang: Không giống các ngôn ngữ kịch bản như Python, Go biên dịch (compile)
code ra nhị phân một cách nhanh chóng. Và không giống như C hoặc C ++, Go biên dịch cực nhanh,
nhanh đến mức khiến bạn cảm thấy khi làm việc với Go giống như là làm việc với một ngôn ngữ kịch bản hơn là một ngôn ngữ biên dịch. Hơn nữa, hệ thống Go build đơn giản hơn so với các ngôn ngữ biên soạn khác).

Hỗ trợ nhiều nền tảng hệ điều hành khác nhau: Linux, Windows, Mac.

Hỗ trợ nhiều dịch vụ điện toán đám mây nổi tiếng như: Microsoft Azure, Amazon Web Service, ...

(Azure sẽ mang lại nhiều chức năng cho các ứng dụng Web, cho phép doanh nghiệp nhanh chóng triển khai và cập nhật các dịch vụ với chi phí thấp hơn.)

Amazon Web Service là nền tảng đám mây toàn diện và được sử dụng rộng rãi nhất, cung cấp đầy đủ tính nưng khai thác dữ liệu từ các trung tâm trên toàn thế giới.


**Cách hoạt động:**

**Docker image** là nền tảng của container, có thể hiểu Docker image như khung xương giúp định hình cho container, nó sẽ tạo ra container khi thực hiện câu lệnh chạy image đó.  

Nếu trong lập trình hướng đối tượng, Docker image là class, còn container là thực thể (instance, thể hiện) của class đó.


Docker có hai khái niệm chính cần hiểu, đó là image và container:

**Container**: Tương tự như một máy ảo, xuất hiện khi mình khởi chạy image.

Tốc độ khởi chạy container nhanh hơn tốc độ khởi chạy máy ảo rất nhiều và bạn có thể thoải mái chạy 4,5 container mà không sợ treo máy.

Các files và settings được sử dụng trong container được lưu, sử dụng lại, gọi chung là **images của docker.**

Một image bao gồm hệ điều hành (Windows, CentOS, Ubuntu, …) và các môi trường lập trình được cài sẵn (httpd, mysqld, nginx, python, git, …).

**Docker Images:**

Là một template chỉ cho phép đọc, ví dụ một image có thể chứa hệ điều hành Ubuntu và web app. Images được dùng để tạo Docker container. Docker cho phép chúng ta build và cập nhật các image có sẵn một cách cơ bản nhất, hoặc bạn có thể download Docker images của người khác.

**Docker Container:**

Docker container giống với các directory.

Một Docker container giữ mọi thứ chúng ta cần để chạy một app. Mỗi container được tạo từ Docker image. 

Docker container có thể có các trạng thái run, started, stopped, moved và deleted.

**b.Cài đặt docker (trên linux)**

Bước 1: Xóa bỏ cài đặt docker cũ

`$ sudo apt --purge autoremove docker docker-engine docker.io containerd runc`

Bước 2: Cài đặt Docker trên Ubuntu

`$ sudo apt update && sudo apt install software-properties-common gnupg2 curl ca-certificates apt-transport-https`

Bước 3: Kiểm tra fingerprint GPG key của Docker.

`$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg`

fingerprint là: 9DC858229FC7DD38854AE2D88D81803C0EBFCD88.

Key được sử dụng để xác minh chữ ký điện tử, vì vậy người dùng có thể đảm bảo phần mềm đang cài đặt là hợp pháp và không phải là phần mềm độc hại được kẻ tấn công upload lên máy chủ.

Khi đã chắc chắn rằng mình có key đúng, hãy thêm nó vào các key đáng tin cậy của APT.

Bước 4:

`$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`

Bước 5: Thêm kho lưu trữ Docker cho Ubuntu vào nguồn phần mềm.

`$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stab`

Bước 6: Cài đặt Docker

`$ sudo apt update && sudo apt install docker-ce`

Nếu cần Docker Compose, người dùng có thể cài đặt nó với:

`$ sudo apt install docker-compose`

Bước 7: Để làm việc với Docker mà không cần nhập sudo ta cấu hình như sau:

`$ sudo setfacl -m user:$USER:rw /var/run/docker.sock`

Sau đó tiến hành run thử câu lênh: `$ docker ps`

c.Hello world với Docker

`$ sudo docker run hello-world`

Kết quả sẽ hiển thị: Hello from Docker!


### Docker architecture(Kiến trúc của Docker):

Docker Daemon 

Docker Client

Docker Registry

Docker Object:

     Image Registry

     Container Management
  


Tìm hiểu chi tiết thành phần:

Docker sử dụng kiến trúc client-server. Docker server (hay còn gọi là daemon) sẽ chịu trách nhiệm build, run, distrubute Docker container. Docker client và Docker server có thể nằm trên cùng một server hoặc khác server. Chúng giao tiếp với nhau thông qua REST API dựa trên UNIX sockets hoặc network interface.

**1.Docker Daemon (dockerd)**: là thành phần core, lắng nghe API request và quản lý các Docker object. Docker daemon host này cũng có thể giao tiếp được với Docker daemon ở host khác.

**2.Docker Client (docker)**: là phương thức chính để người dùng thao tác với Docker. Khi người dùng gõ lệnh docker 

run imageABC tức là người dùng sử dụng CLI (Command Line) và gửi request đến dockerd thông qua api, 

và sau đó Docker daemon sẽ xử lý tiếp.

Docker client có thể giao tiếp và gửi request đến nhiều Docker daemon.

**3.Docker registry**

Docker registry là một kho chứa các Image. Nổi tiếng nhất chính là Docker Hub, ngoài ra bạn có thể tự xây dựng một Docker registry cho riêng mình.

**Docker hub** là nơi lưu giữ và chia sẻ các file images (hiện có khoảng 300.000 images).

File images là Các files và settings được sử dụng trong container được lưu, sử dụng lại, gọi chung là images của docker.

Docker Hub là cộng đồng và có container lớn nhất thế giới. Các nhà phát triển doanh nghiệp có thể
truy cập hình ảnh chính thức và được chứng nhận từ các nguồn đáng tin cậy và cộng tác với cộng
đồng rộng lớn hơn để tăng tốc đổi mới.


**4.Docker Object:** gồm có Image và Container

Image là một template chỉ đọc sử dụng để chạy container.

Một image có thể dựa trên một image khác. Ví dụ bạn muốn tạo một image x ,tất nhiên x phải chạy trên linux ubuntu chẳng hạn. Khi đó image nginx trước hết sẽ phải base trên ubuntu trước đã.

Bạn có thể tự build image cho riêng mình hoặc tải các image có sẵn của người khác trên Docker registry.


Container được chạy dựa trên 1 image cụ thể. Bạn có thể tạo, start, stop, move, delete container.

Có thể kết nối các container với nhau .Container cô lập tài nguyên với host và các container khác.


## Các câu lệnh cơ bản:

`$docker`: List ra các option để tham khảo



### 1.Docker tag:


Khôi phục lại images từ IMAGE_ID

docker tag {iamge_id} {image_new_name}:{tag}

VD: $ docker tag 213r2677 hello-world/httpd:version1.0

Kết quả khi `$ docker images `sẽ là:

Repository: hello-world/httpd

Tag: version1.0

Và nó sẽ được cấp một Image ID mới




### 2.Docker run:

`$ docker run --name myadmin -d --link mysql_db_server:db -p 8080:80 -v /some/local/directory/config.user.inc.php:/etc/phpmyadmin/config.user.inc.php phpmyadmin/phpmyadmin`

--name: gán tên cho container

-d: Chạy container trên nền và hiển thị ID Container

--link: Liên kết link với các container khác

-v: Gán một volumn vào một container, cho phép chúng ta thao tác ở container nhưng dữ liệu vẫn được lưu trữ ở máy chủ. 

volume là một cơ chế dùng để lưu trữ dữ liệu sinh ra và sử dụng bởi Docker.

-- p 8080:80: Do các container của docker được gộp và chạy trong một network riêng nên nó sẽ độc lập với máy chủ 

mà docker chạy trên đó. Để mở các cổng của network các container và ánh xạ nó với cổng của máy host 

ta sử dụng tùy chọn --publish, -p. Hoặc sử dụng --publish-all, -P sẽ mở tất cả các cổng của container.




Restart policies (--restart): Khi mà khởi động lại một Container, nó sẽ show Up or Restarting trong **docker ps**

Mặc định là: No: Không tự động khởi động lại container khi nó bị đóng.


Clean up (--rm): Tự động xóa Container khi đóng command lại (Ctrl+C)





### Sự khác biệt của 2 lênh trên khi dùng Docker Client.

Lệnh **Docker run** thường sử dụng cho một Docker Container mới , tức là bạn sử dụng trong trường hợp chưa có Container và bạn muốn tạo một container mơí từ file image , khởi động nó và  và chạy tiến trình được gán cho docker đó.


Lệnh **Docker exec** thực hiện khi container đang chạy, để thay đổi và lấy thông tin từ Docker container đang chạy.

Exec sử dụng khi bạn muốn vận  hành 1 container đang chạy.



### 4.Docker push: Push một image hoặc một repository đến  Docker Hub

Bước 1: Để đẩy một hình ảnh đến Docker Hub hoặc bất kỳ đăng ký Docker nào khác, bạn phải có một tài khoản ở đó.
[https://hub.docker.com]

Bước 2: Để đẩy hình ảnh của bạn, trước tiên hãy đăng nhập vào Docker Hub.

`$ docker login -u docker-registry-username`

Lưu ý: Nếu tên người dùng đăng ký Docker của bạn khác với tên người dùng cục bộ bạn đã sử dụng để tạo hình ảnh, bạn sẽ phải gắn thẻ hình ảnh của mình với tên người dùng đăng ký. Đối với ví dụ được đưa ra ở bước cuối cùng, bạn sẽ gõ:

`$ docker tag trangtran/ubuntu-nodejs docker-registry-username/ubuntu-nodejs`

Bước 3: Sau đó, bạn có thể đẩy hình ảnh của chính mình bằng cách sử dụng:

`$ docker push docker-registry-username/docker-image-name`

VD: $ docker push trangtran97/ubuntu-nodejs


### 5.Docker pull: 

Hầu hết các image sẽ được tạo dựa trên các image cơ sở từ Docker Hub. Docker Hub chứa rất nhiều các image được dựng sẵn, mà ta có thể pull về và dùng mà không cần phải định nghĩa và cấu hình lại từ đầu. Để 

tải một image cụ thể hoặc một tập hợp image ta dùng docker pull.


`$ docker pull {image_name}`

VD: `$ docker pull mysql

Sau khi hình ảnh được tải xuống, bạn có thể chạy một container bằng hình ảnh được tải xuống  

Như bạn đã thấy với ví dụ hello-world, nếu hình ảnh chưa được tải xuống khi docker được thực thi với lệnh đang chạy, máy khách Docker trước tiên sẽ tải xuống hình ảnh, sau đó chạy một container bằng cách sử dụng nó.

Để xem images đã tải xuống:

`$ docker images`

Liệt kê các container đang chạy:

`$ docker ps `

Liệt kê các container đã tắt:

`$ docker ps -a` 

Hiện ra các images hiện có:

`$ docker images`

Xóa images dựa vào ID hoặc tên images:

`$ docker rmi [images_id/name]`

Xóa 1 container:

`$docker rm -f [Container_ID/name] `

Khởi động 1 container:

`$ docker start [new container_name]`  

Dừng 1 container đang chạy

`$ docker stop <id | name>`

– Dừng toàn bộ container đang chạy

`$ docker stop $(docker ps –a –q)`

 Truy cập vào một container đang chạy(Tạo mới container bằng cách chạy image ).

`$ docker exec -it [new container_name] bash` 

Sau đó thực hiện lệnh hiển thị thông tin Kernel của Ubuntu:

`#uname -a`


Liệt kê tất cả container và images: 

`$ docker container ls -all`

Chỉ liệt kê Image_ID: 

`$ docker container ls -aq`

Xem thông tin hệ thống của Docker: 

`$ docker info`

Liệt kê các option của Docker container [option] :

`$ docker container --help`

Để tìm images có sẵn trong Docker Hub : 

`$ docker search ubuntu`

Kiểm tra IP của container có 2 cách:

`$ docker inspect [container_name] | grep -i IP`

`$ docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name_or_id`

Kiểm tra log của 1 container

`$ docker logs < container_id | container_name>`

Liệt kê các volume mà container sử dụng, (Volume trong docker là cơ chế lưu trữ dữ liệu sinh ra, được dùng để chia sẻ dữ liệu cho c :ontainer

`$ docker volume ls`

 Khi bạn khởi động hình ảnh Docker, bạn có thể tạo, sửa đổi và xóa các tệp giống như bạn có thể với một máy ảo. 
 
 Những thay đổi mà bạn thực hiện sẽ chỉ áp dụng cho container đó. Bạn có thể bắt đầu và dừng nó, nhưng một khi bạn phá hủy nó bằng lệnh docker rm, các thay đổi sẽ bị mất hoàn toàn.

`$ docker commit -m "What you did to the image" -a "Author Name" [container_id repository/][image_name]`


Để xem lại lịch sử commit 

`$ docker history [image_name]`
  
  
 Xem các thay đổi trên container: (Kiểm tra các thay đổi đối với tệ hoặc thư mục trên hệ thống của container)
 
 `$ docker diff [container_name]`



## Kiến thức thêm

### Docker CE / Docker EE

**Docker CE**: Docker Comunity Edittion cung cấp dịch vụ cho các nhà phát triển, lập trình viên,...




**Docker EE**: Docker Engine Edittion cung cấp dịch vụ cho các doanh nghiệp

Docker EE có 3 cấp:

- Docker Enterprise basic tier (như  trình bày ở trên)
- Docker Enterprise standard tier : Được xây dựng dựa trên Docker Engine-Community với sự hỗ trợ của Docker Enterprise như đã mô tả trên nhưng có thêm UCP (universal control plane) bảo mật tích hợp với các kết nối LDAP và RBAC thông qua GUI hoặc CLI để quản lý các policy, định tuyến Layer-7, Docker Trusted Registry (private image registry gắn với mô hình bảo mật UCP)
Docker Enterprise advanced tier: Bao gồm tất cả các tính năng trong Docker Enterprise standard tier nhưng


### Docker Engine là một ứng dụng client-server. Có hai phiên bản Docker Engine phổ biến là:

Docker Community Edition (CE): Là phiên bản miễn phí và chủ yếu dựa vào các sản phầm nguồn mở khác. 

Docker Enterprise: Khi sử dụng phiên bản này bạn sẽ nhận được sự support của nhà phát hành, có thêm các tính năng quản lý và security.

### Docker Compose

Docker Compose là một công cụ giúp định nghĩa và chạy các ứng dụng Docker chạy trên nhiều containers thông qua các file cấu hình YAML. Thao tác sử dụng Compose thông qua ba bước:

B1: Định nghĩa môi trường ứng dụng Dockerfile cho tấyt cả các dịch vụ

B2: Tạo file docker-compose.yml định nghĩa tất cả các dịch vụ bên dưới ứng dụng

Thực thi lệnh docker-compose up để chạy dịch vụ

**Các tính năng của Compose:**

Nhiều môi trường cô lập trên một host: Compose cô lập môi trường thông qua project name

Lưu giữ volume data khi containers được khởi tạo: Compose tìm lại các volumes tương ứng của một containers ở lần khởi tạo trước đó và gán nó cho lần khởi tạo mới, đảm bảo dữ liệu cũ không bị mất đi

Chỉ tạo containers khi cần thiết: Compose tái sử dụng containers cũ khi chúng được khởi tạo lại

Lưu trữ biến trong Compose files và tái sử dụng cho nhiều môi trường

**Các ca sử dụng phổ biến:**

Triển khai môi trường
   
Tự động hóa kiểm thử môi trường

Triển khai trên Docker engine từ xa


**Cài đặt Compose**

`$ sudo curl -L "https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`

`$ sudo chmod +x /usr/local/bin/docker-compose`
    
**File Docker Compose**:

Ví dụ 1 file docker-compose.yml:

Cấu hình docker-compose version 3, một dịch vụ được thêm vào và được đặt tên là web

```
version:'3'

services:

    db:
    
        image: mysql
        
        container_name: mysql_db
        
        restart: always
        
        environment:
        
        - MYSQL_ROOT_PASSWORD="secret"
        
    web:
    
        image: apache
        
        build: .
        
        container_name: apache_web
        
        restart: always
        
        ports:
        
        - "8080:80"
```

**1 số command compose**

Build tất cả service

`$ docker-compose build`

Build 1 service

`$ docker com-pose build web`

Tạo docker container với các dịch vụ có sẵn trong docker-compose.yml. -d để khởi đọng container trong chế độ chạy ngầm

`$ docker-compose up -d web`

Để dừng và xóa container, network, images trong file config:

`$ docker-compose down web`

Bên cạnh đó còn có lệnh: ps, start, stop(dừng container đang chạy), restart, pause, unpause

**Ví dụ về Docker-compose:**

Tiến hành tạo 2 docker container: Mysql và Apache web server

Thư mục ứng dụng web: webapp

B1: Tạo cấu trúc thư mục

`$ mkdir dockercompose && cd dockercompose`

`$ mkdir webapp`

`$ echo "<h2>Loading...</h2>" > webapp/index.html` 

B2: Tạo Dockerfile cho webapp để tạo image tùy chỉnh cho ứng dụng bao  gồm Apache webserver

`$ vim webapp/Dockerfile`


```
FROM tecadmin/ubuntu-ssh:18.04


RUN apt-get update \

	&& apt-get install -y apache2


COPY index.html /var/www/html/

WORKDIR /var/www/html

CMD ["apachectl", "-D", "FOREGROUND"]

EXPOSE 80
```

B3: Tạo file docker compose: định nghĩa tất cả các container được dùng

`$ vim docker-compose.yml`

```
version:'3'

services:

    db:
    
        image: mysql
        
        container_name: mysql_db
        
        restart: always
        
        environment:
        
        - MYSQL_ROOT_PASSWORD="secret"
        
    web:
    
        image: apache
        
        build: ./webapp
        
        depend _on:
        
        - db
        
        container_name: apache_web
        
        restart: always
        
        ports:
        
        - "8080:80"
```

B4: Build webapp image

`$ docker-compose build`

B5: Khởi động docker compose

`$ docker-compose up -d`

Truy cập: 10.61.127.111:8080
        
  
### Docker CLI:

Đóng vai trò là client, cung cấp giao diện tương tác với người dùng (command lind) và gửi các request đến Docker Daemon

### Docker Daemon

Chạy trên host, đóng vai trò server, nhận các yêu cầu từ Docker Client và thực thi(build, run quản lý các containers và các thành phần liên quan)


### Docker Registry: 

Là nơi lưu trữ và chia sẻ các Docker Image.


### Docker network: Host, Bridge, Overlay, ...

Cung cấp private network (VLAN) để các container trên 1 host có thể liên lạc với nhau, hoặc các containers trên nhiều host có thể liên lạc với nhau (multi-host networking)

Docker cung cấp tùy chọn để tạo và quản lý network riêng giữa các container.

Liệt kê Docker network

`$ docker network ls`

Tạo docker network

`$ docker network create -d bridge [bridge_network_name]`

Kết nối container với network bằng cách sử dụng tên container hoặc ID

`$ docker network connect [bridge_network_name][name/ID_container]`

Kiểm tra Docker network vừa kết nối:

`$ docker network inspect lan1`

Ngắt kết nối docker khỏi network

`$ docker network disconect lan1 [name/ID_container]`

Xóa docker network

`$ docker network rm lan1`

Xóa tất cả network không sử dụng khỏi system host:

`$ docker network prune`



### Docker Volume:

Dùng để lưu trữ các dữ liệu độc lập với vòng đời của container giúp cho các containers có thể chia sẻ dữ liệu với nhau và với host

Giúp giữ lại dữ liệu khi một container bị xóa.

**Sử dụng volumn để gắn (mount)một thư mục nào đó trong host với container**

B1: Tạo thư mục trên host

`$ mkdir /var/datahost`

B2: Khởi tạo container và chỉ ra volume được gắn

`$ docker run -it -v /var/datahost ubuntu`

Kiểm tra bằng việc tạo dữ liệu trên host và kiểm tra ở container

**Sử dụng volume để chia sẻ dữ liệu giữa host và container sử dụng bind mount**

Cụ thể là thư mục gắn trên máy host sẽ được mount với thư mục trên container, dữ liệu sinh ra trong thư mục được mount của container 

sẽ xuất hiện trong thư mục của host

B1: Tạo thư mục bindthis trên host (chứa container)

`$ mkdir bindthis`

B2: Thư mục /root/bindthis này sẽ được mount với thư mục /var/www/html/webapp nằm trên container.

`$ docker run -it -v $(pwd)/bindthis/:/var/www/html/webapp ubuntu bash`

B3: Tạo ra 1 file trong thư mục /var/www/html/webapp trên container.

`$ touch /var/www/html/webapp/index.html`

`$ exit`

B4: Kiểm tra xem trong thư mục /root/bindthis trên host có hay không.

`$ ls bindthis/`

Hien thi file **index.html**

**Chia se du lieu giua cac container sử dụng volume**

B1: Tạo mới 1 volume có tên : Datavolume1

`$ docker volume create --name Datavolume1`

B2: Thực hiện run Datavolume1 trong thư mục : /datavolume1(thư mục hệ thống tự tạo khi sử dụng -v ):

`$ docker run -ti --rm -v DataVolume1:/datavolume1 ubuntu`

Kiểm tra volume vừa run:

`$ docker volume inspect Datavolume1`

Start volume: Datavolume1

`$ docker run --rm -ti -v DataVolume1:/datavolume1 ubuntu`

B3: Tao 1 container moi: container1 có gắn volume vừa tạo

`$ docker run -ti --name=container1 -v Datavolume1:/datavolume1 ubuntu`

Thử tạo 1 file ex1.txt:

`$ echo "Example1.txt" > /datavolume1/ex1.txt`


B2: Tạo container khác sử dụng container volumecontainer làm volume. Khi đó, mọi sự thay đổi trên container mới sẽ được cập nhật trong container volumecontainer:

`$ docker run -ti --name=Container2 --volumes-from Container2 ubuntu `

Thực hiện **cat /datavolume1/ex1.txt** sẽ có output

Thử thêm 1 file ex2.txt rồi vào lại Container1 sẽ có file ex2.txt

Thực hiện xóa Container1 thì dữ liệu vẫn còn trên Container2

-t: chạy terminal trong container

-i: tạo các kết nối tương tác

B3: Tạo ra 1 tập tin trong /trangtran:

`#echo "My name is Trang" > /trangtran/index.html`

`#exit`

B4: Chạy lại container đã tạo ban đầu và thực hiện cat /trangtran/index.html:

`$ docker run -t -i --volumes-from volumecontainer ubuntu /bin/bash`

Sau khi cat sẽ có thông tin đầu ra: "My name is Trang"

Thử thêm file trong container vừa tạo rồi quay lại check container lúc đầu

`$ docker start -ai [container_name]`

**Backup và Restore volume**

Backup thư mục volume là /trangtran trong container volumecontainer và nén lại dưới dạng file backup.tar.

`$ docker run --rm --volumes-from volumecontainer -v $(pwd):/backup ubuntu tar cvf /backup/backup.tar /trangtran`

Restore: Tạo volume /trangtran trên container data-container

`$ docker run -v /trangtran --name data-container ubuntu /bin/bash`

Tạo container thực hiện nhiệm vụ giải nén file backup.tar vào thư mục /trangtran.

Container này có liên kết với container data-container ở trên.

`$ docker run --rm --volumes-from data-container -v $(pwd):/backup ubuntu bash -c "cd /trangtran && tar -zxvf /backup/backup.tar"`



### Cấu hình proxy

Cấu hình Docker để sử dụng proxy

Nếu container của bạn cần sử dụng HTTP, HTTPS, FTP proxy, bạn có thể cấu hình:

1.Nếu Docker 17.07  trở lên, bạn có thể định cấu hình máy khách Docker để tự động chuyển thông tin proxy đến các container

2.Nếu Docker 17.06 trở xuống bạn phải đặt các biến môi trường phù hợp trong container, có thể làm vậy khi build 1 image hoặc thêm hoặc chyaj container



**Cho docker daemon**

B1: Start Docker Daemon

`$ sudo systemctl start docker`

B2: Cài đặt cờ theo dõi **daemon.json**

```
{
    "data-root": "/mnt/docker-data",
    
    "storage-driver": "overlay2"
}
```

Docker Daemon thường sử dụng các biến môi trường HTTP_PROXY, HTTPS_PROXY, và NO_PROXY để cấu hình hành vi của HTTP và HTTPS

Chúng ta không thể cấu hình các biến môi trường theo file daemon.json

Nếu bạn đứng sau máy chủ proxy HTTP , HTTPS, ta cần thêm cấu hình vào tập hệ thống của Docker

B1: Tạo thư mục cho dịch vụ Docker

`$ sudo mkdir -p /etc/systemd/system/docker.service.d`

B2: Thêm 1 file gọi **/etc/systemd/system/docker.service.d/http-proxy.conf** có thể thêm biến môi trường HTTP_PROXY:

`$ cd /etc/systemd/system/docker.service.d`

`$ sudo vim /etc/systemd/system/docker.service.d/http-proxy.config`

[Service]

Environment="HTTP_PROXY=http://proxy.example.com:80/"

`$ sudo vim /etc/systemd/system/docker.service.d/https-proxy.config`

[Service]

Environment="HTTPS_PROXY=https://proxy.example.com:443/"

Nếu có đăng kí Docker nội bộ, bạn có thể chỉ định chúng thông qua biến môi trường NO_PROXY để liên hệ mà không cần ủy quyền.

Biến NO_PROXY chỉ định một chuỗi chứa các giá trị được phân tách bằng dấu phẩy cho các máy chủ nên được loại trừ khỏi proxy. 

Đây là các tùy chọn bạn có thể chỉ định để loại trừ máy chủ:

1.Tiền tố địa chỉ IP: 1,2,3,4

2.Tên miền, nhãn DNS đặc biệt

3.Dấu * chỉ ra không thực hiện ủy quyền

4.Số cổng bằng chữ được chấp nhận bởi tiền tố địa chỉ IP ( 1.2.3.4:80 ) và tên miền ( foo.example.com:80 )


File cấu hình HTTP:

[Service]    

Environment= "NO_PROXY=localhost,127.0.0.1,registry.me"



B3: `$ sudo systemctl daemon-reload`

B4: `$ sudo systemctl restart docker`

B5: `$ systemctl show --property=Environment docker`


**Docker client**

B1: Trên máy khách Docker, tạo hoặc chỉnh sửa file `~/.docker/config.json`:

```
{

 "proxies":
 
 {
 
   "default":
   
   {
   
     "httpProxy": "http://127.0.0.1:3001",
     
     "httpsProxy": "http://127.0.0.1:3001",
     
     "noProxy": "*.test.example.com,.example2.com"
     
   }
   
 }
 
}
```
Lưu tập tin

B2: Khi tạo hoặc start 1 container mới, các biến môi trường tự động cập nhật vào container.

 
### Cấu hình trusted registry trong daemon.json

Mặc dù rất khuyến khích để bảo mật sổ đăng ký của bạn bằng chứng chỉ TLS do CA đã biết, bạn có thể 

chọn sử dụng chứng chỉ tự ký hoặc sử dụng sổ đăng ký của mình qua kết nối HTTP không được mã hóa. Một 

trong những lựa chọn này liên quan đến sự đánh đổi bảo mật và các bước cấu hình bổ sung.

#### Triển khai 1 sổ đăng kí HTTP đơn giản

Nếu muốn tạo một kho chứa Image riêng , để thực hiện cập nhật và lấy các Image từ đó thì ta sử dụng image registry:2

Cổng dịch vụ:5000 - là cổng mặc định mà docker registry đăng kí

Phiên bản có thể dùng: registry:2.7

Dữ liệu lưu tại /var/lib/registry (biết để muốn ánh xạ đĩa)

Giả sử máy host đang có địa chỉ IP là 127.0.0.1, cài đặt để Docker truy cập được đến kho ở địa chỉ 127.0.0.1:5000, 

có thiết lập để lưu dữ liệu cố định ở máy host /home/registry/


`$ docker run -d -p 5000:5000 --restart=always -v /home/registry:/var/lib/registry registry:2.7`


Chỉnh sửa tệp **/etc/docker/daemon.json**

```
{
  "insecure-registries" : ["127.0.0.1:5000",
                           "registry.me:5000"],
                           
                           "debug" : true,
                           "experimental" : false
}
```
hoặc đăng kí theo tên: registry.me:5000 / localhost:5000


Khi đăng ký không an toàn được bật, Docker thực hiện các bước sau:

Trước tiên, hãy thử sử dụng HTTPS.

Nếu HTTPS không có sẵn, hãy quay lại HTTP.

Khởi động lại Docker để những thay đổi có hiệu lực.

Lặp lại các bước này trên mỗi máy chủ Engine muốn truy cập vào sổ đăng ký của bạn.


**Push / Pull từ registry riêng**

Để đẩy 1 Image đang có ở máy local lên kho riêng có địa chỉ **registry.me:5000**thì cần đặt tên image phần tên có chứa địa chỉ ở đầu

`addr/nameimage:tag`

Giả sử pull 1 Image về sẽ có ID, thực hiện đổi image này thành tên: registry.me:5000/trangtran

`$ docker tag [Image_ID] registry.me:5000/trangtran`

Rồi push lên registry đã đăng kí:

`$ docker push registry.me:5000/trangtran`

Có thể pull lại bất kỳ lúc nào:

`$ docker pull registry.me:5000/trangtran`

Muốn truy caạp vào registry đã đăng kí:

Trên của sổ Chrome gõ: registry.me:5000/v2/_catalog

#### Sử dụng chứng chỉ tự kí

An tòan hơn so với phương pháp kí không an toàn

B1: Tạo chứng chỉ riêng mình

`$ mkdir -p certs`

`$ openssl req -newkey rsa:4096 -nodes -sha256 -keyout certs/domain.key -x509 -days 365 -out certs/domain.crt`

B2: Sử dụng kết quả để bắt đầu đăng ký của bạn với TLS được kích hoạt 

B3: Hướng dẫn cho mọi Docker Daemon tin tưởng vào chứng chỉ

Copy domain.crt vào /etc/docker/cert.d/myregistrydomain.com:5000/ca.crt trên mỗi máy chủ Docker.

Bạn không cần phải khởi động lại Docker.


#### Khắc phục sự cố đăng kí không an toàn

```
FATA[0000] Error response from daemon: v1 ping attempt failed with error:
Get https://myregistrydomain.com:5000/v1/_ping: tls: oversized record received with length 20527.
If this private registry supports only HTTP or HTTPS with an unknown CA certificate, add
`--insecure-registry myregistrydomain.com:5000` to the daemon's arguments.
In the case of HTTPS, if you have access to the registry's CA certificate, no need for the flag;
simply place the CA certificate at /etc/docker/certs.d/myregistrydomain.com:5000/ca.crt
```
Khi sử dụng xác thực, một số phiên bản của Docker  yêu cầu bạn tin tưởng vào chứng chỉ hệ điều hành

Đối với Ubuntu:

`$ cp certs/domain.crt /usr/local/share/ca-certificates/myregistrydomain.com.crt` 

`$ update-ca-certificates`



