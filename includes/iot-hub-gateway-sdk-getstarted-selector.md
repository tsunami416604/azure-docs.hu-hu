> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-get-started.md)
> 
> 

Ez a cikk a [Hello World mintakód][lnk-helloworld-sample] részletes bemutatóját tartalmazza, amely képet ad az [Azure IoT Gateway SDK][lnk-gateway-sdk] architektúrájáról. A példa az Azure IoT Gateway SDK használatával egy egyszerű átjárót hoz létre, amely öt másodpercenként egy „hello world” üzenetet naplóz egy fájlba.

A bemutató tartalma:

* **Fogalmak:** Az IoT Gateway SDK-val létrehozott átjárókat alkotó összetevők fogalmi áttekintése.  
* **Hello World mintaarchitektúra:** A Hello World mintára alkalmazott fogalmakat írja le, valamint az összetevők illeszkedését.
* **Minta létrehozása:** A minta megvalósításához szükséges lépések.
* **Minta futtatása:** A minta futtatásához szükséges lépések. 
* **Tipikus kimenet:** Példa a minta futtatásakor várható kimenetre.
* **Kódrészletek:** Kódrészletek gyűjteménye, amely bemutatja, hogy a Hello World minta hogyan implementálja a kulcsfontosságú átjáró-összetevőket.

## <a name="azure-iot-gateway-sdk-concepts"></a>Az Azure IoT Gateway SDK – alapelvek
Mielőtt vizsgálni kezdené a mintakódot, vagy létrehozná saját éles átjáróját az IoT Gateway SDK használatával, meg kell ismerkednie az SDK architektúrája mögött rejlő alapfogalmakkal.

### <a name="modules"></a>Modulok
Az Azure IoT átjáró-SDK használata esetén *modulok* létrehozásával és összeállításával készíthet átjárókat. A modulok *üzenetek* használatával cserélnek adatokat egymással. Az egyes modulok üzeneteket fogadnak, végrehajtanak valamilyen műveletet rajtuk, esetleg átalakítják őket új üzenetekké, majd közzéteszik azokat más modulok számára feldolgozásra. Egyes modulok esetleg kizárólag új üzeneteket állítanak elő, és nem dolgoznak fel beérkező üzeneteket. Modulok láncba rendezésével egy adatfeldolgozó folyamat hozható létre, amelynek minden pontján valamely modul valamilyen módon átalakítja az adatokat.

![Az átjáró moduljainak láncba rendezése az Azure IoT átjáró SDK-val][1]

Az SDK a következőket tartalmazza:

* Előre megírt modulok, amelyek gyakori átjárófunkciókat hajtanak végre.
* Felületek, amelyek segítségével a fejlesztők egyedi modulokat hozhatnak létre.
* A modulok üzembe helyezéséhez és futtatásához szükséges infrastruktúra.

Az SDK egy olyan absztrakciós réteget biztosít, amelynek segítségével különféle operációs rendszereken és platformokon futtatható átjárók készíthetők.

![Az Azure IoT Gateway SDK absztrakciós rétege][2]

### <a name="messages"></a>Üzenetek
Ha úgy képzeljük el, hogy a modulok egymásnak küldözgetnek üzeneteket, könnyen megragadható az átjáró működése mögött rejlő elv, azonban ez nem pontosan így történik. A modulok egy közvetítő segítségével kommunikálnak egymással: üzeneteket tesznek közzé a közvetítőn (busz, közzététel és előfizetés vagy bármely más üzenetkezelési minta), majd hagyják, hogy a közvetítő továbbítsa az üzenetet a hozzá csatlakoztatott moduloknak.

A modul a **Broker_Publish** függvény használatával teszi közzé az üzeneteket a közvetítőn. A közvetítő egy visszahívási függvény használatával továbbítja az üzeneteket az egyes moduloknak. Az üzenetek kulcs/érték tulajdonságokból és tartalmakból állnak, amelyek memóriablokként vannak továbbítva.

![A közvetítő szerepe az Azure IoT átjáró-SDK-ban][3]

### <a name="message-routing-and-filtering"></a>Üzenettovábbítás és -szűrés
Az üzeneteket kétféle módon lehet a megfelelő modulokhoz irányítani. Átadható egy hivatkozáskészlet a közvetítő számára, hogy megismerje az egyes modulok forrását és fogadóját, vagy a modul szűrheti az üzenet tulajdonságait. Az egyes moduloknak azonban csak akkor kell foglalkozniuk egy üzenettel, ha azt nekik szánták. A hivatkozások és az üzenetszűrés hozza létre végeredményben az üzenetfolyamatot.

## <a name="hello-world-sample-architecture"></a>Hello World mintaarchitektúra
A Hello World minta az előző szakaszban leírt fogalmakat mutatja be. A Hello World egy olyan átjárót hoz létre, amely egy két modulból álló folyamatot tartalmaz:

* A *hello world* modul egy üzenetet hoz létre öt másodpercenként, és továbbítja azt a naplózó modulnak.
* A *naplózó* modul a fogadott üzeneteket egy fájlba írja.

![Példa az Azure IoT átjáró-SDK-val összeállított Hello World- architektúrára][4]

Az előző szakaszban foglaltak szerint a Hello World modul nem közvetlenül továbbítja öt másodpercenként az üzeneteket a naplózó modulnak. Ehelyett a közvetítőn teszi közzé öt másodpercenként az üzenetet.

A naplózó modul fogadja az üzenetet a közvetítőtől, foglalkozik vele, és az üzenet tartalmát egy fájlba írja.

A naplózó modul kizárólag fogadja az üzeneteket a közvetítőtől, soha nem tesz közzé új üzeneteket a közvetítőn.

![Az Azure IoT átjáró-SDK moduljai közötti üzenek irányítása a közvetítő által][5]

A fenti ábrán a Hello World mintaarchitektúrája látható, valamint a minta egyes részeit implementáló forrásfájlok relatív elérési útjai a [tárházban][lnk-gateway-sdk]. Ismerkedjen meg a kóddal, vagy használja az alábbi kódrészleteket segítségképp.

<!-- Images -->
[1]: media/iot-hub-gateway-sdk-getstarted-selector/modules.png
[2]: media/iot-hub-gateway-sdk-getstarted-selector/modules_2.png
[3]: media/iot-hub-gateway-sdk-getstarted-selector/messages_1.png
[4]: media/iot-hub-gateway-sdk-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-gateway-sdk-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/hello_world
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

<!--HONumber=Feb17_HO2-->


