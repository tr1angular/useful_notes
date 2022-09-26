<h1 align="center">
<hr>
<a href="https://git.io/typing-svg"><img src="https://readme-typing-svg.herokuapp.com?font=Cascadia+Code&size=25&pause=10000&color=E63946&center=true&width=435&lines=LVM" alt="Typing SVG" /></a>
</h1>

## Настройка LVM
1. Установка
```
apt install lvm2
yum install lvm2
```

2. Создание разделов
```
pvcreate /dev/sdb /dev/sdc
pvdisplay
```
3. Создание групп томов
```
vgcreate vg01 /dev/sdb /dev/sdc
vgdisplay
```
4. Создание логических томов
```
lvcreate [options] <имя группы томов>
lvcreate -L 1G vg01                 # том на 1 гб
lvcreate -L50 -n lv01 vg01          # том на 50мб с именем lv01
lvcreate -l 40%VG vg01              # использовать 40% от дискового пространства группы vg01
lvcreate -l 100%FREE -n lv01 vg01   # использовать все свободное пространство группы vg01
lvdisplay
```
5. Создание файловой системы и монтирование тома
```
mkfs.ext4 /dev/vg01/lv01
mount /dev/vg01/lv01 /mnt/lvm

/etc/fstab:
/dev/vg01/lv01 /mnt/lvm ext4 defaults 1 2

mount -a # проверить на ошибки в fstab
df -hT   # проверить примонтирован ли диск
```
6. Просмотр информации
```
lsblk

pvs # кратко
pvdisplay
pvdisplay /dev/sdb

vgs
vgdisplay
vgdisplay vg01

lvdisplay
lvdisplay /dev/vg01/lv01
lvmdiskscan # поиск всех устройств, имеющих отношение к LVM
```
7. Увеличение томов
```
pvcreate /dev/sdd           # новый диск
pvresize /dev/sda           # какой диск будем увеличивать
vgextend vg01 /dev/sdd      # добавить в группу новый диск

lvextend -l +100%FREE /dev/vg01/lv01   # добавить весь объем
lvexetend -L+30G /dev/vg01/lv01        # 30 гигабайт

resize2fs /dev/vg01/lv01    # изменить размер файловой системы
```
8. Уменьшение томов
```
umount /mnt/lvm
e2fsck -fy /dev/vg01/lv01     # проверить ФС  
resize2fs /dev/vg01/lv01 500M # уменьшить на 500Мб
lvreduce -L-500 /dev/vg01/lv01
```
9. Удаление томов
```
umount /mnt/lvm
убрать запись из fstab (если есть)
lvremove /dev/vg01/lv01
vgremove vg01
pvremove /dev/sd{b,c,d}
```
10. Создание зеркала
```
pvcreate /dev/sd{d,e}
vgcreate vg02 /dev/{d,e}
lvcreate -L200 -m1 -n lv-mir vg02 # том на 200Мб
```
11. Создание снапшотов
```
lvcreate -L500 -s -n sn01 /dev/vg01/lv01    # 500Мб под снапшоты
mkdir /tmp/snp
mount /dev/vg01/sn01 /tmp/snp
lvconvert --merge /dev/vg01/sn01 # откат до снапшота
```
12. Импорт диска из другой системы
```
umount /mnt/lvm
vgchange -an -vg_test # деактивировать группу томов
vgexport vg_test      # экспорт для переноса на новый компьютер

pvscan      # на новой системе найти LVM-тома
vgimport vg_test
vgchange -ay vg_test # активировать LVM-том
lvdisplay
mount /dev/vg_test/lvol0    /mnt/lvm-test
```

## Ссылки
1. [Принципы работы](https://interface31.ru/tech_it/2020/07/lvm-dlya-nachinayushhih-chast-1-obshhie-voprosy.html)
2. [Настройка LVM от dmosk](https://www.dmosk.ru/instruktions.php?object=lvm)
3. [Настройка LVM от interface31](https://interface31.ru/tech_it/2020/08/lvm-dlya-nachinayushhih-chast-2-osnovy-upravleniya-tomami.html)