Домашнее задание №7

1. Попасть в систему без пароля несколькими способами

2. Установить систему с LVM, после чего переименовать VG

3. Добавить модуль в initrd

Ход работы:

Для выполнения ДЗ используется ОС CentOS 7, установленная на Oracle VirtualBox.

Часть 1. Попасть в систему без пароля несколькими способами

Запускаем виртуальную машину и при выборе ядра для загрузки нажимаем клавишу e.

Первый способ.

Добавляем в конце строки, начинающейся с linux16 строку init=/bin/sh. В этой же строке меняем ro на rw.

![Первый способ. Вносим изменения в загрузчик](https://github.com/DmitryV81/HW7_system_boot/blob/main/boot_method_1.JPG)

Нажимаем комбинацию клавиш ctrl+x и загружаемся:

![Первый способ. Загружаемся в shell](https://github.com/DmitryV81/HW7_system_boot/blob/main/boot_method_1_part2.JPG)

Проверяем, что всё получилось:
 
![Первый способ. Проверяем](https://github.com/DmitryV81/HW7_system_boot/blob/main/boot_method_1_part3.JPG)

Второй способ.

Добавляем в конце строки, начинающейся с linux16 строку rd.break. Также можно изменить ro на rw.

![Второй способ. Вносим изменения в загрузчик](https://github.com/DmitryV81/HW7_system_boot/blob/main/boot_method_2.JPG)

Нажимаем комбинацию клавиш ctrl+x и загружаемся. После загрузки мы попадаем в emergency mode.

Если меняли ro на rw, то выполняем следующие команды:

```
chroot /sysroot
passwd root
touch /.autorelabel
```

Если не меняли ro на rw, то выполняем без mount -o remount,rw /sysroot

```
mount -o remount,rw /sysroot
chroot /sysroot
passwd root
touch /.autorelabel
```

![Второй способ. Загружаемся и меняем пароль root](https://github.com/DmitryV81/HW7_system_boot/blob/main/boot_method_2_part2.JPG)

Таким образом, мы поменяли пароль root. Перезагружаемся и входим под новым паролем:

![Второй способ. Перезагружаемся и входим под новым паролем root](https://github.com/DmitryV81/HW7_system_boot/blob/main/boot_method_2_part3.JPG)

Третий способ.

Добавляем в конце строки, начинающейся с linux16 строку rd.break. И, меняем ro на rw.

![Третий способ. Вносим изменения в загрузчик](https://github.com/DmitryV81/HW7_system_boot/blob/main/boot_method_3.JPG)

Нажимаем комбинацию клавиш ctrl+x и загружаемся. Меняем пароль root:

```
chroot /sysroot
passwd root
touch /.autorelabel
```

![Третий способ. Меняем пароль root](https://github.com/DmitryV81/HW7_system_boot/blob/main/boot_method_3_part2.JPG)

Перезагружаемся. Входим под новым паролем root:

![Третий способ. Входим с новым паролем root](https://github.com/DmitryV81/HW7_system_boot/blob/main/boot_method_3_part3.JPG)

Часть 2. Установить систему с LVM, после чего переименовать VG

Отобразим текущее имя VG:

```
[root@localhost ~]# vgs
  VG     #PV #LV #SN Attr   VSize  VFree
  centos   1   2   0 wz--n- 18,61g 4,00m
```

Переименуем centos на OtusRoot:

```
[root@localhost ~]# vgrename centos OtusRoot
  Volume group "centos" successfully renamed to "OtusRoot"
```

После этого необходимо внести изменения в файлы:

/etc/fstab

/etc/default/grub

/boot/grub2/grub.cfg

В них мы изменяем старое имя VG centos на новое OtusRoot. *Файлы с внесёнными изменениями прилагаются

Пересоздаём initrd image:

```
[root@localhost ~]# mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)
```

Перезагружаемся. Проверяем имя VG:

```
[root@localhost ~]# vgs
  VG       #PV #LV #SN Attr   VSize  VFree
  OtusRoot   1   2   0 wz--n- 18,61g 4,00m
```

Часть 3. Добавить модуль в initrd

Скрипты модулей расположены в каталоге: /usr/lib/dracut/modules.d/

Для добавления модуля создаём каталог 01test по пути /usr/lib/dracut/modules.d/

Создаем внутри нового каталоге два файла:

module-setup.sh

test.sh 

Файлы прилагаются к проекту. Файл module-setup.sh служит для установки модуля и вызова скрипта test.sh

Далее пересобираем образ initrd:

```
[root@localhost ~]# mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)
```

Проверяем, загружен ли наш образ:

```
[root@localhost ~]# lsinitrd -m /boot/initramfs-$(uname -r).img | grep test
test
```

Для проверки функционирования модуля вносим изменения в загрузчик- убираем опции rhgb и quit из строки, начинающейся с linux16 в файле /boot/grub2/grub.cfg

В результате, после перезагрузки мы увидим во время загрузки ОС изображение пингвина.
