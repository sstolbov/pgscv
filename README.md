# pgSCV - PostgreSQL ecosystem metrics collector

[По-русски / In Russian](README.ru.md)

### pgSCV
- [collects](https://github.com/cherts/pgscv/wiki/Collectors) a lot of stats about PostgreSQL environment.
- exposes metrics through the HTTP `/metrics` endpoint in [Prometheus metrics exposition format](https://prometheus.io/docs/concepts/data_model/).

**IMPORTANT NOTES**
This project is a continuation of the development of the original pgSCV by [Alexey Lesovsky](https://github.com/lesovsky)

### Features
- **Supported services:** support collecting metrics of PostgreSQL, Pgbouncer and Patroni.
- **OS metrics:** support collecting metrics of operating system.
- **TLS and authentication**. `/metrics` endpoint could be protected with basic authentication and TLS.
- **Collecting metrics from multiple services**. pgSCV can collect metrics from many databases instances.
- **User-defined metrics**. pgSCV could be configured in a way to collect metrics defined by user.
- **Collectors management**. Collectors could be disabled if necessary.
- **Collectors filters**. Collectors could be adjusted to skip collecting metrics based on labels values, like
  block devices, network interfaces, filesystems, users, databases, etc.

### Requirements
- Can run on Linux only; can connect to remote services running on other OS/PaaS.
- Requisites for connecting to the services, such as login and password.
- Database user should have privileges for executing stats functions and reading views.
  For more details see [security considerations](https://github.com/cherts/pgscv/wiki/Security-considerations).

### Quick start
Download the archive from [releases](https://github.com/cherts/pgscv/releases). Unpack the archive. Create minimum config file. Start pgSCV systemd service under `postgres` user.

```bash
curl -s -L https://github.com/cherts/pgscv/releases/download/v0.8.1/pgscv_0.8.1_linux_amd64.tar.gz -o - | tar xzf - -C /tmp && \
mv /tmp/pgscv.yaml /etc && \
mv /tmp/pgscv.service /etc/systemd/system &&  \
mv /tmp/pgscv.default /etc/default/pgscv && \
mv /tmp/pgscv /usr/sbin && \
chown postgres:postgres /etc/pgscv.yaml && \
systemctl daemon-reload && \
systemctl enable pgscv --now
```

or using Docker, use `DATABASE_DSN` for setting up a connection to PostgreSQL:
```bash
docker pull cherts/pgscv:latest
docker run -ti -d --name pgscv \
   -e PGSCV_LISTEN_ADDRESS=0.0.0.0:9890 \
   -e PGSCV_DISABLE_COLLECTORS="system" \
   -e DATABASE_DSN="postgresql://postgres:password@dbhost:5432/postgres" \
   -p 9890:9890 \
   --restart=always \
   cherts/pgscv:latest
```

or using Docker, save `deploy/pgscv.yaml` config file to local directory /etc/pgscv:
```bash
docker pull cherts/pgscv:latest
docker run -ti -d --name pgscv \
   -v /etc/pgscv:/etc/app \
   -p 9890:9890 \
   --restart=always \
   cherts/pgscv:latest \
   --config-file=/etc/app/pgscv.yaml
```

or using Docker-compose, edit file `docker-compose.yaml` for setting up a connection to PostgreSQL:
```bash
mkdir ~/pgscv
curl -s -L https://raw.githubusercontent.com/CHERTS/pgscv/master/deploy/docker-compose.yaml -o ~/pgscv/docker-compose.yaml && cd ~/pgscv
docker-compose up -d
```

When pgSCV has been started it is ready to accept HTTP requests at `http://127.0.0.1:9890/metrics`.

or using k8s deployment
```bash
curl -s -L https://raw.githubusercontent.com/CHERTS/pgscv/master/deploy/deployment.yaml -o ~/deployment.yaml
kubectl apply -f ~/deployment.yaml
```

or using k8s helm chart
```bash
git clone https://github.com/CHERTS/pgscv.git && cd pgscv
kubectl create ns pgscv-ns
helm install -n pgscv-ns pgscv deploy/helm-chart/
```

### Complete setup
Checkout complete setup [guide](https://github.com/cherts/pgscv/wiki/Setup-for-regular-users).

### Documentation
For further documentation see [wiki](https://github.com/cherts/pgscv/wiki).

### Support and feedback
If you need help using pgSCV feel free to open discussion via [email](sleuthhound@gmail.com) or Telegram [@cherts](https://t.me/cherts) or create an [issue](https://github.com/cherts/pgscv/issues)

### Development and contribution
To help development you are encouraged to:
- provide feedback via [email](mailto:sleuthhound@gmail.com) or Telegram [@cherts](https://t.me/cherts) or create an [issue](https://github.com/cherts/pgscv/issues)
- pull requests for new features
- star the project

### Current developer and maintaner
- [Mikhail Grigorev](https://github.com/cherts)

### Authors of original version
- [Alexey Lesovsky](https://github.com/lesovsky)

### License
BSD-3. See [LICENSE](./LICENSE) for more details.
