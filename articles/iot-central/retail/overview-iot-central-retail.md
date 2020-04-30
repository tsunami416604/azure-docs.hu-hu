---
title: Kiskereskedelmi megoldások kiépítése az Azure IoT Centralkal | Microsoft Docs
description: Ismerje meg, hogyan használhatók az Azure IoT Central alkalmazás-sablonok a csatlakoztatott logisztika, a digitális terjesztési központ, az áruházbeli elemzés, a feltételek figyelése, a pénztár, az intelligens leltár-kezelés és a kiskereskedelmi megoldások létrehozásához.
author: avneet723
ms.author: avneets
ms.date: 01/10/2020
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 9427b007bc047e2f01db2dad02e06bf0ab0f5dea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81000615"
---
# <a name="building-retail-solutions-with-azure-iot-central"></a>Kiskereskedelmi megoldások létrehozása az Azure IoT Centrallal



Az Azure IoT Central egy IoT, amely csökkenti a nagyvállalati szintű IoT-megoldások fejlesztésével, kezelésével és karbantartásával járó terheket és költségeket. Az Azure IoT Central kiválasztásával lehetősége van arra, hogy az Ön üzleti adatait IoT-adataival alakítsa át, és nem csupán egy összetett és folyamatosan fejlődő IoT-infrastruktúra karbantartására és frissítésére koncentrál.

Ez a cikk több, a kiskereskedelmi IoT Central alkalmazás sablonjait ismerteti. Megoldás-szerkesztőként használhatja ezeket a sablonokat olyan IoT-megoldások készítéséhez, amelyek optimalizálják az ellátási láncokat, javítják az ügyfelek számára az áruházbeli tapasztalatokat, és hatékonyabban nyomon követhetik a leltárt.

> [!div class="mx-imgBorder"]
> ![Az Azure IoT kiskereskedelmi áttekintése](./media/overview-iot-central-retail/retail-app-templates.png)

Az alábbi szakaszok ismertetik az alkalmazás-sablonok képességeit:

## <a name="connected-logistics"></a>Összekapcsolt logisztika

A globális logisztikai költségek várhatóan elérik a $10 600 000 000 000-es kiadást 2020-ben. A kiadások és a hajózási szolgáltatók nagy részének szállítása jelentős versenynyomást és kényszereket biztosít.

A IoT érzékelőkkel olyan környezeti körülményeket gyűjthet és figyelheti, mint például a hőmérséklet, a páratartalom, a dőlés, a sokk, a fény és a szállítás helye. A IoT érzékelőkből és eszközökből gyűjtött telemetria kombinálhatja más adatforrásokkal, például időjárási és forgalmi információkkal a felhőalapú üzleti intelligencia rendszerekben.

A csatlakoztatott logisztikai megoldás előnyei a következők:

* A szállítás figyelése valós idejű nyomkövetéssel és nyomon követéssel. 
* A szállítás integritásának valós idejű környezeti állapotának figyelése.
* A lopás, a veszteség vagy a szállítmányok sérülése elleni védelem.
* Geo-kerítés, útvonal-optimalizálás, flotta-felügyelet és a jármű-elemzés.
* Előrejelzés a beszállítások kiszámítható távozásához és érkezéséhez.

A következő képernyőképek az alkalmazás sablonjában lévő beépített irányítópultot mutatják be. Az irányítópult teljes mértékben testreszabható, hogy megfeleljen az adott megoldás követelményeinek:

> [!div class="mx-imgBorder"]
> ![Csatlakoztatott logisztikai irányítópult](./media/overview-iot-central-retail/connected-logistics-dashboard1.png)

> [!div class="mx-imgBorder"]
> ![Csatlakoztatott logisztikai irányítópult](./media/overview-iot-central-retail/connected-logistics-dashboard2.png)

További információért lásd: az [üzembe helyezés és az útmutató egy csatlakoztatott logisztikai alkalmazás sablonjának](./tutorial-iot-central-connected-logistics.md) létrehozásához.

## <a name="digital-distribution-center"></a>Digitális terjesztési központ

Mivel a gyártók és a kiskereskedők globális jelenlétet alkotnak, az ellátási láncok egyre összetettebbek. A fogyasztók mostantól nagy számú termék elérhetővé tételére számíthatnak, és ezek a termékek egy vagy két napon belül érkeznek. A terjesztési központoknak alkalmazkodnia kell ezekhez a trendekhez, és a meglévő eredménytelenség leküzdéséhez. 

Napjainkban a manuális munkaterhelések azt jelentik, hogy a kitárolási és csomagolási fiókok a Distribution Center költségeinek 55-65%-át használják. A manuális kivételezés és a csomagolás is általában lassabb, mint az automatizált rendszerek, és a munkaerő gyors ingadozása még nehezebbé teszi a szállítási kötetek teljesítését. Ez az idényjellegű ingadozás a személyzet magas szintű forgalmát eredményezi, és növelheti a költséges hibák valószínűségét.

A IoT-kompatibilis kamerákon alapuló megoldások a digitális visszajelzési hurok engedélyezésével képesek átalakítási előnyökre. A terjesztési központból származó adatok a gyakorlatban hasznosítható elemzésekhez vezetnek, amelyek viszont jobb adatmennyiséget eredményeznek.

A digitális terjesztési központ előnyei a következők:

* A kamerák megfigyelik az árukat a megérkezésük után, és áthelyezhetik azokat a továbbító rendszeren.
* Hibás termékek automatikus azonosítása.
* Hatékony megrendelés nyomon követése.
* Csökkentett költségek, hatékonyabb termelékenység és optimalizált használat.

Az alábbi képernyőfelvételen a beépített irányítópult látható az alkalmazás sablonjában. Az irányítópult teljes mértékben testreszabható, hogy megfeleljen az adott megoldás követelményeinek: 

> [!div class="mx-imgBorder"]
> ![Digitális terjesztési központ irányítópultja](./media/overview-iot-central-retail/digital-distribution-center-dashboard.png)

További információért lásd: a [Digital Distribution Center alkalmazás-sablon üzembe helyezése és](./tutorial-iot-central-digital-distribution-center.md) áttekintő oktatóanyaga.

## <a name="in-store-analytics---condition-monitoring"></a>Tárolt elemzési állapot figyelése

Számos kiskereskedő esetében az áruházban lévő környezeti feltételek kulcsfontosságú differenciált versenytársaik számára. A kereskedők kellemes feltételeket szeretnének fenntartani az üzleteik számára az ügyfelek számára.  

Megoldás-szerkesztőként használhatja a IoT Central in-store Analytics feltételeit figyelő alkalmazás sablonját, amellyel teljes körű megoldást hozhat létre. Az alkalmazás sablonja lehetővé teszi, hogy digitálisan kapcsolódjon egy kiskereskedelmi tárolási környezethez, és figyelje a különböző típusú érzékelő eszközök használatával. Ezek az érzékelők-eszközök olyan telemetria hoznak létre, amelyeket üzleti elemzési eredményekre válthatnak, így csökkenthetik az üzemeltetési költségeket, és nagyszerű élményt nyújtanak ügyfeleiknek.

Az alkalmazás sablonjának használata a következőhöz:

* Különböző IoT-érzékelők összekötése egy IoT Central alkalmazás-példánnyal.
* Az érzékelő hálózat, valamint a környezetben található átjárók állapotának monitorozása és felügyelete.
* Hozzon létre egyéni szabályokat az áruházban lévő környezeti feltételek köré, hogy riasztásokat indítson az áruház-kezelők számára.
* Alakítsa át az áruházban lévő környezeti körülményeket, és Ismerje meg, hogy a kiskereskedelmi áruház csapata az ügyfelek felhasználói élményének javítására használja fel.
* A kiskereskedelmi munkatársak számára hasznos és kellő időben elérhetővé teszi az összesített információk meglévő vagy új üzleti alkalmazásokba való exportálását.

Az alkalmazás sablonja tartalmazza az eszközök sablonjait, és szimulált eszközök használatával tölti fel az irányítópultot. 

Az alábbi képernyőfelvételen a beépített irányítópult látható az alkalmazás sablonjában. Az irányítópult teljes mértékben testreszabható, hogy megfeleljen az adott megoldás követelményeinek: 

> [!div class="mx-imgBorder"]
> ![Az áruházbeli elemzési feltétel figyelése](./media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png)

További információért lásd az [Azure-ban tárolt elemzési alkalmazás létrehozása az Azure IoT Central](./tutorial-in-store-analytics-create-app.md) oktatóanyagban című témakört.

## <a name="in-store-analytics---checkout"></a>Tárolt elemzések – pénztár

Egyes kiskereskedőknél az áruházban lévő fizetési élmény a versenytársak egyik kulcsfontosságú megkülönböztetője. A kiskereskedők zökkenőmentes fizetési lehetőséget szeretnének biztosítani az áruházban, hogy az ügyfelek visszatérjenek.  

Megoldás-szerkesztőként használhatja a IoT Central in-store Analytics Checkout-alkalmazás sablonját olyan megoldás létrehozásához, amely az áruház pénztári zónáján belüli elemzéseket biztosít a kiskereskedelmi munkatársaknak. Az érzékelők például adatokat biztosíthatnak a várólista hosszáról, valamint az egyes pénztárak átlagos várakozási idejéről.

Az alkalmazás sablonjának használata a következőhöz:

* Különböző IoT-érzékelők összekötése egy IoT Central alkalmazás-példánnyal.
* Az érzékelő hálózat, valamint a környezetben található átjárók állapotának monitorozása és felügyelete.
* Hozzon létre egyéni szabályokat a pénztári feltétel köré a tárolón belül a kereskedelmi személyzet riasztásának elindításához.
* Alakítsa át a pénztári feltételeket az áruházban, és tekintse át, hogy a kiskereskedelmi áruház csapata hogyan növelheti a felhasználói élményt.
* A kiskereskedelmi munkatársak számára hasznos és kellő időben elérhetővé teszi az összesített információk meglévő vagy új üzleti alkalmazásokba való exportálását.

Az alkalmazás sablonja tartalmazza az eszközök sablonjait, és szimulált eszközök használatával tölti fel az irányítópultot a sáv kihasználtsági adataival. 

Az alábbi képernyőfelvételen a beépített irányítópult látható az alkalmazás sablonjában. Az irányítópult teljes mértékben testreszabható, hogy megfeleljen az adott megoldás követelményeinek: 

> [!div class="mx-imgBorder"]
> ![Tárolt elemzési pénztár](./media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png)

További információért lásd az [Azure-ban tárolt elemzési alkalmazás létrehozása az Azure IoT Central](./tutorial-in-store-analytics-create-app.md) oktatóanyagban című témakört.

## <a name="smart-inventory-management"></a>Intelligens készletkezelés

A leltár az a kiskereskedelmi tulajdonú árucikkek készlete. A Készletkezelés kritikus fontosságú annak biztosítása érdekében, hogy a megfelelő termék megfelelő helyen legyen a megfelelő időben. A kereskedőnek el kell osztania a túl sok leltár tárolásával járó költségeket azzal a költségekkel, hogy az igények kielégítése érdekében ne legyen elegendő tétel a készletben.

A rádiófrekvenciás azonosító (RFID) címkékből, figyelőkből és kamerákból generált IoT-adatok lehetővé teszik a készletkezelési folyamatok fejlesztését. A IoT érzékelőkből és eszközökből gyűjtött telemetria kombinálhatja más adatforrásokkal, például időjárási és forgalmi információkkal a felhőalapú üzleti intelligencia rendszerekben.

Az intelligens leltár kezelésének előnyei a következők:

* Csökkentheti az elemek kialakulásának kockázatát, és biztosíthatja a kívánt ügyfél-szolgáltatási szintet. 
* Részletes elemzés és betekintés a leltár pontosságára közel valós időben.
* Eszközök, amelyekkel eldöntheti, hogy megfelelő mennyiségű leltárt szeretne-e tárolni az ügyfelek rendeléseinek kielégítése érdekében.

Ez az alkalmazási sablon az eszközök kapcsolatára koncentrál, valamint az RFID-és a Bluetooth-alapú, alacsony energiafogyasztású olvasó eszközök konfigurálására és felügyeletére.

Az alábbi képernyőfelvételen a beépített irányítópult látható az alkalmazás sablonjában. Az irányítópult teljes mértékben testreszabható, hogy megfeleljen az adott megoldás követelményeinek:

> [!div class="mx-imgBorder"]
> ![Intelligens leltár-kezelési irányítópult](./media/overview-iot-central-retail/smart-inventory-management-dashboard.png)

További információ: [üzembe helyezés és útmutató az intelligens leltár kezelési alkalmazás sablonjának](./tutorial-iot-central-smart-inventory-management.md) oktatóanyaga.

## <a name="micro-fulfillment-center"></a>Mikro-teljesítési központ

Az egyre versenyképesebb kiskereskedelmi környezetben a kiskereskedők folyamatosan nyomást gyakorolnak a kereslet és a teljesítés közötti szakadék lezárására. Az egyre növekvő fogyasztói kereslettel foglalkozó új tendencia az, hogy a leltárt a végfelhasználók és a meglátogatott üzletek közelében tárolják.

A IoT Central Micro-beteljesülés Center alkalmazás sablon lehetővé teszi a megoldás-építők számára a teljes mértékben automatizált teljesítési központok minden aspektusának figyelését és kezelését. A sablon szimulált feltételt figyelő érzékelőkkel és robot-szolgáltatókkal is rendelkezik a megoldás fejlesztési folyamatának felgyorsításához. Ezek az érzékelő eszközök olyan értelmes jeleket rögzítenek, amelyek az üzleti eredményekre alakíthatók, így a kiskereskedők csökkenthetik működési költségeiket, és tapasztalatokat hozhatnak létre ügyfeleiknek.

Az alkalmazás sablonja a következőket teszi lehetővé: 

- Zökkenőmentesen csatlakoztathatók különböző típusú IoT-érzékelők, például robotok vagy feltételt figyelő érzékelők egy IoT Central alkalmazás-példányhoz.
- Figyelheti és kezelheti az érzékelő hálózat állapotát, valamint a környezetben található bármely átjáró eszközét.
- A megfelelő riasztások elindításához hozzon létre egyéni szabályokat a környezeti feltételek köré a teljesítési központban.
- Alakítsa át a környezeti feltételeket a teljesítési központban a kiskereskedelmi tárház csapata által kihasználható elemzésekre.
- Exportálja az összesített betekintést meglévő vagy új üzleti alkalmazásokba a kiskereskedelmi személyzet tagjai számára.

Az alábbi képernyőfelvételen a beépített irányítópult látható az alkalmazás sablonjában. Az irányítópult teljes mértékben testreszabható, hogy megfeleljen az adott megoldás követelményeinek:

> [!div class="mx-imgBorder"]
> ![Micro-beteljesülés központja](./media/overview-iot-central-retail/MFC-Dashboard.png)

További információért lásd: [üzembe helyezés és útmutató a Micro-teljesítési központ alkalmazás sablonjának](./tutorial-micro-fulfillment-center.md) oktatóanyaga.

## <a name="next-steps"></a>További lépések

A kereskedelmi megoldások létrehozásának megkezdéséhez:

* Ismerkedjen meg az [Azure IoT Central in-store Analytics-alkalmazás létrehozása](./tutorial-in-store-analytics-create-app.md) című oktatóanyaggal, amely végigvezeti a megoldásnak az áruházbeli elemzési alkalmazás-sablonokkal való kiépítésének lépésein.
* [Üzembe helyezés és végigvezeti a csatlakoztatott logisztikai alkalmazás sablonján](./tutorial-iot-central-connected-logistics.md).
* [A Digital Distribution Center alkalmazás sablonjának üzembe helyezése és átjárása](./tutorial-iot-central-digital-distribution-center.md).
* [Helyezzen üzembe és járjon el egy intelligens leltár-kezelési alkalmazás sablonján](./tutorial-iot-central-smart-inventory-management.md).
* [Üzembe helyezheti és végigvezeti a Micro-teljesítési központ alkalmazás sablonján](./tutorial-micro-fulfillment-center.md).
* További információ a IoT Centralről a [IoT Central áttekintésében](../preview/overview-iot-central.md).
