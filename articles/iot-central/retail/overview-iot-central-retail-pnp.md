---
title: Kiskereskedelmi megoldások létrehozása az Azure IoT Central szolgáltatással | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre összekapcsolt logisztikai, digitális elosztóközpont, üzleten belüli elemzés, állapotfigyelés, fizetés, intelligens készletkezelés és kiskereskedelmi megoldások azure IoT Central alkalmazássablonjai.
author: avneet723
ms.author: avneets
ms.date: 01/10/2020
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 808a17fcf303c6eb72f11f5379fa172302dc782a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77059852"
---
# <a name="building-retail-solutions-with-azure-iot-central"></a>Kiskereskedelmi megoldások létrehozása az Azure IoT Centrallal



Az Azure IoT Central egy IoT-alkalmazásplatform, amely csökkenti a nagyvállalati szintű IoT-megoldások fejlesztésével, kezelésével és karbantartásával járó terheket és költségeket. Ha az Azure IoT Central használatával szeretne építeni, azzal a lehetőséggel koncentrálhatja idejét, pénzét és energiáját az IoT-adatokkal való átalakítására, ahelyett, hogy csak egy összetett és folyamatosan fejlődő IoT-infrastruktúrát tartna fenn és frissítene.

Ez a cikk számos kiskereskedelmi specifikus IoT Central alkalmazássablont ismertet. Megoldásszerkesztőként ezekkel a sablonokkal olyan IoT-megoldásokat hozhat létre, amelyek optimalizálják az ellátási láncokat, javítják az ügyfelek bolti élményét, és hatékonyabban követik nyomon a készletet.

> [!div class="mx-imgBorder"]
> ![Az Azure IoT kiskereskedelmi adatainak áttekintése](./media/overview-iot-central-retail/retail-app-templates.png)

Az alábbi szakaszok az alkalmazássablonok képességeit ismertetik:

## <a name="connected-logistics"></a>Összekapcsolt logisztika

A globális logisztikai kiadások 2020-ban várhatóan elérik a 10,6 billió dollárt. Az áruk szállítása teszi ki a kiadások többségét, és a hajózási szolgáltatók erős versenynyomás és korlátok alatt állnak.

Az IoT-érzékelők segítségével összegyűjtheti és felügyelheti a környezeti feltételeket, például a hőmérsékletet, a páratartalmat, a dőlést, a sokkot, a fényt és a szállítmány helyét. Az IoT-érzékelőkből és -eszközökből gyűjtött telemetriai adatokat kombinálhatja más adatforrásokkal, például az időjárás és a forgalmi információk felhőalapú üzletiintelligencia-rendszerekben.

Az összekapcsolt logisztikai megoldás előnyei a következők:

* Szállítmányfigyelés valós idejű nyomon követéssel és nyomon követéssel. 
* A szállítás integritása valós idejű környezeti állapotfigyeléssel.
* Biztonság a lopás, elvesztés vagy a szállítás károsodása ellen.
* Geo-kerítés, útvonaloptimalizálás, flottamenedzsment és járműelemzés.
* A szállítmányok kiszámítható indulásának és érkezésének előrejelzése.

Az alábbi képernyőképek az alkalmazássablonban a beépített irányítópultot mutatják be. Az irányítópult teljes mértékben testreszabható, hogy megfeleljen az adott megoldáskövetelményeinek:

> [!div class="mx-imgBorder"]
> ![Csatlakoztatott logisztikai irányítópult](./media/overview-iot-central-retail/connected-logistics-dashboard1.png)

> [!div class="mx-imgBorder"]
> ![Csatlakoztatott logisztikai irányítópult](./media/overview-iot-central-retail/connected-logistics-dashboard2.png)

További információ: [A telepítés és a séta egy csatlakoztatott logisztikai alkalmazássablon](./tutorial-iot-central-connected-logistics-pnp.md) oktatóanyag.

## <a name="digital-distribution-center"></a>Digitális terjesztési központ

Ahogy a gyártók és a kiskereskedők világszerte jelen vannak, ellátási láncaik terjeszkednek és összetettebbé válnak. A fogyasztók most arra számítanak, hogy a termékek nagy választéka áll majd rendelkezésre, és hogy ezek az áruk a vásárlástól számított egy-két napon belül megérkezzenek. Az elosztóközpontoknak alkalmazkodniuk kell ezekhez a trendekhez, miközben le kell küzdeni a meglévő hatékonysági hiányosságokat. 

Ma a fizikai munkára való támaszkodás azt jelenti, hogy a kitárolás és a csomagolás az elosztóközpont költségeinek 55-65%-át teszi ki. A kézi kitárolás és csomagolás is jellemzően lassabb, mint az automatizált rendszerek, és a gyorsan ingadozó személyzeti igények még nehezebbé teszik a szállítási mennyiségek teljesítését. Ez a szezonális ingadozás a személyzet magas fluktuációját eredményezi, és növeli a költséges hibák valószínűségét.

Az IoT-kompatibilis kamerákon alapuló megoldások átalakítási előnyökkel járhatnak a digitális visszacsatolási hurok engedélyezésével. Az elosztóközpont-on belüli adatok olyan működésbe hozható elemzésekhez vezetnek, amelyek viszont jobb adatokat eredményeznek.

A digitális elosztóközpont előnyei a következők:

* A kamerák figyelik az árukat, amint megérkeznek és áthaladnak a szállítószalag-rendszeren.
* A hibás áruk automatikus azonosítása.
* Hatékony rendeléskövetés.
* Csökkentett költségek, nagyobb termelékenység és optimalizált használat.

A következő képernyőképen látható a beépített irányítópult az alkalmazássablonban. Az irányítópult teljes mértékben testreszabható, hogy megfeleljen az adott megoldáskövetelményeinek: 

> [!div class="mx-imgBorder"]
> ![Digitális elosztóközpont irányítópultja](./media/overview-iot-central-retail/digital-distribution-center-dashboard.png)

További információ: [A telepítés és a séta a digitális elosztóközpont alkalmazássablon](./tutorial-iot-central-digital-distribution-center-pnp.md) oktatóanyag.

## <a name="in-store-analytics---condition-monitoring"></a>Bolti elemzés – állapotfigyelés

Sok kiskereskedő számára az üzleteikben a környezeti feltételek kulcsfontosságú különbséget jelentenek versenytársaiktól. A kiskereskedők kellemes körülményeket szeretnének fenntartani az üzleteikben, ügyfeleik érdekében.  

Megoldásszerkesztőként használhatja az IoT Central store analytics állapotfigyelési feltételfigyelő alkalmazássablonját egy teljes körű megoldás létrehozásához. Az alkalmazássablon lehetővé teszi, hogy digitálisan csatlakozzon és figyeljen egy kiskereskedelmi környezetet különböző típusú érzékelőeszközök használatával. Ezek az érzékelőeszközök telemetriai adatokat hoznak létre, amelyek üzleti elemzéssé alakíthatók, és segítik a kiskereskedőt a működési költségek csökkentésében és az ügyfelek számára nagyszerű élmény kialakításában.

Az alkalmazássablon használatával:

* Számos IoT-érzékelőt csatlakoztathat egy IoT-központi alkalmazáspéldányhoz.
* Az érzékelőhálózat, valamint a környezetben lévő átjáróeszközök állapotának figyelése és kezelése.
* Hozzon létre egyéni szabályokat az üzleten belüli környezeti feltételek körül, hogy riasztásokat indítson az üzletvezetők számára.
* Alakítsa át az üzleten belüli környezeti feltételeket olyan elemzési adatokká, amelyeket a kiskereskedelmi áruház csapata felhasználhat az ügyfélélmény javítására.
* Exportálja az összesített elemzéseket a meglévő vagy új üzleti alkalmazásokba, hogy hasznos és időszerű információkat nyújtson a kiskereskedelmi munkatársaknak.

Az alkalmazássablon eszközsablonokat hoz létre, és szimulált eszközök készletét használja az irányítópult feltöltéséhez. 

A következő képernyőképen látható a beépített irányítópult az alkalmazássablonban. Az irányítópult teljes mértékben testreszabható, hogy megfeleljen az adott megoldáskövetelményeinek: 

> [!div class="mx-imgBorder"]
> ![Az üzleten belüli elemzési állapot figyelése](./media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png)

További információkért tekintse meg a [Hozzon létre egy in-store elemzési alkalmazás az Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md) oktatóanyag.

## <a name="in-store-analytics---checkout"></a>Bolti analitika - pénztár

Egyes kiskereskedők, a pénztár tapasztalat belül tárolja a legfontosabb különbséga a versenytársak. A kiskereskedők zökkenőmentes fizetést szeretnének biztosítani az üzleteikben, hogy ösztönözzék az ügyfeleket a visszatérésre.  

Megoldásszerkesztőként az IoT Central üzleten belüli elemzési pénztáralkalmazássablonjával olyan megoldást hozhat létre, amely az üzlet pénztári zónájából származó elemzéseket biztosít a kiskereskedelmi munkatársaknak. Az érzékelők például információt nyújthatnak a várólista hosszáról és az egyes kijelentkezési sávok átlagos várakozási idejéről.

Az alkalmazássablon használatával:

* Számos IoT-érzékelőt csatlakoztathat egy IoT-központi alkalmazáspéldányhoz.
* Az érzékelőhálózat, valamint a környezetben lévő átjáróeszközök állapotának figyelése és kezelése.
* Hozzon létre egyéni szabályokat az üzleten belüli kivételi feltétel körül, hogy riasztásokat indítson a kiskereskedelmi munkatársak számára.
* Az áruházon belüli kijelentkezési feltételeket olyan elemzési adatokká alakíthatja, amelyeket a kiskereskedelmi áruház csapata az ügyfélélmény javítása érdekében használhat.
* Exportálja az összesített elemzéseket a meglévő vagy új üzleti alkalmazásokba, hogy hasznos és időszerű információkat nyújtson a kiskereskedelmi munkatársaknak.

Az alkalmazássablon eszközsablonokkészletét használja, és szimulált eszközök készletét használja az irányítópult sávkihasználtsági adatokkal való feltöltéséhez. 

A következő képernyőképen látható a beépített irányítópult az alkalmazássablonban. Az irányítópult teljes mértékben testreszabható, hogy megfeleljen az adott megoldáskövetelményeinek: 

> [!div class="mx-imgBorder"]
> ![Üzleten belüli analytics-fizetés](./media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png)

További információkért tekintse meg a [Hozzon létre egy in-store elemzési alkalmazás az Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md) oktatóanyag.

## <a name="smart-inventory-management"></a>Intelligens készletkezelés

A készlet a kiskereskedő által birtokolt áruk készlete. A készletkezelés kritikus fontosságú annak biztosításához, hogy a megfelelő termék a megfelelő helyen és a megfelelő időben legyen. A kiskereskedőnek egyensúlyba kell hoznia a túl sok készlet tárolásának költségeit azzal a költséggel, hogy nincs elegendő készleten lévő cikk a kereslet kielégítésére.

A rádiófrekvenciás azonosítási (RFID) címkékből, jelzőkből és kamerákból generált IoT-adatok lehetőséget nyújtanak a készletkezelési folyamatok javítására. Az IoT-érzékelőkből és -eszközökből gyűjtött telemetriai adatokat kombinálhatja más adatforrásokkal, például az időjárás és a forgalmi információk felhőalapú üzletiintelligencia-rendszerekben.

Az intelligens készletkezelés előnyei a következők:

* A cikkek készleten létének kockázatának csökkentése és a kívánt ügyfélszolgálati szint biztosítása. 
* Részletes elemzés és a készlet pontosságának elemzése közel valós időben.
* Eszközök, amelyek segítenek eldönteni, hogy a vevői rendelések teljesítéséhez milyen készletet kell tárolni.

Ez az alkalmazássablon az eszközkapcsolatra, valamint az RFID és a Bluetooth alacsony energiafelhasználású (BLE) olvasóeszközök konfigurálására és kezelésére összpontosít.

A következő képernyőképen látható a beépített irányítópult az alkalmazássablonban. Az irányítópult teljes mértékben testreszabható, hogy megfeleljen az adott megoldáskövetelményeinek:

> [!div class="mx-imgBorder"]
> ![Intelligens készletkezelési irányítópult](./media/overview-iot-central-retail/smart-inventory-management-dashboard.png)

További információ: [A telepítés és az intelligens készletkezelési alkalmazássablon oktatóanyaga.](./tutorial-iot-central-smart-inventory-management-pnp.md)

## <a name="micro-fulfillment-center"></a>Mikro-teljesítési központ

Az egyre versenyképesebb kiskereskedelmi környezetben a kiskereskedők folyamatosan szembesülnek nyomással, hogy megzárják a kereslet és a teljesítés közötti szakadékot. Egy új trend, hogy alakult ki, hogy foglalkozzon a növekvő fogyasztói kereslet, hogy a ház leltár közelében a végső vásárlók és a boltokban látogatnak.

Az IoT Central mikro-teljesítési központ alkalmazássablon lehetővé teszi a megoldáskészítők számára, hogy a teljesen automatizált teljesítési központjaik minden aspektusát felügyeljék és kezeljék. A sablon egy sor szimulált állapotfigyelő érzékelőt és robothordozót tartalmaz a megoldásfejlesztési folyamat felgyorsítása érdekében. Ezek az érzékelőeszközök olyan értelmes jeleket rögzítenek, amelyek üzleti elemzéssé alakíthatók, lehetővé téve a kiskereskedők számára, hogy csökkentsék működési költségeiket, és élményeket teremtsenek ügyfeleik számára.

Az alkalmazássablon lehetővé teszi a következőket: 

- Zökkenőmentesen csatlakoztathat különböző Típusú IoT-érzékelőket, például robotokat vagy állapotfigyelő érzékelőket egy IoT Central alkalmazáspéldányhoz.
- Az érzékelőhálózat és a környezetben lévő átjáróeszközök állapotának figyelése és kezelése.
- Hozzon létre egyéni szabályokat a környezeti feltételek körül egy teljesítési központban a megfelelő riasztások aktiválásához.
- Alakítsa át a teljesítési központon belüli környezeti feltételeket olyan elemzési adatokká, amelyeket a kiskereskedelmi raktárcsapata használhat ki.
- Exportálja az összesített betekintést a meglévő vagy új üzleti alkalmazásokba a kiskereskedelmi alkalmazottak javára.

A következő képernyőképen látható a beépített irányítópult az alkalmazássablonban. Az irányítópult teljes mértékben testreszabható, hogy megfeleljen az adott megoldáskövetelményeinek:

> [!div class="mx-imgBorder"]
> ![Mikro-teljesítési központ](./media/overview-iot-central-retail/MFC-Dashboard.png)

További információ: [A telepítés és a séta a mikro-teljesítési központ alkalmazássablon](./tutorial-micro-fulfillment-center-pnp.md) oktatóanyag.

## <a name="next-steps"></a>További lépések

Kiskereskedelmi megoldás létrehozásának első lépései:

* Első lépések: [Hozzon létre egy in-store elemzési alkalmazást az Azure IoT Central oktatóanyagban,](./tutorial-in-store-analytics-create-app-pnp.md) amely bemutatja, hogyan hozhat létre megoldást az egyik üzleten belüli elemzési alkalmazássablonnal.
* [Telepítsen és léptsen végig egy csatlakoztatott logisztikai alkalmazássablonon.](./tutorial-iot-central-connected-logistics-pnp.md)
* [Digitális elosztóközpont-alkalmazássablon telepítése és végigvezeti azt.](./tutorial-iot-central-digital-distribution-center-pnp.md)
* [Intelligens készletkezelési alkalmazássablon üzembe helyezése és végigvezeti azt.](./tutorial-iot-central-smart-inventory-management-pnp.md)
* [Telepítse és sétáljon végig a mikro-teljesítési központ alkalmazássablonon.](./tutorial-micro-fulfillment-center-pnp.md)
* Az IoT Central ról az [IoT Central áttekintése című témakörben olvashat bővebben.](../preview/overview-iot-central.md)
