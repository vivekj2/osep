# osep

https://hideandsec.sh/books/cheatsheets-82c/page/active-directory

# Samba install 
sudo mv /etc/samba/smb.conf /etc/samba/smb.conf.old

sudo vim /etc/samba/smb.conf

[visualstudio]
path = /home/kali/data
browseable = yes
read only = no

sudo smbpasswd -a kali

sudo systemctl start smbd

sudo systemctl start nmbd

mkdir /home/kali/data

chmod -R 777 /home/kali/data
