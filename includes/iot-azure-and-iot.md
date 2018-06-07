---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/25/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: f27dbf20eab5866ac3061d888c17e970a1d197f7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34666755"
---
# <a name="azure-and-the-internet-of-things"></a>Azure és az eszközök internetes hálózata

Üdvözöljük a Microsoft Azure-ban és az eszközök internetes hálózatában (Internet of Things, IoT). Ez a cikk a felhőalapú IoT-megoldások gyakori jellemzőit ismerteti. Az IoT-megoldásokhoz biztonságos, kétirányú kommunikációra van szükség olyan eszközök között, amelyek száma akár a több milliót is elérheti, valamint egy olyan háttérrendszerre, amely például automatizált, prediktív elemzések elvégzésével nyújt betekintést az eszköz-felhő eseménystreambe.

## <a name="iot-solution-architecture"></a>Az IoT-megoldásarchitektúra

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

A megjelenítési és üzleti kapcsolati réteg lehetővé teszi a végfelhasználók számára, hogy kapcsolatba lépjenek az IoT-megoldással és az eszközökkel. Segítségével a felhasználók megtekinthetik és elemezhetik az eszközeikről összegyűjtött adatokat. Ezek irányítópultokon vagy BI-jelentések formájában is megtekinthetők, amelyek az előzmény- és a közel valós idejű adatokat egyaránt megjelenítik. A kezelő például ellenőrizheti egy adott szivattyútelep állapotát, és megtekintheti a rendszer által kiadott riasztásokat. Ez a réteg emellett lehetővé teszi az IoT-megoldás háttérrendszerének integrációját egy létező üzleti alkalmazással, hogy be lehessen vonni az üzleti vagy munkafolyamatokba. A prediktív karbantartási megoldásgyorsító például integrálható egy ütemezési rendszerbe, amely kihív egy mérnököt a szivattyútelephez, ha megállapítja, hogy valamelyik szivattyú karbantartásra szorul.

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-iot-hub]: ../articles/iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: ../articles/iot-accelerators/iot-accelerators-what-are-solution-accelerators.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT solution accelerators]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
