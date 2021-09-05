## Install Tools
Periapan Awal adalah memasang perangkat lunak yang nantinya digunakan sebagai perkakas dalam pembuat distro remastering.  
```
sudo apt install live-build
```

```
sudo apt-get install squashfs-tools genisoimage isolinux syslinux resolvconf syslinux-utils -y
```
## Membuat Kerangka kerja menggunak debian live Project

membuat direktori 
```
mkdir distro && cd distro
```
1.debian live
```bash
  lb config -b iso --cache true --apt-recommends false -a amd64 --binary-images iso --debian live --linux-flavours 486 --mode debian --debian-installer true --archive-areas "main contrib non-free" --security false --win32-loader false --interactive shell --updates false
```
2. Mengcopikan file resolv.conf dari host ke directoy "chroot"
```bash
  sudo cp /etc/resolv.conf ./chroot/etc/
```
3.Membuat kait (mount) /dev/
```
sudo mount --bind /dev/ ./chroot/dev/
```
4.Masuk dalam chroot dari folder "chroot"
```
sudo chroot ./chroot
```
5.Mengaikatan proc sysfs dan devpts
```
mount -t proc none /proc && mount -t sysfs none /sys && mount -t devpts none /dev/pts
```
6.Membuat variabel dari HOME yaitu /root dan mengatur Bahasa Default "C"
```
export HOME=/root && export LC_ALL=C
```
7.Perintah di bawah ini digunakan untuk awal dari pertama kali membuat distro remastering
  Jika ingin Mengulang lagi Remastering sebelumnya dilewati 2 perintah dibawah ini
```
cd /etc/skel
mkdir Desktop Documents Downloads Music Pictures Public Templates Videos
```
8.Ok anda sudah dalam mode chroot dari edit. Silahkan edit mau ditambahkan paket atau
  mengurangi paket dan lainnya.
```
cd /
```

## Keluar dari mode chroot

1.Gunakan Perintah di bawah ini jika keluar dari chroot.
  Perintah ini untuk membersihkan paket dari paket debian yang
  didapat dari cache saat memasang aplikasi atau paket dari apt dan aptitude

```
apt-get clean
aptitude clean
```
2.Perintah ini untuk membersihkan paket yang sudah tidak diperlukan lagi
```
apt-get autoremove

```
3. Memberikan Chace dan Bash_History
```
rm -rf /tmp/* ~/.bash_history
```
4. Menghapus ID mesin
```
rm /var/lib/dbus/machine-id
rm /sbin/initctl
dpkg-divert --rename --remove /sbin/initctl
```
5. Memutuskan titik kait dari proc sys dan devpts
```
umount /proc && umount /sys && umount /dev/pts
exit
```
6. Memutuskan titik kait dari edit/dev/ (umount)
```
sudo umount ./edit/dev/
```
## Membuat image ISO Remaster

1. Menghapus filesistem.squashfs jika sebelumnya membuat filesistem.squashfs
Jika Pertama kali membuat iso lewatkan perintah dibawah ini
PENTING UNTUK DIPERHATIKAN JIKA MAU BIKIN LAGI filesystem.squashfs
FILE YANG LAMA WAJIB HAPUS JIKA TIDAK AKAN MENGALAMI ERROR

```
sudo rm $HOME/distro/binary/live/filesystem.squashfs
```
2. Membuat Folder Kerangka dvd (live).
```
cd $HOME/distro/
sudo chmod +w ./binary/live/filesystem.manifest
sudo su
sudo chroot ./chroot dpkg-query -W --showformat='${Package} ${Version}\n' > ./binary/live/filesystem.manifest
printf $(sudo du -sx --block-size=1 chroot | cut -f1) > ./binary/live/filesystem.size
exit
sudo cp ./binary/live/filesystem.manifest ./binary/live/filesystem.manifest-desktop
```
3. Membuat filesystem.squashfs dari Hasil Proyek Remastering STANDAR COMPRESS
```
cd $HOME/distro/
sudo mksquashfs ./chroot ./binary/live/filesystem.squashfs
```
4.Menghapus md5sum.txt and calculate baru md5sums (Silahkan Hapus file md5sum.txt atau MD5SUMS).
```
cd $HOME/distro/binary/
sudo rm MD5SUMS
sudo rm md5sums.txt
```
5. Membuat generate MD5SUMS baru.
```
cd $HOME/distro/binary/
find -type f -print0 | sudo xargs -0 md5sum | grep -v isolinux/boot.cat | sudo tee MD5SUMS
```
6. Membuat File image ISO Standar Kurang Dari 4GB**

```
cd $HOME/distro/
sudo mkisofs -r -V "DracOS-3.1.0-amd64" -cache-inodes -J -l -b isolinux/isolinux.bin -c isolinux/boot.cat -no-emul-boot -boot-load-size 4 -boot-info-table -o ../DracOS-3.1.0-amd64.iso binary
cd $HOME/ && sudo chmod 777 DracOS-3.1.0-amd64.iso
```
7. Membuat ISO Hybrid.

```
cd $HOME/
isohybrid DracOS-3.1.0-amd64.iso
```

8. Membuat Checksum File ISO MD5sum.
```
cd $HOME/
md5sum DracOS-3.1.0-amd64.iso > DracOS-3.1.0-amd64.iso.md5sum
```
9. Membuat Checksum File ISO SHA1sum.
```
sha1sum DracOS-3.1.0-amd64.iso > DracOS-3.1.0-amd64.iso.sha1sum
```
10. sha2sum DracOS-3.1.0-amd64.iso > DracOS-3.1.0-amd64.iso.sha2sum
