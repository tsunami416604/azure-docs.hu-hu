---
title: Azure eszközök internetes hálózata (IoT, Internet of Things) – bevezetés
description: Az Azure IoT és a kapcsolódó szolgáltatások és technológiák áttekintése.
author: BryanLa
manager: timlt
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 05/18/2018
ms.author: bryanla
ms.openlocfilehash: ed96181606e2db4102aa609973ade9ecbfde6c90
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187274"
---
# <a name="introduction-to-azure-and-the-internet-of-things"></a>Az Azure és az eszközök internetes hálózatának bemutatása

Az Azure IoT technológiák és megoldások három területét foglalja magában – a megoldásokat, a platformszolgáltatásokat és a peremhálózatot, amelyek mind azzal a céllal lettek kifejlesztve, hogy megkönnyítsék az IoT-alkalmazások teljes körű fejlesztését. Ez a cikk a felhőalapú IoT-megoldások gyakori jellemzőinek ismertetésével indul, majd áttekintést nyújt arról, hogy az Azure IoT milyen válaszokkal szolgál az IoT-projektek kihívásaira, és miért érdemes fontolóra venni az Azure IoT bevezetését.

## <a name="iot-solution-architecture"></a>Az IoT-megoldásarchitektúra

Az IoT-megoldásokhoz biztonságos, kétirányú kommunikációra van szükség olyan eszközök között, amelyek száma akár a több milliót is elérheti, valamint egy olyan háttérrendszerre, amely például automatizált, prediktív elemzések elvégzésével nyújt betekintést az eszköz-felhő eseménystreambe. 

Az alábbi ábrán egy tipikus IoT-megoldásarchitektúra fő elemei láthatók. Az ábra nem tartalmaz olyan megvalósítási részleteket, mint a használt Azure-szolgáltatások vagy az eszközök operációs rendszerei. Ebben az architektúrában az IoT-eszközök begyűjtik az adatokat, majd továbbítják azokat egy felhőátjáróhoz. A felhőátjáró elérhetővé teszi az adatokat más háttérszolgáltatások számára a feldolgozáshoz. Ezek a háttérszolgáltatások a következőknek továbbíthatnak adatokat:

* Más üzletági alkalmazásoknak.
* Felhasználóknak, egy irányítópulton vagy más megjelenítő eszközön keresztül.

![Az IoT-megoldásarchitektúra][img-solution-architecture]

> [!NOTE]
> Az IoT-architektúra részletes ismertetéséhez tekintse át a következő dokumentumot: [Microsoft Azure IoT Reference Architecture][lnk-refarch] (Microsoft Azure IoT-referenciaarchitektúra).

### <a name="device-connectivity"></a>Eszközkapcsolatok

Az IoT-megoldásarchitektúrákban az eszközök általában telemetriát küldenek a felhőbe tárolás és feldolgozás céljából. Egy prediktív karbantartási forgatókönyvben például a megoldás háttérrendszere az érzékelők adatstreamének felhasználásával megállapíthatja, hogy egy adott szivattyú mikor igényel karbantartást. Az eszközök emellett a felhővégpontokból érkező üzenetek olvasásával fogadhatnak a felhőből az eszközre érkező üzeneteket, és válaszolhatnak is azokra. Ugyanebben a példában a megoldás háttérrendszere üzeneteket küldhet a szivattyútelep többi szivattyújának, hogy azok elkezdjék az áramlás átirányítását a karbantartás megkezdése előtt. Ez az eljárás biztosítja, hogy a karbantartó mérnök megérkezésekor azonnal munkához láthasson.

Az IoT-megoldások esetében gyakran az eszközök biztonságos és megbízható csatlakoztatása jelenti a legnagyobb kihívást. Ez azért van, mert az IoT-eszközök más jellemzőkkel rendelkeznek, mint a korábban megszokott ügyfelek, például a böngészők vagy a mobilalkalmazások. Pontosabban, az IoT-eszközök:

* Általában beágyazott, emberi beavatkozást nem igénylő rendszerek (a telefonokkal ellentétben).
* Távoli helyeken is üzembe helyezhetők, ahol a fizikai hozzáférés drága lenne.
* Előfordulhat, hogy csak a megoldás háttérrendszerén keresztül érhetők el. Az eszközzel más módon nem lehet kapcsolatba lépni.
* Áramellátásuk és feldolgozási erőforrásaik korlátozottak lehetnek.
* A hálózati kapcsolat időszakos, lassú vagy drága lehet.
* Saját fejlesztésű, egyedi vagy iparág-specifikus alkalmazás-protokollokra lehet szükség.
* Számos népszerű hardver- és szoftverplatform használatával létrehozhatók.

Az előző korlátozások mellett minden egyes IoT-megoldásnak méretezhetőnek, biztonságosnak és megbízhatónak kell lennie.

A kommunikációs protokolltól és a hálózati rendelkezésre állástól függően egy eszköz közvetlenül vagy egy köztes átjárón keresztül kommunikálhat a felhővel. Az IoT-architektúrák gyakran mindkét kommunikációs mintát használják.

### <a name="data-processing-and-analytics"></a>Adatfeldolgozás és -elemzés

A modern IoT-megoldásokban az adatfeldolgozás a felhőben vagy az eszközoldalon is történhet. Az eszközoldali feldolgozást *peremhálózati számítástechnikának* nevezik. Az adatfeldolgozás helye például a következő tényezőktől függ:

* Hálózati korlátozások. Ha az eszközök és a felhő közötti sávszélesség korlátozott, érdemes megnövelni a peremhálózati feldolgozás mennyiségét.
* Válaszidő. Ha egy eszközön közel valós időben kell műveleteket végezni, érdemes lehet magán az eszközön feldolgozni a választ. Például, ha vészhelyzet esetén le kell állítani egy robotkart.
* Szabályozási környezet. Bizonyos adatokat nem lehet elküldeni a felhőbe.

Az adatfeldolgozás általában a peremhálózaton és a felhőben is az alábbi képességek kombinációjából áll:

* Az eszközöktől érkező telemetriák fogadása nagy mennyiségben, valamint az adatfeldolgozás és -tárolás módjának meghatározása.
* A telemetria elemzése annak megállapításához, hogy az adatok valós idejűek-e vagy utólagosak.
* Parancsok küldése egy adott eszköznek a felhőből vagy egy átjáróeszközről.

Egy IoT-felhő háttérrendszerének emellett a következőket kell biztosítania:

* Eszközregisztrációs képességeket, amelyek a következőket teszik lehetővé:
    * Eszközök üzembe helyezése.
    * Annak szabályozása, hogy melyik eszköz csatlakozhat az infrastruktúrához.
* Eszközfelügyeletet az eszközök állapotának szabályozásához és a tevékenységeik megfigyeléséhez.

Egy prediktív karbantartási forgatókönyvben például a felhő háttérrendszere eltárolja a korábbi telemetriai adatokat. A megoldás ezeket az adatokat használja a lehetséges rendellenes viselkedés azonosításához az adott szivattyúknál, mielőtt az komoly problémát okozna. Az adatelemzés segítségével azonosítani tudja, hogy megelőző megoldásként vissza kell küldeni egy parancsot az eszköznek, hogy az hajtson végre egy korrekciós műveletet. A folyamat létrehoz egy automatizált visszajelzési hurkot az eszköz és a felhő között, amely jelentősen növeli a megoldás hatékonyságát.

### <a name="presentation-and-business-connectivity"></a>Megjelenítés és üzleti kapcsolatok

A megjelenítési és üzleti kapcsolati réteg lehetővé teszi a végfelhasználók számára, hogy kapcsolatba lépjenek az IoT-megoldással és az eszközökkel. Segítségével a felhasználók megtekinthetik és elemezhetik az eszközeikről összegyűjtött adatokat. Ezek irányítópultokon vagy BI-jelentések formájában is megtekinthetők, amelyek az előzmény- és a közel valós idejű adatokat egyaránt megjelenítik. A kezelő például ellenőrizheti egy adott szivattyútelep állapotát, és megtekintheti a rendszer által kiadott riasztásokat. Ez a réteg emellett lehetővé teszi az IoT-megoldás háttérrendszerének integrációját egy létező üzleti alkalmazással, hogy be lehessen vonni az üzleti vagy munkafolyamatokba. A prediktív karbantartási megoldás például integrálható egy ütemezési rendszerbe, amely kihív egy mérnököt a szivattyútelephez, ha megállapítja, hogy valamelyik szivattyú karbantartásra szorul.

## <a name="why-azure-iot"></a>Milyen előnyöket kínál az Azure IoT?

Az Azure IoT csökkenti az IoT-projektek összetettségét, továbbá megoldásokat kínál a különféle kihívásokra, például a biztonság, az infrastruktúra-kompatibilitás vagy az IoT-megoldás méretezése terén. Ezt az alábbiak révén éri el:

**Rugalmas** <br>
Haladjon gyorsabban az IoT világa felé
* Méretezés: Kezdje kicsiben, majd növekedjen tetszőleges méretre bárhol és mindenhol – eszközök milliói és több terabájt adat a világ legtöbb régiójában.

* Nyílt: Használja, amije van, vagy készüljön fel korszerűbb rendszerekkel a jövőre bármilyen eszköz, szoftver vagy szolgáltatás csatlakoztatásával.

* Hibrid: IoT-megoldását a peremhálózaton, a felhőben vagy a kettő között bárhol üzembe helyezheti, így igényeinek megfelelően építkezhet.

* Ütemezés: Gyorsabban végezheti az üzembe helyezést és gyorsabban juttathatja piacra a termékeit, megőrizve ezáltal versenyelőnyét a megoldásgyorsítók és az innováció tempója terén piacvezető IoT-szolgáltatóval.

**Átfogó** <br>
Biztosítsa vállalkozása lendületét

* Teljes: A Microsoft az egyetlen IoT-megoldásszolgáltató, amely egy olyan teljes platformmal rendelkezik, amelynek szolgáltatásai a különféle eszközöktől a felhőn keresztül a big datáig, a fejlett adatelemzésig és a felügyelt szolgáltatásokig terjednek.

* Partner a sikerhez: Aknázza ki a világ legnagyobb partner-ökoszisztémája nyújtotta lehetőségeket, és keltse új életre üzleti és informatikai folyamatait a legkülönfélébb iparágakban és a világ bármely táján.

* Adatalapú: Az IoT az adatokról szól, ezért a legjobb IoT-megoldások egyesítik magukban az összes szükséges eszközt az adatok tárolásához, értelmezéséhez, átalakításához, elemzéséhez és a megfelelő felhasználóknak a megfelelő helyen és a megfelelő időben való megjelenítéséhez.

* Eszközközpontú: A Microsoft IoT segítségével bármit csatlakoztathat, a régi berendezésektől kezdve egészen a hitelesített hardvereszközök széles választékáig, sőt, akár saját eszközöket is készíthet a peremhálózati, mobil és beágyazott rendszerekben.

**Biztonságos** <br>
Megoldás az IoT legnagyobb kihívására – a biztonságra

* Hatékonyság: A Microsoft IoT segítségével elképzeléseit a technológiával, a bevált gyakorlatokkal és a képességekkel összehangolva találhat megoldást az IoT legnehezebb kihívására – a biztonságra.

* Cselekvés: Védje meg IoT-adatait és kezelje a kockázatokat identitás- és hozzáférés-felügyeleti, a kockázatok elleni védelmi és információbiztonsági, valamint biztonságkezelési megoldásokkal.

* Nyugalom: Biztosítsa a bizalmas adatok védelmét a különböző eszközökön, szoftverekben, alkalmazásokban, felhőalapú szolgáltatásokban és a helyszíni környezetekben.

* Megfelelőség: A Microsoft mindig is piacvezető volt az olyan biztonsági követelmények kialakításában, amelyek az IoT-eszközökre, -adatokra és -szolgáltatásokra vonatkozó nemzetközi és iparági szabványok széles körének megfelelnek.

## <a name="next-steps"></a>További lépések

Fedezze fel a technológiák és megoldások következő területeit, vagy tekintse át az Azure IoT-szolgáltatások listáját a baloldali tartalomjegyzékben.

<ul class="panelContent cardsF">  
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Megoldások</h3>
                        <a href="/azure/iot-suite">IoT-megoldásgyorsítók</a><br/>
                        <a href="/azure/iot-central">IoT-központ</a>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Platformszolgáltatások</h3>
                        <a href="/azure/iot-hub">IoT Hub</a><br/>
                        <a href="/azure/iot-dps">IoT Hub eszközregisztrációs szolgáltatás</a><br/>
                        <a href="/azure/azure-maps">Maps</a><br/>
                        <a href="/azure/time-series-insights">Time Series Insights</a>
                    </div>
                </div>
            </div>
        </div>
    </li>  
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Edge</h3>
                        <a href="/azure/iot-edge">IoT Edge</a><br/>
                        <a href="/azure/iot-edge/how-iot-edge-works">Mi az az IoT Edge?</a>
                    </div>
                </div>
            </div>
        </div>
    </li>      
</ul>

[img-paas-saas-technologies-solutions]: media/index/paas-saas-technologies-solutions.png
[img-solution-architecture]: ./media/iot-introduction/iot-reference-architecture.png
[img-dashboard]: ./media/iot-introduction/iot-suite.png

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-iot-central-land]: https://docs.microsoft.com/microsoft-iot-central/
[lnk-iot-dps-land]: /azure/iot-dps/index.yml
[lnk-iot-edge-land]: /azure/iot-edge/index.yml
[lnk-iot-hub-land]: /azure/iot-hub/index.md
[lnk-iot-maps-land]: /azure/maps/index.yml
[lnk-iot-sa-land]: ../iot-accelerators/index.yml
[lnk-iot-tsi-land]: /azure/time-series-insights/index.yml

[lnk-iot-hub]: ../iot-hub/about-iot-hub.md
[lnk-iot-sa]: ../iot-accelerators/about-iot-accelerators.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[lnk-protocol-gateway]:  ../iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: https://aka.ms/iotrefarchitecture


