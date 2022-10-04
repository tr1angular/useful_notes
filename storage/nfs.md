<h1 align="center">
<hr>
<a href="https://git.io/typing-svg"><img src="https://readme-typing-svg.herokuapp.com?font=Cascadia+Code&size=25&pause=10000&color=E63946&center=true&width=435&lines=NFS" alt="Typing SVG" /></a>
</h1>

## NFS-сервер
1. Установка
```
apt install nfs-kernel-server
dnf install nfs-utils
```
2. Создать корневую директорию NFS
```
mkdir /mnt/sharedir
chown nobody:nogroup /mnt/sharedir
chmod 777 /mnt/sharedir
```
3. Определить уровень доступа для NFS-клиентов
```
/etc/exports

/mnt/sharedir <client-ip>(rw,sync,no_subtree_check)     # доступ для одного клиента
/mnt/sharedir <client-ip-1>(rw,sync,no_subtree_check)<client-ip-2>(...) # доступ для нескольких клиентов
/mnt/sharedir <subnet-ip>/<subnet-mask>(rw,sync,no_subtree_check)
```
4. Сделать шару доступной для клиентов
```
exportfs -a

Debian:
systemctl restart nfs-kernel-server

RHEL:
systemctl enable --now rpcbind
systemctl enable --now nfs-server
```
5. Разрешить порты в файрволе
```
Debian:
iptables -t filter -A INPUT -p tcp --dport 111 -j ACCEPT
iptables -t filter -A INPUT -p tcp --dport 2049 -j ACCEPT
iptables -t filter -A INPUT -p tcp --dport 20048 -j ACCEPT
service iptables save
service iptables restart

RHEL:
firewall-cmd --permanent --add-port=111/tcp
firewall-cmd --permanent --add-port=20048/tcp
firewall-cmd --permanent --zone=public --add-service=nfs
firewall-cmd --permanent --zone=public --add-service=rpc-bind
firewall-cmd --reload
```

## NFS-клиент
1. Установить NFS-клиент
```
sudo apt update
sudo apt install nfs-common

sudo yum install nfs-utils
```
2. Временное монтирование
```
sudo mkdir /var/locally-mounted
sudo mount -t nfs <server-ip>:<folder-path-on-server> /var/locally-mounted

sudo mount -t nfs 192.168.100.2:/myshareddir /var/locally-mounted
```
3. Проверка монтирования
```
df -h
mount
```
4. Монтирование через fstab
```
mkdir /var/locally-mounted

/etc/fstab:
192.168.100.2:/myshareddir /var/locally-mounted nfs defaults 0 0
```

## Ссылки
1. [Установка и настройка NFS-сервера и клиента в CentOS/RHEL](https://winitpro.ru/index.php/2020/06/05/ustanovka-nastrojka-nfs-servera-i-klienta-linux/?ysclid=l8uip2acfw511688769)
2. [Linux NFS Server: How to Set Up Server and Client](https://cloud.netapp.com/blog/azure-anf-blg-linux-nfs-server-how-to-set-up-server-and-client)