# [practica-7].(https://github.com/leo-dds/practica--7.git# practica--7)

1.- Creamos el contenedor con **docker run -dit --name dns_cliente alpine**

2.-Archivo  docker-compose.yml:
~~~
  sri@sri-VirtualBox:~/Docker$ cat docker-compose.yml 
version: '3.8'

services:
  dns_server:
    image: ubuntu/bind9
    container_name: dns_server
    networks:
      dns_network:
        ipv4_address: 10.0.2.15
    volumes:
      - ./bind_config:/etc/bind
    environment:
      - BIND9_USER=root
    command: ["-g", "-c", "/etc/bind/named.conf"]

  dns_client:
    image: alpine
    container_name: dns_cliente
    networks:
      dns_network:
    depends_on:
      - dns_server
    entrypoint: ["/bin/sh", "-c", "apk add --no-cache bind-tools && sleep infinity"]

networks:
  dns_network:
    driver: bridge
    ipam:
      config:
        - subnet: 172.18.0.0/16
~~~

