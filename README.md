![Banner](./assets/banner.png)

# Conceptos
MQTT es un sistema de comunicación optimizado para el Internet de las Cosas (IoT) y la domótica.
<table>
  <tr>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>broker</td>
    <td>servidor intermediario para los mensajes MQTT. Recibe mensajes de "publicadores" y los entrega a los "suscriptores"</td>
  </tr>
  <tr>
    <td>Publicadores</td>
    <td>Clientes que envían información. Un publicador no necesita saber quién recibirá el mensaje.</td>
  </tr>
  <tr>
    <td>Suscriptores</td>
    <td>Clientes que quieren recibir información. Se suscriben a uno o más topics en el broker.<br>Cuando se publica un msg en un topic al que están suscritos, el broker se lo envía.</td>
  </tr>
  <tr>
    <td>Topic </td>
    <td>Dirección a la que se envían los mensajes o de la que se reciben. </td>
  </tr>
  <tr>
    <td>mensaje o payload</td>
    <td>Payload que los clientes publican en un topic, suele contener un JSON.</td>
  </tr>
  <tr>
    <td>QoS (Quality of Service)</td>
    <td>nivel de garantía que MQTT ofrece para que un mensaje llegue a su destino.<br>
      - QoS 0: Sin confirmación de entrega<br>
      - QoS 1: Publicador espera una confirmación del broker: El msg llegará al menos una vez.<br>
      - QoS 2: Utiliza un "handshake": mensaje llegará exactamente una vez.</td>
  </tr>
</table>

# Localizar broker mqtt en tu red

Buscaremos con nmap los puertos 1883 y 8883 **abiertos** en nuestra red local.
```
$ nmap -p 1883,8883 192.168.18.0/24
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-06-11 11:22 CEST
Nmap scan report for _gateway (192.168.18.1)
Host is up (0.0046s latency).

[...]

Nmap scan report for 192.168.18.3
Host is up (0.0083s latency).

PORT     STATE    SERVICE
1883/tcp open     mqtt
8883/tcp filtered secure-mqtt

[...]
```

En nuestro caso el broker es 192.168.18.3

# Publicar MQTT desde linux

Instalamos el cliente mosquitto para linux
```
sudo apt update
sudo apt install mosquitto-clients
```
Publicamos el mensaje que nos interese con este comando
```
$ mosquitto_pub -h <servidor_mqtt> -t <payload>
```
Ejemplos:
``` 
$ mosquitto_pub -h 192.168.18.3 -t "ch15/Cortina/set" -m '{"position": 0}'
$ mosquitto_pub -h 192.168.18.3 -t "ch15/0xa4c138702cbbba31/set" -m '{"brightness": 50, "state": "ON"}'
$ mosquitto_pub -h 192.168.18.3 -t "ch15/0xa4c138702cbbba31/set" -m '{"state": "OFF"}'
```

# Suscribirse a un topic
```
$ mosquitto_sub -h <servidor_mqtt> -t <topic>
```
```
$ mosquitto_sub -h 192.168.18.3 -t "ch15/0xa4c138702cbbba31/#"
```
# Comodines en MQTT:

Los comodines **solo** se pueden usar en filtros de tópicos para suscriptores.

### Comodín de un solo nivel: + 

Debe ocupar un nivel completo, es decir, debe estar rodeado por barras / (o ser el único nivel).

Ejemplo:

Suscripcion a todos los comandos set de dispositivos directamente bajo ch15/, como ch15/lampara1/set o ch15/cortina/set:
```
ch15/+/set: 
```
### Comodín de múltiples niveles: \# 

Debe ser el último carácter en el filtro del tópico y siempre debe ir precedido de una barra /.

Ejemplo:
Suscripcion a todos los tópicos que comiencen con casa/:
```
casa/#
```
Si te suscribes solo a almohadilla, recibirás absolutamente todos los mensajes que pasan por el broker MQTT
