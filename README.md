# greyhole-git

The aim of this project is to create an AUR wrapper that will download the 
latest version of greyhole from the git repository: https://github.com/gboudreau/Greyhole

PKGBUILD and greyhole.service are heavily based off of the greyhole 0.9.60-2 package in the AUR 
(link: https://aur.archlinux.org/packages/greyhole/). As of the time of writing, the greyhole AUR package only installs the 
samba 4.1 vfs module; modifications were made to support installation of vfs files for all the supported samba versions (3.4,3.5,
3.6,4.0,4.1,4.2). Please see the Greyhole github page for more info.
