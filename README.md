# dracos-remastering
remastering based on debian
1. Membuat debian live
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
