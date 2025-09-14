# Lab dns explained till this point

# Guía Completa: Funcionamiento del Sistema DNS

## Laboratorio Slackware/Solaris - Análisis Técnico Detallado

---

## 1. ¿QUÉ ES DNS Y CÓMO FUNCIONA?

### Concepto Fundamental

DNS (Domain Name System) es un sistema distribuido que traduce nombres de dominio legibles para humanos (como `www.google.com`) a direcciones IP que entienden las máquinas (como `172.217.16.164`).

### Arquitectura Jerárquica

```
                    . (root)
                   / | \
                 com org net
                /     |    \
           google  andersson cristian
          /    \       |        \
        www   mail   server1   server2

```

### Tu Configuración Específica

```
Slackware (10.2.77.176)  ←→  Solaris (10.2.77.178)
     ↓                           ↓
PRIMARY: andersson.org.uk   PRIMARY: cristian.com.it
SECONDARY: cristian.com.it  SECONDARY: andersson.org.uk

```

---

## 2. ANÁLISIS DE ARCHIVOS DE CONFIGURACIÓN

### 2.1 `/etc/named.conf` - El Archivo Maestro

**Propósito**: Archivo principal que define cómo debe comportarse el servidor DNS.

### Sección `options`

```bash
options {
    directory "/var/named";     # Directorio base para archivos de zona
    allow-query { any; };       # Quién puede hacer consultas
    recursion yes;              # Permite consultas recursivas
    listen-on { any; };         # En qué interfaces escuchar
};

```

**Explicación Técnica**:

- `directory`: Define dónde buscar los archivos de zona referenciados
- `allow-query { any; }`: Permite consultas desde cualquier IP (en producción sería más restrictivo)
- `recursion yes`: El servidor resolverá consultas completas, no solo autoritativas

### Sección de Zonas

```bash
// PRIMARY zone for andersson.org.uk
zone "andersson.org.uk" IN {
    type master;                    # Este servidor es autoritativo
    file "andersson.org.uk.zone";   # Archivo que contiene los registros
    allow-transfer { 10.2.77.178; }; # Solo Solaris puede hacer transferencias
};

```

**Tipos de Zona**:

- `master`: Servidor primario (autoritativo)
- `slave`: Servidor secundario (replica)
- `hint`: Apunta a servidores root

---

### 2.2 Archivos de Zona - Donde Vive la Información

### Estructura del Archivo SOA

```bash
@ IN SOA dns.andersson.org.uk. admin.andersson.org.uk. (
    2024120301  ; Serial - DEBE incrementarse en cada cambio
    3600        ; Refresh - Cada cuánto los secundarios verifican cambios
    1800        ; Retry - Si falla refresh, cada cuánto reintentar
    604800      ; Expire - Tras este tiempo, datos se consideran obsoletos
    86400       ; Minimum TTL - TTL mínimo para registros negativos
)

```

**Detalles Críticos del SOA**:

- **Serial**: Número de versión. Los secundarios solo actualizan si es mayor
- **Formato recomendado**: YYYYMMDDNN (año-mes-día-número de cambio del día)
- **admin.andersson.org.uk.**: Email del administrador (@ se reemplaza por .)

### Tipos de Registros DNS

```bash
; Name Server (NS) - Define servidores autoritativos
andersson.org.uk. IN NS dns.andersson.org.uk.

; Address (A) - IPv4
dns.andersson.org.uk.     IN A 10.2.77.176
server1.andersson.org.uk. IN A 10.2.77.177

; IPv6 Address (AAAA) - IPv6
server1.andersson.org.uk. IN AAAA 2001:db8::1

; Canonical Name (CNAME) - Alias
www.andersson.org.uk.     IN CNAME server1.andersson.org.uk.

```

**Funcionamiento de CNAME**:

- `www.andersson.org.uk` → `server1.andersson.org.uk` → `10.2.77.177`
- Es una redirección, no una dirección directa

---

### 2.3 Root Hints (`named.ca`)

```bash
.                        3600000      NS    A.ROOT-SERVERS.NET.
A.ROOT-SERVERS.NET.      3600000      A     198.41.0.4

```

**Propósito**: Dice al servidor dónde encontrar los servidores root cuando necesita resolver dominios que no conoce.

---

## 3. PROCESO DE RESOLUCIÓN DNS

### 3.1 Consulta Local (Tu Servidor es Autoritativo)

```
Cliente → nslookup www.andersson.org.uk
       ↓
Tu Servidor Slackware:
1. Busca zona "andersson.org.uk" ✓
2. Encuentra registro CNAME: www → server1
3. Resuelve server1 → 10.2.77.177
4. Responde al cliente

```

### 3.2 Consulta Recursiva (Dominio Externo)

```
Cliente → nslookup www.google.com
       ↓
Tu Servidor:
1. No tiene zona "google.com"
2. Consulta Root Servers → .com servers
3. Consulta .com servers → google.com servers
4. Consulta google.com servers → IP final
5. Cachea resultado y responde

```

### 3.3 Transferencia de Zona (Master → Slave)

```
Solaris (Slave) cada 3600 segundos:
1. Consulta SOA de andersson.org.uk en Slackware
2. Compara serial: ¿2024120301 > cached serial?
3. Si es mayor: Solicita transferencia AXFR
4. Descarga zona completa
5. Guarda en cristian.com.it.slave

```

---

## 4. COMANDOS TÉCNICOS ESENCIALES

### 4.1 Diagnóstico y Testing

```bash
# Verificar sintaxis de configuración
named-checkconf /etc/named.conf

# Verificar sintaxis de zona específica
named-checkzone andersson.org.uk /var/named/andersson.org.uk.zone

# Ver qué zonas están cargadas
rndc status

# Forzar recarga de zonas
rndc reload

# Ver estadísticas del servidor
rndc stats

```

### 4.2 Testing con nslookup

```bash
# Consulta específica a tu servidor
nslookup www.andersson.org.uk 10.2.77.176

# Consulta de tipo específico
nslookup -type=NS andersson.org.uk
nslookup -type=SOA andersson.org.uk
nslookup -type=CNAME www.andersson.org.uk

# Consulta inversa (IP → nombre)
nslookup 10.2.77.177

```

### 4.3 Testing con dig (más potente)

```bash
# Consulta completa con detalles
dig @10.2.77.176 www.andersson.org.uk

# Ver el camino de resolución
dig +trace www.andersson.org.uk

# Transferencia de zona (si está permitida)
dig @10.2.77.176 andersson.org.uk AXFR

# Ver solo la respuesta (sin headers)
dig +short www.andersson.org.uk

```

### 4.4 Monitoreo del Servicio

```bash
# Ver procesos named activos
ps aux | grep named

# Ver puertos en uso
netstat -tuln | grep :53
ss -tuln | grep :53

# Logs del sistema (ubicación varía por OS)
tail -f /var/log/messages | grep named
tail -f /var/log/syslog | grep named

# En Solaris con SMF
svcs -l dns/server

```

---

## 5. ARCHIVOS CRÍTICOS Y SU FUNCIÓN

### 5.1 En Slackware (`/var/named/`)

```
andersson.soa              # Definición SOA para tu dominio primario
andersson.org.uk.zone      # Registros de tu dominio primario
cristian.com.it.slave      # Copia automática desde Solaris
caching-example/named.root # Root servers para recursión
caching-example/localhost.zone # Zona localhost estándar

```

### 5.2 En Solaris (`/var/named/`)

```
cristian.com.it.zone       # Tu dominio primario
andersson.org.uk.slave     # Copia automática desde Slackware
named.ca                   # Root servers
localhost.zone             # Zona localhost
named.local               # Zona reversa localhost

```

---

## 6. FLUJO DE DATOS ENTRE SERVIDORES

### Configuración Master-Slave

```
SLACKWARE (Master) ←―――――――――――→ SOLARIS (Slave)
andersson.org.uk              andersson.org.uk (copia)
cristian.com.it (copia)  ←―――――――――――→ cristian.com.it (Master)

```

### Proceso de Sincronización

1. **Notify**: Master informa cambios a slaves
2. **SOA Query**: Slave verifica serial number
3. **Zone Transfer**: Si hay cambios, descarga zona completa
4. **Update**: Slave actualiza su copia local

---

## 7. CONCEPTOS AVANZADOS

### 7.1 TTL (Time To Live)

```bash
www.andersson.org.uk. 3600 IN A 10.2.77.177
#                     ↑
#                   TTL = 1 hora

```

**Significado**: Los resolvers pueden cachear esta respuesta durante 1 hora.

### 7.2 Authoritative vs Non-Authoritative

- **Authoritative**: Tu servidor tiene los datos originales
- **Non-Authoritative**: Datos obtenidos de cache o otros servidores

### 7.3 Recursión vs Iteración

- **Recursiva**: "Dame la respuesta completa"
- **Iterativa**: "Dame la mejor pista que tengas"

---

## 8. TROUBLESHOOTING COMÚN

### 8.1 Problemas de Resolución

```bash
# ¿El servicio está corriendo?
ps aux | grep named

# ¿Está escuchando en el puerto correcto?
netstat -tuln | grep :53

# ¿La configuración es válida?
named-checkconf

# ¿Los archivos de zona son válidos?
named-checkzone domain.com /var/named/domain.com.zone

```

### 8.2 Problemas de Transferencia

```bash
# ¿Está permitida la transferencia?
# En named.conf: allow-transfer { IP; };

# ¿El serial se incrementó?
# Verificar SOA serial number

# ¿Hay conectividad?
telnet 10.2.77.176 53

```

### 8.3 Logs y Debugging

```bash
# Iniciar named con debug
named -f -g -d 3

# Ver logs en tiempo real
tail -f /var/log/messages

# Consultas específicas de debug
dig +trace +debug @servidor dominio

```

---

## 9. SEGURIDAD Y MEJORES PRÁCTICAS

### 9.1 Restricciones de Acceso

```bash
# Solo permitir consultas desde red local
allow-query { 10.2.77.0/24; 127.0.0.1; };

# Solo permitir transferencias a servidores específicos
allow-transfer { 10.2.77.178; };

# Deshabilitar recursión para consultas externas
recursion no;

```

### 9.2 Actualizaciones de Zona

- Siempre incrementar serial number
- Probar configuración antes de recargar
- Mantener backups de archivos de zona

---

## 10. COMANDOS DE ADMINISTRACIÓN DIARIA

```bash
# Recargar configuración sin interrumpir servicio
rndc reload

# Recargar zona específica
rndc reload andersson.org.uk

# Ver estadísticas
rndc stats
cat /var/named/named.stats

# Limpiar cache
rndc flush

# Parar/iniciar servicio
rndc stop
/usr/sbin/named

# En sistemas con systemd/SMF
systemctl restart named
svcadm restart dns/server

```

---

## CONCLUSIÓN

Tu configuración DNS implementa un sistema distribuido robusto donde:

- Cada servidor es master de su propio dominio
- Ambos actúan como backup del otro (slave)
- Se propagan cambios automáticamente
- Proporcionan resolución tanto local como recursiva

Este setup es similar a lo que usan grandes organizaciones para garantizar alta disponibilidad y rendimiento en sus servicios DNS.