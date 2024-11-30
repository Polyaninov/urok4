
    1. Добавить два дополнительных диска к ВМ
    2. Установить на ВМ postgresql, убедиться что он запущен
    pmn@pmn-ubuntu:~$ sudo apt install postgresql postgresql-contrib -y
    pmn@pmn-ubuntu:~$ sudo systemctl status postgresql


    3. Выяснить, в какой директории postgres хранит данные

sudo su - postgres
psql
# SHOW data_directory;

    4. На одном новом диске создать 2 раздела (обычных, с помощью fdisk)
                   pmn@pmn-ubuntu:~$ sudo fdisk /dev/sdb


    Второй диск полностью отдать под LVM:

    сделать из него PV

   pmn@pmn-ubuntu:~$ sudo pvcreate /dev/sdc
   Physical volume "/dev/sdc" successfully created.
    
    из PV сделать VG

   pmn@pmn-ubuntu:~$ sudo vgcreate vg01 /dev/sdc
   Volume group "vg01" successfully created
   pmn@pmn-ubuntu:~$ sudo vgdisplay 


    
    из VG отрезать раздел под данные postgresql
        pmn@pmn-ubuntu:~$ sudo lvcreate -l 50%VG vg01


          pmn@pmn-ubuntu:~$ mkfs.ext4 /dev/mapper/vg01-lvol0 


    5. Смонтировать созданный раздел в директорию, которую нашли в задании 3
     pmn@pmn-ubuntu:~$ sudo mount /dev/mapper/vg01-lvol0 /var/lib/postgresql

    
    7. Создать из двух разделов на оставшемся диске два PV
    8. Добавить их в сущеуствующую VG
    9. Увеличить размер LV под данными postgres
