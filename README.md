## Steps to run Nextcloud in rootless docker:
1. Copy systemd service file to default folder and run it:
```bash
cp nextcloud-rootless-compose.service ~/.config/systemd/user/
systemctl --user enable --now nextcloud-rootless-compose.service
```
2. Open port `80` and `443`:
```bash
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
# or if you are using iptables
sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 443 -j ACCEPT
sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 80 -j ACCEPT
sudo iptables-save > /etc/iptables/rules.v4
```

This compose file based on example from oficial nextcloud docker repository, [link](https://github.com/nextcloud/docker/tree/master/.examples/docker-compose/with-nginx-proxy/mariadb/fpm). I chose compose file with `mariadb` and `nginx` proxy.

Before running rename all `.exapmle` files, leave `redis-session.ini` empty, set all environment variables in `nc.env` and `web.env`.

Also, you should run cronjob on machine, not docker. Run `crontab -e` and insert this, change nextcloud container name:
```bash
*/5 * * * * DOCKER_HOST=unix:///run/user/$(id -u)/docker.sock docker exec -u www-data:www-data NEXTCLOUD_CONTAINER_NAME php ./cron.php 
```

Database and proxy run in separate service, so I can use it in other services. Check out [this](https://github.com/semka95/common-compose) repository for more information.

Install [preview generator](https://apps.nextcloud.com/apps/previewgenerator) app and add cronjob:
```bash
*/50 * * * * DOCKER_HOST=unix:///run/user/$(id -u)/docker.sock docker exec -u www-data:www-data rootless-nextcloud-compose_app_1 php ./occ preview:pre-generate
```
This will prevent server crashing when generating a lot of previews wnen opening folder with a lot of images.


### Links
- [Rootless (non-root user) Docker and Docker Compose running NGINX on Debian 10](https://blog.leandrotoledo.org/rootless-docker-and-docker-compose-running-nginx-on-debian-10/)
- [Run the Docker daemon as a non-root user (Rootless mode)](https://docs.docker.com/engine/security/rootless/)
- [How to run cron job in rootless mode](https://github.com/nextcloud/docker/issues/458#issuecomment-986224253)
- [Solve problem with redis not running in rootless mode](https://github.com/nextcloud/docker/issues/763#issuecomment-1007447212)
- [Solution to "You are accessing your instance over a secure connection..." problem](https://github.com/nextcloud/docker/issues/1672#issuecomment-1013956922)
