 [#practica-7](https://github.com/leo-dds/practica--7.git)

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
        ipv4_address: 172.18.0.2
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
3.- named.conf (Configuración principal de bind9)
~~~
cat named.conf

options {
    directory "/var/cache/bind";

    forwarders {
        8.8.8.8; // Google DNS como ejemplo
        8.8.4.4;
    };

    allow-query { any; };
};

zone "mizona.int" {
    type master;
    file "/etc/bind/db.mizona.int";
};
~~~

4.- db.mizona.int (Configuración de la zona DNS)

~~~
cat db.mizona.int 

$TTL    604800
@       IN      SOA     ns.mizona.int. admin.mizona.int. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;

@       IN      NS      ns.mizona.int.
ns      IN      A       172.18.0.2
www     IN      CNAME   ns.mizona.int.
mail    IN      A       172.18.0.2
@       IN      TXT     "Bienvenido a mizona.int"

~~~
