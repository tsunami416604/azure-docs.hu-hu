---
title: Gyors útmutató – az Azure költségeinek megismerése a Cost Analysis szolgáltatással | Microsoft Docs
description: Ez a rövid útmutató bemutatja, hogyan vizsgálhatja meg és elemezheti vállalata Azure-költségeit a Költségelemzés szolgáltatás használatával.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: quickstart
ms.service: cost-management-billing
manager: micflan
ms.custom: seodec18
ms.openlocfilehash: 858672a7fcfcfd5f50c91eb769fdd0d42578195f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229994"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Gyors útmutató: költségek feltárása és elemzése a Cost Analysis szolgáltatással

Ahhoz, hogy érdemben szabályozhassa és optimalizálhassa Azure-költségeit, fontos, hogy tudja a vállalaton belül hol keletkeznek ezek a költségek. Azt is érdemes megismerni, hogy Mennyibe kerül a szolgáltatás díja, és hogy mely környezetek és rendszerek támogatására van lehetőség. A költségek teljes spektrumának ismerete elengedhetetlen a szervezet kiadásaiban fellépő mintázatok pontos megértéséhez. A költségkeretek segítségével kikényszerítheti a költség-ellenőrzési mechanizmusokat, például a költségvetést.

Ebben a rövid útmutatóban költségelemzés használatával megvizsgálja és elemzi a szervezet költségeit. Az összesített költségeket szervezetekre lebontva jelenítheti meg, így jobban átláthatja, hogy az idő múlásával mely területeken keletkeznek költségei és felismerheti a kiadások trendjeit. Megtekintheti az egy adott időszak során felhalmozott költségeket is, így havi, negyedéves vagy akár éves költségtrendbecsléseket is készíthet a költségvetéshez viszonyítva. Költségvetés használatával könnyebb betartani a pénzügyi korlátozásokat. Ezen felül a költségvetés segít áttekinteni napi vagy havi szintű költségeit és elkülöníteni a rendhagyó kiadásokat. Az adatokat le is töltheti további elemzés vagy külső rendszerben való használat céljából.

Ezen rövid útmutató segítségével megtanulhatja a következőket:

- Költségek áttekintése a Költségelemzés használatával
- Költségnézetek testreszabása
- Költségelemzési adatok letöltése


## <a name="prerequisites"></a>Előfeltételek

A Cost Analysis különböző típusú Azure-fiókokat támogat. A támogatott fióktípus teljes listájának megtekintéséhez lásd: Cost Management- [adat megismerése](understand-cost-mgt-data.md). A költségadatok megtekintéséhez legalább olvasási hozzáféréssel kell rendelkeznie az Azure-fiókhoz.

További információ a Azure Cost Management adatokhoz való hozzáférés kiosztásáról: az [adatokhoz való hozzáférés kiosztása](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

- Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="review-costs-in-cost-analysis"></a>Költségek áttekintése a Költségelemzés használatával

Ha szeretné áttekinteni a költségeket a Cost Analysis szolgáltatásban, nyissa meg a hatókört a Azure Portalban, és válassza a **Cost Analysis** lehetőséget a menüben Például lépjen az **előfizetések**elemre, válasszon ki egy előfizetést a listából, majd válassza a menü **Cost Analysis** elemét. A **Hatókör** elemmel másik hatókörre válthat a költségelemzésben. További információ a hatókörökről: a [hatókörök megismerése és használata](understand-work-scopes.md).

A kiválasztott hatókört a rendszer az egész Cost Management használja az adatösszesítés biztosításához és a költségadatok hozzáférésének szabályozásához. A hatókörök használata során nem lehet egyszerre több hatókört is kiválasztani. Ehelyett nagyobb hatókört kell választania, amelyet mások is felhasználhatnak, majd szűrniük kell azokat a beágyazott hatókörökre, amelyekre szüksége van. Ez a megközelítés azért fontos, mert előfordulhat, hogy egyes személyek nem férhetnek hozzá egyetlen szülő hatókörhöz, amely több beágyazott hatókörre is kiterjed.

A kezdeti Cost Analysis View a következő területeket tartalmazza.

**Halmozott Cost nézet**: az előre definiált Cost Analysis View konfigurációt jelöli. Minden nézet tartalmazza a dátumtartomány, a részletesség, a Csoportosítás és a szűrés beállításait. Az alapértelmezett nézetben az aktuális számlázási időszak összesített költségei láthatók, de más beépített nézetekre is válthat. További információ: a [Cost views testreszabása](#customize-cost-views).

**Tényleges költség**: az aktuális hónap teljes használati és vásárlási költségét jeleníti meg, amint azok felhalmozódnak, és megjelennek a számlán.

**Előrejelzés**: a kiválasztott időszakra vonatkozó összes előrejelzett költséget jeleníti meg. (Az előrejelzés előzetes verzióban érhető el.)

Költségvetés: a kiválasztott hatókör tervezett **költségkeretét**jeleníti meg, ha van ilyen.

**Halmozott részletesség**: a számlázási időszak elejétől számított összesített napi költségeket mutatja. Miután számlázási fiókjához vagy előfizetéséhez [létrehozta költségvetését](tutorial-acm-create-budgets.md), itt gyorsan, költségkeretéhez viszonyítottan áttekintheti kiadási trendjeit. A kurzort egy adott dátum felé helyezve megtekintheti az adott nap halmozott költségeit.

**Pivot (fánk) diagramok**: dinamikus kimutatások biztosítása, a teljes díj lebontása a szokásos tulajdonságok közös készletével. Az aktuális hónap legnagyobb költségét jelenítik meg. A kimutatásdiagramokat bármikor módosíthatja másik kimutatás kiválasztásával. A költségeket a szolgáltatás (fogyasztásmérő kategória), a hely (régió) és a gyermek hatóköre alapértelmezés szerint kategorizálja. Például a beléptetési fiókok számlázási fiókok alatt találhatók, az erőforráscsoportok az előfizetések alatt találhatók, és az erőforrások az erőforráscsoportok alatt találhatók.

![Az Azure Portalon költségelemzés kezdeti nézete](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Költségnézetek testreszabása

A Cost Analysis négy beépített nézetet tartalmaz, amelyek a leggyakoribb célokhoz vannak optimalizálva:

Nézet | Válaszoljon például a következő kérdésekre:
--- | ---
Halmozott díj | Mennyit töltöttem eddig ebben a hónapban? Belül maradok-e a költségkeretemen?
Napi díj | Az elmúlt 30 napban a napi költségek növekedtek?
Szolgáltatás díja | Hogyan változik a havi használat az elmúlt három számlán?
Költségek erőforrások szerint | Mely erőforrások jelentik a legtöbbet ebben a hónapban?

![Az adott hónapra kijelölni kívánt példát mutató választó megtekintése](./media/quick-acm-cost-analysis/view-selector.png)

Azonban sok esetben ennél mélyebb elemzésre is szükség lehet. A testreszabást az oldal tetején a dátum kiválasztásánál kezdhetjük.

Alapértelmezetten a költségelemzés az aktuális hónap adatait jeleníti meg. A dátumválasztó használatával gyorsan válthat a gyakori dátumtartomány-tartományokra. Ilyenek például az elmúlt hét nap, az utolsó hónap, az aktuális év vagy egy egyéni dátumtartomány. Az utólagos elszámolású előfizetések a számlázási időszak alapján is tartalmazzák a dátumtartományt, amely nem a naptári hónaphoz van kötve, például az aktuális számlázási időszakhoz vagy az utolsó számlához. A menü tetején lévő **< előző** és **következő >** hivatkozásokat használva ugorjon az előző vagy a következő időszakra. Az **előző <** például az **elmúlt 7 nap** és **8-14 napja** , illetve a **15-21 napja**után vált.

![Dátum-választó megjelenítése egy példa kiválasztása ebben a hónapban](./media/quick-acm-cost-analysis/date-selector.png)

A költségelemzés alapértelmezetten a **Halmozott** költségeket jeleníti meg. A halmozott költségek a napi összesített költségek folyamatosan bővülő, az előző napokra vonatkozó összes költségét tartalmazzák. A nézetet arra optimalizáltuk, hogy megvizsgálhassa, hogyan alakulnak költségei az adott időszakban a tervezett költségvetéshez képest.

Az előrejelzési diagram nézetet használva azonosíthatja a lehetséges költségvetési szabálysértéseket. A lehetséges költségvetési megsértés esetén a tervezett túltöltés piros színnel jelenik meg. A diagramon a kijelző szimbóluma is látható. A szimbólum fölé helyezve a költségvetési szerződésszegés becsült dátuma látható.

![A lehetséges költségvetési szabálysértést bemutató példa](./media/quick-acm-cost-analysis/budget-breach.png)

Emellett a **napi** nézet használatával az egyes napok költségeit is megtekintheti. A napi nézet nem jeleníti meg a növekedési trendeket. A nézet célja a szabálytalanságok, vagyis a kiugróan magas vagy alacsony értékek megjelenítése a napról napra jelentkező költségekben. Ha kiválasztott egy költségvetést, a napi nézet a napi költségkeret becslését is megjeleníti.

Ha a napi költségek konzisztensek a becsült napi költségkeret felett, akkor várható, hogy a havi költségkeretet meghaladja. A becsült napi költségkeret egy olyan eszköz, amely segít a költségvetés alacsonyabb szintű megjelenítésében. Ha napi költségei ingadozóak, a becsült napi költségkeret viszonya a havi költségkeretével kevésbé lesz pontos.

Itt látható a közelmúltbeli kiadások száma, amelyekben az előrejelzés bekapcsolva.
![napi nézet, amely az aktuális hónapra vonatkozó napi költségeket mutatja](./media/quick-acm-cost-analysis/daily-view.png)

Ha kikapcsolja a kiadások előrejelzését, a jövőbeli dátumok esetében nem látja a tervezett kiadásokat. Továbbá, ha a múltbeli időszakokra vonatkozó költségeket tekinti meg, a költségek előrejelzése nem mutatja a költségeket.

Általánosságban elmondható, hogy a felhasznált erőforrásokra vonatkozó adatok vagy értesítések 8 – 12 órán belül megjelennek.


**Csoportosítsa** a közös tulajdonságok alapján a költségeket, és azonosítsa a legfontosabb közreműködőket. Az erőforrás-címkék csoportosításához például válassza ki azt a címke-kulcsot, amelyet szeretne csoportosítani. A költségek az egyes kódelemek szerint vannak lebontva, és az olyan erőforrások külön szegmense van, amelyeken nincs alkalmazva a címke.

A legtöbb [Azure-erőforrás támogatja a címkézést](../azure-resource-manager/tag-support.md). Azonban egyes címkék nem érhetők el Cost Management és számlázásban. Emellett az erőforráscsoportok címkéi nem támogatottak. Cost Management csak abban a dátumban támogatja az erőforrás-címkéket, amikor a címkék közvetlenül az erőforrásra lesznek alkalmazva. Tekintse meg, [Hogyan tekintheti át a címkézési szabályzatokat Azure Cost Management](https://www.youtube.com/watch?v=nHQYcYGKuyw) videóval, hogy megtudja, hogyan használhatja az Azure-címkézési házirendet a költségadatok láthatóságának

Itt látható az aktuális hónapra vonatkozó Azure-szolgáltatás költségeinek áttekintése.

![Csoportosított napi halmozott nézet-példa az Azure szolgáltatási költségei teszik a múlt hónapban](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Alapértelmezés szerint a Cost Analysis megjeleníti az összes használati és vásárlási költséget, amint azok felhalmozódnak, és megjelennek a számlán, más néven a **tényleges költségek**. A tényleges díj megtekintése ideális a számla egyeztetéséhez. A költségek megvásárlása azonban riasztó lehet, ha a felmerülő hibákért és a költségek egyéb változásaihoz vezet. A foglalások vásárlásának költségei által okozott tüskék kisimításához váltson az elszámolt **költségekre**.

![A tényleges és az elszámolt díj közötti váltás a foglalási vásárlások elosztása és a foglalást használó erőforrások kiosztása között](./media/quick-acm-cost-analysis/metric-picker.png)

Az elszámolt díj a foglalások napi adattömbökbe való beszerzését, valamint a foglalás időtartama alatt terjeszti. Ha például a január 1-től a $365-es vásárlást látja, akkor minden nap január 1-től december 31-ig megjelenik a $1-es vásárlás. Az alapszintű amortizáción kívül ezeket a költségeket a foglalást használó konkrét erőforrásokkal is újra kiosztjuk és társítjuk. Ha például a $1-es napi díjat két virtuális gép között osztották fel, akkor a napi díj $2 0,50. Ha a foglalás egy részét nem használják fel az adott napra, a $1 0,50 díjat a megfelelő virtuális géphez és egy másik $0,50-díj `UnusedReservation`. Vegye figyelembe, hogy a fel nem használt foglalási költségek csak az elszámolt költségek megtekintésekor láthatók.

A költségek kiosztásának változása miatt fontos megjegyezni, hogy a tényleges költségek és az elszámolt költségek különböző összesített számokat mutatnak. Általánosságban elmondható, hogy a foglalások megvásárlása után a hónapok teljes költsége csökken az elszámolt költségek megtekintésekor, és a foglalások megvásárlását követő hónapok száma növekedni fog. Az amortizáció csak foglalási vásárlások esetén érhető el, és jelenleg nem vonatkozik az Azure Marketplace-beli vásárlásokra.

Az alábbi képen az erőforráscsoportok nevei láthatók. Az adott erőforráshoz tartozó összes címke megtekintéséhez csoportosítsa a címkét a címkék teljes költségének megtekintéséhez, vagy használja a **Cost by Resource View (költség szerint** ) lehetőséget.

![Összes adat megjelenítése az erőforrás csoportnevek aktuális nézet](./media/quick-acm-cost-analysis/full-data-set.png)

Ha egy adott attribútum alapján csoportosítja a költségeket, a Top 10 Cost közreműködők a legmagasabbtól a legalacsonyabb értékig jelennek meg. Ha több mint 10 van, akkor az első kilenc Cost közreműködő egy **másik** csoporttal együtt jelenik meg, amely az összes többi csoport együttesét jelöli. Címkék alapján történő csoportosítás **esetén a címke nélküli csoportok** nem jelennek meg azoknál a költségeknél, amelyek nem rendelkeznek a tag kulcsával. A **címkézetlen** érték mindig utolsó, még akkor is, ha a címkézett költségek nagyobbak a címkézett költségeknél. A címkézetlen költségek **mások**részei lesznek, ha 10 vagy több címke érték létezik. Váltson a Table (táblázat) nézetre, és módosítsa a részletességet a **nincs** értékre, hogy az összes érték a legmagasabbról a legalacsonyabbra legyen rangsorolva.

A klasszikus virtuális gépek, a Hálózatkezelés és a tárolási erőforrások nem osztják meg a részletes számlázási információt. Ezeket **klasszikus szolgáltatásként** egyesítjük a költségek csoportosításakor.

A fő diagram alatti diagramokon különböző Csoportosítások jelennek meg, amelyek a kiválasztott időszakra és szűrőkre vonatkozó általános költségekkel kapcsolatos átfogó képet nyújtanak. Válasszon egy tulajdonságot vagy címke minden dimenzió összesített költségeit megtekintésére.

![Példa pivot diagramokra](./media/quick-acm-cost-analysis/pivot-charts.png)

Megtekintheti bármely nézet teljes adatkészletét. Attól függően, hogy milyen beállítások vagy szűrők vannak érvényben, hatással van a bemutatott adatelemekre. A teljes adatkészlet megtekintéséhez válassza a **diagram típusa** listát, majd válassza a **táblázat** nézet lehetőséget.

![Az aktuális nézetre vonatkozó adattábla nézet](./media/quick-acm-cost-analysis/chart-type-table-view.png)


## <a name="understanding-grouping-and-filtering-options"></a>A csoportosítási és szűrési beállítások ismertetése

A következő táblázat felsorolja a leggyakoribb csoportosítási és szűrési lehetőségeket, valamint azt, hogy mikor érdemes használni őket.

| Tulajdonság | A következő esetekben használja |
| --- | --- |
| **Számlázási időszak** | Költségek lebontása a számla hónapja szerint. Ez a lehetőség az utólagos elszámolású és fejlesztési/tesztelési előfizetések esetében fontos, amelyek nem a naptári hónapokhoz kötődnek. Az EA/MCA-fiókok naptári hónapokat használhatnak a dátumválasztó vagy a havi részletesség használatával, hogy ugyanazt a célt lehessen elérni. |
| **Díj típusa** | A használat, a vásárlás, a visszatérítés és a nem használt foglalási költségek lebontása. A foglalási beszerzések és a visszatérítések csak akkor érhetők el, ha az elszámolt költségek használata nem történik meg. A nem használt foglalási költségek csak az elszámolt költségeknél érhetők el. |
| **Felhő** | Az AWS és az Azure költségeinek lebontása. Az AWS-költségek csak felügyeleti csoportok, külső számlázási fiókok és külső előfizetések esetén érhetők el. |
| **Részleg** / **számla szakasz** | A költségeket a nagyvállalati szerződés vagy az MCA-számla szakasza alapján bontják le. Ez a beállítás csak az EA/MCA számlázási fiókokhoz és az MCA számlázási profiljaihoz érhető el. |
| **Beléptetési fiók** | Az EA-fiók tulajdonosának költségeinek lebontása. Ez a beállítás csak EA számlázási fiókokhoz és részlegekhez érhető el. |
| **Gyakoriság** | A használaton alapuló, egyszeri és ismétlődő költségek lebontása. |
| **Mérő** | A költségeket az Azure használati mérőszáma alapján bontják le. Ez a beállítás csak az Azure-használathoz érhető el. A vásárlások és a Piactérek használata **nem megadott vagy nem** **hozzárendelt**módon jelenik meg. |
| **Közzétevő típusa** | Az AWS, az Azure és a Marketplace költségeinek lebontása. |
| **Foglalás** | Költségek lebontása foglalás alapján. A foglalást nem tartalmazó használat **nem megadottként**jelenik meg. |
| **Erőforrás** | Költségek lebontása erőforrás alapján. Az összes vásárlás **nem megadottként**jelenik meg, mert egy EA/TB számlázási fiók vagy MCA számlázási profil szintjén vannak alkalmazva.  |
| **Erőforráscsoport** | Az erőforráscsoport költségeinek lebontása. Ez a beállítás csak a nem klasszikus használat esetén érhető el. A klasszikus erőforrás-használat **más**néven jelenik meg, és a vásárlások **nem megadott**módon jelennek meg. |
| **Erőforrás típusa** | A költségek kibontása erőforrástípus alapján. Ez a beállítás csak a nem klasszikus használat esetén érhető el. A klasszikus erőforrás-használat **más**néven jelenik meg, és a vásárlások **nem megadott**módon jelennek meg. |
| **Szolgáltatásnév** vagy **fogyasztásmérő kategóriája** | Az Azure-szolgáltatás költségeit lebonthatja. Ez a beállítás csak az Azure-használathoz érhető el. A vásárlások és a Piactérek használata **nem megadott vagy nem** **hozzárendelt**módon jelenik meg. |
| **Szolgáltatási** vagy **fogyasztásmérő alkategória** | A költségeket az Azure használati mérési albesorolása alapján bontják le. Ez a beállítás csak az Azure-használathoz érhető el. A vásárlások és a Piactérek használata **nem megadott vagy nem** **hozzárendelt**módon jelenik meg. |
| **Előfizetés** | Költségek lebontása előfizetés alapján. Az összes beszerzés **nem megadottként**jelenik meg. |
| **Tag** | A költségek lebontása egy adott címke kulcsának értékei alapján. |

A feltételekkel kapcsolatos további információkért lásd: az [Azure-használat és a díjak fájljában használt kifejezések ismertetése](../billing/billing-understand-your-usage.md).


## <a name="saving-and-sharing-customized-views"></a>Testreszabott nézetek mentése és megosztása

Testreszabott nézeteket menthet és oszthat meg másokkal a Cost Analysis Azure Portal-irányítópultra való rögzítésével vagy a Cost Analysis-ra mutató hivatkozás másolásával.

A költségadatok rögzítéséhez válassza a jobb felső sarokban található rögzítés ikont. A rögzítési ár elemzése csak a fő diagramot vagy a táblázat nézetet fogja menteni. Ossza meg az irányítópultot, hogy mások hozzáférhessenek a csempéhez. Vegye figyelembe, hogy ez csak az irányítópult konfigurációját osztja meg, és nem biztosít másoknak hozzáférést az alapul szolgáló adathoz. Ha nincs hozzáférése a költségekhez, de van hozzáférése egy megosztott irányítópulthoz, a "hozzáférés megtagadva" üzenet jelenik meg.

Ha meg szeretné osztani a Cost Analysis-ra mutató hivatkozást, válassza a panel tetején található **megosztás** elemet. Egy egyéni URL-cím fog megjelenni, amely megnyitja ezt az adott hatókörre vonatkozó nézetet. Ha nincs hozzáférése az URL-címhez, akkor a "hozzáférés megtagadva" üzenet jelenik meg.

Ha többet szeretne megtudni az egyes támogatott hatókörökkel kapcsolatos költségek elérésének biztosításáról, tekintse át a [hatókörök ismertetése és használata](understand-work-scopes.md)című témakört.

## <a name="automation-and-offline-analysis"></a>Automatizálás és offline elemzés

Ha további elemzés céljából le kell töltenie az adatait, egyesíteni kell azokat a saját adataival, vagy integrálnia kell azokat a saját rendszereibe. Cost Management néhány különböző lehetőséget kínál. Kiindulási pontként, ha az ad hoc magas szintű összefoglalásra van szüksége, például a Cost Analysis szolgáltatásban, hozza létre a szükséges nézetet. Ezután töltse le az **Exportálás** lehetőséget, majd válassza az adatfájlok **letöltése CSV-be** vagy az **Excelbe való letöltés**lehetőséget. Az Excel Letöltés további kontextust biztosít a letöltés, például a hatókör, a lekérdezés konfigurációja, a teljes és a dátum létrehozásához használt nézetben.

Ha a teljes, nem aggregált adatkészletre van szüksége, töltse le a számlázási fiókból. Ezután a portál bal oldali navigációs paneljén a szolgáltatások listájából válassza a **Cost Management + számlázás**lehetőséget. Válassza ki a számlázási fiókját, ha van ilyen. Nyissa meg a **használati és díjszabási**lehetőséget, majd válassza ki a kívánt számlázási időszak **Letöltés** ikonját.

Hasonló megközelítést használhat a költségadatok fogadásának automatizálására. A [lekérdezési API](/rest/api/cost-management/query) -val gazdagabb elemzést végezhet a dinamikus szűréssel, csoportosítással és összesítéssel, vagy használhatja a [UsageDetails API](/rest/api/consumption/usageDetails) -t a teljes, nem aggregált adatkészlet esetében. Ezen API-k általánosan elérhető (GA) verziója 2019-01-01. A **2019-04-01 – előzetes** verzió használatával elérheti a foglalási és Piactéri vásárlások előnézetét ezeken az API-kon belül.

A következőkben például az elszámolási költségek összesített nézete (használat, vásárlás vagy visszatérítés), a közzétevő típusa (Azure vagy Marketplace), az erőforráscsoport (a vásárlások esetében üres) és a foglalás (üres, ha nem alkalmazható).

```
POST https://management.azure.com/{scope}/providers/Microsoft.CostManagement/query?api-version=2019-04-01-preview
Content-Type: application/json

{
  "type": "AmortizedCost",
  "timeframe": "Custom",
  "timePeriod": { "from": "2019-04-01", "to": "2019-04-30" },
  "dataset": {
    "granularity": "None",
    "aggregation": {
      "totalCost": { "name": "PreTaxCost", "function": "Sum" }
    },
    "grouping": [
      { "type": "dimension", "name": "ChargeType" },
      { "type": "dimension", "name": "PublisherType" },
      { "type": "dimension", "name": "Frequency" },
      { "type": "dimension", "name": "ResourceGroup" },
      { "type": "dimension", "name": "SubscriptionName" },
      { "type": "dimension", "name": "SubscriptionId" },
      { "type": "dimension", "name": "ReservationName" },
      { "type": "dimension", "name": "ReservationId" },
    ]
  },
}
```

Ha nincs szüksége az összesítésre, és inkább a teljes, nyers adatkészletet szeretné előnyben részesíteni:

```
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

Ha tényleges költségekre van szüksége a beérkező beszerzések megjelenítéséhez, módosítsa/**metrika** típusát **ActualCost**értékre. További információ ezekről az API-król: [lekérdezési](/rest/api/cost-management/query) és [UsageDetails](/rest/api/consumption/usageDetails) API-dokumentáció. Vegye figyelembe, hogy a közzétett dokumentumok a GA verzióra vonatkoznak. Ugyanakkor a *2019-04-01 – előzetes* verziójú API-verzió esetében is ugyanúgy működnek, mint az új Type/metrikus attribútumon kívül, és megváltozott a tulajdonságok nevei. (További információ az alábbi tulajdonságok neveiről.)

Cost Management API-k az erőforrások feletti összes hatókörben működnek: erőforráscsoport, előfizetés és felügyeleti csoport az Azure RBAC-hozzáférés, az EA számlázási fiókok (beléptetések), a részlegek és a regisztrációs fiókok használata az EA portálon keresztül. További információ a hatókörökről, többek között a hatókör-azonosító meghatározásáról vagy a hozzáférés kezeléséről a [hatókörök megismerése és használata](understand-work-scopes.md)című témakörben.

## <a name="next-steps"></a>További lépések

Lépjen tovább az első oktatóanyaghoz, amely a költségvetés létrehozását és kezelését mutatja be.

> [!div class="nextstepaction"]
> [Költségvetések létrehozása és kezelése](tutorial-acm-create-budgets.md)
