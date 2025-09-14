# Guía de Compilación LaTeX - MiKTeX

## ✅ Configuración Completada

MiKTeX ha sido instalado y configurado correctamente en tu sistema. El comando `pdflatex` ya está disponible en la terminal.

## 🚀 Comandos Básicos para Compilar

### Compilar un documento LaTeX a PDF
```powershell
pdflatex nombre_del_archivo.tex
```

### Ejemplo para tu documento actual
```powershell
pdflatex lab03_guide.tex
```

### Compilar múltiples veces (para referencias cruzadas)
```powershell
pdflatex lab03_guide.tex
pdflatex lab03_guide.tex
```

## 📁 Ubicación de MiKTeX
- **Instalación:** `C:\Users\Usuario\AppData\Local\Programs\MiKTeX`
- **Binarios:** `C:\Users\Usuario\AppData\Local\Programs\MiKTeX\miktex\bin\x64`
- **PATH:** Ya agregado permanentemente al PATH del usuario

## 🔧 Comandos Útiles Adicionales

### Verificar versión de pdflatex
```powershell
pdflatex --version
```

### Limpiar archivos auxiliares
```powershell
Remove-Item *.aux, *.log, *.toc, *.out, *.fdb_latexmk, *.fls -ErrorAction SilentlyContinue
```

### Compilar con información detallada
```powershell
pdflatex -interaction=nonstopmode lab03_guide.tex
```

## 📝 Notas sobre la Compilación Actual

### ✅ Compilación Exitosa
- ✅ PDF generado: `lab03_guide.pdf` (18 páginas, 188KB)
- ✅ MiKTeX funcionando correctamente
- ✅ Todas las librerías necesarias instaladas

### ⚠️ Advertencias Menores (no afectan la compilación)
1. **Archivos de imagen faltantes:**
   - `university_logo.png` - Línea 67
   - `media/image1.jpeg` - Línea 130

2. **Caracteres Unicode no soportados:**
   - Los emojis ✅ no son compatibles con LaTeX estándar
   - Solución: Reemplazar con texto o usar paquetes Unicode

3. **Advertencias de formato:**
   - `\headheight` muy pequeño para fancyhdr
   - Solución: Agregar `\setlength{\headheight}{14.5pt}` en el preámbulo

## 🛠️ Mejoras Sugeridas para el Documento

### 1. Arreglar caracteres Unicode
Reemplazar en el archivo .tex:
```latex
% Cambiar de:
\item ✅ Primary DNS server...

% A:
\item[\checkmark] Primary DNS server...
% o simplemente:
\item Primary DNS server...
```

### 2. Arreglar headheight
Agregar en el preámbulo (después de `\usepackage{fancyhdr}`):
```latex
\setlength{\headheight}{14.5pt}
```

### 3. Agregar imágenes
- Crear carpeta `media/` en el directorio del documento
- Agregar `university_logo.png` e `image1.jpeg`

## 🔄 Flujo de Trabajo Recomendado

1. **Editar** el archivo `.tex` en VS Code
2. **Compilar** con `pdflatex lab03_guide.tex`
3. **Revisar** el PDF generado
4. **Repetir** si es necesario

## 🆘 Solución de Problemas

### Si pdflatex no se reconoce:
```powershell
# Verificar PATH
$env:PATH -split ';' | Where-Object { $_ -like "*miktex*" }

# Agregar manualmente si es necesario
$env:PATH += ";C:\Users\Usuario\AppData\Local\Programs\MiKTeX\miktex\bin\x64"
```

### Si faltan paquetes:
MiKTeX instalará automáticamente los paquetes faltantes la primera vez que los uses.

### Para reinstalar MiKTeX:
```powershell
winget uninstall MiKTeX.MiKTeX
winget install MiKTeX.MiKTeX
```

---
*Documento generado automáticamente - Fecha: 14 de septiembre de 2025*
