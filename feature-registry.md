# Registro de caracteristicas Firmata sysex

Los registros de caracteristicas de
The feature registry defines allocated and proposed Firmata feature IDs. The feature ID is the 2nd byte in the sysex message. An extended set of IDs is also available by setting the initial ID byte to `00H` and then following with a 2 byte ID. All bytes between `START_SYSEX` and `END_SYSEX` must have the most significant bit set to 0.

Los registro de características define los ID de características Firmata asignados y propuestos. El ID de característica es el 2do byte en el mensaje sysex. Un conjunto extendido de ID's también está disponible estableciendo el byte de ID inicial en '00H' y luego siguiendo con un identificador de 2 bytes. Todos los bytes entre `START_SYSEX` y `END_SYSEX` deben tener el bit más significativo establecido en 0.

| byte 0      | byte 1       | bytes 2 - N-1                             | byte N    |
| ----------- | ------------ | ----------------------------------------- | --------- |
| START_SYSEX | ID (01H-7DH) | PAYLOAD                                   | END_SYSEX |
| START_SYSEX | ID (00H)     | EXTENDED_ID (00H 00H - 7FH 7FH) + PAYLOAD | END_SYSEX |

Proponer una nueva funcion
===

There are two different feature sets: [Core features](#core-feature-set) and [optional features](#optional-feature-set). See the descriptions for each type of feature set below. To propose a new core feature, open an issue to start a discussion. To propose a new optional feature, [open an issue](https://github.com/firmata/protocol/issues)/and or a pull request adding a markdown file for the proposed feature. Also edit the [optional feature set table](#optional-feature-set) to reserve an ID for the proposed feature and enter the status as "proposed". If the proposed feature exposes a very specific device or device driver (a NeoPixel light strip for example), assign an ID in the extended ID set (`00H nnH nnH`).

Hay dos conjuntos de características diferentes: [Características principales](#core-feature-set) y [características opcionales](#optional-feature-set). Consulte las descripciones de cada tipo de conjunto de características a continuación. Para proponer una nueva característica básica, abra un problema para iniciar un debate. Para proponer una nueva característica opcional, [abrir un problema](https://github.com/firmata/protocol/issues)/y una solicitud de extracción agregando un archivo de reducción para la característica propuesta. Edite también la [tabla de conjunto de características opcional](#optional-feature-set) para reservar un ID para la entidad propuesta e introduzca el estado como "propuesto". Si la característica propuesta expone un dispositivo o controlador de dispositivo muy específico (por ejemplo, una tira de luz NeoPixel), asigne un ID en el conjunto de ID extendido (`00H nnH nnH`).


Conjunto de características principales
===

Las características principales están relacionadas con funciones como E/S digitales y analógicas, consultando información sobre el estado y las capacidades de la placa de microcontrolador y el firmware que se ejecuta en la placa. Las características principales se documentan en el archivo [protocol.md](https://github.com/firmata/protocol/blob/master/protocol.md) y el conjunto completo de características principales se versiona juntos mediante la notación [semver](http://semver.org/). La versión actual del protocolo es 2.5.1.

El firmware de Firmata debe informar de la versión actual del protocolo (mediante el comando [protocol version: 0xF9](https://github.com/firmata/protocol/blob/master/protocol.md-message-types)) e implementar el conjunto completo de características principales actuales definidas para esa versión (con la excepción de hardware muy limitado que puede implementar un subconjunto del conjunto de características principal).

*El rango 01H - 0FH está reservado para las características definidas por el usuario que no se agregan a este registro.*


| Feature ID  | Feature name / link to documentation | Status     |
| ----------- | ------------------------------------ | ---------  |
| 00H         | EXTENDED_ID                          | proposed   |
| 01H - 0FH   | *Reserved for user features*         | n/a        |
| 63H         | [REPORT_DIGTIAL_PIN - proposal](https://github.com/firmata/protocol/issues/68#issuecomment-257105540) | proposed |
| 64H         | [EXTENDED_REPORT_ANALOG - proposal](https://github.com/firmata/protocol/issues/68#issuecomment-258748963) | proposed |
| 65H         | [REPORT_FEATURES - proposal](https://github.com/firmata/protocol/issues/41) | proposed |
| 69H         | [ANALOG_MAPPING_QUERY](https://github.com/firmata/protocol/blob/master/protocol.md#analog-mapping-query) | current |
| 6AH         | [ANALOG_MAPPING_RESPONSE](https://github.com/firmata/protocol/blob/master/protocol.md#analog-mapping-query) | current |
| 6BH         | [CAPABILITY_QUERY](https://github.com/firmata/protocol/blob/master/protocol.md#capability-query) | current |
| 6CH         | [CAPABILITY_RESPONSE](https://github.com/firmata/protocol/blob/master/protocol.md#capability-query) | current |
| 6DH         | [PIN_STATE_QUERY](https://github.com/firmata/protocol/blob/master/protocol.md#pin-state-query) | current |
| 6EH         | [PIN_STATE_RESPONSE](https://github.com/firmata/protocol/blob/master/protocol.md#pin-state-query) | current |
| 6FH         | [EXTENDED_ANALOG](https://github.com/firmata/protocol/blob/master/protocol.md#extended-analog) | current |
| 71H         | [STRING_DATA](https://github.com/firmata/protocol/blob/master/protocol.md#string) | current |
| 79H         | [REPORT_FIRMWARE](https://github.com/firmata/protocol/blob/master/protocol.md#query-firmware-name-and-version) | current |
| 7AH         | [SAMPLING_INTERVAL](https://github.com/firmata/protocol/blob/master/protocol.md#sampling-interval) | current |
| 7CH         | [ANALOG_CONFIG - proposal](https://github.com/firmata/protocol/pull/8/files) | proposed |
| 7EH         | SYSEX_NON_REALTIME*                  | n/a        |
| 7FH         | SYSEX_REALTIME*                      | n/a        |

**7EH y 7FH están reservados porque tienen un significado especial para los analizadores midi.*


Conjunto de funciones opcional
===

Las características opcionales amplían las capacidades de hardware más allá de la E/S digital básica y la E/S analógica (por ejemplo: I2C, Serial/UART, etc.). Las características opcionales también proporcionan API para interactuar con componentes generales (por ejemplo: servo, paso a paso, codificador rotativo, etc.), así como componentes específicos (por ejemplo: DHT11, NeoPixel, etc.). El conjunto de características opcionales también incluye funciones como una API de programador de propósito general y una API de interfaz de dispositivo estandarizada. Las características generales deben utilizar el ID de entidad de un solo byte (asignación de nuevos identificadores en orden descendente). Sin embargo, cualquier característica que envuelve un controlador específico, un sensor específico, un componente personalizado único, etc. debe utilizar el ID de característica extendida (`00H nnH nnH`) o debe utilizar la API `DEVICE_QUERY/RESPONSE`.

Cada característica debe documentarse en un archivo de markdown y versionarse de forma independiente utilizando la notación [semver](http://semver.org/). En el caso de que una característica abarque varios ID (I2C por ejemplo), todo ese conjunto se documenta en un único archivo y se versiona juntos.


| Feature ID  | Feature name                       | Link to documentation  | Status     |
| ----------- | ---------------------------------- | ---------------------- | ---------- |
| 5CH         | RCOUTPUT_DATA                      | [rcswitch-proposal.md](https://github.com/firmata/protocol/blob/master/proposals/rcswitch-proposal.md) | proposed |
| 5DH         | RCINPUT_DATA                       | [rcswitch-proposal.md](https://github.com/firmata/protocol/blob/master/proposals/rcswitch-proposal.md) | proposed |
| 5EH         | DEVICE_QUERY                       | [proposal](https://github.com/finson-release/Luni/blob/master/extras/v0.9/v0.8-device-driver-C-firmata-messages.md) | proposed |
| 5FH         | DEVICE_RESPONSE                    | [proposal](https://github.com/finson-release/Luni/blob/master/extras/v0.9/v0.8-device-driver-C-firmata-messages.md) | proposed |
| 60H         | SERIAL_DATA (1.0)                  | [serial-1.0.md](https://github.com/firmata/protocol/blob/master/serial.md) | current |
| 61H         | ENCODER_DATA                       | [encoder.md](https://github.com/firmata/protocol/blob/master/encoder.md) | current |
| 62H         | ACCELSTEPPER_DATA                  | [accelStepperFirmata.md](https://github.com/firmata/protocol/blob/master/accelStepperFirmata.md) | current |
| 67H         | SERIAL_DATA (2.0)                  | [proposal](https://github.com/firmata/protocol/blob/master/proposals/serial-2.0-proposal.md) | proposed |
| 68H         | SPI_DATA                           | [proposal](https://github.com/firmata/protocol/blob/master/proposals/spi-proposal.md) | proposed |
| 70H         | SERVO_CONFIG                       | [servos.md](https://github.com/firmata/protocol/blob/master/servos.md) | current |
| 72H         | STEPPER_DATA                       | [stepper-legacy.md](https://github.com/firmata/protocol/blob/master/stepper-legacy.md) | deprecated |
| 73H         | ONEWIRE_DATA                       | [onewire.md](https://github.com/firmata/protocol/blob/master/onewire.md) | current |
| 75H         | SHIFT_DATA                         | [shift-proposal.md](https://github.com/firmata/protocol/blob/master/proposals/shift-proposal.md) | proposed |
| 76H         | I2C_REQUEST                        | [i2c.md](https://github.com/firmata/protocol/blob/master/i2c.md) | current |
| 77H         | I2C_REPLY                          | [i2c.md](https://github.com/firmata/protocol/blob/master/i2c.md) | current |
| 78H         | I2C_CONFIG                         | [i2c.md](https://github.com/firmata/protocol/blob/master/i2c.md) | current |
| 7BH         | SCHEDULER_DATA                     | [scheduler.md](https://github.com/firmata/protocol/blob/master/scheduler.md) | current |
|             |                                    |                        |            |
| 00H nnH nnH | (start of extended feature ID set) |                        |            |

