> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
> * [C#/node.js](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-how-to-configure.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-how-to-configure.md)
> 
> 

## <a name="introduction"></a>Bevezetés

A [Ismerkedés az IoT Hub eszköz twins][lnk-twin-tutorial], megtudta, hogyan állítható be az eszköz metaadatait a megoldás háttér használata *címkék*, egy eszköz alkalmazásból eszköz feltételek jelentés használatával *tulajdonságok jelentett*, és lekérdezheti az SQL-szerű nyelv használatával adatokat.

Ebben az oktatóanyagban elsajátíthatja, hogyan használja a két eszköz *szükséges tulajdonságok* együtt *tulajdonságok jelentett*, és így távolról konfigurálhat az eszközön futó alkalmazások. Pontosabban Ez az oktatóanyag bemutatja, hogyan egy eszköz iker jelentett és kívánt tulajdonságokkal engedélyezze a többlépéses konfigurálása egy alkalmazást, és adja meg a válnak láthatóvá, a megoldás háttérrendszeréhez, ez a művelet állapotát az összes eszközön. Az eszköz-konfigurációk a szerepkör további információhoz található [IoT-központ az eszközkezelés áttekintése][lnk-dm-overview].

Magas szinten eszköz twins használata lehetővé teszi, hogy a megoldás háttérrendszeréhez, adja meg a kívánt konfigurációs parancsok küldése helyett a kezelt eszközök. Ez az eszköz feladata frissíti a konfigurációját (IoT forgatókönyvekben, ahol adott eszközhöz feltételek azonnal a parancsok végrehajtására hatással fontos), a legjobb módszer beállítása helyezi a megoldás háttérrendszeréhez folyamatosan jelentéskészítés közben az aktuális állapot és a lehetséges hibaállapotok, a frissítési folyamat. Ez a minta nem műszeres felügyeleti eszközöket, a nagy, mert lehetővé teszi, hogy a megoldás háttérrendszeréhez, hogy a teljes látható-e a konfigurációs folyamat állapotát az összes eszközön.

> [!NOTE]
> Olyan esetekben, ahol vezérelt eszközök több interaktív módon (egy felhasználó által felügyelt alkalmazásból ventilátor bekapcsolása), érdemes lehet [módszerek közvetlen][lnk-methods].
> 
> 

Ebben az oktatóanyagban a megoldás háttérrendszeréhez a céleszközön telemetriai konfigurációját módosítja, és emiatt az adott, az eszköz alkalmazás a többlépéses folyamatot követi egy konfigurációs frissítés (például a számítógép újraindítására szoftver modul, amely ezt az oktatóanyag szimulálja egyszerű késéssel).

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

> [!NOTE]
> Konfigurációk lehetnek összetett objektumra, mert rendelt egyedi azonosítók (kivonatok vagy [GUID][lnk-guid]) egyszerűbbé teheti az összehasonlítást.
> 
> 

Az eszköz alkalmazás jelent a kívánt tulajdonságot tükrözés aktuális konfigurációja **telemetryConfig** jelentett tulajdonságai:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

Megjegyzés: hogyan a jelentett **telemetryConfig** további tulajdonsága **állapot**, a konfiguráció frissítési folyamat állapotának jelentésére használt.

Amikor egy új szükségeskonfiguráció érkezik, az eszköz alkalmazás egy függőben lévő konfigurációs adatok módosításával jelentések:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "changeId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

Majd egy későbbi időpontban, az eszköz alkalmazás jelentést készít a sikeres vagy sikertelen volt, a művelet által a fenti tulajdonság.
Vegye figyelembe, hogy a megoldás háttérrendszeréhez Mitől képes, tetszőleges időpontban, a konfigurációs folyamat állapotának lekérdezése az eszközön.

Ez az oktatóanyag a következőket mutatja be:

* Létrehoz egy szimulált eszköz alkalmazást, amely konfigurációs frissítések kap a megoldás háttérrendszeréhez, és több frissítések jelentések *tulajdonságok jelentett* a konfigurációban folyamatot nem lehet frissíteni.
* Hozzon létre egy háttér-alkalmazást, amely frissíti az eszköz kívánt beállításait, és ezután lekérdezi a konfigurációs frissítési folyamat.

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier
