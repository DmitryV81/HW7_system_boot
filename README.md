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

