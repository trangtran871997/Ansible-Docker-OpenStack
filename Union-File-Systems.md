


### Docker image

Nói qua lại Docker thì Docker được viết bằng `Go`, và tận dụng các tính năng của Linux để tạo nên các tính năng coi như là tuyệt vời của Docker: đóng gói ứng dụng trong docker image, triển khai ứng dụng trong container, ...
- Namespace: Docker sử dụng các namespace trong Linux như `pid, net, ipc, mnt, uts namespaces` để tạo ra `isolated workspace` hay còn được gọi là container để ứng dụng chạy trong đó. Mỗi khi tạo container mới thì docker sẽ tạo ra các namespace riêng biệt cho container đó.
- Controls Group: còn được gọi là `cgroup`. Docker dùng cgroup để limit tài nguyên được phép sử dụng của các ứng dụng bên trong container. Ví dụ: memory: memory-swap, memory-swappines, kernel-memory; cpu: cpus, cpu-quota, cpu-shares ... 
- Union file systems: là các `file systems` được `union mount` (giải thích phía dưới) lại với nhau, hoạt động bằng cách `conbined` nội dụng của các folder/files lại với nhau trong 1 folder duy nhất. Docker engine sử dụng các `implemented của Union file systems` như: `UnionFS, authFS, overlayFS, vfs ...` để lưu docker images thành các layers, hoặc để tạo ra các layers (block) cho các containers.
- Container format: Docker engine kết hợp namespace, cgroup và UnionFS tạo ra cái gọi là `container format`. Mặc định thì container format là `libcontainer` và hiện tại thì Docker cũng mới chỉ support format này.

Trong bài viết này sẽ tìm hiểu về `Union file systems` với mục tiêu cụ thể là:
- UnionFS là cái méo gì
- Nó giúp Docker lưu trữ các image hiệu quả dư lào


### Union mount

Full định nghĩa trên wiki [1]
> In computer operating systems, union mounting is a way of combining multiple directories into one that appears to contain their combined contents. Union mounting is supported in Linux, BSD and several of its successors, and Plan 9, with similar but subtly different behavior.

Theo định nghĩa trên thì nôm na `Union mount` cho phép ta trộn các file systems trong các folders khác nhau, tạo ra 1 folder `merged + chứa` toàn bộ nội dung của các file systems trong các folders đó. Trên Linux thì `Union mounting` được implemented từ bản 0.99 vào năm 1993, với tên gọi là `Inheriting File System`, nhưng sau đó đã không được phát triển tiếp vì sự phức tạp của nó. Phiên bản implement tiếp theo có tên là `UnionFS` , sau đó là đến `AUFS (Another Union File System)` được released vào năm 2006 và tiếp theo là `OverlayFS` trong năm 2009. Đến tận 2014 thì `OverlayFS` mới chính thức được thêm vào code base của Linux kernel ở bản 3.18 [2].

Việc `Union file systems` phải mất rất lâu mới được thêm vào mainline Linux kernel thì mình nghĩ là do tính phức tạp và mức độ stable của các bản implement của nó. Ví dụ một số vấn đề cần giải quyết trong việc implement `Union file systems`:
- Việc duplicate file name trong cùng 1 folder là không được phép, do đó khi merged các files này thì cần có thứ tự logic ưu tiên hợp lý.
- Việc xóa 1 file: cần phải xóa file đó ở tất cả các  `union directory's constituents`, vì chỉ cần sót ở 1 folder thì khi thực hiện combine, file đó sẽ lại xuất hiện trở lại.
- Tương tự như với hành động: rename
- Ngoài ra: stable inode numbers for files, hard links and memory-mapped I/O (mmap) are hard to implement correctly

Ok sau khi đã sơ qua về khái niệm thì cùng đi thực hành xem `union mount` nó như nào, cụ thể là sử dụng `OverlayFS`.

**Kiểm tra các filesystem type mà OS ta đang cài support**

```shell
donghm@donghm:~/docker/overlay$ lsb_release -a
Distributor ID:	Ubuntu
Description:	Ubuntu 18.04.1 LTS
Release:	18.04
Codename:	bionic

donghm@donghm:~/docker/overlay$ cat /proc/filesystems
nodev	sysfs
nodev	rootfs
nodev	ramfs
nodev	bdev
nodev	proc
nodev	cpuset
nodev	cgroup
nodev	cgroup2
nodev	tmpfs
nodev	devtmpfs
nodev	configfs
nodev	debugfs
nodev	tracefs
nodev	securityfs
nodev	sockfs
nodev	dax
nodev	bpf
nodev	pipefs
nodev	hugetlbfs
nodev	devpts
		ext3
		ext2
		ext4
		squashfs
		vfat
nodev	ecryptfs
		fuseblk
nodev	fuse
nodev	fusectl
nodev	pstore
nodev	mqueue
		btrfs
nodev	autofs
nodev	binfmt_misc
nodev	overlay
nodev	aufs
		xfs
		jfs
		msdos
		ntfs
		minix
		hfs
		hfsplus
		qnx4
		ufs
```

Hiện thư mục gốc có 2 folders với tree như dưới
```
$ pwd
/home/donghm/docker

$ tree
.
├── sdb
│   ├── dir1
│   │   └── file_b1
│   ├── file1
│   └── link1 -> file1
└── sdc
    ├── dir1
    │   └── file_c1
    ├── dir4
    └── link1 -> dir4
```

**Tạo 3 thư mục sau**
```
$ cd /home/donghm/docker
$ mkdir diff workdir merged
```

Tiến hành `union mount` 2 folders `sdb` và `sdc` lại vào thư mục `merged` (Xem cách mount overlayfs ở [mount man page](http://man7.org/linux/man-pages/man8/mount.8.html))
```
$ sudo mount \
     -t overlay overlay \
     -o lowerdir=/home/donghm/docker/sdb:/home/donghm/docker/sdc,upperdir=/home/donghm/docker/diff,workdir=/home/donghm/docker/workdir \
     /home/donghm/docker/merged

// Kiểm tra mount
$ mount | grep overlay
overlay on /home/donghm/docker/merged type overlay (rw,relatime,lowerdir=/home/donghm/docker/sdb:/home/donghm/docker/sdc,upperdir=/home/donghm/docker/diff,workdir=/home/donghm/docker/workdir)

// tree lại cái xem coi nào
$ tree
.
├── diff
├── merged
│   ├── dir1
│   │   ├── file_b1
│   │   └── file_c1
│   ├── dir4
│   ├── file1
│   └── link1 -> file1
├── sdb
│   ├── dir1
│   │   └── file_b1
│   ├── file1
│   └── link1 -> file1
├── sdc
│   ├── dir1
│   │   └── file_c1
│   ├── dir4
│   └── link1 -> dir4
└── workdir
    └── work [error opening dir]
```
Như kết quả trên thì overlay đã kết hợp được 2 file trong dir1 của cả sdb và sdb để đưa vào `merged/dir1`. Nhưng với `link1` do trùng tên và thấy là hiện trong `merged/link1 -> file1` nên là overlayfs đã ưu tiên lấy link1 trong sdb.

Ok giờ ta thử tạo ra 1 vài thay đổi xem:
```
donghm@donghm:~/docker$ echo "New content after merging to c1" >> merged/dir1/file_c1
donghm@donghm:~/docker$ cat merged/dir1/file_c1
File c1 in sdc
New content after merging to c1

donghm@donghm:~/docker$ cat sdc/dir1/file_c1 
File c1 in sdc

donghm@donghm:~/docker$ touch merged/dir4/new_file
donghm@donghm:~/docker$ tree
.
├── diff
│   ├── dir1
│   │   └── file_c1
│   └── dir4
│       └── new_file
├── merged
│   ├── dir1
│   │   ├── file_b1
│   │   └── file_c1
│   ├── dir4
│   │   └── new_file
│   ├── file1
│   └── link1 -> file1
├── sdb
│   ├── dir1
│   │   └── file_b1
│   ├── file1
│   └── link1 -> file1
├── sdc
│   ├── dir1
│   │   └── file_c1
│   ├── dir4
│   └── link1 -> dir4
└── workdir
    └── work [error opening dir]

donghm@donghm:~/docker$ echo "New file in diff folder" > diff/file2
donghm@donghm:~/docker$ tree merged/
merged/
├── dir1
│   ├── file_b1
│   └── file_c1
├── dir4
│   └── new_file
├── file1
├── file2
└── link1 -> file1
```

Như vậy mọi thay đổi ta tạo ra trong folder `merged` thì đều nhảy vô folder `diff` và không có thay đổi nào được apply xuống các base folders (trong option `lowerdir`) cả, và ngược lại.

Còn thử thay đổi nội dung file ở trong 1 base folders thì không thấy được update lên `/merged`, nhưng tạo 1 file mới thì lại có được update.

```
donghm@donghm:~/docker$ touch sdc/test
donghm@donghm:~/docker$ tree
.
├── diff
│   ├── dir1
│   │   └── file_c1
│   ├── dir4
│   │   └── new_file
│   └── file2
├── merged
│   ├── dir1
│   │   ├── file_b1
│   │   └── file_c1
│   ├── dir4
│   │   └── new_file
│   ├── file1
│   ├── file2
│   ├── link1 -> file1
│   └── test
├── sdb
│   ├── dir1
│   │   └── file_b1
│   ├── file1
│   └── link1 -> file1
├── sdc
│   ├── dir1
│   │   └── file_c1
│   ├── dir4
│   ├── link1 -> dir4
│   └── test
└── workdir
    └── work [error opening dir]

donghm@donghm:~/docker$ echo "hihi" >> sdc/dir1/file_c1

donghm@donghm:~/docker$ cat sdc/dir1/file_c1 
File c1 in sdc
hihi

donghm@donghm:~/docker$ cat merged/dir1/file_c1 
File c1 in sdc
New content after merging to c1

donghm@donghm:~/docker$ cat diff/dir1/file_c1 
File c1 in sdc
New content after merging to c1
```

### Docker image sử dụng overlayFS để lưu images như nào

**Hình ảnh minh họa**

![docker_image_layers](https://docs.docker.com/storage/storagedriver/images/container-layers.jpg)

Ví dụ với container rabbitmq

```
$ docker start rabbitmq

$ docker ps 
CONTAINER ID        IMAGE   			....
6e15dc2c8069        rabbitmq 			....

// check mount
$ mount | grep overlay
overlay on /var/lib/docker/overlay2/d78d0a316a70e3be5750d0e0087c9a5f834706a9cf5c6b766b86762eca52145b/merged type overlay (rw,relatime,lowerdir=/var/lib/docker/overlay2/l/B5YU3UAP3RST4SC5W4LSGXZ7P5:/var/lib/docker/overlay2/l/EWATLAGQXBAXQ57ZRNISD26IQ6:/var/lib/docker/overlay2/l/ZEBGKBUQQCS5WCDPV3XV6N3QAG:/var/lib/docker/overlay2/l/IKEN4LVQCP3UUDBFMM73T6ST3L:/var/lib/docker/overlay2/l/5MI3U6RKYQ45TJFCJPZLJJMSOV:/var/lib/docker/overlay2/l/HRCRLAL7KWY6EPWJTCTJGBTK4H:/var/lib/docker/overlay2/l/PVSGTFWZNFRV33DH2DLCAPOPDN:/var/lib/docker/overlay2/l/NOQVDOMNE3FD3WVCSCAJ5YXFIX:/var/lib/docker/overlay2/l/5RPHMDX5MPANJF3V7JIMXU2V6Y:/var/lib/docker/overlay2/l/O34XZQ6LX7S6MGJLQYZ6TJWJAO:/var/lib/docker/overlay2/l/QDMURQXEYMQTVDYL4PHVISGOIT,upperdir=/var/lib/docker/overlay2/d78d0a316a70e3be5750d0e0087c9a5f834706a9cf5c6b766b86762eca52145b/diff,workdir=/var/lib/docker/overlay2/d78d0a316a70e3be5750d0e0087c9a5f834706a9cf5c6b766b86762eca52145b/work)
```


[1] https://en.wikipedia.org/wiki/Union_mount
[2] https://www.phoronix.com/scan.php?page=news_item&px=MTc5OTc
[3] https://medium.com/@jessgreb01/digging-into-docker-layers-c22f948ed612
