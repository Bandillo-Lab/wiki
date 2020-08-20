# bandilo wiki
config files for private wiki
### steps to reproduce:
1. install docker, on Centos8, this is: 
`dnf -y install docker-ce --nobest`
2. configure firewall so docker containers can forward packets to each other without issues: 
```
  firewall-cmd --permanant --zone=public --add-rich-rule='rule family=ipv4 source address=172.27.0.0/16 accept'
  firewall-cmd --reload
  sysctl net.bridge.bridge-nf-call-iptables=0
  sysctl net.bridge.bridge-nf-call-iptables=0
  sysctl net.bridge.bridge-nf-call-arptables=0
  sysctl net.bridge.bridge-nf-call-ip6tables=0
```
3. pull the needed docker images and run them: 
```
docker run --name=mariadb -p 3306:3306 -v wiki-volume:/var/lib/mysql -e MARIADB_ROOT_PASSWORD=CHANGETHISPASS123 -d mariadb
docker run --name=wikiname -p 80:80 -p 443:443 --link maria-db -d simplyintricate/mediawiki
```
4. copy over config files, including the system .key file you generate with a certificate signing authority - otherwise just use [letsencrypt](https://letsencrypt.org/)
```
docker cp ./bundle.cer wikiname:/etc/nginx/conf.d/
docker cp ./yoursite.key wikiname:/etc/nginx/conf.d/
docker cp ./default.conf wikiname:/etc/nginx/conf.d/
docker restart wikiname
```
5. install mediawiki as per normal, but use the name of the mariadb server container as your SQL server host during installation. 
