> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-simulated-device.md)
> 
> 

A [szimulált eszközről felhőbe történő feltöltés mintájának] útmutatója bemutatja, hogyan küldhet eszköz–felhő telemetriát az IoT Hubra szimulált eszközökről az [Azure IoT Gateway SDK][lnk-sdk] használatával.

A bemutató tartalma:

1. **Architektúra**: fontos, az architektúrára vonatkozó információk a szimulált eszközről felhőbe történő feltöltés mintájáról.
2. **Létrehozás és futtatás**: A minta elkészítéséhez és futtatásához szükséges lépések.

## <a name="architecture"></a>Architektúra
A szimulált eszközről felhőbe történő feltöltés mintája bemutatja, hogyan használható az SDK egy olyan átjáró létrehozására, amely telemetriát küld a szimulált eszközökről egy IoT Hubra. A szimulált eszközök nem képesek közvetlenül csatlakozni az IoT Hubhoz, mert:

* Az eszközök nem használnak az IoT Hub által ismert kommunikációs protokollt.
* Az eszközök nem elég intelligensek ahhoz, hogy megjegyezzék az IoT Hub által hozzájuk rendelt identitást.

Az átjáró az alábbi módokon oldja meg a szimulált eszközök ezen problémáit:

* Az átjáró ismeri a szimulált eszközök által használt protokollt, fogadja az eszköz–felhő telemetriát az eszközökről, és továbbítja ezeket az üzeneteket az IoT Hubra egy, az IoT Hub által ismert protokoll használatával.
* Az átjáró tárolja az IoT Hub-identitásokat a szimulált eszközök nevében, és proxyként funkcionál, amikor a szimulált eszköz üzenetet küld az IoT Hubra.

Az alábbi diagram a minta fő alkotóelemeit mutatja be, többek között az átjárómodulokat:

![][1]

> [!NOTE]
> A modulok nem adnak át üzeneteket közvetlenül egymásnak. A modulok üzeneteket tesznek közzé egy belső közvetítőnek, amely az alábbi diagramon bemutatott előfizetési mechanizmus segítségével kézbesíti az üzenetet a többi modulnak. További információk: [IoT Gateway SDK – első lépések][lnk-gw-getstarted].
> 
> 

### <a name="protocol-ingestion-module"></a>Protokollfeldolgozási modul
Ez a modul a kezdőpontja az adatok eszközökről történő lekérését, az átjárón való továbbítását és a felhőbe küldését magában foglaló folyamatnak. A példában a modul négy feladatot hajt végre:

1. Szimulált hőmérsékleti adatokat hoz létre. Megjegyzés: Ha valós eszközöket használ, a modul azokról a fizikai eszközökről olvassa be az adatokat.
2. A szimulált hőmérsékleti adatokat egy üzenet törzsébe helyezi.
3. A szimulált hőmérsékleti adatokat tartalmazó üzenethez hozzáad egy hamis MAC-című tulajdonságot.
4. Elérhetővé teszi az üzenetet a láncban következő modul számára.

> [!NOTE]
> A fenti diagramban **Protocol X ingestion** (Protokoll X-feldolgozás) néven szereplő modul neve a forráskódban **Simulated device** (Szimulált eszköz).
> 
> 

### <a name="mac-lt-gt-iot-hub-id-module"></a>MAC &lt;-&gt; IoT Hub-azonosítómodul
Ez a modul megkeresi azokat az üzeneteket, amelyekben a szimulált eszközalkalmazás protokoll-feldolgozási modulja által hozzáadott, MAC-címet tartalmazó tulajdonság található. Ha a modul talál ilyen tulajdonságot, hozzáad egy másik, IoT Hub-eszközkulcsot tartalmazó tulajdonságot az üzenethez, majd elérhetővé teszi az üzenetet a láncban következő modul számára. A minta így társít IoT Hub-eszközidentitásokat a szimulált eszközökhöz. A fejlesztő manuálisan, a modulkonfiguráció részeként állítja be a leképezést a MAC-címek és az IoT Hub-identitások között. 

> [!NOTE]
> Ez a minta egy MAC-címet használ egyedi eszközazonosítóként, és azt egy IoT Hub-eszközidentitáshoz kapcsolja. Írhat azonban saját, eltérő egyedi azonosítót használó modult is. Előfordulhat, hogy az eszközeinek egyedi sorozatszáma van, vagy a telemetriai adatokba be van ágyazva egy egyedi eszköznév. Ez esetben használhatja például ezeket az IoT Hub eszközidentitásának megállapítására.
> 
> 

### <a name="iot-hub-communication-module"></a>IoT Hub-kommunikációs modul
Ez a modul veszi azokat az üzeneteket, amelyekhez az előző modul IoT Hub-eszközidentitást rendelt, és HTTP használatával elküldi a tartalmukat az IoT Hubra. A három, az IoT Hub által ismert protokoll közül az egyik a HTTP.

Ahelyett, hogy minden szimulált eszközalkalmazásnál csatlakozna az IoT Hubhoz, ez a modul nyit egy egyszeri HTTP-kapcsolatot az átjáróból az IoT Hub felé, és az összes szimulált eszköz felől induló kapcsolatot ezen a kapcsolaton közvetíti. Ez lehetővé teszi, hogy egy átjáró sokkal több eszközt (legyen az szimulált vagy egyéb) csatlakoztasson, mint ha minden eszközhöz egyedi kapcsolatot nyitna.

![][2]

<!-- Images -->
[1]: media/iot-hub-gateway-sdk-simulated-selector/image1.png
[2]: media/iot-hub-gateway-sdk-simulated-selector/image2.png

<!-- Links -->
[szimulált eszközről felhőbe történő feltöltés mintájának]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/simulated_device_cloud_upload/README.md
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md

<!--HONumber=Feb17_HO1-->


