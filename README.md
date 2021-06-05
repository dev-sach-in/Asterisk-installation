# Asterisk-installation

### A little house keeping before installation.
```shell
yum update
sed -i 's/\(^SELINUX=\).*/\SELINUX=disabled/' /etc/sysconfig/selinux
sed -i 's/\(^SELINUX=\).*/\SELINUX=disabled/' /etc/selinux/config
sestatus
reboot
sestatus
sudo yum install httpd
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --zone=public --permanent --add-service=http
firewall-cmd --zone=public --permanent --add-service=https
firewall-cmd --reload
sudo systemctl enable httpd.service
sudo systemctl start httpd.service
```

### Installing DependenciesPermalink

```shell
yum install -y epel-release dmidecode gcc-c++ ncurses-devel libxml2-devel make wget openssl-devel newt-devel kernel-devel sqlite-devel libuuid-devel gtk2-devel jansson-devel binutils-devel
```
-----------------------------------
#### For asterisk 16
```shell
dnf --enablerepo=PowerTools install libedit-devel
dnf --enablerepo=PowerTools install libsrtp
dnf --enablerepo=PowerTools install libsrtp-devel
```

# Install Asterisk 16
```shell
cd /usr/src/
```
```shell
wget http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-16-current.tar.gz
```
```shell
tar -zxvf asterisk-16-current.tar.gz
```
```shell
mv asterisk-16.x.x asterisk
```
```shell
cd asterisk
```

```shell
contrib/scripts/install_prereq install
```
```shell
./configure --with-pjproject-bundled --with-crypto --with-ssl=ssl --with-srtp --with-jansson-bundled
```
```shell
make menuselect
```
```shell
make
```
```shell
make install
```
```shell
make samples
```
```shell
make config
```

Start at server restart
```shell
ldconfig
```
```shell
sudo service asterisk start
```

Entering into command line of asterisk
```shell
asterisk -rvv
```
```shell
core show help
```
```shell
exit
```

Configure extensions.conf and pjsip.conf
```shell
cd /etc/asterisk
```
```shell
mv extensions.conf extensions.sample
```
```shell
mv sip.conf sip.sample
```
```shell
mv pjsip.conf pjsip.sample
```

#### Creating a basic Dial Plan by editing extensions.conf
```shell
vi extensions.conf
```
```
[public]
exten = 100,1,Goto(hello-world,s,1)
[default]
[hello-world]
exten = s,1,Answer()
same = n,Wait(1)
same = n,Playback(hello-world)
same = n,Hangup()
```
#### Create SIP members in pjsip.conf
```shell
vi pjsip.conf
```
```
[transport-udp]
type=transport
protocol=udp
bind=0.0.0.0

[7000]
type=endpoint
context=public
disallow=all
allow=ulaw
auth=7000
aors=7000

[7000]
type=auth
auth_type=userpass
password=7000
username=7000

[7000]
type=aor
max_contacts=1
```

```shell
asterisk -rx "core restart now"
```
```shell
asterisk -rvvvvv
```
