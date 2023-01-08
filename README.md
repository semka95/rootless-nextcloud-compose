## Nextcloud Rootless

This compose file based on example from oficial nextcloud docker repository, [link](https://github.com/nextcloud/docker/tree/master/.examples/docker-compose/with-nginx-proxy/mariadb/fpm). I chose compose file with `mariadb` and `nginx` proxy.

Before running rename all `.exapmle` files, leave `redis-session.ini` empty, set all environment variables in `nc.env` and `web.env`.

Also, you should run cronjob on machine, not docker. Run `crontab -e` and insert this, change nextcloud container name:
```bash
*/5 * * * * DOCKER_HOST=unix:///run/user/$(id -u)/docker.sock docker exec -u www-data:www-data NEXTCLOUD_CONTAINER_NAME php ./cron.php 
```

Database and proxy run in separate service, so I can use it in other services. Check out [this](https://github.com/semka95/common-compose) repository for more information.

TODO:
- Run collabora, currently there is an issue - https://github.com/CollaboraOnline/online/issues/2905.

### Links
- [Rootless (non-root user) Docker and Docker Compose running NGINX on Debian 10](https://blog.leandrotoledo.org/rootless-docker-and-docker-compose-running-nginx-on-debian-10/)
- [Run the Docker daemon as a non-root user (Rootless mode)](https://docs.docker.com/engine/security/rootless/)
- [How to run cron job in rootless mode](https://github.com/nextcloud/docker/issues/458#issuecomment-986224253)
- [Solve problem with redis not running in rootless mode](https://github.com/nextcloud/docker/issues/763#issuecomment-1007447212)
- [Solution to "You are accessing your instance over a secure connection..." problem](https://github.com/nextcloud/docker/issues/1672#issuecomment-1013956922)
