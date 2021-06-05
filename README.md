# Asterisk-installation

###A little house keeping before installation.
`yum update`
`sed -i 's/\(^SELINUX=\).*/\SELINUX=disabled/' /etc/sysconfig/selinux`
`sed -i 's/\(^SELINUX=\).*/\SELINUX=disabled/' /etc/selinux/config`
`sestatus`
`reboot`
`sestatus`
`sudo yum install httpd`
`firewall-cmd --zone=public --add-port=80/tcp --permanent`
`firewall-cmd --zone=public --permanent --add-service=http`
`firewall-cmd --zone=public --permanent --add-service=https`
`firewall-cmd --reload`
`sudo systemctl enable httpd.service`
`sudo systemctl start httpd.service`

###Installing DependenciesPermalink
`yum install -y epel-release dmidecode gcc-c++ ncurses-devel libxml2-devel make wget openssl-devel newt-devel kernel-devel sqlite-devel libuuid-devel gtk2-devel jansson-devel binutils-devel`
-----------------------------------
####For asterisk 16
`dnf --enablerepo=PowerTools install libedit-devel`
`dnf --enablerepo=PowerTools install libsrtp`
`dnf --enablerepo=PowerTools install libsrtp-devel`


#Install Asterisk 16
`cd /usr/src/`
`wget http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-16-current.tar.gz`
`tar -zxvf asterisk-16-current.tar.gz`
`mv asterisk-16.x.x asterisk`
`cd asterisk`

`contrib/scripts/install_prereq install`
`./configure --with-pjproject-bundled --with-crypto --with-ssl=ssl --with-srtp --with-jansson-bundled`
`make menuselect`
`make`
`make install`
`make samples`
`make config`
`ldconfig`
`sudo service asterisk start`

`asterisk -rvv`
`core show help`
`exit`

`cd /etc/asterisk`
`mv extensions.conf extensions.sample`
`mv sip.conf sip.sample`
`mv pjsip.conf pjsip.sample`

####Create extensions.conf
`vi extensions.conf`
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
####Create pjsip.conf
`vi pjsip.conf`
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

`asterisk -rx "core restart now"`
`asterisk -rvvvvv`
