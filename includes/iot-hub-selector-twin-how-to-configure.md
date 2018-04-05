> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
> * [C#/node.js](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-how-to-configure.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-how-to-configure.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-twin-how-to-configure.md)
> 
> 

## <a name="introduction"></a>Bevezetés

A [Ismerkedés az IoT Hub eszköz twins][lnk-twin-tutorial], megtudta, hogyan kell beállítani, az eszköz metaadatokat használ *címkék*. Eszköz feltételek kapott egy eszköz alkalmazás használt *tulajdonságok jelentett*, és majd lekérdezett ezt az információt az SQL-szerű nyelv használatával.

Ez az oktatóanyag leírja, hogyan használható a két eszköz *szükséges tulajdonságok* és *tulajdonságok jelentett* és így távolról konfigurálhat az eszközön futó alkalmazások. Jelentette, és egy eszköz iker kívánt tulajdonságokat engedélyezése egy többlépéses konfigurációja, amely egy alkalmazást, és adja meg a látható-e a művelet állapotát az összes eszközön. Az eszköz-konfigurációk a szerepkör további információhoz található [IoT-központ az eszközkezelés áttekintése][lnk-dm-overview].

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Magas szinten eszköz twins használata lehetővé teszi, hogy a megoldás háttérrendszeréhez, adja meg a kívánt konfigurációs parancsok küldése helyett a kezelt eszközök. Az eszköz nem frissíti a konfigurációját (IoT forgatókönyvekben, ahol adott eszközhöz feltételek azonnal a parancsok végrehajtására hatással fontos), a legjobb módszer beállítása feladata az aktuális állapot és a lehetséges folyamatosan reporting közben a frissítési folyamat hibaállapotok. Zárolás műszeres felügyeleti eszközöket, a nagy, az összes eszközön nyújtja a megoldás háttér-teljes látható-e a konfigurációs folyamat állapotát.

> [!TIP]
> Olyan esetekben, ahol eszközök (például egy felhasználó által felügyelt alkalmazásból ventilátor bekapcsolása) több interaktív módon szabályozhatók, érdemes lehet [módszerek közvetlen][lnk-methods].

Ebben az oktatóanyagban a megoldás háttérrendszeréhez a célként megadott eszköz telemetriai konfigurációját módosítja, úgy, hogy az eszköz alkalmazás a konfigurációs frissítés vonatkozik. Például egy konfigurációs frissítés volna kell a számítógép újraindítására szoftver modul, ebben az oktatóanyagban egy egyszerű késéssel szimulálja.

A megoldás háttérrendszeréhez tárolja a konfiguráció a két eszköz kívánt tulajdonságok az alábbi módon:

        {
            ...
            "properties": {
                ...
                "desired": {
                    "telemetryConfig": {
                        "configId": "{id of the configuration}",
                        "sendFrequency": "{config}"
                    }
                }
                ...
            }
            ...
        }

Konfigurációk lehetnek összetett objektumra, mert rendelt egyedi azonosítók (kivonatok vagy [GUID][lnk-guid]).


Az eszköz alkalmazás jelent a kívánt tulajdonságot tükrözés aktuális konfigurációja **telemetryConfig** jelentett tulajdonságai:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "configId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

Megjegyzés: hogyan a jelentett **telemetryConfig** további tulajdonsága **állapot**, a konfiguráció frissítési folyamat állapotának jelentésére használt.

Amikor egy új szükségeskonfiguráció érkezik, az eszköz alkalmazás egy függőben lévő konfigurációs állapotát módosításával jelentések:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "configId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "configId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

Ezt követően egy későbbi időpontban az eszköz alkalmazás jelentést készít a sikeres vagy sikertelen volt, a művelet a tulajdonság által. A megoldás háttérrendszeréhez lekérdezheti a konfigurációs folyamat állapotát az összes eszközön bármikor.

Ez az oktatóanyag a következőket mutatja be:

* Létrehoz egy szimulált eszköz alkalmazást, amely konfigurációs frissítések kap a megoldás háttérrendszeréhez, és több frissítések jelentések *tulajdonságok jelentett* a konfigurációban folyamatot nem lehet frissíteni.
* Hozzon létre egy háttér-alkalmazást, amely frissíti az eszköz kívánt beállításait, és ezután lekérdezi a konfigurációs frissítési folyamat.

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier
