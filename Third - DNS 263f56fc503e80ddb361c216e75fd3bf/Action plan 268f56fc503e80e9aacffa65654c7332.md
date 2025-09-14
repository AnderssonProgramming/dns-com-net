# Action plan

# Análisis Laboratorio 03 - DNS Complete Setup

# YOU MUST CORRECT THIS

```jsx
dns.andersson.org.uk.     IN A 10.2.77.176  # ← Tu Slackware (ya existe)
server1.andersson.org.uk. IN A 10.2.77.177  # ← VM a crear
server2.andersson.org.uk. IN A 10.2.77.178  # ← Tu Solaris (ya existe) ❌ 
server3.andersson.org.uk. IN A 10.2.77.179  # ← VM a crear
```

<aside>
❌

It is wrong cuz my solaris dns server already has this IP

</aside>

## PARTE 1: CONFIGURACIÓN DE SERVIDORES DNS (Lo que ya tienes casi listo)

### Configuración Actual - Grupos de 2 estudiantes:

```
Dominio 1: cristian.com.it
├── PRIMARY: Solaris (10.2.77.178)
├── SECONDARY: Slackware (10.2.77.176) ✅ LISTO
└── SECONDARY: Windows Server ❌ FALTA

Dominio 2: andersson.org.uk
├── PRIMARY: Slackware (10.2.77.176)
├── SECONDARY: Solaris (10.2.77.178) ✅ LISTO
└── SECONDARY: Windows Server ❌ FALTA

```

### ¿Qué te falta?

**SOLO Windows Server como secundario de AMBOS dominios**

---

## PARTE 2: MÁQUINAS VIRTUALES ADICIONALES (Para testing)

### ¿Para qué sirven?

**NO son parte de la infraestructura DNS**, son **CLIENTES** para probar que el DNS funciona.

### ¿Cuántas necesitas crear?

Según el laboratorio, necesitas crear máquinas que representen los **nombres declarados en DNS**:

### Para andersson.org.uk (ya declarados en tu zona):

```bash
dns.andersson.org.uk.     IN A 10.2.77.176  # ← Tu Slackware (ya existe)
server1.andersson.org.uk. IN A 10.2.77.177  # ← VM a crear
server2.andersson.org.uk. IN A 10.2.77.178  # ← Tu Solaris (ya existe)
server3.andersson.org.uk. IN A 10.2.77.179  # ← VM a crear

```

### Para cristian.com.it (ya declarados en tu zona):

```bash
dns.cristian.com.it.     IN A 10.2.77.178   # ← Tu Solaris (ya existe)
server1.cristian.com.it. IN A 10.2.77.180   # ← VM a crear
server2.cristian.com.it. IN A 10.2.77.181   # ← VM a crear
server3.cristian.com.it. IN A 10.2.77.182   # ← VM a crear

```

---

## RESPUESTA DIRECTA A TUS PREGUNTAS

### 1. ¿Cuántas VMs adicionales crear?

**4 VMs adicionales** (pueden ser Linux básico o cualquier OS):

- 1 VM con IP 10.2.77.177 (server1.andersson.org.uk)
- 1 VM con IP 10.2.77.179 (server3.andersson.org.uk)
- 1 VM con IP 10.2.77.180 (server1.cristian.com.it)
- 1 VM con IP 10.2.77.181 (server2.cristian.com.it)

### 2. ¿Windows Server debe ser esclavo?

**SÍ**, Windows Server debe configurarse como secundario de AMBOS dominios:

- Secundario de cristian.com.it (desde Solaris)
- Secundario de andersson.org.uk (desde Slackware)

---

## ARQUITECTURA FINAL COMPLETA

```
┌─────────────────────────────────────────────────────────────────┐
│                    SERVIDORES DNS                               │
├─────────────────────────────────────────────────────────────────┤
│ Slackware (10.2.77.176)     Solaris (10.2.77.178)            │
│ ├── PRIMARY andersson.org.uk ├── PRIMARY cristian.com.it      │
│ └── SECONDARY cristian.com.it └── SECONDARY andersson.org.uk   │
│                                                                 │
│ Windows Server (IP asignada)                                   │
│ ├── SECONDARY andersson.org.uk                                │
│ └── SECONDARY cristian.com.it                                 │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│                   CLIENTES DE PRUEBA                           │
├─────────────────────────────────────────────────────────────────┤
│ VM1 (10.2.77.177) - server1.andersson.org.uk                 │
│ VM2 (10.2.77.179) - server3.andersson.org.uk                 │
│ VM3 (10.2.77.180) - server1.cristian.com.it                  │
│ VM4 (10.2.77.181) - server2.cristian.com.it                  │
└─────────────────────────────────────────────────────────────────┘

```

---

## PLAN DE ACCIÓN PASO A PASO

### Paso 1: Completar Windows Server DNS ⏳

1. Instalar DNS Server role en Windows
2. Configurar como secundario de ambos dominios
3. Verificar transferencias de zona

### Paso 2: Crear VMs clientes 🔄

1. Crear 4 VMs básicas con las IPs correspondientes
2. Configurar cada VM para usar tus servidores DNS
3. Verificar que pueden resolver nombres

### Paso 3: Testing completo 🧪

Desde cualquier VM cliente, probar:

```bash
# Resolución local
nslookup server1.andersson.org.uk
nslookup www.cristian.com.it

# Resolución externa
nslookup www.google.com

```

---

## CONFIGURACIÓN WINDOWS SERVER DNS

### Instalar DNS Server Role:

```powershell
# En PowerShell como Administrator
Install-WindowsFeature DNS -IncludeManagementTools

```

### Configurar Zonas Secundarias:

```
1. Abrir DNS Manager
2. Crear Secondary Zone para "andersson.org.uk"
   - Master: 10.2.77.176 (Slackware)
3. Crear Secondary Zone para "cristian.com.it"
   - Master: 10.2.77.178 (Solaris)

```

---

## TESTING QUE DEBES DEMOSTRAR

### Desde VMs Cliente:

```bash
# Test 1: Resolución interna andersson.org.uk
nslookup server1.andersson.org.uk
# Debe responder: 10.2.77.177

# Test 2: Resolución interna cristian.com.it
nslookup server1.cristian.com.it
# Debe responder: 10.2.77.180

# Test 3: Resolución de alias
nslookup www.andersson.org.uk
# Debe responder: server1.andersson.org.uk → 10.2.77.177

# Test 4: Resolución externa
nslookup www.google.com
# Debe funcionar (recursión)

```

### Desde Servidores DNS:

```bash
# Verificar transferencias de zona
rndc status
ls /var/named/*.slave

```

---

## RESUMEN: ¿Qué te falta exactamente?

1. **Windows Server DNS** configurado como secundario ⏳
2. **4 VMs cliente** con IPs correspondientes para testing 🔄
3. **Pruebas completas** desde clientes y video de demostración 🧪

**Tu configuración DNS principal (Slackware↔Solaris) ya está perfecta** ✅

¿Necesitas ayuda específica configurando Windows Server DNS o creando las VMs cliente?