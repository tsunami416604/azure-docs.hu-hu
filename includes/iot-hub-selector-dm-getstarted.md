> [!div class="op_single_selector"]
> * [Eszköz: Node.js Szolgáltatás: Node.js.](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [Eszköz: Node.js Szolgáltatás: C#](../articles/iot-hub/iot-hub-csharp-node-device-management-get-started.md)
> * [Eszköz: C# szolgáltatás: C#](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Eszköz: Java szolgáltatás: Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Eszköz: Python szolgáltatás: Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Háttér-alkalmazások használhatják Azure IoT Hub primitívek, például a [eszköz iker] [ lnk-devtwin] és [módszerek közvetlen][lnk-c2dmethod], a távolról start és eszköz figyelése felügyeleti műveleteket. Az oktatóanyag bemutatja, hogyan egy háttér-alkalmazást és egy eszköz alkalmazásának együttműködése hogyan kezdeményezésére és figyelésére a távoli eszköz IoT-központ használatával újra kell indítani.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

A közvetlen módszer segítségével eszköz felügyeleti műveletek (például újraindítás, a gyári beállítások visszaállítása és a belső vezérlőprogram frissítése) kezdeményez egy háttér-alkalmazást a felhőben. Az eszköz felelős:

* A metódus kérelmet küldött az IoT-központ kezelő.
* Kezdeményezi a megfelelő eszközre vonatkozó művelet az eszközön.
* Állapot frissítéseket biztosító *tulajdonságok jelentett* az IoT hubhoz.

A felhő egy háttér-alkalmazás használatával az eszköz felügyeleti művelet haladásával eszköz iker lekérdezéseket futtathat.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
