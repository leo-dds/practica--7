 [#practica-7](https://github.com/leo-dds/practica--7.git)


1.-Archivo  docker-compose.yml:
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
    command: ["/usr/sbin/named", "-g", "-c", "/etc/bind/named.conf"]

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

2.-Lanzamos **docker-compose up -d** 
sri@sri-VirtualBox:~/Docker$ sudo docker-compose up -d 
[+] Running 2/2
 ✔ Container dns_server   Started                                          0.0s 
 ✔ Container dns_cliente  Started  

3.- named.conf (Configuración principal de bind9)
~~~
cat named.conf.local 
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";

zone "leo.castelao.org" {
	type master;
	file "/etc/bind/db.leo.castelao.org";
};

zone "1.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/db.192";
};

~~~

4.- db.leo.castelao.org (Configuración de la zona DNS)

~~~
sudo cat db.leo.castelao.org 
;
; BIND data file for local loopback interface
;
$TTL	604800
@	IN	SOA	localhost. root.localhost. (
			      2		; Serial
			 604800		; Refresh
			  86400		; Retry
			2419200		; Expire
			 604800 )	; Negative Cache TTL
;
@	IN	NS	localhost.
@	IN	A	192.168.1.10
@	IN	AAAA	::1

~~~
