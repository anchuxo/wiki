# Mengenal Berkas-berkas di direktori debian
Direktori **debian** merupakan direktori khas distro debian/turunannya yang ditambahkan pada file sumber sebagai metadata dalam pembuatan paket. Direktori debian terdiri dari banyak file, file yang wajib ada adalah: changelog, control, copyright dan rules. 

##Persiapan

 1. Buat identitas pemaket  
 
   ```
  $ vim .bashrc
  (tambahkan pada akhir baris:)
  export DEBFULLNAME="Joe Hacker" (sesuaikan dengan nama Anda)
  export DEBEMAIL="joe.hacker@isp.com" (sesuaikan dengan e-mail Anda)
  $ source .bashrc
  ```
  
 2.Install paket yang di butuhkan
 ```
  $ sudo apt-get install build-essential devscripts debhelper dh-make fakeroot lintian gnupg
  ```
     
     
build-essensial: paket meta yang akan menginstall paket-paket dasar yang dibutuhkan untuk development kaya gcc, make, libc6-dev, dpkg-dev, dll.
devscripts: kumpulan tools yang membuat hidup pemaket jadi lebih menyenangkan, seperti dch (tool edit changelog), debuild (bangun paket) dsb.
debhelper: kumpulan script2 yang digunakan di berkas debian/rules
dh-make: tool untuk mendebiankan paket kode sumber (isinya template2 untuk direktori debian)
lintian: nah ini nih si tukang ngecek paket apakah sudah sesuai tidak dengan
gnupg: dibutuhkan untuk menandatangi paket debian
fakeroot: biar bisa ngompail tanpa akses root sebenernya 

 3.Buat gpg key
  ```
  $ gpg --gen-key
  Real name: Joe Hacker
  E-mail address: joe.hacker@isp.com
  Passphrase: paswordpercobaanjanganditiru
  ```
FYI di debian dan ubuntu setiap paket yang akan dikirim ke pabrik paket perlu ditandatangani oleh pemaket, nanti robot pabrik akan ngecek apakah tandatanganya benar2 milik anda. klo sudah beres HARAP BACKUP direktori .gnupg ke tempat yg aman karena kunci gpg adalah satu-satunya identitas Anda agar dapat dikenal oleh irgsh

 4.Download paket yang akan dijadikan contoh pada lokakarya ini. letakkan pada direktori tertentu 
  ```
  $ mkdir -p lokakarya
  $ cd lokakarya
  $ wget http://cecunguk.blankonlinux.or.id/~imtheface/lokakarya/jao-theme-1.8.tar.gz
  $ tar xzf jao-theme-1.8.tar.gz
  $ cd jao-theme-1.8
  $ ls
  ```
  
 5.Buat direktori debian dengan menggunakan dh_make 
  ```
  $ dh_make -c gpl -f ../jao-theme-1.8.tar.gz
  Type of package: single binary, multiple binary, library, kernel module or cdbs?
   [s/m/l/k/b] s

  Maintainer name : Joe Hacker
  Email-Address   : joe.hacker@isp.com
  Date            : Thu, 13 Nov 2008 05:37:17 +0700
  Package Name    : jao-theme
  Version         : 1.8
  License         : gpl
  Type of Package : Single
  Hit <enter> to confirm: 
  Done. Please edit the files in the debian/ subdirectory now. jao-theme
  uses a configure script, so you probably don't have to edit the Makefiles.
  ```
Proses ini akan membuat direktori debian beserta isinya dari template yang telah disediakan. Serta membuat file jao-theme_1.8.orig.tar.gz sejajar dengan direktori jao-theme-1.8.tar.gz
Di debian ada policy bahwa kode sumber dari upstream tidak boleh kita boleh kita ubah secara langsung, setiap perubahan yg kita lakukan akan dilakukan di dalam direktori debian. Nantinya isi dari direktori debian itu kalau paket sudah dibangun akan di kompres menjadi nama-paket.diff.gz 

##File Control
Secara umum file ini berisi:
  ```
  Source: jao-theme
  Section: unknown
  Priority: extra
  Maintainer: Joe Hacker <joe.hacker@isp.com>
  Build-Depends: debhelper (>= 5), autotools-dev
  Standards-Version: 3.7.2

  Package: jao-theme
  Architecture: any
  Depends: ${shlibs:Depends}, ${misc:Depends}
  Description: <insert up to 60 chars description>
   <insert long description, indented with spaces>
   ```

Isi control itu terdiri dari 2 bagian: 
 1.Bagian source 

**Source:** nama-paket

**Section:** itu entri dimana paket debian mau ditaro

klo di debian policy section itu terdiri dari beberapa: admin, comm, devel, doc, editors, electronics, embedded, games, gnome, graphics, hamradio, interpreters, kde, libs, libdevel, mail, math, misc, net, news, oldlibs, otherosfs, perl, python, science, shells, sound, tex, text, utils, web, x11. (​http://www.debian.org/doc/debian-policy/ch-archive.html#s-subsections)

**Priority:** menunjukkan tingkat pentingnya paket. Dapat berisi: required, important, standard, optional, extra. Paket-paket biasanya sih menggunakan priority "optional" saja, di atas priority "optional" hanya digunakan paket-paket penting. Kalau di debian perlu mendapatkan persetujuan dari para bos untuk bisa menggunakan priority di atas "optional". (​http://www.debian.org/doc/debian-policy/ch-archive.html#s-priorities)

**Maintainer:** nama orang yang memaketkan.

**Build-Depends:** Paket yg dibutuhkan untuk membuat paket tersebut.
 Bagaimana cari tahu isinya build-depends?

 *liat README 
 *tanya pengelola/pembuat 
 *liat Makefile.am 

**Standars-Version:** Versi dari dh-make yang digunakan 