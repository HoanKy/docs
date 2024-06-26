1. SSH

- On local:
```
ls -l ~/.ssh/id*
mkdir ~/.ssh
chmod 700 ~/.ssh
ssh-keygen
   /home/<username>/.ssh/id_rsa_<server_name>
ls -l ~/.ssh/
cat /home/<username>/.ssh/id_rsa_<server_name>.pub

nano ~/.ssh/config
Host <alias_name>
    HostName xxx.xxx.xx.xxx
    User <username>
    Port xxx
ssh <alias_name>
```


- On server:
```
# Change port:
nano /etc/ssh/sshd_config
Port 123456
systemctl restart sshd
netstat -tulpn | grep ssh
ssh -p 123456 192.168.1.100

# Disable login with password for all accout
PasswordAuthentication no
nano /etc/ssh/sshd_config.d/50-cloud-init.conf  # It will mix with ssh/sshd_config
If see PasswordAuthentication yes, set it to no

If not working, read more about 
ChallengeResponseAuthentication no

#Login with key:
sudo useradd -m -s /usr/bin/zsh <username>
sudo passwd <username>
ls -l ~/.ssh/
mkdir ~/.ssh
chmod 700 ~/.ssh
touch authorized_keys
chmod 600 ~/.ssh/authorized_keys
nano ~/.ssh/authorized_keys
```


2. nginx
```
sudo nano /etc/nginx/sites-available/default
server {
    listen 80;
    access_log off; # if don't want get logs
    server_name abc.com;

    location / {
        proxy_set_header Host $host;
        proxy_pass http://127.0.0.1:5000;
        proxy_redirect off;
    }
}
```

```
/etc/nginx/nginx.conf

events {
    worker_connections 20000;
}
```


```
sudo nano /etc/nginx/nginx.conf
client_max_body_size 100M;
```
```
sudo rm /var/log/nginx/access.log 
sudo service nginx reload // after reload nginx, automatic recreate access.log and error.log
sudo truncate --size 0 /var/log/nginx/access.log // truncate to 0 kb and increase from 0 kb
```

```
Note: Remember config sticky session
https://github.com/socketio/socket.io/issues/4239#issuecomment-1011912700
https://socketio.bootcss.com/docs/using-multiple-nodes/#NginX-configuration
http {
  	server {
		listen 3000;
		server_name io.yourhost.com;

		location / {
			proxy_set_header X- Forwarded - For $proxy_add_x_forwarded_for;
			proxy_set_header Host $host;

			proxy_pass http://nodes;

			# enable WebSockets
			proxy_http_version 1.1;
			proxy_set_header Upgrade $http_upgrade;
			proxy_set_header Connection "upgrade";
		}
}

	upstream nodes {
	    # enable sticky session based on IP;
	    ip_hash;

	    server app01: 3000;
	    server app02: 3000;
	    server app03: 3000;
	}
}
```

3. ufw
```
sudo nano /etc/default/ufw
IPV6=yes

sudo ufw default deny incoming
sudo ufw default allow outgoing

sudo ufw allow ssh
ufw allow 'Nginx HTTP'
sudo ufw allow 1234/tcp

sudo ufw logging on

sudo ufw status
sudo ufw status numbered
sudo ufw delete 3
sudo ufw delete allow 22/tcp

sudo ufw status verbose (list)
sudo ufw reload

SSH
sudo ufw allow 'Nginx Full'
sudo ufw delete allow 'Nginx HTTP'
```


4. docker
```
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu `lsb_release -cs` test"
sudo apt update
sudo apt install docker-ce

mkdir /etc/docker
sudo nano /etc/docker/daemon.json
{
  "log-driver": "json-file", # none
  "log-opts": {
    "max-size": "1m",
    "max-file": "3"
  }
}
sudo service docker restart
sudo systemctl stop docker.socket // If can not restart and restart again

[sudo permission ](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user)
```


5. scp
```
scp -P 2290 public/uploads.zip root@159.223.64.220:
scp -i ~/.ssh/id_rsa_server  -P 2290 public/uploads.zip root@159.223.64.220:
scp user@server:/path/to/remotefile.zip /Local/Target/Destination
```

6. npm
```
apt install npm
```

7. git 

``` 
Multi ssh for diffrent project
ssh-keygen -t rsa -b 4096 -C "bamboo@gmail.com"
/root/.ssh/id_rsa_<app_name>
cat ~/.ssh/id_rsa_<app_name>.pub 

nano ~/.ssh/config
Host github.com_<app_name>
	HostName github.com
	User git
	IdentityFile ~/.ssh/id_rsa_<app_name>

git clone git@github.com_<app_name>:xxx/xxx.git
```

8. SSL

```
sudo apt install certbot
sudo apt install python3-certbot-dns-cloudflare

cat ~/.secrets/certbot/cloudflare.ini
dns_cloudflare_api_token = cewqd2sqxsxs

sudo chmod 600 ~/.secrets/certbot/cloudflare.ini

certbot certonly \
  --dns-cloudflare \
  --dns-cloudflare-credentials ~/.secrets/certbot/cloudflare.ini \
  -d abc.com \
  -d *.abc.com

sudo nano /etc/nginx/sites-available/default
server {
    listen 80;
    #access_log off;
    server_name abc.com;

    listen 443 ssl;

    # RSA certificate
    ssl_certificate /etc/letsencrypt/live/abc.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/abc.com/privkey.pem;

    location / {
        proxy_set_header Host $host;
        proxy_pass http://127.0.0.1:4000;
        proxy_redirect off;
    }
}

sudo certbot renew --dry-run
sudo certbot renew --reuse-key
sudo certbot renew --reuse-key --dry-run

sudo crontab -e
0 1,13 * * * sudo certbot renew --reuse-key && sudo service nginx reload >> /var/log/letsencrypt/renew.log

ls -la /etc/letsencrypt/live/abc.com
```


9. Monitoring

```
iotop: Disk I/O
htop: CPU / RAM
???: Bandwidth
```

10. Redis

```
echo 1 >/proc/sys/vm/overcommit_memory
```
