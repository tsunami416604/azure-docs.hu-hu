> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-simulated-device.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-simulated-device.md)

Ez a forgatókönyv a [szimulált eszköz felhő feltöltése minta] bemutatja, hogyan használható [Azure IoT peremhálózati] [ lnk-sdk] eszközről a felhőbe telemetriai adatokat küldhet az IoT-központ szimulált eszközökről .

A bemutató tartalma:

* **Architektúra**: architekturális információt a [szimulált eszköz felhő feltöltése minta].
* **Létrehozás és futtatás**: A minta elkészítéséhez és futtatásához szükséges lépések.

## <a name="architecture"></a>Architektúra

A [szimulált eszköz felhő feltöltése minta] mutatja be, amely a szimulált eszköz telemetriai adatokat küld az IoT-központ átjáró létrehozásához. Egy eszköz nem lehet kapcsolódni a közvetlenül az IoT hubhoz, mert az eszközön:

* Az IoT-központ által értelmezhető kommunikációs protokoll nem használja.
* Nincs elég intelligens jegyezze meg az IoT-központ által hozzárendelt identitása.

Az IoT-átjárónak e problémák megoldását, a következőképpen:

* Az átjáró tisztában van azzal, az eszköz által használt protokoll eszközről a felhőbe telemetriai adatokat fogad az eszközt, és továbbítja azokat az üzeneteket az IoT-központ tudja értelmezni az IoT hub protokoll használatát.

* Az átjáró IoT-központ identitásokat az eszközökre, és proxyként funkcionál, ha egy eszközt az IoT-központ üzeneteket küld.

Az alábbi ábrán látható, a minta, beleértve az IoT-Edge modulok fő elemei:

![Diagram – a szimulált eszköz üzenet végighalad az IoT Hub-átjáró][1]

Ez a minta az átjárót alkotó három modulok tartalmazza:
1. Protokollfeldolgozási modul
1. MAC &lt;-&gt; IoT Hub-azonosítómodul
1. IoT Hub-kommunikációs modul

A modulok nem adnak át üzeneteket közvetlenül egymásnak. A modulok üzenetek közzététele egy belső broker, amely továbbítja az üzeneteket egy előfizetési mechanizmus használatával az egyéb modulok. További információkért lásd: [Ismerkedés az Azure IoT peremhálózati][lnk-gw-getstarted].

![Diagram – az átjáró modulok broker folytatott kommunikációhoz.][2]

### <a name="protocol-ingestion-module"></a>Protokollfeldolgozási modul

A protokoll adatfeldolgozást modul az kiindulópont folyamat során a adatok eszközökről, az átjárón keresztül, és a felhőben. 

A példában ez a modul:

1. Szimulált hőmérséklet adatokat hoz létre. Ha a fizikai eszközöket használ, a modul olvassa be az adatokat fizikai eszközöket.
1. Létrehoz egy üzenetet.
1. A szimulált hőmérséklet adatok helyezi el az üzenet tartalmát.
1. Az üzenet ad hozzá egy tulajdonság hamis MAC-címmel.
1. Elérhetővé teszi az üzenet a következő modulhoz a láncban.

A protokoll adatfeldolgozást modul **simulated_device.c** a forráskód.

### <a name="mac-lt-gt-iot-hub-id-module"></a>MAC &lt;-&gt; IoT Hub-azonosítómodul

A MAC &lt; - &gt; IoT Hub azonosító modul fordító működik. Ez a minta egy MAC-címet használ egyedi eszközazonosítóként, és azt egy IoT Hub-eszközidentitáshoz kapcsolja. Írhat azonban saját, eltérő egyedi azonosítót használó modult is. Például előfordulhat, hogy az eszközök egyedi sorozatszámokat, vagy a telemetriai adatok tartalmazhatják a beágyazott eszköz egyedi neve.

A példában ez a modul:

1. A MAC-cím tulajdonsággal rendelkező üzenetek keres.
1. Ha egy MAC-címet, ad hozzá egy másik tulajdonság IoT Hub eszköz kulccsal rendelkező az üzenetet. 
1. Elérhetővé teszi az üzenet a következő modulhoz a láncban.

A fejlesztői állít be egy MAC-címek és az IoT-központ identitások közötti leképezést a szimulált eszköz társítandó IoT Hub eszköz identitásokat. A fejlesztő a leképezés a modul konfigurációjának részeként manuálisan hozzáadja.

A MAC &lt; - &gt; IoT Hub azonosítója a modul **identitymap.c** a forráskód. 

### <a name="iot-hub-communication-module"></a>IoT Hub-kommunikációs modul

Az IoT-központ kommunikációs modul egy HTTPS-kapcsolat megnyitása az IoT-központ az átjárót. HTTPS szerepel a három protokollok, az IoT-központ tudja értelmezni. Ez a modul révén nem az egyes eszközök a kapcsolat megnyitásához az eszközök közötti kapcsolatok multiplexáló egy kapcsolaton keresztül. Ez a megközelítés lehetővé teszi, hogy egyetlen átjáró sok eszközök csatlakoztatásához. 

A példában ez a modul:

1. Időt vesz igénybe üzenetek az IoT-központ és az előző modul által hozzárendelt kulcstulajdonság eszköz. 
1. Az üzenet tartalma küld az IoT-központ a HTTPS protokoll használatával. 

Az IoT-központ kommunikációs modul **iothub.c** a forráskód.

## <a name="before-you-get-started"></a>A kezdés előtt

Mielőtt hozzáfogna, a következőket kell tennie:

* [Létrehoz egy IoT-központot] [ lnk-create-hub] az Azure-előfizetésben. Ez a minta forgatókönyv nevét a hub van szükség. Ha nincs fiókja, néhány perc alatt létrehozhat egy [ingyenes fiókot][lnk-free-trial].
* Két eszközök hozzáadása az IoT hub, és jegyezze fel az azonosítót és az eszköz kulcsok. Használhatja a [eszköz explorer] [ lnk-device-explorer] vagy [IOT hubbal-explorer] [ lnk-iothub-explorer] eszközök eszközök hozzáadása az IoT-központot, és azok kulcsait beolvasása.


<!-- Images -->
[1]: media/iot-hub-iot-edge-simulated-selector/image1.png
[2]: media/iot-hub-iot-edge-simulated-selector/image2.png

<!-- Links -->
[Szimulált eszközről felhőbe történő feltöltés mintája]: https://github.com/Azure/iot-edge/blob/master/samples/simulated_device_cloud_upload/README.md
[lnk-sdk]: https://github.com/Azure/iot-edge
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
[lnk-create-hub]: ../articles/iot-hub/iot-hub-create-through-portal.md