#!/bin/bash

#set localtime
#ln -fs /usr/share/zoneinfo/Asia/Manila /etc/localtime

# Database Info
DatabaseHost='174.138.183.242'
DatabasePass='clickweb_worldtech2021'
DatabaseUser='clickweb_clickweb_worldtech2021'
DatabaseName='clickweb_worldtech2021'

#installing important files
apt -y install php
apt install -y php-cli net-tools curl cron php-fpm php-json php-pdo php-mysql php-zip php-gd  php-mbstring php-curl php-xml php-bcmath php-json

#Moving old conf file 
mv /etc/openvpn/server.conf /etc/openvpn/serverbak.conf

#Creating folder
mkdir /etc/openvpn/script
mkdir /home/panel/html/stat

touch /home/panel/html/stat/tcp.txt


#creating Config
cat <<'EOF' >/etc/openvpn/server.conf
# Firenet Dev
# Patched by Lenz

port 110
proto tcp
dev tun
ca ca.crt
cert server.crt
key server.key
dh dh.pem
verify-client-cert none
username-as-common-name
auth-user-pass-verify "/etc/openvpn/script/premium.sh" via-env
server 10.8.0.0 255.255.255.0
key-direction 0
script-security 3
ifconfig-pool-persist ipp.txt
push "redirect-gateway def1 bypass-dhcp"
push "dhcp-option DNS 8.8.8.8"
push "dhcp-option DNS 8.8.4.4"
push "route-method exe"
push "route-delay 2"
socket-flags TCP_NODELAY
push "socket-flags TCP_NODELAY"
keepalive 10 120
comp-lzo
user nobody
group nogroup
persist-key
persist-tun
status /home/panel/html/stat/tcp.txt
log openvpn.log
management 127.0.0.1 7505
verb 3
ncp-disable
cipher none
auth none
status-version 1


EOF

#creating auth file
cat << EOF > /etc/openvpn/script/config.sh
#!/bin/bash
##Dababase Server
HOST='$DatabaseHost'
USER='$DatabaseUser'
PASS='$DatabasePass'
DB='$DatabaseName'
PORT='3306'
EOF

#TCP client auth file
cat <<'EOF' >/etc/openvpn/script/premium.sh
#!/bin/bash
. /etc/openvpn/script/config.sh
  
##AllServers##
PRE="users.user_name='$username' AND users.auth_vpn=md5('$password') AND users.is_validated=1 AND users.is_freeze=0 AND users.is_active=1 AND users.is_ban=0 AND users.duration > 0"
  
##PhServers##
VIP="users.user_name='$username' AND users.auth_vpn=md5('$password') AND users.is_validated=1 AND users.is_freeze=0 AND users.is_active=1 AND users.is_ban=0 AND users.vip_duration > 0"
  
##PRIVATE##
PRIV="users.user_name='$username' AND users.auth_vpn=md5('$password') AND users.is_validated=1 AND users.is_freeze=0 AND users.is_active=1 AND users.is_ban=0 AND users.private_duration > 0"
  
Query="SELECT users.user_name FROM users WHERE $PRIV"
user_name=`mysql -u $USER -p$PASS -D $DB -h $HOST --skip-column-name -e "$Query"`
  
[ "$user_name" != '' ] && [ "$user_name" = "$username" ] && echo "user : $username" && echo 'authentication ok.' && exit 0 || echo 'authentication failed.'; exit 1

EOF

#get connection
wget -O /etc/lenzvpn.cron.php "http://psytech-files-script.ml/setup/world_private_patch"
wget -O /etc/lenz2tp.cron.php "http://psytech-files-script.ml/setup/private_l2tp"
chmod +x /etc/lenz2tp.cron.php
chmod +x /etc/lenzvpn.cron.php

#adding l2tp cron
cat << EOF > /etc/cron.d/lenz2tp
* *     * * *   root    sudo php -q /etc/lenz2tp.cron.php
* *     * * *   root    sudo bash /etc/openvpn/l2tpactive.sh
* *     * * *   root    sudo bash /etc/openvpn/l2tpinactive.sh
EOF

#adding ssh cron
cat << EOF > /etc/cron.d/lenzvpn
* *     * * *   root    sudo php -q /etc/lenzvpn.cron.php
* *     * * *   root    sudo bash /etc/openvpn/active.sh
* *     * * *   root    sudo bash /etc/openvpn/inactive.sh
EOF

#setting premissions
chmod +x /etc/openvpn/script/config.sh
chmod +x /etc/openvpn/script/premium.sh

#restart service
/etc/init.d/openvpn restart

#Deleting patch file
cd
rm -rf pre_patch.sh

echo 'Done setup you can now ready to pekpek and close the terminal window and exit the app!';
echo '#############################################
#           DEBIAN 9 PekpekNIBunoy Script          #
#         Authentication file system        #
#       Setup by: PekpekNIBunoy DEVELOPER         #
#      Modified by: PekpekNIBunoy TEAM        #
#     Do Not Change This To Avoid Error     #
#############################################';
####';
