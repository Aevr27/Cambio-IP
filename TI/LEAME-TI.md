# IP Changer 0.3.1: instrucciones de despliegue para TI

Este archivo y la carpeta `TI\` son solo para TI: **no** los copie a la carpeta compartida de los empleados.

## Contenido del paquete

```
IPChanger-v0.3.1\
├── Guía de usuario.pdf          guía para empleados (va a la raíz del recurso compartido)
├── App\IPChanger.exe            ejecutable autónomo v0.3.1 (no requiere instalar .NET)
├── App\Logs\                    vacía; aquí se guardan las bitácoras de cada equipo
├── Datos\                       vacía; aquí va el registro compartido de IP (ip-registry.json)
└── TI\
    ├── LEAME-TI.md                         este archivo
    └── IPChanger.settings.ejemplo.json     plantilla de configuración (opcional)
```

SHA-256 de `App\IPChanger.exe`: `D1AA869CF89E0528B6B641422DA3CB37A6AB427CD45D1B4990671F5F112CE927`

## 1. Copiar al servidor

Copie `Guía de usuario.pdf`, `App\` y `Datos\` a la raíz del recurso, por ejemplo `\\<servidor>\<recurso>\IPChanger`.
No deje copias de `IPChanger.exe` fuera de `App\`. Si el ZIP llegó por correo o se descargó, quite la marca de Internet:

```powershell
Unblock-File -LiteralPath "\\<servidor>\<recurso>\IPChanger\App\IPChanger.exe"
```

Use el **nombre corto** del servidor (no IP ni FQDN); si no, Windows muestra «Abrir archivo – Advertencia de seguridad»
antes del UAC, salvo que el servidor esté en la zona Intranet local por directiva de grupo.

## 2. Permisos (ejecutar en el servidor, ruta local del recurso)

Los empleados **no** deben poder modificar la raíz ni `App\`: IP Changer se ejecuta como administrador, y un EXE
reemplazado se ejecutaría con permisos de administrador en los equipos de los demás.
Cambie `<DOMINIO>` y los nombres de grupo por los reales.

```powershell
$r = 'D:\Shares\IPChanger'   # ruta local en el servidor
icacls $r /inheritance:r /grant:r "*S-1-5-18:(OI)(CI)F" "*S-1-5-32-544:(OI)(CI)F" "<DOMINIO>\TI-IPChanger:(OI)(CI)M" "<DOMINIO>\IPChanger-Usuarios:(OI)(CI)RX"
icacls "$r\App"  /inheritance:r /grant:r "*S-1-5-18:(OI)(CI)F" "*S-1-5-32-544:(OI)(CI)F" "<DOMINIO>\TI-IPChanger:(OI)(CI)M" "<DOMINIO>\IPChanger-Usuarios:(OI)(CI)RX"
icacls "$r\App\Logs" /inheritance:r /grant:r "*S-1-5-18:(OI)(CI)F" "*S-1-5-32-544:(OI)(CI)F" "<DOMINIO>\TI-IPChanger:(OI)(CI)M" "<DOMINIO>\IPChanger-Usuarios:(RD,X,RA,WD,AD)" "*S-1-3-0:(OI)(CI)(IO)M"
icacls "$r\Datos" /inheritance:r /grant:r "*S-1-5-18:(OI)(CI)F" "*S-1-5-32-544:(OI)(CI)F" "<DOMINIO>\TI-IPChanger:(OI)(CI)M" "<DOMINIO>\IPChanger-Usuarios:(OI)(CI)M"
```

Permisos del recurso compartido: *Usuarios autenticados* = Cambiar (los permisos NTFS de arriba son los que limitan).
La cuenta que cuenta es la del proceso elevado (si un técnico escribe sus credenciales en el UAC, también necesita estos permisos).

## 3. Configuración (opcional)

Para que todos los equipos usen el mismo registro de IP y servidor de prueba, copie
`TI\IPChanger.settings.ejemplo.json` a `App\IPChanger.settings.json` y escriba los valores reales:

- `RegistryPath`: ruta **UNC** completa de `Datos\ip-registry.json` (no letra de unidad).
- `CompanyServer`: IPv4 o nombre de un servidor de la empresa que responda ping, o `null`.

Si el archivo no existe, cada equipo usa su propia configuración (`C:\ProgramData\IPChanger\settings.json`).
Un archivo no válido se ignora y queda un aviso en la bitácora.

## 4. Cómo lo abren los empleados

Este paquete **no** incluye acceso directo. Los empleados abren la carpeta compartida en el Explorador por su ruta UNC
(`\\<servidor>\<recurso>\IPChanger`), entran a `App\` y hacen doble clic en `IPChanger.exe`. Indíqueles la ruta real.

- Use la ruta UNC, no una letra de unidad asignada: el proceso elevado de IP Changer no ve las unidades asignadas.
- No hace falta «Ejecutar como administrador»: el EXE pide los permisos y Windows muestra el aviso de UAC.
- Esto es lo mismo que indica el punto 1 de `Guía de usuario.pdf`.

## 5. Prueba con un empleado (piloto)

1. Con una cuenta de empleado, abra `\\<servidor>\<recurso>\IPChanger\App\IPChanger.exe`: aparece el UAC
   (*Editor desconocido*, el EXE no está firmado) y **no** debe aparecer antes «Abrir archivo – Advertencia de seguridad».
2. Con **Sí**, el título dice **IP Changer v0.3.1 (Administrador)**. Cierre sin aplicar nada.
3. Con la cuenta del empleado, crear un archivo en `App\` o renombrar `App\IPChanger.exe` debe fallar («Acceso denegado»).
4. La bitácora aparece en `App\Logs\` con el nombre del equipo.

## 6. Actualizar

Reemplace solo `App\IPChanger.exe` (cuando nadie lo use, o renombre el actual a `IPChanger-anterior.exe`, copie el
nuevo y borre el anterior después). `Datos\`, `Logs\` y la configuración no se tocan.

## Pendiente antes del despliegue general

- La guía trae en blanco el contacto de soporte (punto 11, «¿Necesita ayuda?»). Pida al equipo de desarrollo una
  guía regenerada con el contacto real.
- No verificado aún: el UAC real desde el servidor de la empresa, la zona Intranet y los permisos del servidor real.
