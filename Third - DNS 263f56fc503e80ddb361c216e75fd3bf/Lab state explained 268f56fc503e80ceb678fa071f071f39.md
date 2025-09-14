# Lab state explained

¡Excelente pregunta! Has tocado un punto fundamental sobre cómo funciona DNS. Te explico paso a paso:

## ¿Qué son esos registros A en tu zona?

Esos registros que tienes en tu archivo de zona:

```bash
dns.andersson.org.uk.     IN A 10.2.77.176
server1.andersson.org.uk. IN A 10.2.77.177
server2.andersson.org.uk. IN A 10.2.77.178
server3.andersson.org.uk. IN A 10.2.77.179

```

Son **DECLARACIONES** de DNS, no verificaciones de existencia real. DNS es básicamente una "base de datos de nombres" que dice:

> "Si alguien pregunta por server1.andersson.org.uk, yo le respondo que está en 10.2.77.177"
> 

## El DNS NO verifica si las máquinas existen

DNS funciona como una "guía telefónica":

- **Lo que hace**: "Juan Pérez vive en Calle 123"
- **Lo que NO hace**: "Verificar si Juan realmente vive ahí"

## Demostración práctica

Haz esta prueba **AHORA MISMO**:

```bash
# Desde cualquier máquina que use tu DNS server
nslookup server1.andersson.org.uk

# Te responderá: 10.2.77.177
# Pero si haces ping:
ping server1.andersson.org.uk
# Te dará "Host unreachable" o timeout

```

¿Por qué? Porque:

1. **DNS resolvió correctamente** el nombre → IP
2. **No hay máquina real** en esa IP

## ¿Qué pasa si creas las máquinas reales?

**SÍ, exactamente!** Si creas máquinas reales con esas IPs:

### Escenario ANTES (actual):

```bash
Cliente: nslookup server1.andersson.org.uk
DNS: "Es 10.2.77.177"
Cliente: ping 10.2.77.177
Red: "No hay nadie ahí" → FAIL

```

### Escenario DESPUÉS (con máquina real):

```bash
Cliente: nslookup server1.andersson.org.uk
DNS: "Es 10.2.77.177"
Cliente: ping 10.2.77.177
Máquina real: "¡Hola! Aquí estoy" → SUCCESS

```

## Ejemplo Visual de tu Laboratorio

```
Estado ACTUAL:
┌─────────────────┐    ┌─────────────────┐
│ Slackware       │    │ Solaris         │
│ 10.2.77.176     │    │ 10.2.77.178     │
│ (DNS server)    │    │ (DNS server)    │
└─────────────────┘    └─────────────────┘

┌─────────────────┐    ┌─────────────────┐
│ 10.2.77.177     │    │ 10.2.77.179     │
│ (vacío)         │    │ (vacío)         │
│ server1 "debería"│   │ server3 "debería"│
│ estar aquí      │    │ estar aquí      │
└─────────────────┘    └─────────────────┘

```

## Prueba Práctica que Puedes Hacer AHORA

### 1. Verificar que DNS resuelve nombres inexistentes:

```bash
nslookup server1.andersson.org.uk
# Resultado: 10.2.77.177

nslookup server3.andersson.org.uk
# Resultado: 10.2.77.179

```

### 2. Verificar que las IPs no responden:

```bash
ping server1.andersson.org.uk
# Resultado: Timeout/unreachable

telnet server1.andersson.org.uk 80
# Resultado: Connection refused

```

### 3. Comparar con máquina real:

```bash
ping dns.andersson.org.uk
# Resultado: SUCCESS (porque apunta a 10.2.77.176 = tu Slackware real)

```

## ¿Por qué pusiste esas IPs en el DNS?

Es una práctica común en laboratorios:

1. **Planificar la infraestructura** antes de construirla
2. **Reservar nombres y IPs** para uso futuro
3. **Preparar el DNS** antes que las máquinas

## Si Creas las Máquinas Reales

**SÍ**, funcionará perfectamente. Por ejemplo, si instalas una VM con IP 10.2.77.177:

```bash
# Cualquier cliente consultando tu DNS:
nslookup server1.andersson.org.uk
# → 10.2.77.177

ping server1.andersson.org.uk
# → SUCCESS! Responde la VM real

ssh admin@server1.andersson.org.uk
# → Se conecta a la VM real

```

## Analogía Simple

Es como crear **direcciones de casas** antes de construir las casas:

- **Municipalidad** (DNS): "La Calle Luna #123 existe en nuestros planos"
- **GPS** (nslookup): "Te llevo a Calle Luna #123"
- **Realidad**: Puede que encuentres una casa o un lote vacío

**¿Entiendes ahora la diferencia entre "resolución de nombres" y "existencia real de servicios"?**