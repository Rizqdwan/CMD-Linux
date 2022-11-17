~> mengecek route

    ip route

~> Mengecek renderer :

    pidof systemd && echo "systemd" || echo "other"
 
    sudo services systemd-networkd status

   *- jika informasi "Active: active (running)" maka systemd-networkd sudah berjalan*

~> Mengecek lokasi file konfigurasi Netplan

    ls /etc/netplan

~> Mengecek pengaturan cloud-init

    cat /etc/cloud/cloud.efg.d/subiquity-disable-cloudinit-networking.cfg

   *- pastikan "network : {config: disabel}" ip*

~> Mengecek ip

    ip add show

~> Mengedit file konfigurasi Netplan

    sudo nano /etc/netplan/00-installer-config.yaml

~> Mengecek error konfigurasi Netplan

    sudo netplan generate

    sudo netplan try

~> Menyetujui perubahan konfigurasi Netplan

    sudo netplan apply

~> Merestart interface renderer systemd-networkd

    sudo ip link set enp0s3 down
    sudo ip link set enp0s3 up

   *- jika ada enp0s8*

    sudo ip link set enp0s8 down
    sudo ip link set enp0s8 up

~> Untuk me reboot ubuntu

    sudo reboot

~> Menyetting Ip Masquerading

    echo 1 > /proc/sys/net/ipv4/ip_forward

    iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE

    iptables -A FORWARD -i enp0s3 -o enp0s8 -m state --state RELATED,ESTABLISHED -j ACCEPT

    iptables –t nat -L 

~> Untuk membuat Ip Masquerading permanen

   *- cara mengedit rc.local*

      sudo nano /etc/rc.local
      
   *- jika sudah root maka tidak perlu sudo didepan*

      nano /etc/rc.local
  
   *- sebelum masukkan code pastikan status rc.local ( Active )*

   *- cara mengaktifkan rc.local*

     https://linuxhint.com/use-etc-rc-local-boot/
  
   *- cara cek status rc.local*
  
      systemctl status rc-local

   *- masukkan code*

      #!/bin/sh -e
      iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE
      iptables -A FORWARD -i enpos3 -o enp0s8 -m state --state RELATED,ESTABLISHED -j ACCEPT
      iptables -A FORWARD -i enpos8 -o enpos3 -j ACCEPT
      exit 0
