# greyhole-git
The aim of this project is to create an AUR wrapper that will download the 
latest version of greyhole from the git repository: https://github.com/gboudreau/Greyhole
PKGBUILD and greyhole.service are heavily based off of the greyhole package in the AUR (link: https://aur.archlinux.org/packages/greyhole/). Modifications were made in order to ensure it will link the correct samba vfs instead of only one version.

NOTE: Currently this will only support php installations where php.ini is in /etc/php.ini and the open_basedir attribute does not end in :, like below:
open_basedir = /srv/http/:/home/:/tmp/:/usr/share/pear/:/usr/share/webapps/
