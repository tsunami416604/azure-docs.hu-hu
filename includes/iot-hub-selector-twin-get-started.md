> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Az ikereszközök JSON-dokumentumok, amelyek az eszközök állapotinformációit (metaadatokat, konfigurációkat és állapotokat) tárolják. Az IoT-központ továbbra is fennáll, egy eszköz iker az egyes eszközök ahhoz csatlakozó ügyfélnél.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Az eszköz twins használja:

* A megoldás háttérből eszköz metaadatait tárolja.
* Például a rendelkezésre álló lehetőségeket, és a feltételek (például a kapcsolat használt módszer) aktuális állapotadatokat jelentést az eszköz alkalmazásból.
* Egy eszköz alkalmazás és a háttér-alkalmazások között (például a belső vezérlőprogram és konfigurációja frissítések) hosszan futó munkafolyamatok állapotának szinkronizálása.
* Az eszköz metaadatait, konfigurációs vagy az állapot lekérdezése.

Eszköz twins úgy tervezték, a szinkronizálás és eszköz-konfigurációk és a kikötések lekérdezése. További információ a használati eszköz twins található [eszköz twins megértéséhez][lnk-twins].

Eszköz twins az IoT-központ tárolódnak, és tartalmazza:

* *címkék*, csak a megoldás háttérrendszeréhez; által elérhető eszköz metaadatait
* *szükségeskonfiguráció-tulajdonságok*, a megoldás által módosítható JSON-objektumok biztonsági vége és megfigyelhető az eszköz alkalmazás; és
* *Tulajdonságok jelentett*, JSON-objektumok módosítható az eszköz alkalmazás, és a megoldás háttérrendszeréhez által is olvasható. Címkék és a Tulajdonságok tömb nem tartalmazhat, de az objektumok egymásba ágyazható.

![][img-twin]

Ezen felül a megoldás háttérrendszeréhez lekérdezheti az eszköz twins a fenti adatok alapján.
Tekintse meg [eszköz twins megértéséhez] [ lnk-twins] eszköz twins, és a további információt a [IoT-központ lekérdezési nyelv] [ lnk-query] hivatkozást lekérdezése.


Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy háttér-alkalmazást, amely *címkék* egy eszköz iker, és a szimulált eszköz alkalmazást, amely szerint a kapcsolat csatorna jelentések egy *tulajdonság jelentett* meg az eszköz iker.
* A szűrők használata a címkék és a korábban létrehozott tulajdonságok a háttér-alkalmazás eszközök lekérdezése.

<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md