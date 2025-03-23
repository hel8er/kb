---
title: Установка MikroTik RouterOS на VPS
tags:
  - mikrotik
---


https://mikrotik.com/download#chr

```bash
cd /tmp
wget https://download.mikrotik.com/routeros/7.9/chr-7.9.img.zip
unzip chr-7.9.img.zip
```
Следующим шагом нам нужно будет узнать имя диска на нашем сервере. Для этого мы использовали следующую команду:
```bash
fdisk -l
```
Следующим шагом необходимо перемонтировать файловые системы в режим “только чтение”:
```bash
echo u > /proc/sysrq-trigger
```
И наконец, на диск `/dev/sda` нужно записать распакованный образ:
```bash
dd if=chr-7.9.img of=/dev/vda bs=4M oflag=sync
```

Следующими командами производится перезагрузка сервера:

```bash
echo 1 > /proc/sys/kernel/sysrq
echo b > /proc/sysrq-trigger
```

