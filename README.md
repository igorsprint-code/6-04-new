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

`При необходимости прикрепитe сюда скриншоты
![zadanie3](https://github.com/igorsprint-code/6-04-new/blob/main/images/zad3.jpg)

### Задание 4

`Приведите ответ в свободной форме........`

1. `Заполните здесь этапы выполнения, если требуется ....`
2. `Заполните здесь этапы выполнения, если требуется ....`
3. `Заполните здесь этапы выполнения, если требуется ....`
4. `Заполните здесь этапы выполнения, если требуется ....`
5. `Заполните здесь этапы выполнения, если требуется ....`
6. 

```
Поле для вставки кода...
....
....
....
....
```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота](ссылка на скриншот)`
