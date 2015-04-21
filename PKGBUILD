# Maintainer: Robert Holak <rholak@gmail.com>
# Maintainer: Christopher Krooß <didi2002@web.de>
# Modified for personal use: Scott Dose
# Also modified to use
# Skip data integrity checks for git repository

pkgname=greyhole-git
_gitname=Greyhole
pkgver=0.9.51.2.r46.g11c04f3
pkgrel=1
pkgdesc="Greyhole is an application that uses Samba to create a storage pool of all your available hard drives and allows you to create redundant copies of the files you store, in order to prevent data loss when part of your hardware fails.This version downloads the latest github release."
url="http://greyhole.net"
arch=('x86_64' 'i686')
license=('GPLv3')
depends=('php' 'php-intl' 'samba' 'mysql' 'rsync' 'lsof')
makedepends=('git')
backup=("etc/greyhole.conf")
install='greyhole.install'
source=("git+https://github.com/gboudreau/Greyhole"
	"greyhole.service")
md5sums=('SKIP'
         '667531c2298d018f8487bfbb58f0a94d')

pkgver() {
  cd "$_gitname"
  git describe --long | sed 's/\([^-]*-g\)/r\1/;s/-/./g' 
}

package() {
  # Copy from INSTALL in git directory
  cd "$_gitname"
  
  # Setup Required Dirs and permissions
  mkdir -p $pkgdir/var/spool/greyhole
  chmod 777 $pkgdir/var/spool/greyhole
  mkdir -p $pkgdir/usr/share/greyhole
  
  # Temporarily copy over php.ini file and add current directory to open_basedir
  cp /etc/php/php.ini .|| echo "Can't find php.ini file in /etc/php" || die 1
  (sed -i -e 's,\(open_basedir = .*\),\1:'`pwd`',' php.ini)

  # Manually enter commands from Makefile, as it requires root access
  php -c php.ini inject-includes.php greyhole
  php -c php.ini inject-includes.php greyhole-dfree.php
  php -c php.ini inject-includes.php web-app/index.php 

  (cd docs && gzip -9 greyhole.1 && gzip -9 greyhole-dfree.1 && gzip -9 greyhole.conf.5)  



  # Binaries
  install -m 0755 -D -p greyhole $pkgdir/usr/bin/greyhole
  install -m 0755 -D -p greyhole-dfree $pkgdir/usr/bin/greyhole-dfree
  install -m 0755 -D -p greyhole-dfree.php $pkgdir/usr/share/greyhole/greyhole-dfree.php

  # Files
  install -m 0644 -D -p logrotate.greyhole $pkgdir/etc/logrotate.d/greyhole
  install -m 0644 -D -p greyhole.cron.d $pkgdir/etc/cron.d/greyhole
  install -m 0644 -D -p greyhole.example.conf $pkgdir/etc/greyhole.conf

  # Cron Scripts
  install -m 0755 -D -p greyhole.cron.weekly $pkgdir/etc/cron.weekly/greyhole
  install -m 0755 -D -p greyhole.cron.daily $pkgdir/etc/cron.daily/greyhole

  # manpages
  install -m 0644 -D -p docs/greyhole.1.gz $pkgdir/usr/share/man/man1/greyhole.1.gz
  install -m 0644 -D -p docs/greyhole-dfree.1.gz $pkgdir/usr/share/man/man1/greyhole-dfree.1.gz
  install -m 0644 -D -p docs/greyhole.conf.5.gz $pkgdir/usr/share/man/man5/greyhole.conf.5.gz

  # PhP Web App
  mkdir -p ${pkgdir}/usr/share/greyhole/web-app/
  install -m 0644 -D -p web-app/README $pkgdir/usr/share/greyhole/web-app
  install -m 0644 -D -p web-app/index.php $pkgdir/usr/share/greyhole/web-app

  # Other Files
  install -m 0644 -D -p schema-mysql.sql $pkgdir/usr/share/greyhole/schema-mysql.sql
  install -m 0644 -D -p USAGE $pkgdir/usr/share/greyhole/USAGE
  install -m 0644 -D -p $startdir/greyhole.service $pkgdir/usr/lib/systemd/system/greyhole.service


  #Choose correct samba module
  if [ "$CARCH" = "i686" ] ; then
    SMB_ARCH="i386"
  #  vfs_file=greyhole-i386.so
  else
    SMB_ARCH="x86_64"
  #  vfs_file=greyhole-x86_64.so
  fi

  
  SMB_VERSION="`smbd --version | awk '{print $2}' | awk -F'-' '{print $1}' | awk -F'.' '{print $1"."$2}'`"
  echo "You appear to be running Samba $SMB_VERSION"

  # Case statement for selecting the correct Samba module
  case $SMB_VERSION in
    '3.4') 
	    ;;
 
    '3.5') 
	    ;;
	    
    '3.6') 
	    ;;
 
    '4.0') 
	    ;;

    '4.1') 
	    ;;

    '4.2') 
	    ;;
    *)
	    echo "Unsupported Version of Samba, please install from source and build custom Samba module."
	    die 1
	    ;;
  esac
     
  vfs_file=samba-module/bin/${SMB_VERSION}/greyhole-${SMB_ARCH}.so 
 
  mkdir -p "$pkgdir/usr/lib64/greyhole/"
  mkdir -p "$pkgdir/usr/lib64/samba/vfs/"
  install -m 644 "$vfs_file" "$pkgdir/usr/lib64/greyhole/greyhole-samba${SMB_VERSION/\./}.so"
  ln -s "/usr/lib64/greyhole/greyhole-samba${SMB_VERSION/\./}.so" "$pkgdir/usr/lib64/samba/vfs/greyhole.so"
}
