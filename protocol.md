Protocolo
========

Version Actual: 2.6.0

La intención de este protocolo es permitir que la mayor parte del microcontrolador se controle como sea posible desde el equipo host o remoto. Este protocolo fue diseñado para la comunicación directa entre un microcontrolador y un software en un equipo host. A continuación, el software host debe proporcionar una interfaz que tenga sentido en ese entorno.

El formato de comunicación de datos utiliza mensajes MIDI. No es necesariamente un dispositivo MIDI, primero utiliza una velocidad de serie más rápida, y en segundo lugar, los mensajes no siempre se asignan de la misma manera.


Tipo de Mensaje
===

Este protocolo utiliza el [formato de mensaje MIDI](http://www.midi.org/techspecs/midimessages.php), pero no utiliza todo el protocolo.
La mayoría de las asignaciones de comandos aquí no serán directamente utilizables en términos de controladores MIDI y sintetizadores. Debe coexistir con MIDI sin problemas y puede ser analizado por intérpretes MIDI estándar. Sólo algunos de los datos del mensaje se utilizan diferentemente.


| Tipo                   | Comando | Canal MIDI   | Primer byte         | Segundo byte    |
| ---------------------- | ------- | ------------ | ------------------- | --------------- |
| analog I/O message     | 0xE0    | pin #        | LSB(bits 0-6)       | MSB(bits 7-13)  |
| digital I/O message    | 0x90    | port         | LSB(bits 0-6)       | MSB(bits 7-13)  |
| report analog pin      | 0xC0    | pin #        | disable/enable(0/1) | - n/a -         |
| report digital port    | 0xD0    | port         | disable/enable(0/1) | - n/a -         |
|                        |         |              |                     |                 |
| start sysex            | 0xF0    |              |                     |                 |
| set pin mode(I/O)      | 0xF4    |              | pin # (0-127)       | pin mode        |
| set digital pin value  | 0xF5    |              | pin # (0-127)       | pin value(0/1)  |
| sysex end              | 0xF7    |              |                     |                 |
| Version Protocolo      | 0xF9    |              | Version Principal   | Version Menor   |
| Reinicio (system reset)| 0xFF    |              |                     |                 |


Los sub commands basados en Sysex (0x00 - 0x7F) se utilizan para un conjunto de comandos extendido.

| Tipo                  | sub-comando | Primer byte      | Segundo byte  | ...            |
| --------------------- | -------     | ---------------  | ------------- | -------------- |
| string                | 0x71        | char *string ... |               |                |
| firmware name/version | 0x79        | Version Principal| Version Menor | char *name ... |


Expansion de Mensaje de Datos
===

Formato de datos digitales de dos bytes, segundo nibble del byte 0 da el numero del puerto (ej. 0x92 es el tercer puerto, puerto 2)
```
0  digital data, 0x90-0x9F, (En MIDI es NoteOn, pero diferente formato de datos)
1  digital pins 0-6 bitmask
2  digital pin 7 bitmask
```

Formato de Datos Analogico de 14-bit
```
0  pin analogico, 0xE0-0xEF, (En MIDI es Pitch Wheel)
1  analogico menos significativo 7 bits
2  analogico mas significativo 7 bits
```

Formato Informe de Version
```
0  Cabecera Informe de Version (0xF9) (En MIDI no esta definido)
1  Principal version (0-127)
2  Menor version (0-127)
```

Expansión de mensajes de control
===

Establecer el modo del pin
```
0  establecer modo digital del pin (0xF4) (En MIDI no esta definido)
1  establecer el numero del pin (0-127)
2  modo (INPUT=0/OUTPUT=1/ANALOG=2/PWM=3/SERVO=4/I2C=6/ONEWIRE=7/STEPPER=8/ENCODER=9/SERIAL=10/PULLUP=11)
```

Establecer el valor del pin (agregado en la v2.5)
```
0  establecer el valor del pin (0xF5) (En MIDI no esta definido)
1  establecer el numero del pin (0-127)
2  valor (LOW=0/HIGH=1)
```

Alternar informes analogIn por pin
```
0  alternar informes analogIn (0xC0-0xCF) (En MIDI es Program Change)
1  desactivar(0) / habilitar(no-zero)
```
*A partir de Firmata 2.4.0, al habilitar un pin analógico, el valor del pin debe ser reportado al cliente aplicación.*

Toggle digital port reporting by port (second nibble of byte 0), eg 0xD1 is port 1 is pins 8 to 15
Alternar los informes de puerto digital por puerto (segundo nibble del byte 0), por ejemplo 0xD1 es el puerto 1 es pines 8 al 15
```
0  alternar informes de los puertos digitales (0xD0-0xDF) (En MIDI es Aftertouch)
1  desactivar(0) / habilitar(no-zero)
```
*A partir de Firmata 2.4.0, al habilitar un puerto digital, el valor del puerto debe ser reportado al cliente aplicación.*

Solicitar informe de versión
```
0 solicitar informe de versión (0xF9) (En MIDI no esta definido)
```

Formato de mensaje de Sysex
===

System exclusive (sysex) los mensajes se utilizan para definir conjuntos de características principales y firmata opcionales. Las características principales están relacionadas con funciones como E/S digitales y analógicas, consultando información sobre el estado y las capacidades de la placa de microcontrolador y el firmware que se ejecuta en la placa. Todas las características principales se documentan en este archivo protocol.md. Las características opcionales amplían las capacidades de hardware más allá de la E/S digital básica y la E/S analógica y también proporcionan API para interactuar con componentes y servicios del sistema generales y específicos. Las características opcionales se documentan individualmente en archivos de markdowns independientes.

Cada mensaje firmata sysex tiene un ID de característica, compuesto de un solo byte o un ID extendido compuesto por 3 bytes donde el primer byte siempre es 0 para indicar que es un ID extendido. En la tabla siguiente se muestra la estructura. El bit mas significativo debe establecerse en 0 en cada byte entre el `START_SYSEX` y `END_SYSEX` que enmarcan el mensaje.

| byte 0      | byte 1       | bytes 2 - N-1                             | byte N    |
| ----------- | ------------ | ----------------------------------------- | --------- |
| START_SYSEX | ID (01H-7DH) | PAYLOAD                                   | END_SYSEX |
| START_SYSEX | ID (00H)     | EXTENDED_ID (00H 00H - 7FH 7FH) + PAYLOAD | END_SYSEX |

A continuación se muestran los comandos SysEx utilizados para las características principales definidas en esta versión del protocolo:

```
EXTENDED_ID                 0x00 // Un valor de 0x00 indica que los siguientes 2 bytes define que es un ID extendido
RESERVED               0x01-0x0F // ID's 0x01 - 0x0F son reservado para comandos definidos por el usuario
ANALOG_MAPPING_QUERY        0x69 // consultar mapeo analogicos a numero de pin
ANALOG_MAPPING_RESPONSE     0x6A // respuesta con informacion de mapeo
CAPABILITY_QUERY            0x6B // consultar modos compatibles y la resolución de todos los pines
CAPABILITY_RESPONSE         0x6C // respuesta con modos y resolución compatibles
PIN_STATE_QUERY             0x6D // consultar el modo y el estado actuales de los pines (diferente del valor)
PIN_STATE_RESPONSE          0x6E // respuesta el modo y el estado actuales de los pines (diferente del valor)
EXTENDED_ANALOG             0x6F // escritura analogica (PWM, Servo, etc.) a cualquier pin
STRING_DATA                 0x71 // un mensaje de cadena con 14 bits por char
REPORT_FIRMWARE             0x79 // nombre del informe y versión del firmware
SAMPLING_INTERVAL           0x7A // el intervalo en el que se muestrea la entrada analógica (predeterminado: 19 ms)
SYSEX_NON_REALTIME          0x7E // MIDI reservado para mensajes no en tiempo real
SYSEX_REALTIME              0X7F // MIDI reservado para mensajes en tiempo real
```

El conjunto completo de IDs de características firmata principales y opcionales se define en el archivo [firmata-registry.md](https://github.com/firmata/protocol/blob/master/feature-registry.md). Consulte el registro para obtener más información sobre cómo proponer una nueva característica y obtener un ID de característica.

Consultar Nombre del Firmware y Version
---
El nombre del firmware que se debe notificar debe ser exactamente el mismo que el nombre de la
Archivo de cliente Firmata, menos la extensión de archivo. Así que para StandardFirmata.ino, el
el nombre del firmware es: StandardFirmata.

Consultar nombre del firmware y version
```
0  START_SYSEX       (0xF0)
1  queryFirmware     (0x79)
2  END_SYSEX         (0xF7)
```

Recibir el nombre del Firmware y version (despues de consultar query)
```
0  START_SYSEX       (0xF0)
1  queryFirmware     (0x79)
2  principal version (0-127)
3  menor version     (0-127)
4  primer char del nombre firmware (LSB)
5  primer char del nombre firmware (MSB)
6  segundo char del nombre firmware (LSB)
7  segundo char del nombre firmware (MSB)
... para tantos byte como necesite
N  END_SYSEX         (0xF7)
```

Analogico extendido
---

Como alternativa al mensaje analógico normal, esta versión extendida permite
más allá del pin 15 y admite el envío de valores analógicos con cualquier número de
bits. El número de bits de datos se deduce por la longitud del mensaje.

```
0  START_SYSEX              (0xF0)
1  extended analog message  (0x6F)
2  pin                      (0-127)
3  bits 0-6                 (menos significativo byte LSM)
4  bits 7-13                (mas significativo byte MSB)
... bytes adicionales se pueden enviar si se necesitan más bits.
N  END_SYSEX                (0xF7)
```

Consulta de capacidad
---

The capability query provides a list of all modes supported by each pin. Each mode is described by 2 bytes where the first byte is the pin mode (such as digital input, digital output, PWM) and the second byte is the resolution (or sometimes the type of pin such as RX or TX for a UART pin). A value of `0x7F` is used as a separator to mark the end each pin's list of modes. The number of pins supported is inferred by the message length.
La consulta de capacidad proporciona una lista de todos los modos admitidos por cada pin. Cada modo se describe mediante 2 bytes donde el primer byte es el modo pin (como entrada digital, salida digital, PWM) y el segundo byte es la resolución (o a veces el tipo de pin como RX o TX para un pin UART). Un valor de `0x7F` se utiliza como separador para marcar el final de la lista de modos de cada pin. La longitud del mensaje deduce el número de pines admitidos.

### Consulta de capacidad
```
0  START_SYSEX              (0xF0)
1  CAPABILITY_QUERY         (0x6B)
2  END_SYSEX                (0xF7)
```

### Respuesta capacidad
```
0  START_SYSEX              (0xF0)
1  CAPABILITY_RESPONSE      (0x6C)
2  1st supported mode of pin 0
3  1st mode's resolution of pin 0
4  2nd supported mode of pin 0
5  2nd mode's resolution of pin 0
... additional modes/resolutions, followed by `0x7F`,
    to mark the end of the pin's modes. Subsequently, each pin
    follows with its modes/resolutions and `0x7F`,
    until all pins are defined.
N  END_SYSEX                (0xF7)
```

#### Modos soportados
Los modos de la lista siguiente son los modos de operación que se pueden devolver durante la respuesta de capacidad:
```
DIGITAL_INPUT      (0x00)
DIGITAL_OUTPUT     (0x01)
ANALOG_INPUT       (0x02)
PWM                (0x03)
SERVO              (0x04)
SHIFT              (0x05)
I2C                (0x06)
ONEWIRE            (0x07)
STEPPER            (0x08)
ENCODER            (0x09)
SERIAL             (0x0A)
INPUT_PULLUP       (0x0B)
```

*Si no se define ningún modo para un pin, no se devuelve ningún valor (que no sea el valor separador '0x7F') y se debe suponer que el pin no es compatible con Firmata.*

#### Modo Resolucion
El byte de resolución tiene un par de propósitos diferentes:

1. El propósito original era definir la resolución para la entrada analógica, pwm, servo y otros modos que definen una resolución específica (como 10 bits para analógico).
2. El byte de resolución se ha adaptado para otro propósito para los pines Serial/UART, define si el pin es RX o TX y a qué UART pertenece. [RX0] (https://github.com/firmata/protocol/blob/master/serial.md#serial-pin-capability-response) es el pin RX de UART0 (Serial en un Arduino por ejemplo), TX1 si el pin TX de UART1 (Serial1 en un Arduino).:

Modos que utilizan el byte de resolución como datos de resolución:
```
DIGITAL_INPUT      (0x00) // resolution is 1 (binary)
DIGITAL_OUTPUT     (0x01) // resolution is 1 (binary)
ANALOG_INPUT       (0x02) // analog input resolution in number of bits
PWM                (0x03) // pwm resolution in number of bits
SERVO              (0x04) // servo resolution in number of bits
STEPPER            (0x08) // resolution is number number of bits in max number of steps
INPUT_PULLUP       (0x0B) // resolution is 1 (binary)
```

Modos que utilizan el byte de resolución para definir el tipo de pin:
```
SERIAL             (0x0A) // Ver descripcion en [serial.md](https://github.com/firmata/protocol/blob/master/serial.md#serial-pin-capability-response)
// también se añadirá a I2C en el futuro para definir pines SCL y SDA
```

*Para otras características (incluyendo I2C hasta que se actualice) la información de resolución es menos importante por lo que se utiliza un valor de 1.*

Consulta de mapeo analogico
---

Analog messages are numbered 0 to 15, which traditionally refer to the Arduino
pins labeled A0, A1, A2, etc. However, these pins are actually configured using
"normal" pin numbers in the pin mode message, and when those pins are used for
non-analog functions. The analog mapping query provides the information about
which pins (as used with Firmata's pin mode message) correspond to the analog
channels.


Los mensajes analógicos están numerados del 0 al 15, que tradicionalmente se refieren al
pines etiquetados A0, A1, A2, etc. Sin embargo, estos pines se configuran realmente usando
números de pin "normales" en el mensaje de modo pin, y cuando esos pines se utilizan para
funciones no analógicas. La consulta de mapeo analógico proporciona la información sobre
que los pines (como se utiliza con el mensaje de modo pin de Firmata) corresponden a los
Canales Analogicos.

Consulta de mapeo analogico
```
0  START_SYSEX              (0xF0)
1  analog mapping query     (0x69)
2  END_SYSEX                (0xF7)
```

Respuesta de mapeo analogico
```
0  START_SYSEX              (0xF0)
1  analog mapping response  (0x6A)
2  analog channel corresponding to pin 0, or 127 if pin 0 does not support analog
3  analog channel corresponding to pin 1, or 127 if pin 1 does not support analog
4  analog channel corresponding to pin 2, or 127 if pin 2 does not support analog
... etc, one byte for each pin
N  END_SYSEX                (0xF7)
```

*The above 2 queries provide static data (should never change for a particular
board). Because this information is fixed and should only need to be read once,
these messages are designed for a simple implementation in StandardFirmata,
rather that bandwidth savings (eg, using packed bit fields).*

*Las 2 consultas anteriores proporcionan datos estáticos (nunca deben cambiar para un
board). Debido a que esta información es fija y sólo debe leerse una vez,
estos mensajes están diseñados para una implementación simple en StandardFirmata,
más bien que el ahorro de ancho de banda (por ejemplo, utilizando campos de bits empaquetados).*


Consulta del estado del Pin
---

El **estado** del pin es cualquier dato escrito en el pin (*es importante tener en cuenta que el estado del pin es distinto al valor del pin*). Para los modos de salida (salida digital, PWM y Servo), el estado es cualquier valor que se haya escrito previamente en el pin. Para los modos de entrada, normalmente el estado es cero. Sin embargo, para las entradas digitales, el estado es el estado de la resistencia pull-up que es 1 si está habilitado, 0 si está deshabilitado.

La consulta de estado de pin también se puede utilizar como verificación después de enviar modos de pin
o mensajes de datos.

Consulta de estado de pin
```
0  START_SYSEX              (0xF0)
1  pin state query          (0x6D)
2  pin                      (0-127)
3  END_SYSEX                (0xF7)
```

Respuesta del estado del pin
```
0  START_SYSEX              (0xF0)
1  pin state response       (0x6E)
2  pin                      (0-127)
3  pin mode (the currently configured mode)
4  pin state, bits 0-6
5  (optional) pin state, bits 7-13
6  (optional) pin state, bits 14-20
... additional optional bytes, as many as needed
N  END_SYSEX                (0xF7)
```

String
---

Enviar mensajes de texto corto entre el board y la aplicación cliente (Software remoto o host). La longitud del texto es limitada
a la mitad del tamaño del búfer - 3 (para Arduino esto limita las cadenas a 30 caracteres). Comúnmente utilizado para reportar
mensajes de error al cliente.

```
0  START_SYSEX        (0xF0)
1  STRING_DATA        (0x71)
2  first char LSB
3  first char MSB
4  second char LSB
5  second char MSB
... additional bytes up to half the buffer size - 3 (START_SYSEX, STRING_DATA, END_SYSEX)
N  END_SYSEX          (0xF7)
```

Intervalo de muestreo
---

The sampling interval sets how often analog data and i2c data is reported to the
client. The default for the arduino implementation is 19ms. This means that every
19ms analog data will be reported and any i2c devices with read continuous mode
will be read.
El intervalo de muestreo establece la frecuencia con la que se notifican los datos analógicos y los datos i2c a la al Cliente. El valor predeterminado para la implementación de arduino es 19ms. Esto significa que cada 19ms se informarán datos analógicos y cualquier dispositivo i2c con modo continuo de lectura será leído.
```
0  START_SYSEX        (0xF0)
1  SAMPLING_INTERVAL  (0x7A)
2  sampling interval on the millisecond time scale (LSB)
3  sampling interval on the millisecond time scale (MSB)
4  END_SYSEX          (0xF7)
```
