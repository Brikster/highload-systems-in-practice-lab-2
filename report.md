# Linux Архитектура и файловые системы

# Задание 1. Kernel and Module Inspection

1. Продемонстрировать версию ядра вашей ОС.
```bash
root@hiplet-48656:~# uname -a
Linux hiplet-48656 6.8.0-35-generic #35-Ubuntu SMP PREEMPT_DYNAMIC Mon May 20 15:51:52 UTC 2024 x86_64 x86_64 x86_64 GNU/Linux
```

2. Показать все загруженные модули ядра.
```bash
root@hiplet-48656:~# lsmod
Module                  Size  Used by
tls                   151552  0
8021q                  45056  0
garp                   20480  1 8021q
mrp                    20480  1 8021q
stp                    12288  1 garp
llc                    16384  2 stp,garp
binfmt_misc            24576  1
nls_iso8859_1          12288  1
kvm_intel             487424  0
kvm                  1437696  1 kvm_intel
irqbypass              12288  1 kvm
joydev                 32768  0
input_leds             12288  0
serio_raw              20480  0
dm_multipath           45056  0
msr                    12288  0
efi_pstore             12288  0
nfnetlink              20480  2
dmi_sysfs              24576  0
ip_tables              36864  0
x_tables               69632  1 ip_tables
autofs4                57344  2
btrfs                2019328  0
blake2b_generic        24576  0
raid10                 73728  0
raid456               196608  0
async_raid6_recov      20480  1 raid456
async_memcpy           16384  2 raid456,async_raid6_recov
async_pq               20480  2 raid456,async_raid6_recov
async_xor              16384  3 async_pq,raid456,async_raid6_recov
async_tx               16384  5 async_pq,async_memcpy,async_xor,raid456,async_raid6_recov
xor                    20480  2 async_xor,btrfs
raid6_pq              126976  4 async_pq,btrfs,raid456,async_raid6_recov
libcrc32c              12288  2 btrfs,raid456
raid1                  57344  0
raid0                  24576  0
hid_generic            12288  0
crct10dif_pclmul       12288  1
crc32_pclmul           12288  0
polyval_clmulni        12288  0
polyval_generic        12288  1 polyval_clmulni
ghash_clmulni_intel    16384  0
sha256_ssse3           32768  0
sha1_ssse3             32768  0
usbhid                 77824  0
psmouse               217088  0
hid                   184320  2 usbhid,hid_generic
virtio_gpu             94208  0
virtio_rng             12288  0
virtio_dma_buf         12288  1 virtio_gpu
floppy                131072  0
aesni_intel           356352  0
crypto_simd            16384  1 aesni_intel
cryptd                 28672  2 crypto_simd,ghash_clmulni_intel
```

3. Отключить автозагрузку модуля cdrom.
```bash
root@hiplet-48656:~# bash -c 'echo "blacklist cdrom" > /etc/modprobe.d/blacklist-cdrom.conf'
root@hiplet-48656:~# update-initramfs -u
update-initramfs: Generating /boot/initrd.img-6.8.0-35-generic
```

4. Найти и описать конфигурацию ядра (файл конфигурации, параметр CONFIG_XFS_FS).
```bash
root@hiplet-48656:~# ls -l /boot/config-$(uname -r)
-rw-r--r-- 1 root root 287440 May 20  2024 /boot/config-6.8.0-35-generic

root@hiplet-48656:~# grep CONFIG_XFS_FS /boot/config-$(uname -r)
CONFIG_XFS_FS=m
```
Параметр отвечает за модуль файловой системы XFS. Значение "m" значит, что XFS скомпилирован как загружаемый модуль.

# Задание 2. Наблюдение за VFS
```bash
root@hiplet-48656:~# strace -e trace=openat,read,write,close cat /etc/os-release > /dev/null
openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
close(3)                                = 0
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\220\243\2\0\0\0\0\0"..., 832) = 832
close(3)                                = 0
openat(AT_FDCWD, "/usr/lib/locale/locale-archive", O_RDONLY|O_CLOEXEC) = 3
close(3)                                = 0
openat(AT_FDCWD, "/usr/share/locale/locale.alias", O_RDONLY|O_CLOEXEC) = 3
read(3, "# Locale name alias data base.\n#"..., 4096) = 2996
read(3, "", 4096)                       = 0
close(3)                                = 0
openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_IDENTIFICATION", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_IDENTIFICATION", O_RDONLY|O_CLOEXEC) = 3
close(3)                                = 0
openat(AT_FDCWD, "/usr/lib/x86_64-linux-gnu/gconv/gconv-modules.cache", O_RDONLY|O_CLOEXEC) = 3
close(3)                                = 0
openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_MEASUREMENT", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_MEASUREMENT", O_RDONLY|O_CLOEXEC) = 3
close(3)                                = 0
openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_TELEPHONE", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_TELEPHONE", O_RDONLY|O_CLOEXEC) = 3
close(3)                                = 0
openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_ADDRESS", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_ADDRESS", O_RDONLY|O_CLOEXEC) = 3
close(3)                                = 0
openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_NAME", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_NAME", O_RDONLY|O_CLOEXEC) = 3
close(3)                                = 0
openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_PAPER", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_PAPER", O_RDONLY|O_CLOEXEC) = 3
close(3)                                = 0
openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_MESSAGES", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_MESSAGES", O_RDONLY|O_CLOEXEC) = 3
close(3)                                = 0
openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_MESSAGES/SYS_LC_MESSAGES", O_RDONLY|O_CLOEXEC) = 3
close(3)                                = 0
openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_MONETARY", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_MONETARY", O_RDONLY|O_CLOEXEC) = 3
close(3)                                = 0
openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_COLLATE", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_COLLATE", O_RDONLY|O_CLOEXEC) = 3
close(3)                                = 0
openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_TIME", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_TIME", O_RDONLY|O_CLOEXEC) = 3
close(3)                                = 0
openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_NUMERIC", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_NUMERIC", O_RDONLY|O_CLOEXEC) = 3
close(3)                                = 0
openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_CTYPE", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_CTYPE", O_RDONLY|O_CLOEXEC) = 3
close(3)                                = 0
openat(AT_FDCWD, "/etc/os-release", O_RDONLY) = 3
read(3, "PRETTY_NAME=\"Ubuntu 24.04.3 LTS\""..., 131072) = 400
write(1, "PRETTY_NAME=\"Ubuntu 24.04.3 LTS\""..., 400) = 400
read(3, "", 131072)                     = 0
close(3)                                = 0
close(1)                                = 0
close(2)                                = 0
```

Основные строки здесь:
```bash
openat(AT_FDCWD, "/etc/os-release", O_RDONLY) = 3
read(3, "PRETTY_NAME=\"Ubuntu 24.04.3 LTS\""..., 131072) = 400
write(1, "PRETTY_NAME=\"Ubuntu 24.04.3 LTS\""..., 400) = 400
read(3, "", 131072)                     = 0
```
* Мы открываем файл в режиме read-only (O_RDONLY), затем читаем файл и получаем из него 400 байт.
* На следующей строке ВИДИМ write на 400 байт; т.к. пишем в `/dev/null`, то данные отбрасываются и фактически мы никуда не пишем. Тем не менее, вызов здесь отображается.
* На строке `read(3, "", 131072)` прочитали 0 байт, т.е. достигнут конец файла.

Также можно попробовать сделать перенаправление не в `/dev/null`, а в файл. Там действительно не будет write вызова, т.к. там используется оптимизация и данные копируются напрямую из /etc/os-release в файл (например: `cat /etc/os-release > test.txt`). В нашем случае это выглядит так:
```bash
copy_file_range(3, NULL, 1, NULL, 9223372035781033984, 0) = 400
copy_file_range(3, NULL, 1, NULL, 9223372035781033984, 0) = 0
```

Чтобы появился вызов write, можно, например, использовать pipe: `cat /etc/os-release | cat > test.txt`.
  
# Задание 3. LVM Management
Для решения заданий я использую виртуальный сервер (VDS), а не виртуальную машину, поэтому вместо создания виртуального диска я создам loopback устройство.

## Создание устройство
```bash
root@hiplet-48656:~# fallocate -l 2G /disk.img
root@hiplet-48656:~# losetup -fP /disk.img
root@hiplet-48656:~# losetup -a
/dev/loop0: [2049]:1617 (/disk.img)
```

## Выполнение задания
1. Создадим физический том и группу томов
```bash
root@hiplet-48656:~# pvcreate /dev/loop0
  Physical volume "/dev/loop0" successfully created.
root@hiplet-48656:~# vgcreate vg_highload /dev/loop0
  Volume group "vg_highload" successfully created
```
2. Создадим логические тома
```bash
root@hiplet-48656:~# lvcreate -L 1200M -n data_lv vg_highload
  Logical volume "data_lv" created.
root@hiplet-48656:~# lvcreate -l 100%FREE -n logs_lv vg_highload
  Logical volume "logs_lv" created.
```

3. Отформатируем их
```bash
root@hiplet-48656:~# mkfs.ext4 /dev/vg_highload/data_lv
mke2fs 1.47.0 (5-Feb-2023)
Discarding device blocks: done                            
Creating filesystem with 307200 4k blocks and 76800 inodes
Filesystem UUID: 36b0fe78-3483-448a-b435-3627137321d2
Superblock backups stored on blocks: 
        32768, 98304, 163840, 229376, 294912

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done 

root@hiplet-48656:~# mkfs.xfs /dev/vg_highload/logs_lv
meta-data=/dev/vg_highload/logs_lv isize=512    agcount=4, agsize=54016 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=1
         =                       reflink=1    bigtime=1 inobtcount=1 nrext64=0
data     =                       bsize=4096   blocks=216064, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=16384, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
Discarding blocks...Done.
```

4. Создадим папки и примонтируем их к томам
```bash
root@hiplet-48656:~# mkdir -p /mnt/app_data /mnt/app_logs
root@hiplet-48656:~# mount /dev/vg_highload/data_lv /mnt/app_data
root@hiplet-48656:~# mount /dev/vg_highload/logs_lv /mnt/app_logs
```

5. Посмотрим на результат
```
root@hiplet-48656:~# lsblk
NAME                  MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0                   7:0    0    2G  0 loop 
├─vg_highload-data_lv 252:0    0  1.2G  0 lvm  /mnt/app_data
└─vg_highload-logs_lv 252:1    0  844M  0 lvm  /mnt/app_logs
sda                     8:0    0   10G  0 disk 
├─sda1                  8:1    0    9G  0 part /
├─sda14                 8:14   0    4M  0 part 
├─sda15                 8:15   0  106M  0 part /boot/efi
└─sda16               259:0    0  913M  0 part /boot
```

# Задание 4. Использование pseudo filesystem

1. Извлечём модель процессора и объём ОЗУ
```bash
root@hiplet-48656:~# cat /proc/cpuinfo | grep "model name"
model name      : Intel Core Processor (Broadwell, IBRS)
root@hiplet-48656:~# cat /proc/meminfo | grep MemTotal
MemTotal:         984736 kB
```

2. Получим PPid для процесса нашего шелла
```bash
root@hiplet-48656:~# cat /proc/$$/status | grep PPid
PPid:   1364
```

\$\$ - это специальная переменная в bash, которая возвращает PID (process ID) текущего процесса терминала. 

Также можем посмотреть, что шелл запущен через SSH:
```bash
root@hiplet-48656:~# ps -p 1364 -o pid,ppid,cmd
    PID    PPID CMD
   1364       1 sshd: root@pts/0
```

3. Проверим настройки планировщика для /dev/sda
```bash
root@hiplet-48656:~# cat /sys/block/sda/queue/scheduler
none [mq-deadline] 
```

I/O шедулер - это компонент ядра, который определяет, в каком порядке операции блочного ввода-вывода передаются на блочное устройство (диск).
Доступны планировщики `none` и `mq-deadline`, выбран второй. Это реализация deadline планировщика с blk-mq (оптимально для SSD).

4. Определим MTU для основного сетевого интерфейса

Для начала определим название интерфейса:
```bash
root@hiplet-48656:~# ip route | grep default
default via 46.20.106.1 dev ens3 proto dhcp src 46.20.106.186 metric 100 
```
Основной интерфейс называется `ens3`.

Теперь определим сам MTU:
```bash
root@hiplet-48656:~# cat /sys/class/net/ens3/mtu
1500
```

MTU (maximum transmission unit) - максимальный размер (в байтах) пакета, который может быть передан по сети без фрагментации. 
