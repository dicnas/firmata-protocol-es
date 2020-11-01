# Documentacion del Protocolo Firmata
[Documentacion original](https://github.com/firmata/protocol)

[![Join the chat at https://gitter.im/firmata/protocol](https://badges.gitter.im/firmata/protocol.svg)](https://gitter.im/firmata/protocol?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

Firmata es un protocolo para la comunicación con microcontroladores desde software en un ordenador (o smartphone/tableta, etc.). El protocolo se puede implementar en el firmware en cualquier arquitectura de microcontrolador, así como software en cualquier paquete de software de computadora (consulte la lista de bibliotecas de cliente a continuación).

Firmata se basa en el [formato de mensaje midi](http://www.midi.org/techspecs/midimessages.php) en que los bytes de comandos son 8 bits y los bytes de datos son 7 bits. Por ejemplo el mensaje MIDI Channel Pressure (Comando: 0xD0) tiene 2 bytes de longitud, en Firmata el Comando 0xD0 se utiliza para habilitar la generación de informes para un puerto digital (coleccion de 8 pines). Las versiones midi y Firmata tienen 2 bytes de largo, pero el significado es obviamente diferente. En Firmata, el número de bytes de un mensaje debe ajustarse al mensaje midi correspondiente. Midi [System Exclusive](http://www.2writers.com/eddie/tutsysex.htm) (Sysex) mensajes sin embargo, puede ser de cualquier longitud y por lo tanto se utilizan de manera más prominente en todo el protocolo Firmata.

Este repositorio contiene documentación del protocolo Firmata. El núcleo del protocolo se describe en el archivo [protocol.md file](protocol.md). La documentación específica de alguna característica se describe en archivos markdowns individuales ([i2c.md](i2c.md), [accelStepperFirmata.md](https://github.com/firmata/protocol/blob/master/accelStepperFirmata.md), [servos.md](servos.md), etc). Los archivos añadidos al directorio de propuestas son propuestas para nuevas características que aún no se han finalizado. Consulte [firmata-registry.md](https://github.com/firmata/protocol/blob/master/feature-registry.md) para obtener la lista completa de características firmata documentadas.

El protocolo Firmata teóricamente podría implementarse para cualquier plataforma de microcontrolador. Actualmente, sin embargo, la implementación más completa es para [Arduino](http://arduino.cc) (incluidos los microcontroladores compatibles con Arduino). Estas son las implementaciones conocidas de la plataforma de microcontrolador Firmata:

* [Firmata para Arduino](https://github.com/firmata/arduino)
* [Firmata para Spark.io](https://github.com/firmata/spark)

*Tenga en cuenta: Estoy seguro de que hay otras implementaciones. Si conoce a otros, envíe una solicitud a la [Documentacion original](https://github.com/firmata/protocol) para actualizar este archivo Léame o abra un issue que proporcione el vínculo que se agregará a este documento.*

## Bibliotecas de Clientes Firmata
Hay varias bibliotecas de cliente. Se trata de bibliotecas que implementan el protocolo Firmata para comunicarse (por ejemplo desde un ordenador, smartphone o tableta) con el firmware de Firmata ejecutándose en una plataforma de microcontrolador. A continuación se muestra una lista de implementaciones de biblioteca de cliente de Firmata:

* processing
  * [https://github.com/firmata/processing]
  * [http://funnel.cc]
* python
  * [https://github.com/MrYsLab/pymata4]
  * [https://github.com/MrYsLab/pymata-express]
  * [https://github.com/firmata/pyduino]
  * [https://github.com/lupeke/python-firmata]
  * [https://github.com/tino/pyFirmata]
* perl
  * [https://github.com/ntruchsess/perl-firmata]
  * [https://github.com/rcaputo/rx-firmata]
* ruby
  * [https://github.com/hardbap/firmata]
  * [https://github.com/PlasticLizard/rufinol]
  * [http://funnel.cc]
* clojure
  * [https://github.com/nakkaya/clodiuno]
  * [https://github.com/peterschwarz/clj-firmata]
* javascript
  * [https://github.com/jgautier/firmata]
  * [http://breakoutjs.com]
  * [https://github.com/rwldrn/johnny-five]
* java
  * [https://github.com/4ntoine/Firmata]
  * [https://github.com/kurbatov/firmata4j]
  * [https://github.com/reapzor/FiloFirmata]
* .NET
  * [https://github.com/SolidSoils/Arduino]
  * [http://www.imagitronics.org/projects/firmatanet/]
  * [https://github.com/wbadry/FirmataCSharpClientClass]
* Flash/AS3
  * [http://funnel.cc]
  * [http://code.google.com/p/as3glue/]
* PHP
  * [https://bitbucket.org/ThomasWeinert/carica-firmata]
  * [https://github.com/oasynnoum/phpmake_firmata]
* Haskell
  * [http://hackage.haskell.org/package/hArduino]
* iOS
  * [https://github.com/jacobrosenthal/iosfirmata]
* Dart
  * [https://github.com/nfrancois/firmata]
* Max/MSP
  * [http://www.maxuino.org/]
* Elixir
  * [https://github.com/kfatehi/firmata]
* Modelica
  * [https://www.wolfram.com/system-modeler/libraries/model-plug/]
* golang
  * [https://github.com/kraman/go-firmata]
* Qt/QML
  * [https://github.com/callaa/qfirmata]
* Android/Kotlin
  * [https://github.com/xujiaao/android-firmata]
* Smalltalk
  * [https://github.com/pharo-iot/Firmata] 

*Es posible que cada biblioteca de cliente no admita la versión más reciente del protocolo Firmata y todas las características descritas en este repositorio.*

## Contribuir

Ir a la [Documentacion original](https://github.com/firmata/protocol) para enviar una propuesta para una nueva característica
