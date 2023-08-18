# 拯救老archlinux

pacman更新什么都corrupted

重新初始化pacman-key [ref](https://wiki.archlinux.org/title/Pacman/Package_signing#Resetting_all_the_keys)

``` bash
sudo rm -rf /etc/pacman.d/gnupg
sudo pacman-key --init
sudo pacman-key --populate
sudo pacman-key --refresh-keys
```

定期更新keyring并升级系统 [ref](https://wiki.archlinux.org/title/Pacman/Package_signing#Upgrade_system_regularly)

``` bash
sudo pacman -Sy archlinux-keyring && sudo pacman -Su
```

如同时使用archlinuxcn，还需要更新其keyring，然后再次执行 `sudo pacman -Su` [ref](https://github.com/archlinuxcn/repo/blob/master/README.md)

``` bash
sudo pacman -Sy && sudo pacman -S archlinuxcn-keyring
```

定期同步时间 [ref](https://wiki.archlinux.org/title/Pacman/Package_signing#Update_system_time_regularly)
