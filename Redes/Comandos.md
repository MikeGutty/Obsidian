```bash
# para ver detalle de la red
ifconfig
ip addr show

# envia datos de paquetes a un server para ver la conexion de red
ping 192.168.40.12

# envia 3 paquetes al destino y mostrara hosts o saltos para llegar al servidor
tracert www.google.in

# muestra todos los puertos abiertos tcp
netstat -antp

# muestra el pid del programa
ps -ef | grep apache2

# para ver los puertos del PID
netstat - antp | grep 3336

# para ver los puertos usados
ss -tunlp

# Escanear el puerto de destino
nmap DOMAIN
nmap IP
```