
# <a name="azure-and-internet-of-things"></a>Az Azure és az eszközök internetes hálózata

Üdvözöljük a Microsoft Azure-ban és az eszközök internetes hálózatában (Internet of Things, IoT). Ez a cikk egy olyan IoT-megoldás gyakori jellemzőit mutatja be, amelyet Ön is telepíthet az Azure-szolgáltatások használatával. Az IoT-megoldásokhoz biztonságos, kétirányú kommunikációra van szükség nagyszámú eszköz között, valamint egy háttérrendszerre. Ez a háttérrendszer automatizált, prediktív elemzések elvégzésével nyújthat betekintést az eszköz-felhő eseménystreambe.

Az [Azure IoT Hub][lnk-iot-hub] kulcsfontosságú építőelem bármely IoT-megoldás Azure-szolgáltatásokkal történő megvalósítása során. Az IoT Hub egy teljesen felügyelt szolgáltatás, amely megbízható és biztonságos kétirányú kommunikációt tesz lehetővé több millió IoT-eszköz között, valamint megoldást biztosít a háttérrendszer kialakításához. 

Az [Azure IoT Suite][lnk-iot-suite] programcsomag biztosítja a jelen architektúra teljes körű megvalósítását bizonyos IoT-forgatókönyvek esetén. Példa:

* A *távoli megfigyelési* megoldás lehetővé teszi az eszközök (például automaták) állapotának felügyeletét.
* A *prediktív karbantartás* segítségével előre felkészülhet az eszközök (például távoli szivattyútelepek szivattyúi) karbantartási igényeire, így elkerülheti a nem tervezett leállást.
* A *csatlakoztatott gyár* megoldással csatlakoztathatja és megfigyelheti az ipari eszközeit.

## <a name="iot-solution-architecture"></a>Az IoT-megoldásarchitektúra

Az alábbi ábrán egy tipikus IoT-megoldásarchitektúra látható. Az ábrán nem látható egyetlen konkrét Azure-szolgáltatás neve sem, ehelyett egy általános IoT-megoldásarchitektúra kulcselemeit ismerteti. Ebben az architektúrában az IoT-eszközök begyűjtik az adatokat, majd továbbítják azokat egy felhőátjáróhoz. A felhőátjáró elérhetővé teszi az adatokat más háttérszolgáltatások számára a feldolgozáshoz. A megoldás háttérrendszere továbbítja az adatokat egy irányítópulton vagy jelentésen keresztül további üzleti alkalmazások vagy felhasználók felé.

![Az IoT-megoldásarchitektúra][img-solution-architecture]

> [!NOTE]
> Az IoT-architektúra részletes ismertetéséhez tekintse át a következő dokumentumot: [Microsoft Azure IoT Reference Architecture][lnk-refarch] (Microsoft Azure IoT-referenciaarchitektúra).

### <a name="device-connectivity"></a>Eszközkapcsolatok

Ebben az IoT-megoldásban az eszközök telemetriát (pl. szivattyútelepek érzékelőinek adatai) küldenek a felhővégpontnak tárolás és feldolgozás céljából. Egy prediktív karbantartási forgatókönyvben a megoldás háttérrendszere az érzékelők adatstreamének felhasználásával megállapíthatja, hogy egy adott szivattyú mikor igényel karbantartást. Az eszközök emellett a felhővégpontokból érkező üzenetek olvasásával fogadhatnak a felhőből az eszközre érkező üzeneteket, és válaszolhatnak is azokra. A prediktív karbantartási forgatókönyvben például a megoldás háttérrendszere üzeneteket küldhet a szivattyútelep többi szivattyújának, hogy azok elkezdjék az áramlás átirányítását a karbantartás megkezdése előtt. Ez az eljárás biztosítja, hogy a karbantartó mérnök azonnal hozzákezdhessen a probléma elhárításához.

Az IoT-projektek számára az egyik legnagyobb kihívást az jelenti, hogyan lehet megbízható és biztonságos módon csatlakoztatni az eszközöket a megoldás háttérrendszeréhez. Az IoT-eszközök más jellemzőkkel rendelkeznek, mint a korábban megszokott ügyfelek, például böngészők vagy mobilalkalmazások. Az IoT-eszközök jellemzői:

* Általában beágyazott, emberi beavatkozást nem igénylő rendszerek.
* Távoli helyeken is üzembe helyezhetők, ahol a fizikai hozzáférés drága lenne.
* Előfordulhat, hogy csak a megoldás háttérrendszerén keresztül érhetők el. Az eszközzel más módon nem lehet kapcsolatba lépni.
* Áramellátásuk és feldolgozási erőforrásaik korlátozottak lehetnek.
* A hálózati kapcsolat időszakos, lassú vagy drága lehet.
* Saját fejlesztésű, egyedi vagy iparág-specifikus alkalmazás-protokollokra lehet szükség.
* Számos népszerű hardver- és szoftverplatform használatával létrehozhatók.

A fenti követelmények mellett minden egyes IoT-megoldásnak megfelelő méretezhetőséget, biztonságot és megbízhatóságot kell biztosítania. Az ebből következő kapcsolati követelmények megvalósítása rendkívül nehéz és időigényes az olyan hagyományos technológiák segítségével, mint például a webes tárolók vagy üzenetkezelő közvetítők. Az Azure IoT Hub és az Azure IoT eszközoldali SDK-k megkönnyítik a fenti követelményeknek megfelelő megoldások megvalósítását.

Az eszközök közvetlenül kommunikálhatnak a felhőátjárók végpontjaival. Ha az eszköz nem képes a felhőátjáró által támogatott kommunikációs protokollok használatára, akkor csatlakozhat egy köztes átjáróhoz. Ilyen például az [Azure IoT Hub protokoll-átjáró][lnk-protocol-gateway], amely lefordítja a protokollokat, ha az eszközök nem képesek az IoT Hub által támogatott protokollok használatára.

### <a name="data-processing-and-analytics"></a>Adatfeldolgozás és -elemzés

A felhőben az IoT-megoldás háttérrendszerében megy végbe az adatfeldolgozás nagy része. Az IoT-megoldás háttérrendszere:

* Nagy mennyiségben fogadja az eszközöktől érkező telemetriákat, és meghatározza az adatok feldolgozásának és tárolásának módját. 
* Lehetővé teheti a parancsok küldését a felhőből az egyes eszközökre.
* Eszközregisztrációs képességeket biztosít, amelyek lehetővé teszik az eszközök kiosztását, valamint annak szabályozását, hogy melyik eszköz csatlakozhat az infrastruktúrához.
* Lehetővé teszi az eszközök állapotának nyomon követését és tevékenységeik megfigyelését.

A prediktív karbantartási forgatókönyvben a megoldás háttérrendszere eltárolja a korábbi telemetriai adatokat. A megoldás háttérrendszere a tárolt adatok alapján képes azonosítani azokat a mintákat, amelyek az egyes szivattyúk karbantartásának szükségességét jelzik.

Az IoT-megoldások tartalmazhatnak automatikus visszajelzési hurkokat is. A megoldás háttérrendszerének elemzőmodulja például képes megállapítani a telemetriai adatokból, hogy egy adott eszköz hőmérséklete a normális üzemi szint fölött van-e. A megoldás ezt követően képes parancsot adni az eszköznek a korrekciós műveletek végrehajtására.

### <a name="presentation-and-business-connectivity"></a>Megjelenítés és üzleti kapcsolatok

A megjelenítési és üzleti kapcsolati réteg lehetővé teszi a végfelhasználók számára, hogy kapcsolatba lépjenek az IoT-megoldással és az eszközökkel. Segítségével a felhasználók megtekinthetik és elemezhetik az eszközeikről összegyűjtött adatokat. Ezek irányítópultokon vagy jelentések formájában is megtekinthetők, amelyek az előzmény- és a közel valós idejű adatokat egyaránt megjelenítik. A kezelő például ellenőrizheti egy adott szivattyútelep állapotát, és megtekintheti a rendszer által kiadott riasztásokat. Ez a réteg emellett lehetővé teszi az IoT-megoldás háttérrendszerének integrációját egy létező üzleti alkalmazással, hogy be lehessen vonni az üzleti vagy munkafolyamatokba. A prediktív karbantartási megoldás például integrálható egy ütemezési rendszerbe, amely kihív egy mérnököt a szivattyútelephez, ha a megoldás megállapítja, hogy valamelyik szivattyú karbantartásra szorul.

![Az IoT-megoldás irányítópultja][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-iot-hub]: ../articles/iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: ../articles/iot-suite/iot-suite-overview.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
