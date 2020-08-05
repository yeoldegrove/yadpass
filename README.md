# yadpass

This is a little hacky GTK frontend for pass built using yad (a zenity clone).
It let's you query for passwords for your git cloned pass repository in ~/.password-store.
The found entires can be clipboarded by wl-copy/xclip or shown in cleartext on the screen.
It will work with X11 and Wayland and was initially designed for phosh on pinephone and Librem 5.

### install dependencies (on e.g. Debian)
```
sudo apt-get install pass yad xclip wl-clipboard
```

## install to homedir and add desktop entry
```
mkdir -p ~/bin /tmp/yadpass_install
curl -o ~/bin/yadpass https://raw.githubusercontent.com/yeoldegrove/yadpass/upstream/yadpass
chmod +x ~/bin/yadpass
curl -o /tmp/yadpass_install/yadpass.desktop https://raw.githubusercontent.com/yeoldegrove/yadpass/upstream/yadpass.desktop
sed -i "s#/usr#$HOME#g" /tmp/yadpass_install/yadpass.desktop
xdg-desktop-menu install --novendor /tmp/yadpass_install/yadpass.desktop
curl -o /tmp/yadpass_install/yadpass_256x256.png https://raw.githubusercontent.com/yeoldegrove/yadpass/upstream/yadpass_256x256.png
xdg-icon-resource install --novendor --size 256 /tmp/yadpass_install/yadpass_256x256.png yadpass
rm -r /tmp/yadpass_install

```
