# pgSCV - Сборщик метрик экосистемы PostgreSQL

[In English / По-английски](README.md)

### pgSCV
- [собирает](https://github.com/cherts/pgscv/wiki/Collectors) много статистики о среде PostgreSQL;
- предоставляет метрики по HTTP через эндпойнт `/metrics` в [формат Prometheus](https://prometheus.io/docs/concepts/data_model/);

**IMPORTANT NOTES**
Данный проект является продолжением развития оригинального pgSCV авторства [Alexey Lesovsky](https://github.com/lesovsky)

### Основные возможности
- **Поддерживаемые сервисы**: поддержка сбора показателей работы PostgreSQL, Pgbouncer и Patroni;
- **Метрики ОС:** поддержка сбора показателей работы операционной системы;
- **TLS и аутентификация**: Эндпойнт `/metrics` может быть защищен с помощью базовой аутентификации и TLS;
- **Сбор показателей из нескольких сервисов**: pgSCV может собирать метрики из многих экземпляров баз данных, включая базы данных расположенные в облачных средах (Amazon AWS, Yandex.Cloud, VK.Cloud);
- **Настраиваемые пользовательские метрики**: pgSCV можно настроить на сбор кастомных пользовательских метрик;
- **Управление коллекторами**: При необходимости коллекторы можно отключить;
- **Коллекторные фильтры**: Коллекторы можно настроить так, чтобы они пропускали сбор метрик на основе значений меток, например блочные устройства, сетевые интерфейсы, файловые системы, пользователи, базы данных и т.д.;

### Системные требования:
- Может работать только в ОС Linux;
- Может подключаться к удаленным сервисам, работающим на другой ОС/PaaS;
- Необходимы данные для подключения к сервисам/базам, такие как адрес, логин и пароль;
- Пользователь базы данных должен иметь права на выполнение статистических функций и чтение представлений.
  Для получения более подробной информации смотрите [соображения безопасности](https://github.com/cherts/pgscv/wiki/Security-considerations).

### Быстрый старт
Загрузите архив со страницы [releases](https://github.com/cherts/pgscv/releases). Распакуйте архив. Создайте минимальный файл конфигураации. Запустите pgSCV под пользователем postgres.

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

или используя Docker, используйте `DATABASE_DSN` для настройки соединения с PostgreSQL:
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

или используя Docker, сохраните файл конфигурации `deploy/pgscv.yaml` на локальный сервер в каталог /etc/pgscv:
```bash
docker pull cherts/pgscv:latest
docker run -ti -d --name pgscv \
   -v /etc/pgscv:/etc/app \
   -p 9890:9890 \
   --restart=always \
   cherts/pgscv:latest \
   --config-file=/etc/app/pgscv.yaml
```

или используя Docker-compose, отредактируйте файл `docker-compose.yaml` для настройки соединения с PostgreSQL:
```bash
mkdir ~/pgscv
curl -s -L https://raw.githubusercontent.com/CHERTS/pgscv/master/deploy/docker-compose.yaml -o ~/pgscv/docker-compose.yaml && cd ~/pgscv
docker-compose up -d
```

После запуска pgSCV он готов принимать HTTP-запросы по адресу `http://127.0.0.1:9890/metrics`

или используя развертывание приложения в k8s
```bash
curl -s -L https://raw.githubusercontent.com/CHERTS/pgscv/master/deploy/deployment.yaml -o ~/deployment.yaml
kubectl apply -f ~/deployment.yaml
```

или используя helm chart для k8s
```bash
git clone https://github.com/CHERTS/pgscv.git && cd pgscv
kubectl create ns pgscv-ns
helm install -n pgscv-ns pgscv deploy/helm-chart/
```

### Полная настройка
Полная настройка описана в [руководстве](https://github.com/cherts/pgscv/wiki/Setup-for-regular-users).

### Документация
Дополнительную документацию смотрите в [wiki](https://github.com/cherts/pgscv/wiki).

### Поддержка и обратная связь
Если вам нужна помощь в использовании pgSCV Вы можете написать мне на [email](sleuthhound@gmail.com) или в Telegram [@cherts](https://t.me/cherts) или открыть [issues](https://github.com/cherts/pgscv/issues).

### Развитие и вклад
Для содействия развитию проекта Вы можете:
- написать мне на [email](mailto:sleuthhound@gmail.com) или в Telegram [@cherts](https://t.me/cherts) или создать [issue](https://github.com/cherts/pgscv/issues)
- запросить новую функциональность;
- поставить звезду проекту на Github;

### Текущий разработчик и сопровождающий
- [Mikhail Grigorev](https://github.com/cherts)

### Автор оригинальной версии
- [Alexey Lesovsky](https://github.com/lesovsky)

### Лицензия
BSD-3. Смотри файл [LICENSE](./LICENSE) для получения большей информации.
