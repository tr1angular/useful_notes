<h1 align="center">
<hr>
<a href="https://git.io/typing-svg"><img src="https://readme-typing-svg.herokuapp.com?font=Cascadia+Code&size=25&pause=10000&color=E63946&center=true&width=435&lines=DHCP" alt="Typing SVG" /></a>
</h1>

## Debian-based <img src="https://cdn.betterttv.net/emote/5ea8726b6c27013608bd8fbc/3x" alt="Debian Logo" title="Debian Logo" align="left" width="25" height="25"/>

1. Установить пакет isc-dhcp-server
```
sudo apt install isc-dhcp-server
```
2. Указать сетевой интерфейс, с которого будет присылаться IP-адрес
```
/etc/default/isc-dhcp-server

INTERFACESv4="enp0s8"
```
3. Выполнить настройку DHCP-сервера
```
/etc/dhcp/dhcpd.conf

authorative;

subnet 192.168.100.0 netmask 255.255.255.0 {
    range 192.168.100.5 192.168.100.254;
    option routers 192.168.100.1;
    option subnet-mask 255.255.255.0;
    option broadcast-address 192.168.100.255;
    default-lease-time 600;
    max-lease-time 7200;
    option domain-name "local";
    option domain-name-servers 1.1.1.1, 8.8.8.8;
}
```
4. Перезапустить DHCP-сервер
```
systemctl restart isc-dhcp-server
```
>Если возникла ошибка [Failed to start LSB: DHCP server](#), то:
1. Если dhcpd запущен, остановить его, убить процесс и удалить файл pid. Если не запущен, то просто удалить pid.
```
systemctl disable --now dhcpd
kill -15 $(ps ax | grep dhcp)
rm -f /var/run/dhcpd.pid
```
2. Рестартнуть isc-dhcp-server

## RHEL-based <img src="../images/rhel.png" alt="Red Hat Logo" title="Red Hat Logo" align="left" width="25" height="25"/>