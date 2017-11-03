> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-get-started.md)
> 
> 

Ez a cikk a [Hello World mintakód][lnk-helloworld-sample] részletes bemutatóját tartalmazza, amely képet ad az [Azure IoT Edge][lnk-iot-edge] architektúrájáról. A példa az Azure IoT Edge használatával egy egyszerű átjárót hoz létre, amely öt másodpercenként egy „hello world” üzenetet naplóz egy fájlba.

A bemutató tartalma:

* **Hello World – mintaarchitektúra**: ismerteti, hogyan [Azure IoT peremhálózati architekturális fogalmak] [ lnk-edge-concepts] alkalmazni a Hello World PéldaAlkalmazás, és hogyan az összetevők működnek együtt.
* **Minta létrehozása:** A minta megvalósításához szükséges lépések.
* **Minta futtatása:** A minta futtatásához szükséges lépések. 
* **Tipikus kimenet:** Példa a minta futtatásakor várható kimenetre.
* **Kód kódtöredékek**: hogyan a Hello World PéldaAlkalmazás megvalósítja legfontosabb IoT peremhálózati átjáró összetevők megjelenítendő kódtöredékek gyűjteménye.


## <a name="hello-world-sample-architecture"></a>Hello World mintaarchitektúra
A Hello World minta az előző szakaszban leírt fogalmakat mutatja be. A Hello World PéldaAlkalmazás valósít meg olyan IoT peremhálózati átjáróval, amely rendelkezik egy folyamat két IoT peremhálózati modulok áll:

* A *hello world* modul egy üzenetet hoz létre öt másodpercenként, és továbbítja azt a naplózó modulnak.
* A *naplózó* modul a fogadott üzeneteket egy fájlba írja.

![Példa az Azure IoT Edge segítségével összeállított Hello World- architektúrára][4]

Az előző szakaszban foglaltak szerint a Hello World modul nem közvetlenül továbbítja öt másodpercenként az üzeneteket a naplózó modulnak. Ehelyett a közvetítőn teszi közzé öt másodpercenként az üzenetet.

A naplózó modul fogadja az üzenetet a közvetítőtől, foglalkozik vele, és az üzenet tartalmát egy fájlba írja.

A naplózó modul kizárólag fogadja az üzeneteket a közvetítőtől, soha nem tesz közzé új üzeneteket a közvetítőn.

![Az Azure IoT Edge moduljai közötti üzenek irányítása a közvetítő által][5]

A fenti ábrán a Hello World PéldaAlkalmazás és a relatív elérési utakat a forrásfájlokat, amelyek megvalósítják a minta különböző részeit architektúráját mutatja be a [tárház][lnk-iot-edge]. A kód saját elképzelései, vagy a kódrészleteket használja ebben a cikkben egy útmutató.

<!-- Images -->
[4]: media/iot-hub-iot-edge-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-iot-edge-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/iot-edge/tree/master/samples/hello_world
[lnk-iot-edge]: https://github.com/Azure/iot-edge
[lnk-edge-concepts]: ../articles/iot-hub/iot-hub-iot-edge-overview.md