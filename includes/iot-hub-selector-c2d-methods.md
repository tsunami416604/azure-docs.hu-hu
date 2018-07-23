> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/quickstart-control-device-node.md)
> * [C#](../articles/iot-hub/quickstart-control-device-dotnet.md)
> * [Java](../articles/iot-hub/quickstart-control-device-java.md)
> * [Python](../articles/iot-hub/quickstart-control-device-python.md)

Az Azure IoT Hub egy teljesen felügyelt szolgáltatás, amely lehetővé teszi a megbízható és biztonságos kétirányú kommunikációt több millió eszköz és a egy megoldás között háttér. Előző oktatóanyagokban ([IoT Hub használatának első lépései] és [Az IoT Hub felhőből az eszközre irányuló üzenetek küldéséhez]) alapvető eszközről a felhőbe és a felhőből az eszközre irányuló üzenetküldési funkciói az IoT Hub mutatják be. Az IoT Hub is teszi lehetővé a felhőből az eszközökön nem tartós metódusokat hívhat meg. Közvetlen metódusok képviseli egy kérés-válasz interakció egy HTTPS-hívás hasonló eszközzel, abban, hogy azok sikeres, vagy a hívás állapotát tudja a felhasználók azonnal (felhasználó által megadott időtúllépési) után sikertelen. A cikk [egy eszközön közvetlen metódus meghívása] [ lnk-devguide-methods] közvetlen metódusok részletesebben ismerteti, és mikor használjon közvetlen metódusok helyett a felhőből az eszközre irányuló üzenetek vagy a kívánt tulajdonságok útmutatást nyújt.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Az Azure portal használatával hozzon létre egy IoT hubot, és hozzon létre egy új eszközidentitást az IoT hubban.
* Egy szimulált eszközalkalmazás létrehozása, amely rendelkezik egy közvetlen metódus nem hívható meg a felhő által.
* Hozzon létre egy konzolalkalmazást, amely meghívja a közvetlen metódus a szimulált eszköz alkalmazásban az IoT hub segítségével.


[lnk-devguide-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md

[Az IoT Hub felhőből az eszközre irányuló üzenetek küldéséhez]: ../articles/iot-hub/iot-hub-csharp-csharp-c2d.md
[IoT Hub használatának első lépései]: ../articles/iot-hub/quickstart-send-telemetry-node.md