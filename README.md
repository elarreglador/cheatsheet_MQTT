![Banner](./assets/banner.png)

# Localizar broker mqtt en tu red

Buscaremos con nmap los puertos 1883 y 8883 *abiertos* en nuestra red local.

```
david@david-pc:~/mqtt$ nmap -p 1883,8883 192.168.18.0/24
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-06-11 11:22 CEST
Nmap scan report for _gateway (192.168.18.1)
Host is up (0.0046s latency).

PORT     STATE  SERVICE
1883/tcp closed mqtt
8883/tcp closed secure-mqtt

Nmap scan report for 192.168.18.2
Host is up (0.0086s latency).

PORT     STATE    SERVICE
1883/tcp filtered mqtt
8883/tcp filtered secure-mqtt

Nmap scan report for 192.168.18.3
Host is up (0.0083s latency).

PORT     STATE    SERVICE
1883/tcp open     mqtt
8883/tcp filtered secure-mqtt

...
```

En nuestro caso el broker es 192.168.18.3

# Publicar MQTT desde linux

Instalamos el cliente mosquitto para linux

```
sudo apt update
sudo apt install mosquitto-clients
```

Publicamos el mensaje que nos interese

``` 
$ mosquitto_pub -h 192.168.18.3 -t "ch15/Cortina/set" -m '{"position": 0}'
$ mosquitto_pub -h 192.168.18.3 -t "ch15/0xa4c138702cbbba31/set" -m '{"brightness": 50, "state": "ON"}'
$ mosquitto_pub -h 192.168.18.3 -t "ch15/0xa4c138702cbbba31/set" -m '{"state": "OFF"}'
```
