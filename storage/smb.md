<h1 align="center">
<hr>
<a href="https://git.io/typing-svg"><img src="https://readme-typing-svg.herokuapp.com?font=Cascadia+Code&size=25&pause=10000&color=E63946&center=true&width=435&lines=SMB/Samba" alt="Typing SVG" /></a>
</h1>

## Создание SMB-шары
> Время должно быть синхронизировано по NTP
1. Установка Samba
```
sudo apt install samba

sudo dnf install samba samba-client samba-common
```
2. Настроить файрвол
```
Debian:
iptables -I INPUT -p tcp --dport 445 -j ACCEPT
iptables -I INPUT -p udp --dport 137:138 -j ACCEPT
iptables -I INPUT -p tcp --dport 139 -j ACCEPT
service iptables save
137, 138, 139 - NetBIOS

RHEL:
firewall-cmd --permanent --zone=public --add-service=samba
firewall-cmd --reload
```
3. Настройка через конфиг
```
/etc/samba/smb.conf

[Общая папка]
    comment = Public Folder
    path = /data/public
    public = yes
    writable = yes
    read only = no
    guest ok = yes
    create mask = 0777
    directory mask = 0777
    force create mode = 0777
    force directory mode = 0777

[Папка сотрудников]
    comment = Staff Folder
    path = /data/staff
    public = no
    writable = yes
    read only = no
    guest ok = no
    create mask = 0777
    directory mask = 0777
    force create mode = 0777
    force directory mode = 0777

[Приватная папка]
    comment = Private Folder
    path = /data/private
    public = no
    writable = no
    read only = yes
    guest ok = no
    valid users = admin, staff2, staff3, @privateusers
    write list = admin, staff2
    create mask = 0777
    directory mask = 0777
    force create mode = 0777
    force directory mode = 0777
    inherit owner = yes

[Recycle]
   comment = Snap Directories
   path = /data/recycle
   public = yes
   browseable = yes
   writable = yes
   vfs objects = recycle
   recycle:repository = .recycle/%U
   recycle:keeptree = Yes
   recycle:touch = Yes
   recycle:versions = Yes
   recycle:maxsize = 0
   recycle:exclude = *.tmp, ~$*
   recycle:exclude_dir = /tmp


```
4. Создать папку шары
```
mkdir /data/staff
chmod 777 /data/staff
useradd staff1
passwd staff1
smbpasswd -a staff1
systemctl restart smb
```
5. Запуск Samba
```
Debian:
systemctl enable smbd --now

RHEL:
systemctl enable smb --now
```
6. Настроить SELinux
```
dnf install policycoreutils setroubleshoot

semanage fcontext -a -t samba_share_t "/data(/.*)?"
restorecon -R -v /data
chcon -t samba_share_t /data
```

## Подключение к SMB-шаре
1. Установить Samba-клиент
```
dnf install samba-client
apt install samba-client cifs-utils
```
2. Проверить подключение через smbclient
```
smbclient -L 192.168.100.1 -U user
```
3. Монтирование Samba шары
```
mount -t cifs "//192.168.100.1/Sharedir" /mnt/sambadir -o user=staff1,password=pass
```

## Ссылки
1. [Установка Samba на Ubuntu. Настройка файлового сервера на Linux](https://www.dmosk.ru/instruktions.php?object=samba-ubuntu)
2. [Установка Samba на CentOS 8. Настройка файлового сервера на Linux](https://www.dmosk.ru/instruktions.php?object=samba-centos8)
3. [Установка и конфигурация Samba на CentOS 7](https://drach.pro/blog/linux/item/46-centos-7-samba-server)