# Домашнее задание к занятию "`Docker. Часть 2`" - `Дедяхин Игорь`


### Задание 1

Напишите ответ в свободной форме, не больше одного абзаца текста.

Установите Docker Compose и опишите, для чего он нужен и как может улучшить лично вашу жизнь.

### Решение

Docker compose позволяет запускать несколько контейнеров с помощью одного yaml-файла. Вместо того чтобы запускать каждый контейнер отдельно с помощью команд docker run со множеством параметров, я могу описать все сервисы (Prometheus, Pushgateway, Grafana) одним файлом, указывая связи между сервисами, общие сети и тома. Одной командой "docker compose up" разворачивается вся система мониторинга, контейнеры автоматически видят друг друга по именам, а при изменении конфигурации достаточно выполнить "docker compose restart", что экономит время. 



---

### Задание 2

Выполните действия и приложите текст конфига на этом этапе.

Создайте файл docker-compose.yml и внесите туда первичные настройки:

* version;  
* services;  
* volumes;  
* networks.  
При выполнении задания используйте подсеть 10.5.0.0/16. Ваша подсеть должна называться: <ваши фамилия и инициалы>-my-netology-hw. Все приложения из последующих заданий должны находиться в этой конфигурации.`

### Решение

```ruby
version: '3'

volumes:
  prometheus_data:
  grafana-data:

services:

networks:
  Dedyakhin_IV-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16    
          gateway: 10.5.0.1


```


---

### Задание 3

Выполните действия:

Создайте конфигурацию docker-compose для Prometheus с именем контейнера <ваши фамилия и инициалы>-netology-prometheus.
Добавьте необходимые тома с данными и конфигурацией (конфигурация лежит в репозитории в директории 6-04/prometheus ).
Обеспечьте внешний доступ к порту 9090 c докер-сервера.

### Решение

```ruby
prometheus:
    image: prom/prometheus:v2.47.2
    container_name: Dedyakhin_IV-netology-prometheus
    command: --web.enable-lifecycle --config.file=/etc/prometheus/prometheus.yml
    ports:
      - 9090:9090
    volumes:
      - ./prometheus:/etc/prometheus
      - prometheus_data:/prometheus
    networks:
      - Dedyakhin_IV-my-netology-hw
    restart: always

  node-exporter:
    image: quay.io/prometheus/node-exporter:latest
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro,rslave
      - /:/host:ro,rslave   # Ensure using 'rslave' for /host mount
    command: 
      - '--path.rootfs=/host'
      - '--path.procfs=/host/proc'
      - '--path.sysfs=/host/sys'
      - --collector.filesystem.ignored-mount-points
      - "^/(sys|proc|dev|host|etc|rootfs/var/lib/docker/containers|rootfs/var/lib/docker/overlay2|rootfs/run/docker/netns|rootfs/var/lib/docker/aufs)($$|/)"
    ports:
      - 9100:9100
    networks:
      - Dedyakhin_IV-my-netology-hw
    restart: always
    deploy:
      mode: global
```

Скриншот браузера
![zadanie3](https://github.com/igorsprint-code/6-04-new/blob/main/images/zad3.jpg)


---


### Задание 4

Выполните действия:

Создайте конфигурацию docker-compose для Pushgateway с именем контейнера <ваши фамилия и инициалы>-netology-pushgateway.
Обеспечьте внешний доступ к порту 9091 c докер-сервера.

```ruby
pushgateway:
    image: prom/pushgateway:v1.6.2
    container_name: Dedyakhin_IV-netology-pushgateway
    ports:
      - 9091:9091
    networks:
     - Dedyakhin_IV-my-netology-hw
    depends_on: 
      - prometheus
    restart: unless-stopped
```

Скриншот браузера
![zad4](https://github.com/igorsprint-code/6-04-new/blob/main/images/zad4.jpg)


---

### Задание 5

Выполните действия:

Создайте конфигурацию docker-compose для Grafana с именем контейнера <ваши фамилия и инициалы>-netology-grafana.
Добавьте необходимые тома с данными и конфигурацией (конфигурация лежит в репозитории в директории 6-04/grafana.
Добавьте переменную окружения с путем до файла с кастомными настройками (должен быть в томе), в самом файле пропишите логин=<ваши фамилия и инициалы> пароль=netology.
Обеспечьте внешний доступ к порту 3000 c порта 80 докер-сервера.


### Решение

```ruby
grafana:
    image: grafana/grafana
    container_name: Dedyakhin_IV-netology-grafana
    environment:
      GF_PATHS_CONFIG: /etc/grafana/custom.ini
    ports:
      - 80:3000
    volumes:
      - ./grafana:/etc/grafana
      - grafana-data:/var/lib/grafana
    networks:
      - Dedyakhin_IV-my-netology-hw
    depends_on: 
      - prometheus
    restart: unless-stopped


```

Скриншот браузера
![zad5](https://github.com/igorsprint-code/6-04-new/blob/main/images/zad5.jpg)


---


### Задание 6

Выполните действия.

Настройте поочередность запуска контейнеров.
Настройте режимы перезапуска для контейнеров.
Настройте использование контейнерами одной сети.
Запустите сценарий в detached режиме.


### Решение

Поочередность, режим перезапуска и локальная сеть были указаны выше.


Скриншот запуска сценария в detached режиме.

![zad6](https://github.com/igorsprint-code/6-04-new/blob/main/images/zad6.jpg)



---


### Задание 7

Выполните действия.

1. Выполните запрос в Pushgateway для помещения метрики <ваши фамилия и инициалы> со значением 5 в Prometheus: echo "<ваши фамилия и инициалы> 5" | curl --data-binary @- http://localhost:9091/metrics/job/netology.
2. Залогиньтесь в Grafana с помощью логина и пароля из предыдущего задания.
3. Cоздайте Data Source Prometheus (Home -> Connections -> Data sources -> Add data source -> Prometheus -> указать "Prometheus server URL = http://prometheus:9090" -> Save & Test).
4. Создайте график на основе добавленной в пункте 5 метрики (Build a dashboard -> Add visualization -> Prometheus -> Select metric -> Metric explorer -> <ваши фамилия и инициалы -> Apply.




### Решение

Команда echo

![zad7](https://github.com/igorsprint-code/6-04-new/blob/main/images/zad7a.jpg)

Metric explorer

![zad7](https://github.com/igorsprint-code/6-04-new/blob/main/images/zad7b.jpg)

Скриншот графика, построенного на основе метрики.

![zad7](https://github.com/igorsprint-code/6-04-new/blob/main/images/zad7c.jpg)



---


### Приложения

docker-compose.yml целиком
```
https://github.com/igorsprint-code/6-04-new/blob/main/docker-compose.yml
```

скриншот команды docker ps после запуске docker-compose.yml;




скриншот графика, постоенного на основе вашей метрики.

![zad7](https://github.com/igorsprint-code/6-04-new/blob/main/images/zad7c.jpg)























