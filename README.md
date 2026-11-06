# IP Changer v0.3.1

Herramienta para Windows 10/11 (x64) que permite a un administrador local autorizado cambiar **solo la dirección IPv4
y la puerta de enlace** de un adaptador Ethernet o Wi-Fi, con revisión de IP duplicada, respaldo, confirmación de
60 segundos con reversión automática y registro compartido de IP. Interfaz en español (México).

## Descarga

El paquete de despliegue está en la sección **Releases**: `IPChanger-v0.3.1.zip`.

```
IPChanger-v0.3.1\
├── Guía de usuario.pdf          guía para empleados
├── App\IPChanger.exe            ejecutable autónomo (no requiere instalar .NET)
├── App\Logs\                    bitácoras de cada equipo
├── Datos\                       registro compartido de IP
└── TI\                          instrucciones y plantilla de configuración para TI (no se copian al recurso compartido)
```

SHA-256 de `App\IPChanger.exe`: `D1AA869CF89E0528B6B641422DA3CB37A6AB427CD45D1B4990671F5F112CE927`

## En este repositorio

- `Guía de usuario.pdf`: guía para empleados.
- `TI/LEAME-TI.md`: copia al servidor, permisos (`icacls`), configuración, prueba piloto y actualización.
- `TI/IPChanger.settings.ejemplo.json`: plantilla de configuración opcional.

Los empleados abren la carpeta compartida por su ruta UNC y ejecutan `App\IPChanger.exe`. El ejecutable no está
firmado digitalmente: Windows muestra **Editor desconocido** en el aviso de UAC.
