# Juego de luces interactivo con ESP32

**Sistemas Digitales · APE 002 · Quinto ciclo · Universidad Nacional de Loja · Grupo 7**

## Descripción

En esta práctica se programa un ESP32 para encender cuatro LEDs en secuencia y cambiar la velocidad de la secuencia mediante un pulsador. El programa utiliza `millis()` para controlar los tiempos sin detener el ciclo principal, y combina la resistencia *pull-up* interna con antirrebote por software y detección de flanco para registrar una sola acción por pulsación.

El trabajo contempla una simulación en Wokwi y una prueba con la placa física. Este repositorio permite consultar y cargar el **Código 2**, correspondiente al juego interactivo.

## Estado del repositorio

Por ahora está disponible el archivo [`03APE-C1.ino`](./03APE-C1.ino), que implementa el **Código 2 de la guía APE 002**. Los archivos de la primera parte, el diagrama de Wokwi, las evidencias y el informe se incorporarán por separado cuando se publiquen en el repositorio.

Se comprobó la compilación y la transferencia del programa a un ESP32 desde Ubuntu. La comprobación de los LEDs en un montaje físico depende de que las conexiones de la protoboard sean correctas; una carga exitosa no confirma, por sí sola, que el circuito externo esté funcionando.

## Integrantes

- Anderson Mateo Coello Jaramillo
- Cael Alejandro Soto Castillo
- Alex Francisco Sigcho Serrano
- Steven Alexander Jumbo Jaramillo
- Yandri Alexander Piscocama Jaramillo

**Asignatura:** Sistemas Digitales  
**Docente:** Ing. Luis Darío Sinche Cueva  
**Fecha de la práctica:** 23 de octubre de 2026

## Materiales y herramientas

### Componentes

| Cantidad | Componente |
| ---: | --- |
| 1 | ESP32 DevKit V1 de 30 pines, con módulo ESP32-WROOM-32 |
| 1 | Protoboard |
| 4 | LEDs de 5 mm (rojos, amarillos o verdes) |
| 4 | Resistencias de 330 Ω |
| 1 | Pulsador de cuatro patas |
| 6 | Cables jumper macho-macho, según la lista de la guía |
| 1 | Cable USB **de datos** compatible con la placa |

### Software

- **Arduino IDE 2.x:** se utilizó la versión **2.3.10**.
- **esp32 by Espressif Systems 3.x:** se utilizó la versión **3.3.12**.
- **Wokwi:** simulador disponible en [wokwi.com](https://wokwi.com/); no requiere instalación.
- **Git:** opcional si se descarga el proyecto como ZIP; útil para clonarlo y registrar cambios.

No se necesitan librerías externas adicionales para compilar el sketch.

## Conexiones del circuito

| Componente | Pin ESP32 | Conexión |
| --- | --- | --- |
| LED 1 | GPIO16 (`RX2`) | GPIO → resistencia de 330 Ω → ánodo del LED |
| LED 2 | GPIO17 (`TX2`) | GPIO → resistencia de 330 Ω → ánodo del LED |
| LED 3 | GPIO18 (`D18`) | GPIO → resistencia de 330 Ω → ánodo del LED |
| LED 4 | GPIO19 (`D19`) | GPIO → resistencia de 330 Ω → ánodo del LED |
| Pulsador | GPIO21 (`D21`) | Entre GPIO21 y GND |
| Tierra | `GND` | Cátodos de los cuatro LEDs y retorno del pulsador |

La **pata larga** de cada LED es el ánodo y va hacia el GPIO a través de su resistencia. La **pata corta** es el cátodo y va a GND. El pulsador no necesita resistencia externa porque el programa habilita `INPUT_PULLUP`.

> **Antes de alimentar el circuito:** desconectar el USB para montar o corregir cables; comprobar las resistencias, la polaridad de los LEDs y la continuidad de GND. La línea negativa de una protoboard no queda conectada a GND hasta que se une físicamente al pin correspondiente del ESP32. Verificar la serigrafía de la placa, ya que algunos modelos cambian el orden de los pines.

## Cómo funciona el programa

El sketch controla cuatro salidas y una entrada digital:

- Los GPIO **16, 17, 18 y 19** activan los LEDs de uno en uno.
- El GPIO **21** lee el pulsador con `INPUT_PULLUP`: sin pulsar se lee `HIGH`; al pulsar se lee `LOW`.
- `millis()` permite cambiar de LED sin utilizar `delay()`. Así, el programa puede seguir revisando el pulsador mientras transcurre el tiempo entre cambios.
- `T_REBOTE = 30` exige que la señal del pulsador permanezca estable durante 30 ms antes de aceptar el cambio.
- La detección de flanco evita cambiar varias veces de velocidad por mantener presionado el botón.

Las velocidades programadas son:

| Estado | Tiempo entre cambios |
| --- | ---: |
| Lento (inicial) | 400 ms |
| Medio | 150 ms |
| Rápido | 60 ms |

Cada pulsación sigue el orden **400 → 150 → 60 → 400 ms**. Por eso, desde el estado inicial, la primera pulsación selecciona 150 ms.

El ESP32 envía información por el Monitor Serie a **115200 baudios**. Al arrancar muestra:

```text
AP2 lista. Presiona el pulsador.
```

Y, al detectar las pulsaciones, mensajes como:

```text
Periodo: 150 ms
Periodo: 60 ms
Periodo: 400 ms
```

## Obtener el proyecto

Desde Git, en una terminal de Linux o en Git Bash/PowerShell con Git instalado en Windows:

```bash
git clone https://github.com/AndersonC15/03APE-SistemasDigitales-G7--Coello---Soto---Sigcho---Jumbo---Piscocama-.git
```

También se puede entrar al [repositorio en GitHub](https://github.com/AndersonC15/03APE-SistemasDigitales-G7--Coello---Soto---Sigcho---Jumbo---Piscocama-) y usar **Code → Download ZIP**.

El archivo que se debe abrir es **`03APE-C1.ino`**. Si Arduino IDE pide colocar el archivo en una carpeta con el mismo nombre del sketch, se puede aceptar esa organización para trabajar localmente.

## Preparar Arduino IDE en Windows

1. Descargar e instalar **Arduino IDE 2.x** desde la [página oficial](https://www.arduino.cc/en/software). Se recomienda Windows 10 u 11 de 64 bits.
2. Abrir **Archivo → Preferencias**.
3. En **URLs adicionales de gestor de placas**, agregar la dirección estable de Espressif:

   ```text
   https://espressif.github.io/arduino-esp32/package_esp32_index.json
   ```

4. Abrir **Herramientas → Placa → Gestor de placas**, buscar `esp32` e instalar **esp32 by Espressif Systems**, versión estable **3.x**.
5. Conectar el ESP32 con un cable USB de datos. En el **Administrador de dispositivos** de Windows comprobar qué puerto `COM` se asigna (por ejemplo, `COM3`).
6. En Arduino IDE seleccionar **ESP32 Dev Module** y el puerto correspondiente.
7. Abrir `03APE-C1.ino`, pulsar **Verificar (✓)** y luego **Cargar (→)**.
8. Abrir el **Monitor Serie** a **115200 baudios** para observar los cambios de velocidad.

Si Windows no reconoce el puerto, comprobar primero el cable y revisar si la placa utiliza un convertidor USB-serie **CP210x** o **CH340**. Instalar el controlador correspondiente solo si es necesario.

## Preparar Arduino IDE en Linux (Ubuntu)

Los pasos también son válidos, con ajustes menores, para otras distribuciones Linux de 64 bits. Las pruebas de este proyecto se realizaron en **Ubuntu 26.04.1 LTS**.

1. Descargar el **AppImage oficial de Arduino IDE 2.x** desde [Arduino Software](https://www.arduino.cc/en/software), darle permiso de ejecución y abrirlo. En Ubuntu, si el AppImage informa que falta FUSE 2, se puede instalar el paquete `libfuse2t64`:

   ```bash
   sudo apt update
   sudo apt install libfuse2t64
   ```

2. Agregar la misma URL estable de Espressif en **Archivo → Preferencias → URLs adicionales de gestor de placas** e instalar **esp32 by Espressif Systems 3.x** desde el Gestor de placas.
3. Conectar el ESP32 por USB y comprobar qué dispositivo serie aparece:

   ```bash
   find /dev -maxdepth 1 \( -name 'ttyUSB*' -o -name 'ttyACM*' \) -print
   ```

   En las pruebas realizadas se utilizó `/dev/ttyUSB0`; en otro equipo podría ser `/dev/ttyACM0` o un número diferente.

4. Si aparece `Permission denied` al intentar cargar, comprobar los grupos del usuario:

   ```bash
   groups
   ```

   Si `dialout` no aparece, agregar el usuario al grupo:

   ```bash
   sudo usermod -aG dialout "$USER"
   ```

   **Cerrar sesión y volver a entrar** antes de abrir Arduino IDE nuevamente. No es necesario ejecutar el IDE como `root` ni dar permisos `777` al puerto.

5. En Arduino IDE seleccionar **ESP32 Dev Module**, elegir el puerto detectado, abrir `03APE-C1.ino` y pulsar **Verificar (✓)** y luego **Cargar (→)**.
6. Abrir el Monitor Serie a **115200 baudios**.

Si el AppImage presenta un error de *sandbox* en versiones recientes de Ubuntu, revisar la configuración de AppArmor para esa aplicación. No se recomienda desactivar el aislamiento de seguridad del sistema de manera general.

## Carga del programa y primera prueba

1. Montar el circuito **con el USB desconectado** y revisar las conexiones de la tabla anterior.
2. Conectar la placa al computador usando un cable USB de datos.
3. Confirmar que estén seleccionados **ESP32 Dev Module** y el puerto correcto.
4. Para un primer intento, configurar **Herramientas → Upload Speed → 115200**. Esta velocidad permitió completar la carga durante las pruebas en Ubuntu.
5. Pulsar **Cargar (→)**. Un registro que termina con `Hash of data verified` y `Hard resetting via RTS pin...` confirma que la escritura se completó y se solicitó el reinicio de la placa.
6. Comprobar la secuencia de los cuatro LEDs y presionar el pulsador para cambiar la velocidad.
7. Revisar el Monitor Serie a **115200 baudios**.

El código se ejecuta **en el ESP32**, no dentro de Windows o Linux. Después de una carga correcta, la placa conserva el programa en su memoria flash y puede ejecutarlo al volver a recibir alimentación.

## Simulación con Wokwi

1. Entrar en [Wokwi](https://wokwi.com/) y crear un proyecto de **ESP32**.
2. Agregar cuatro LEDs con sus resistencias de 330 Ω y un pulsador, siguiendo la tabla de conexiones.
3. Abrir `sketch.ino` dentro de Wokwi y pegar el contenido de [`03APE-C1.ino`](./03APE-C1.ino).
4. Iniciar la simulación con **Start**.
5. Presionar el pulsador y observar el cambio de velocidad y los mensajes en el Monitor Serie.

El archivo `diagram.json` utilizado en la práctica no está publicado todavía en este repositorio. La tabla anterior permite recrear el montaje mientras se incorpora ese archivo.

## Resultados y comprobaciones

En el informe del Grupo 7 se registraron estas observaciones durante las pruebas de la práctica:

| Prueba | Observación registrada |
| --- | --- |
| Código 1 con periodo de 1000 ms | Secuencia lenta |
| Código 1 con periodo de 300 ms | Secuencia a velocidad intermedia |
| Código 1 con periodo de 100 ms | Secuencia rápida |
| Código 2 con `T_REBOTE = 0`, 10 clics | 80 líneas registradas en el Monitor Serie |
| Código 2 con `T_REBOTE = 30`, 10 clics | 10 líneas registradas en el Monitor Serie |

Estos valores corresponden a las pruebas anotadas en el informe; el número de detecciones sin antirrebote puede variar según el pulsador y el montaje. En Ubuntu se verificó la compilación, conexión al ESP32 y escritura del firmware. La respuesta de los LEDs del montaje físico debe comprobarse de forma independiente.

El [video del grupo](https://drive.google.com/file/d/15HZync1OXSFLlC2qHbiAxCdG2Zf3PM2L/view?usp=sharing) es el enlace consignado en los anexos del informe de la práctica.

## Problemas frecuentes

| Problema | Qué revisar |
| --- | --- |
| Arduino IDE no muestra una placa ESP32 | Instalar `esp32 by Espressif Systems`, no confundirlo con `Arduino ESP32 Boards`. |
| No aparece un puerto COM o `/dev/ttyUSB*` | Revisar cable de datos, puerto USB y detección del dispositivo. |
| `Permission denied` en Linux | Comprobar pertenencia a `dialout` y volver a iniciar sesión. |
| `Connecting.....` no avanza | Probar una velocidad de carga de 115200; si hace falta, mantener pulsado **BOOT** hasta que comience la carga. |
| `No more data to read from the serial port` | Probar la placa sin el circuito externo, otro cable o puerto USB, y cerrar otros programas que usen el puerto. |
| Se carga el programa, pero los LEDs no encienden | Revisar GPIO, polaridad, resistencias, filas de la protoboard y conexión común a GND. |
| No aparecen mensajes en el Monitor Serie | Confirmar 115200 baudios, el puerto correcto y reiniciar el ESP32 si es necesario. |
| El pulsador cambia varias veces la velocidad | Revisar su conexión a GPIO21/GND y mantener `T_REBOTE = 30`. |

## Organización del repositorio

**Contenido publicado actualmente:**

```text
03APE-SistemasDigitales-G7--Coello---Soto---Sigcho---Jumbo---Piscocama-/
└── 03APE-C1.ino          # Código 2: juego de luces interactivo
```

**Archivos previstos en la guía para completar la entrega:**

```text
codigo1/AP2_Codigo1.ino
codigo2/AP2_Codigo2.ino
wokwi/diagram.json
evidencias/              # Capturas y video o enlace
informe/AP2_Informe.pdf
```

Esos archivos no se consideran publicados hasta que realmente se agreguen al repositorio. El nombre actual del repositorio comienza por `03APE`, pero la guía proporcionada corresponde a la **APE 002**.

## Referencias

- [Guía de Arduino IDE](https://docs.arduino.cc/software/ide-v2/tutorials/getting-started/ide-v2-downloading-and-installing)
- [Instalación de Arduino-ESP32 — Espressif](https://docs.espressif.com/projects/arduino-esp32/en/latest/installing.html)
- [Documentación de Wokwi](https://docs.wokwi.com/)
- [Referencia del lenguaje Arduino](https://docs.arduino.cc/language-reference/)

---

Práctica desarrollada para la asignatura de **Sistemas Digitales**, carrera de Computación, Universidad Nacional de Loja.
