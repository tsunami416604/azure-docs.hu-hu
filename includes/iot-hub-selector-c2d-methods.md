> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-direct-methods.md)
> * [C#/node.js](../articles/iot-hub/iot-hub-csharp-node-direct-methods.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-direct-methods.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-direct-methods.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-direct-methods.md)

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a megbízható és biztonságos kétirányú kommunikációs eszközök millióira és a megoldás közötti háttér. Előző oktatóanyagok ([Ismerkedés az IoT-központ] és [IoT Hub-felhő eszközre üzenetek]) alapvető eszköz-felhő és a felhő eszközre üzenetkezelési funkcióit az IoT-központ bemutatására. Az IoT-központ is lehetővé teszi az eszközök a felhőből nem tartós módszerek meghívására. Közvetlen módszerek határoz meg egy kérelem-válasz interakció egy HTTPS-hívás hasonló eszközökkel abban, hogy sikeres legyen, vagy közvetlenül (után a felhasználó által megadott időtúllépés), hogy a felhasználóknak a hívás állapotáról sikertelen. [Az eszközön közvetlen metódus] [ lnk-devguide-methods] részletesebben közvetlen módszereket írja le, és közvetlen módszerek helyett a felhő-eszközre küldött üzenetek vagy a kívánt tulajdonságok használatával kapcsolatos útmutatást nyújt.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Az Azure-portál használatával létrehoz egy IoT-központot, és az IoT hub hozzon létre egy eszközidentitás.
* Hozzon létre egy szimulált eszköz alkalmazást, amely a felhő által hívható közvetlen metódus.
* Hozzon létre egy konzolalkalmazást, amely közvetlen metódus meghívja a szimulált eszköz alkalmazásban az IoT hub keresztül.


[lnk-devguide-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md

[IoT Hub-felhő eszközre üzenetek]: ../articles/iot-hub/iot-hub-csharp-csharp-c2d.md
[Ismerkedés az IoT-központ]: ../articles/iot-hub/iot-hub-node-node-getstarted.md