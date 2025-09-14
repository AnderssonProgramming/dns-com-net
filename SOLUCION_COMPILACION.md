# Solución Rápida: Usar Overleaf para compilar LaTeX

## Problema Identificado
MiKTeX se instaló pero no configuró correctamente los binarios en el PATH del sistema.

## Solución Recomendada: Overleaf (5 minutos)

### Paso 1: Acceder a Overleaf
1. Ve a https://www.overleaf.com/
2. Crea una cuenta gratuita o inicia sesión
3. Haz clic en "New Project" → "Blank Project"

### Paso 2: Subir el archivo LaTeX
1. En Overleaf, haz clic en "Upload" (ícono de subir)
2. Selecciona el archivo `lab03_guide.tex`
3. Overleaf compilará automáticamente

### Paso 3: Descargar PDF
1. El PDF se generará automáticamente
2. Haz clic en "Download PDF" para descargarlo

## Solución Alternativa: Reinstalar MiKTeX

Si prefieres usar MiKTeX localmente:

### Opción A: Desinstalar y reinstalar
1. Ve a "Configuración" → "Aplicaciones"
2. Busca "MiKTeX" y desinstala
3. Descarga MiKTeX desde: https://miktex.org/download
4. **IMPORTANTE**: Durante la instalación, selecciona:
   - "Install for all users" (Instalar para todos los usuarios)
   - "Add MiKTeX to PATH" (Agregar MiKTeX al PATH)

### Opción B: Agregar manualmente al PATH
1. Busca donde se instaló MiKTeX:
   ```powershell
   Get-ChildItem -Path "C:\" -Filter "miktex.exe" -Recurse -ErrorAction SilentlyContinue
   ```
2. Una vez encontrado, agrega la carpeta bin al PATH del sistema

### Opción C: Usar ruta completa
Si encuentras el pdflatex.exe, úsalo con ruta completa:
```powershell
& "C:\ruta\completa\a\pdflatex.exe" lab03_guide.tex
```

## Recomendación Final

**Usa Overleaf** - es la opción más rápida y confiable:
- No requiere instalación local
- Compila automáticamente
- Maneja todos los paquetes LaTeX
- Funciona en cualquier navegador
- Colaboración en tiempo real si necesitas

## Tiempo estimado:
- Overleaf: 5 minutos
- Reinstalar MiKTeX: 15-30 minutos

¿Qué opción prefieres?
