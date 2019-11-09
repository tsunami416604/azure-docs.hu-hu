---
title: Kiskereskedelmi megoldások kiépítése az Azure IoT Centralkal | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre csatlakoztatott logisztikai, digitális terjesztési központot, tárolt elemzési feltételt, valamint a pénztári és az intelligens leltározást, valamint a kiskereskedelmi megoldásokat az Azure IoT Central alkalmazás-sablonok használatával.
author: KishorIoT
ms.author: nandab
ms.date: 10/22/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 7f82e2a539c968d0dde44cbc2ed411710054c290
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890707"
---
# <a name="building-retail-solutions-with-azure-iot-central"></a>Kiskereskedelmi megoldások létrehozása az Azure IoT Centrallal

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Az Azure IoT Central egy eszközök internetes hálózata (IoT) alkalmazás-platform a megoldás-építők számára, amely leegyszerűsíti a méretezhető alkalmazások kiépítésével kapcsolatos kihívásokat &. Ebben a cikkben több, a IoT Centralon belüli, kiskereskedelemmel kapcsolatos alkalmazás-sablont fogunk kiemelni. A megoldás-építők kihasználhatják a közzétett sablonokat, hogy IoT megoldásokat hozzanak létre az ellátási lánc optimalizálásához, az ügyfelek által nyújtott élmény javításához és a leltár hatékonyabb nyomon követéséhez.

> [!div class="mx-imgBorder"]
> Az Azure IoT kiskereskedelmi ![áttekintése](./media/overview-iot-central-retail/retail-app-templates.png)


## <a name="what-is-connected-logistics-solution"></a>Mi az a csatlakoztatott logisztikai megoldás?
A globális logisztikai költségek várhatóan a 2020-as $10,6 TRN-t érik el, amely az iparág legnagyobb GDP-je. Az áru szállítása a legnagyobb (70%) a logisztikai költségek teljes száma. A szállítási szolgáltatók intenzív versenyfeltételek és korlátozások alatt állnak. a 3PL-szolgáltatók folyamatosan csökkenő időkereteket és növekvő kompenzációs költségeket biztosítanak. A logisztikát tovább terheli a geopolitikai, szélsőséges éghajlati események és bűnözés által jelentett kockázatok. 

A IoT-érzékelők segítségével összegyűjthetjük & a környezeti körülményeket, például a hőmérsékletet, a Páratartalomot, a megdőlést, a sokkot, a fényt és a küldemények helyét a GPS-n keresztül a több-modális átalakítás, például a levegő, a víz és a terep használatával. Az érzékelőkből, eszközökből, időjárási &okból gyűjtött adatok integrálható a felhőalapú üzleti intelligencia rendszerekkel. A kapcsolódó logisztikai megoldás előnyei a következők,
* Átvitel továbbítása a valós idejű nyomkövetési & követésével 
* A szállítás integritása valós idejű környezeti feltételek figyelése & hideg lánc
* Biztonság a lopás, a veszteség vagy a szállítás sérülése miatt
* Geo-kerítés, útvonal-optimalizálás, flotta-kezelés. Gépjármű-elemzés
* Előrejelzés & kiszámíthatóság az indulási & beérkezéssel 

### <a name="out-of-box-experience"></a>Beépített élmény
A partnerek kihasználhatják a teljes körű csatlakoztatott logisztikai megoldások kiépítéséhez szükséges sablonokat, & az előtérben bekövetkező előnyöket. Ez a közzétett sablon az eszközök kapcsolatára, a konfiguráció & a IoT Central eszközeinek felügyeletére koncentrál. 

> [!div class="mx-imgBorder"]
> ![csatlakoztatott logisztikai irányítópult](./media/overview-iot-central-retail/connected-logistics-dashboard1.png)

> [!div class="mx-imgBorder"]
> ![csatlakoztatott logisztikai irányítópult](./media/overview-iot-central-retail/connected-logistics-dashboard2.png)

Vegye figyelembe, hogy a fenti irányítópult egy példa, és teljes mértékben testreszabhatja az alkalmazást, hogy illeszkedjen a kívánt használati esethez.

Ismerkedjen meg a teljes [körű oktatóanyaggal](./tutorial-iot-central-connected-logistics-pnp.md) , amely végigvezeti Önt a kapcsolódó logisztikai megoldási sablonok egyikét kihasználó megoldás létrehozásán.



## <a name="what-is-digital-distribution-center-solution"></a>Mi az a Digital Distribution Center megoldás?
Ahogy egyre több gyártó és kiskereskedő hozza létre a globális jelenlétet, az ellátási láncok egyre összetettebbek lesznek, mint eddig. A terjesztési központok elsődleges kihívásnak számítanak. A terjesztési központ/raktárak megérzik az e-kereskedelmi nyomás terhét. A fogyasztók mostantól jelentős választékot várhatnak a termékek elérhetővé tételéhez, és az ilyen termékek a vásárlást követő két napon belül érkeznek. A terjesztési központoknak alkalmazkodnia kell ezekhez a trendekhez, és a meglévő eredménytelenség leküzdéséhez. 

Napjainkban a manuális munkaterhelésre való túlterhelés a terjesztési központ költségeinek 55-65%-a. Bár rossz, hogy a manuális munka lelassítja a terjesztési központot, gyorsan ingadozó munkaerő-szükségleteket (a munkatársainak 10X-vel való felgyorsítását), még nehezebbé teszi a szállítási kötetek teljesítését. Ez a szezonális ingadozás a nagy forgalmú és a hibák valószínűségét, valamint a költséges újraműködést eredményezi.
A IoT-kompatibilis kamerákon alapuló megoldások a digitális visszajelzési hurok engedélyezésével képesek átalakítási előnyökre. Az adatoknak a terjesztési központban való beáramlása olyan gyakorlatban hasznosítható elemzésekhez vezet, amelyek viszont jobb adatmennyiséget eredményeznek.

Az előnyök a következők. 
* A kamerák megfigyelik az árut, amint megérkeznek, és áthaladnak a továbbító rendszeren
* Hibás termékek azonosítása és javítási célú küldése
* A megrendelések hatékony nyomon követése
* Csökkentse a költségeket, a jobb hatékonyságot & a maximális kihasználtságot

### <a name="out-of-box-experience"></a>Beépített élmény
A partnerek kihasználhatják ezt az alkalmazás-sablont a digitális terjesztési központ létrehozásához, hogy gyakorlatban hasznosítható információkhoz jussanak & a fentiekben ismertetett előnyökkel. A közzétett sablon az eszköz csatlakozási konfigurációjának & a kamera és a peremhálózati eszközök IoT Central-ban való felügyeletére koncentrál. 

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center irányítópultja](./media/overview-iot-central-retail/digital-distribution-center-dashboard.png)

Vegye figyelembe, hogy a fenti irányítópult egy példa, és teljes mértékben testreszabhatja az alkalmazást, hogy illeszkedjen a kívánt használati esethez.

Ismerkedjen meg a teljes [körű oktatóanyaggal](./tutorial-iot-central-digital-distribution-center-pnp.md) , amely végigvezeti a Digital Distribution Center-sablonok egyikét használó megoldás létrehozásán.



## <a name="what-is-in-store-analytics-condition-monitoring"></a>Mi az a áruházbeli elemzési feltételek figyelése?
Napjaink versengő környezetében a kiskereskedők új lehetőségeket keresnek arra, hogy egyedi vagy különleges szolgáltatásokat nyújtsanak az ügyfeleknek, hogy a fizikai áruházakon keresztül irányítsák a forgalmat. Számos kiskereskedő tudomásul veszi, hogy az áruházban lévő környezeti feltételek mennyire fontosak a versenytársakkal való különbségtételhez. A kereskedőknek biztosítaniuk kell, hogy mindig kellemes feltételeket tartsanak az áruházban, hogy kényelmes felhasználói élményt nyújtsanak ügyfeleiknek.  

A IoT Centralon belüli, tárolóban tárolt elemzési alkalmazás sablonja biztosítja a megoldás-szerkesztőt egy olyan vászon használatával, amely teljes körű megoldást kínál a végpontok közötti megoldás létrehozására. Az alkalmazás sablonja lehetővé teszi számukra, hogy digitálisan csatlakozzanak és figyeljenek egy kiskereskedelmi tárolási környezetet különféle érzékelő eszközök használatával. Ezek az érzékelők-eszközök olyan értelmes jeleket rögzítenek, amelyek üzleti eredményekre alakíthatók, így a kiskereskedők csökkenthetik működési költségeiket, és az ügyfelek által kedvelt tapasztalatokat hozhatnak létre.

Az alkalmazás sablonja a következőket teszi lehetővé:

*  Zökkenőmentesen csatlakozhat számos IoT-érzékelőhöz egy IoT Central alkalmazás-példányhoz.
*  Az érzékelő hálózat állapotának monitorozása és kezelése, valamint a környezetben található átjáró-eszközök.
*  A megfelelő riasztások elindításához hozzon létre egyéni szabályokat a környezeti feltételek köré a tárolón belül.
*  Alakítsa át az áruházban lévő környezeti feltételeket a kiskereskedelmi áruház csapata által kihasználható elemzésekre.
* Exportálja az összesített betekintést meglévő vagy új üzleti alkalmazásokba, amelyek felhatalmazzák a kiskereskedelmi személyzet tagjait.

### <a name="out-of-box-experience"></a>Beépített élmény
Az alkalmazás sablonja tartalmaz egy eszköz-sablonokat és egy operátori élményt a dobozból. Szimulált eszközök készletét használja az irányítópult-elemek feltöltéséhez. Miután üzembe helyezett egy IoT Central alkalmazást az [áruházbeli elemzések feltételének figyelése](https://aka.ms/conditiontemplate) alkalmazás sablonnal, a lent látható módon az alapértelmezett alkalmazás-irányítópulton fog megjelenni. 

> [!div class="mx-imgBorder"]
> a ![Store Analytics-feltételek figyelése](./media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png)

Vegye figyelembe, hogy a fenti irányítópult egy példa, és teljes mértékben testreszabhatja az alkalmazást, hogy illeszkedjen a kívánt használati esethez. 

Ismerkedjen meg a teljes [körű oktatóanyaggal](./tutorial-in-store-analytics-create-app-pnp.md) , amely végigvezeti egy olyan megoldás létrehozásán, amely az egyik, az áruházbeli elemzési feltételt figyelő sablonokat hasznosítja.



## <a name="what-is-in-store-analytics-checkout"></a>Mi az a Store Analytics-beli pénztár?
Az egyre versenyképesebb környezetekben a modern kiskereskedők folyamatosan egyre nagyobb terheléssel szembesülnek, így az ügyfelek elvárásaihoz képest nagyobb mennyiségű tapasztalatot biztosítanak. Míg számos kiskereskedő megkezdte a technológia üzembe helyezését ennek a szükségletnek a kielégítése érdekében, egy nagy mértékben nem felfigyelt térség a pénztári élmény.

A IoT Centralon belüli in-store Analytics Checkout alkalmazási sablon lehetővé teszi a megoldás-építők számára, hogy olyan tapasztalatokat hozzanak létre, amelyek a kiskereskedelmi munkatársakat a tárolójuk pénztári zónájában értelmes elemzéssel teszik lehetővé. Szimulált eszközök készletét használja a kiskereskedelmi tárolóban lévő összes fizetési sáv kihasználtsági állapotának meghatározásához. Az érzékelők segítségével rögzítheti a személyek számát, valamint az egyes fizetési sávok átlagos várakozási idejét.

A sablon segítségével a megoldás-szerkesztő felgyorsíthatja a piacra jutási terveket olyan alapkonfigurációt IoT megoldás biztosításával, amely lehetővé teszi a következőket: 

* Zökkenőmentesen csatlakozhat számos IoT-érzékelőhöz egy IoT Central alkalmazás-példányhoz.
* Az érzékelő hálózat állapotának monitorozása és kezelése, valamint a környezetben található átjáró-eszközök.
* A megfelelő riasztások elindításához hozzon létre egyéni szabályokat a pénztári feltétel köré a tárolón belül.
* Alakítsa át a pénztári feltételeket a boltban a kiskereskedelmi áruház csapata által kihasználható elemzésekre.
* Exportálja az összesített betekintést meglévő vagy új üzleti alkalmazásokba, amelyek felhatalmazzák a kiskereskedelmi személyzet tagjait.

### <a name="out-of-box-experience"></a>Beépített élmény
Az alkalmazás sablonja tartalmaz egy eszköz-sablonokat és egy operátori élményt a dobozból. Szimulált eszközök készletét használja az irányítópult-elemek feltöltéséhez. Miután telepített egy IoT Central alkalmazást az [áruházbeli Analytics Checkout-](https://aka.ms/checkouttemplate) alkalmazás sablon használatával, az alapértelmezett alkalmazás-irányítópulton fog megjelenni az alábbi ábrán látható módon. 

> [!div class="mx-imgBorder"]
> ![áruházbeli elemzési pénztár](./media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png)

Vegye figyelembe, hogy a fenti irányítópult egy példa, és teljes mértékben testreszabhatja az alkalmazást, hogy illeszkedjen a kívánt használati esethez. 


Ismerkedjen meg a teljes [körű oktatóanyaggal](./tutorial-in-store-analytics-create-app-pnp.md) , amely végigvezeti egy olyan megoldás létrehozásán, amely az egyik áruházbeli elemzési pénztári sablont hasznosítja.


## <a name="what-is-smart-inventory-management-solution"></a>Mi az az intelligens leltár-kezelési megoldás?
A "leltár" a kiskereskedelmi tulajdonában lévő termékek készlete. Minden kiskereskedő számára leltárt kell készíteni az ellátási és a logisztikai idő kezeléséhez. A leltár vitathatatlanul a legértékesebb erőforrás, amelyet minden kiskereskedő el kell kereskedni. A mai omnichannel világában a Készletkezelés kritikus követelmény annak biztosítása érdekében, hogy a megfelelő termék megfelelő helyen legyen a megfelelő időben. A túl sok vagy túl kevés leltár tárolása megsértheti a kiskereskedelmi üzletét. Minden évben a forgalmazók a készletkezelési képességek hiánya miatt elveszítik a bevétel 8-10%-át.

A rádiófrekvenciás azonosítás (RFID) által engedélyezett IoT-adatok, a Figyelőknek & kamera a nagy kihívást jelentő nagyméretű kihívás megoldásának lehetősége. A IoT-jelekben rejlő kapcsolat és valós idejű elemzések a kiskereskedelmi leltárhoz kapcsolódóan a game-Changer váltak.  Az érzékelőkből, eszközökből, időjárási &okból gyűjtött adatok integrálható a felhőalapú üzleti intelligencia rendszerekkel.  
Az intelligens leltár-kezelési előnyök a következők, 
* Védelmet biztosít a szervezetnek az adatkészletek ellen, és gondoskodik a kívánt ügyfélszolgálati szintről. 
* Részletes elemzés a leltározási pontosságot & közel valós időben
* Döntse el a megfelelő mennyiségű leltárt, amely elegendő az ügyfél rendeléseihez

### <a name="out-of-box-experience"></a>Beépített élmény
A partnerek kihasználhatják a teljes körű intelligens készletkezelési megoldások fejlesztéséhez szükséges sablonokat, & a kihangsúlyozatlan előnyök. Ez a közzétett sablon az eszközök kapcsolatára, a & RFID & Bluetooth alacsony energiafogyasztású (kapcsolatos) olvasóinak az IoT Central-ban való kezelésére koncentrál. 

> [!div class="mx-imgBorder"]
> ![intelligens leltár felügyeleti irányítópultja](./media/overview-iot-central-retail/smart-inventory-management-dashboard.png)

Vegye figyelembe, hogy a fenti irányítópult egy példa, és teljes mértékben testreszabhatja az alkalmazást, hogy illeszkedjen a kívánt használati esethez. 

Ismerkedjen meg a teljes [körű oktatóanyaggal](./tutorial-iot-central-smart-inventory-management-pnp.md) , amely végigvezeti az intelligens leltár-felügyeleti sablonok egyikének megoldásán.


## <a name="next-steps"></a>További lépések
A kereskedelmi megoldások létrehozásának megkezdéséhez:
* Ismerkedjen meg a teljes [körű oktatóanyaggal](./tutorial-in-store-analytics-create-app-pnp.md) , amely végigvezeti egy olyan megoldás létrehozásán, amely az egyik áruházbeli elemzési alkalmazás-sablon kihasználását mutatja be.
* Ismerje meg, hogyan telepítheti a [csatlakoztatott logisztikai megoldás sablonját](./tutorial-iot-central-connected-logistics-pnp.md)
* Útmutató a [digitális terjesztési központ sablonjának](./tutorial-iot-central-digital-distribution-center-pnp.md) üzembe helyezéséhez
* Ismerje meg, hogyan telepítheti az [intelligens leltár felügyeleti sablonját](./tutorial-iot-central-smart-inventory-management-pnp.md)
* További információ a IoT Centralról [IoT Central áttekintés](../preview/overview-iot-central.md)
