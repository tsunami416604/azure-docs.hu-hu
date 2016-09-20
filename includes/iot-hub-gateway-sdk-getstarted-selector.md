> [AZURE.SELECTOR]
- [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md)
- [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-get-started.md)

Ez a cikk a [Hello World mintakód][lnk-helloworld-sample] részletes bemutatóját tartalmazza, amely képet ad az [Azure IoT átjáró-SDK][lnk-gateway-sdk] architektúrájáról. A minta az átjáró-SDK használatával egy egyszerű átjárót hoz létre, amely öt másodpercenként egy „hello world” üzenetet naplóz egy fájlba.

A bemutató tartalma:

- **Fogalmak:** Az átjáró-SDK-val létrehozott átjárókat alkotó összetevők fogalmi áttekintése.  
- **Hello World mintaarchitektúra:** A Hello World mintára alkalmazott fogalmakat írja le, valamint az összetevők illeszkedését.
- **Minta létrehozása:** A minta megvalósításához szükséges lépések.
- **Minta futtatása:** A minta futtatásához szükséges lépések. 
- **Tipikus kimenet:** Példa a minta futtatásakor várható kimenetre.
- **Kódrészletek:** Kódrészletek gyűjteménye, amely bemutatja, hogy a Hello World minta hogyan implementálja a kulcsfontosságú átjáró-összetevőket.

## Átjáró-SDK-fogalmak

Mielőtt vizsgálni kezdené a mintakódot, vagy létrehozná saját éles átjáróját az átjáró-SDK használatával, meg kell ismerkednie az SDK architektúrája mögött rejlő alapfogalmakkal.

### Modulok

Az Azure IoT átjáró-SDK használata esetén *modulok* létrehozásával és összeállításával készíthet átjárókat. A modulok *üzenetek* használatával cserélnek adatokat egymással. Az egyes modulok üzeneteket fogadnak, végrehajtanak valamilyen műveletet rajtuk, esetleg átalakítják őket új üzenetekké, majd közzéteszik azokat más modulok számára feldolgozásra. Egyes modulok esetleg kizárólag új üzeneteket állítanak elő, és nem dolgoznak fel beérkező üzeneteket. Modulok láncba rendezésével egy adatfeldolgozó folyamat hozható létre, amelynek minden pontján valamely modul valamilyen módon átalakítja az adatokat.

![][1]
 
Az SDK a következőket tartalmazza:

- Előre megírt modulok, amelyek gyakori átjárófunkciókat hajtanak végre.
- Felületek, amelyek segítségével a fejlesztők egyedi modulokat hozhatnak létre.
- A modulok üzembe helyezéséhez és futtatásához szükséges infrastruktúra.

Az SDK egy olyan absztrakciós réteget biztosít, amelynek segítségével különféle operációs rendszereken és platformokon futtatható átjárók készíthetők.

![][2]

### Üzenetek

Ha úgy képzeljük el, hogy a modulok egymásnak küldözgetnek üzeneteket, könnyen megragadható az átjáró működése mögött rejlő elv, azonban ez nem pontosan így történik. A modulok egy üzenetbusz segítségével kommunikálnak egymással: üzeneteket tesznek közzé a buszon, az pedig elküldi az üzeneteket a buszhoz csatlakoztatott összes modulnak.

A modulok a **MessageBus_Publish** függvény használatával teszik közzé az üzeneteket az üzenetbuszon. Az üzenetbusz az üzeneteket az egyes moduloknak a visszahívási függvény használatával továbbítja. Az üzenetek kulcs/érték tulajdonságokból és tartalmakból állnak, amelyek memóriablokként vannak továbbítva.

![][3]

Mindegyik modul saját feladata az üzenetek szűrése, mivel az üzenetbusz közzétételi mechanizmusa mindegyik üzenetet továbbítja az összes csatlakoztatott modulnak. Az egyes moduloknak azonban csak a nekik szánt üzenetekkel kell foglalkozniuk. Az üzenetek szűrése hozza voltaképp létre az üzenetfolyamatot. Az egyes modulok általában az üzenettulajdonságok használatával szűrik ki a fogadott üzenetek közül azokat, amelyeket fel kell dolgozniuk.

## Hello World mintaarchitektúra

A Hello World minta az előző szakaszban leírt fogalmakat mutatja be. A Hello World egy olyan átjárót hoz létre, amely egy két modulból álló folyamatot tartalmaz:

-   A *hello world* modul egy üzenetet hoz létre öt másodpercenként, és továbbítja azt a naplózó modulnak.
-   A *naplózó* modul a fogadott üzeneteket egy fájlba írja.

![][4]

Az előző szakaszban foglaltak szerint a Hello World modul nem közvetlenül továbbítja öt másodpercenként az üzeneteket a naplózó modulnak. Ehelyett az üzenetbuszon teszi közzé öt másodpercenként az üzenetet.

A naplózó modul az üzenetbuszról fogadja az üzeneteket, és megvizsgálja azok tulajdonságait egy szűrővel. Ha a naplózó modul azt állapítja meg, hogy fel kell dolgoznia egy adott üzenetet, az üzenet tartalmát egy fájlba írja.

A naplózó modul kizárólag fogadja az üzeneteket az üzenetbuszról, soha nem tesz közzé új üzeneteket a buszon.

![][5]

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


<!--HONumber=sep16_HO1-->


