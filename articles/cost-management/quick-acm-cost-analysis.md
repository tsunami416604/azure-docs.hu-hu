---
title: Rövid útmutató – Ismerkedés az Azure-költségek költségelemzés |} A Microsoft Docs
description: Ez a rövid útmutató bemutatja, hogyan vizsgálhatja meg és elemezheti vállalata Azure-költségeit a Költségelemzés szolgáltatás használatával.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/11/2019
ms.topic: quickstart
ms.service: cost-management
manager: micflan
ms.custom: seodec18
ms.openlocfilehash: d07a52a3cd07c69b9b7e2ef43331ddd9fb455b10
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827833"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Gyors útmutató: Vizsgálata és elemzése a költségeket a költségelemzés

Ahhoz, hogy érdemben szabályozhassa és optimalizálhassa Azure-költségeit, fontos, hogy tudja a vállalaton belül hol keletkeznek ezek a költségek. Emellett hasznos tudja, hogy mennyi pénzt a szolgáltatások költség, és mely környezetek és a rendszerek támogatásához. A költségek teljes spektrumának ismerete elengedhetetlen a szervezet kiadásaiban fellépő mintázatok pontos megértéséhez. Költségkeret mintákat használhat költség mechanizmusai, például a költségvetés kényszerítésére.

Ebben a rövid útmutatóban költségelemzés használatával megvizsgálja és elemzi a szervezet költségeit. Az összesített költségeket szervezetekre lebontva jelenítheti meg, így jobban átláthatja, hogy az idő múlásával mely területeken keletkeznek költségei és felismerheti a kiadások trendjeit. Megtekintheti az egy adott időszak során felhalmozott költségeket is, így havi, negyedéves vagy akár éves költségtrendbecsléseket is készíthet a költségvetéshez viszonyítva. Költségvetés használatával könnyebb betartani a pénzügyi korlátozásokat. Ezen felül a költségvetés segít áttekinteni napi vagy havi szintű költségeit és elkülöníteni a rendhagyó kiadásokat. Az adatokat le is töltheti további elemzés vagy külső rendszerben való használat céljából.

Ezen rövid útmutató segítségével megtanulhatja a következőket:

- Költségek áttekintése a Költségelemzés használatával
- Költségnézetek testreszabása
- Költségelemzési adatok letöltése


## <a name="prerequisites"></a>Előfeltételek

Költségelemzés támogatja a különböző típusú Azure-fiók típusa. A támogatott típusok teljes listáját, tekintse meg [megismerheti a Cost Management adataihoz](understand-cost-mgt-data.md). Költségadatok megtekintése, szükség van legalább olvasási hozzáféréssel az Azure-fiókjával.

A [nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) ügyfelek, rendelkeznie kell legalább olvasási hozzáférést egy vagy több, a költségadatok megtekintéséhez a következő hatókörök.

- Számlázási fiók
- Részleg
- Regisztrációs fiók
- Felügyeleti csoport
- Előfizetés
- Resource group

További információ az Azure Cost Management adataihoz való hozzáférés hozzárendelése: [adatokhoz való hozzáférés hozzárendelése](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

- Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="review-costs-in-cost-analysis"></a>Költségek áttekintése a Költségelemzés használatával

A költségek, a költségek elemzése, nyissa meg a hatókört az Azure Portalon, és válassza a **költségelemzés** menüjében. Például Ugrás **előfizetések**, válasszon ki egy előfizetést a listából, és válassza **költségelemzés** menüjében. Használja a **hatókör** megszámlálásához váltson át egy másik hatókört, a költségek elemzése. Hatókörök kapcsolatos további információkért lásd: [megismerése és együttműködnek a hatókörök](understand-work-scopes.md).

A hatókör választja Cost Management teljes használja adatok összevonása és történő hozzáférés Költséginformációk biztosítására. A hatókörök használata során nem lehet egyszerre több hatókört is kiválasztani. Ehelyett nagyobb hatókör, amely mások akár visszaállítani, válassza ki, és szűrjön rá kell a beágyazott hatókörök le. Ez a megközelítés akkor fontos tudni, mert vannak, akik előfordulhat, hogy rendelkezik hozzáféréssel egyetlen szülő hatókör, amely magában foglalja a több beágyazott hatókörök.

A kezdeti költségek elemzése nézet a következő területeket tartalmazza.

**Halmozott költségek nézet**: Az előre meghatározott cost analysis konfiguráció megtekintése jelöli. Minden egyes nézet tartalmazza a dátumtartomány, a részletesség, a csoportosítás, és a beállítások szűrése. Az alapértelmezett nézet megjeleníti összesített költségek a jelenlegi számlázási időszak, de más beépített nézetekkel módosíthatja. További információkért lásd: [költség nézetek testreszabása](#customize-cost-views).

**Tényleges költség**: Az aktuális hónaphoz teljes használat és a vásárlási költségeit mutatja, még a feladatokkal kapcsolatos és jelennek meg a számlán.

**Előrejelzés**: Az összes előre jelzett költség látható a választott időszakban. (Előrejelzés az előzetes verzióban érhető el.)

**Költségvetés**: A kijelölt hatókör, a a tervezett költségkeret látható, ha elérhető.

**Összesített Granularitás**: Teljes összesített napi költségeit mutatja, az elszámolási időszak kezdetétől fogva. Miután számlázási fiókjához vagy előfizetéséhez [létrehozta költségvetését](tutorial-acm-create-budgets.md), itt gyorsan, költségkeretéhez viszonyítottan áttekintheti kiadási trendjeit. A kurzort egy adott dátum felé helyezve megtekintheti az adott nap halmozott költségeit.

**Forgáspont (fánkdiagramok)** : Adja meg a dinamikus szűkítése, az alapvető tulajdonságok készletének összköltség bontásához. Ezek bemutatják a legnagyobbtól a legkisebb költségeket az aktuális hónaphoz. A kimutatásdiagramokat bármikor módosíthatja másik kimutatás kiválasztásával. Költségek szerint vannak kategóriába sorolva szolgáltatás (mérőszám kategóriája), a helyet (régiót) és a gyermek hatókörben alapértelmezés szerint. Például regisztrációs fiókok számlázási fiókok vannak, erőforráscsoportok az előfizetések alatt állnak, és erőforrások erőforráscsoportok alatt állnak.

![Az Azure Portalon költségelemzés kezdeti nézete](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Költségnézetek testreszabása

Költségelemzés négy beépített nézetek, a leggyakoribb célokat optimalizálva van:

Nézet | Válaszoljon a kérdésekre, mint
--- | ---
Összesített költségeit | IP-címek fenntartási töltötték eddig ebben a hónapban? Belül maradok-e a költségkeretemen?
Napi költség | Az elmúlt 30 napra vonatkozó napi költségek növekedését van voltak?
Díj szolgáltatás szerint | Hogyan rendelkezik az elmúlt három számlákat idővel változhatnak a havi használat?
Költségek erőforrás szerint | Mely erőforrások költsége a legtöbb eddig ebben a hónapban?

![Adatnézet-választóhoz látható egy példa kiválasztása ebben a hónapban](./media/quick-acm-cost-analysis/view-selector.png)

Azonban sok esetben ennél mélyebb elemzésre is szükség lehet. A testreszabást az oldal tetején a dátum kiválasztásánál kezdhetjük.

Alapértelmezetten a költségelemzés az aktuális hónap adatait jeleníti meg. A dátum-választó használatával gyorsan válthat a közös dátumtartományokat. Ilyenek például az elmúlt hét nap, az elmúlt hónapban, az aktuális év vagy egy egyéni dátumtartományt. Utólagos elszámolású előfizetések a számlázási időszak, amely nincs kötve a naptári hónap, például az aktuális elszámolási időszakban vagy a legutóbbi számla alapján dátumtartományokat is tartalmazhatnak. Használja a **< előző** és **Tovább >** Ugrás az előző vagy következő időszak jelölik, a menü tetején található hivatkozásokat. Például **< előző** váltani fog a a **legutóbbi 7 nap** való **8 – 14 napja** vagy **15-21 napos**.

![Dátum-választó megjelenítése egy példa kiválasztása ebben a hónapban](./media/quick-acm-cost-analysis/date-selector.png)

A költségelemzés alapértelmezetten a **Halmozott** költségeket jeleníti meg. Összesített költségek összes költséget tartalmazzák, minden nap, valamint az elmúlt napokban, napi összesített költségeit egyre bővülő megjelenítéséhez. A nézetet arra optimalizáltuk, hogy megvizsgálhassa, hogyan alakulnak költségei az adott időszakban a tervezett költségvetéshez képest.

Az előrejelzési diagram nézet segítségével azonosíthatja a potenciális költségvetés szabályok megsértése esetén alkalmazandók. Ha költségvetés potenciális támadásokról, piros tervezett túlköltekezés látható. A diagram is mutatja egy jelző szimbólum. A szimbólum fölé viszi a költségvetés szerződésszegés becsült dátumának megjelenítése.

![Példa ábrázoló lehetséges költségvetést illetéktelen behatolás](./media/quick-acm-cost-analysis/budget-breach.png)

Emellett a **napi** nézet használatával az egyes napok költségeit is megtekintheti. A napi nézet nem jeleníti meg a növekedési trendeket. A nézet célja a szabálytalanságok, vagyis a kiugróan magas vagy alacsony értékek megjelenítése a napról napra jelentkező költségekben. Ha kiválasztotta a költségvetést, a napi nézetben is látható becsült napi költségkeretét.

Ha a napi költségek egységesen a becsült napi költségvetés fölött, várható havi költségkeretét, fog előremutató. A becsült napi költségvetés segítségével vizualizálhatja a költségvetést, alacsonyabb szintű azt jelenti, hogy az. Ha napi költségei ingadozóak, a becsült napi költségkeret viszonya a havi költségkeretével kevésbé lesz pontos.

Íme a legutóbbi költségeit a költségkeret-beállítási előrejelzési kapcsolva napi nézetét.
![Az aktuális hónapra például napi költségek megjelenítő napi megtekintése](./media/quick-acm-cost-analysis/daily-view.png)

Ha kikapcsolja a költségkeret-beállítási előrejelzési, nem látja, hogy a jövőbeli dátumok tervezett költségeket. Is ha megnézi költségeit a korábbi időszakok, költség-előrejelzési nem jelenít meg költségeket.

Általában a is látja a keresett adatok vagy a felhasznált erőforrások értesítések 8 – 12 órán belül.


**Csoportosítás** bontja a költségek és az általános tulajdonságok leggyakoribb közreműködők. Erőforrás-címkék szerint kell csoportosítani, jelölje be például a címke kulcs szerint kell csoportosítani szeretné. Költségek szerint egyes címkeértékkel rendelkező erőforrások, amelyek nem rendelkeznek a alkalmazni a címkét egy extra szegmens bontásban.

A legtöbb [Azure-erőforrások támogatja a címkézést](../azure-resource-manager/tag-support.md). Azonban az egyes címkék nem érhető el a Cost Management és a számlázási. Ezenkívül a csoport az erőforráscímkék nem támogatottak. A Cost Management csak a dátum, a címkék közvetlenül az erőforrás érvényesek az erőforráscímkék támogatja. Tekintse meg a [címkézési házirendek az Azure Cost Managementbe felülvizsgálata](https://www.youtube.com/watch?v=nHQYcYGKuyw) videó további információ a címke az Azure policyval költség adatok láthatóságának javítása érdekében.

Íme egy megtekintése az Azure szolgáltatási költségei teszik az aktuális hónapra.

![Csoportosított napi halmozott nézet-példa az Azure szolgáltatási költségei teszik a múlt hónapban](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Alapértelmezés szerint a költségelemzés minden használati és beszerzési költségek megjelenik, azt is, és jelennek meg a számlán, más néven **tényleges költség**. Megtekintés a tényleges költségek ideális összeegyezteti a számlán. Vásárlás kiugrások költség azonban akkor lehet ijesztő, ha, még tartja szemmel a rendellenességeket és egyéb változásait költség költségeit. Foglalás beszerzési költségek okozta adatforgalmi kiugrások simítják, váltson **amortizált költségek**. 

![Tényleges és amortizált költsége látható foglalás megállítását megvásárolja a feltételek között, és az erőforrásokat, amelyek a Foglalás számára lefoglalt közötti módosítása](./media/quick-acm-cost-analysis/metric-picker.png)

Amortizált költség foglalás vásárlások adattömbökbe napi felszámolja, és osztja el őket a Foglalás időtartamára időtartama alatt. Például helyett január 1-jén beszerzési $365 lát, látni fogja a $1 vásárolhatnak naponta január 1-től December 31-ig. Alapszintű amortizációs mellett ezeket a díjakat is újra kiosztja, és a konkrét erőforrásokat, amelyek a Foglalás használatával kapcsolatos. Például, hogy 1 $ napi díj volt felosztása két virtuális gép között, két 0,50 USD díj a napi lenne láthatja. A Foglalás része nem használt fel a nap, jelennének meg egy 0,50 a alkalmazni virtuális géphez társított díja szerint számítjuk fel, és a egy másik 0,50 díj típusú díja `UnusedReservation`. Vegye figyelembe, hogy költségeket a fel nem használt foglalás látható csak amikor megtekinti amortizált költségek.

Hogyan szerepelnek az költségek változása miatt fontos megjegyezni, hogy a tényleges költségek, és amortizált költségek nézetek különböző teljes számok jelennek meg. Általában a teljes költsége a hónap egy foglalásvásárlás csökkennek amortizált költségek megtekintésekor, és növeli a foglalásvásárlást követő hónap során. Amortizációs csak foglalás vásárlások érhető el, és az Azure piactéren történt vásárlást jelenleg nem vonatkozik.

Az alábbi ábrán látható erőforrások nevei. Teljes költség / címke vagy használhatják a címke szerint csoportosíthatók az **költségek erőforrás szerint** nézet egy adott erőforráshoz tartozó összes címkéinek megtekintéséhez.

![Összes adat megjelenítése az erőforrás csoportnevek aktuális nézet](./media/quick-acm-cost-analysis/full-data-set.png)

Egy adott attribútum csoportosítás költségeit, ha a felső 10 közreműködőkkel láthatók legmagasabbtól a legalacsonyabb. Ha több mint 10, a felső kilenc közreműködőkkel látható egy **mások** csoport. Ez a csoport tartalmazza az összes többi csoport együtt. Ha a címkék és csoportosítás egy **Untagged** csoport, amelyek nem rendelkeznek a alkalmazni a címkekulcs költségek jelenik meg. **Címkézetlen** mindig utolsó, akkor is, ha a címkézetlen költségek magasabbak címkézett költségeket. A címkézetlen költségek része lesz **mások**, ha létezik legalább 10 címke értéke.

Klasszikus virtuális gépekhez, hálózati és tárolási erőforrások nem adjuk ki a részletes számlázási adatok. Ezek van egyesítve **klasszikus szolgáltatások** Ha csoportosítási költségeket.

A fő diagram megjelenítése a különböző elérésű csoportosítás, amely a kijelölt időszak és a szűrők az általános költségek szélesebb körű képet ad a kimutatásdiagramok. Válasszon egy tulajdonságot vagy címke minden dimenzió összesített költségeit megtekintésére.

![Példa ábrázoló kimutatásdiagramok](./media/quick-acm-cost-analysis/pivot-charts.png)

A teljes adatkészlet bármely nézetben tekintheti meg. Bármelyik beállításokat vagy a alkalmazni szűrők hatással a megjelenített adatokhoz. A teljes adatkészlet megtekintéséhez válassza a **diagramtípus** listában, majd **tábla** megtekintése.

![Aktuális nézet egy tábla adatainak megtekintése](./media/quick-acm-cost-analysis/chart-type-table-view.png)


## <a name="understanding-grouping-and-filtering-options"></a>Understanding csoportosítás és szűrési lehetőségeit

Az alábbi táblázat a leggyakoribb csoportosítás és szűrési lehetőségek és mikor érdemes használni azokat.

| Tulajdonság | A következő esetekben használja |
| --- | --- |
| **Elszámolási időszak** | Költségek felosztania számla hónap szerint. Ez a beállítás akkor fontos a használatalapú fizetés és a fejlesztési és tesztelési előfizetéseket, ami nem kötött naptári hónapban. Nagyvállalati szerződések/MCA fiókok naptári hónapban a dátumválasztó vagy havi granularitási segítségével hajthatók végre ugyanezt a célt. |
| **Díj típusa** | Használati, vásárlási, visszatérítési és a fel nem használt foglalás költségek felosztania. Foglalás beszerzés és a visszatérítéseket érhetők el csak a műveleti költségeket használatakor, és nem amortizált költségek használatakor. Fel nem használt foglalás költségek csak akkor érhetők el az amortizált költségek. |
| **Felhő** | AWS és az Azure költségeket felosztania. AWS-költségek csak a felügyeleti csoportok, a külső számlázási fiókok és a külső előfizetések érhetők el. |
| **Részleg** / **számlázása szakasz** | Nagyvállalati szerződéssel rendelkező részlegtől vagy MCA számla szakasz költségek felosztania. Ez a beállítás csak a nagyvállalati szerződéssel rendelkező/MCA számlázási fiókok és profilok számlázási MCA érhető el. |
| **Eszközregisztráció-fiók** | Nagyvállalati szerződéssel rendelkező fiók tulajdonosa költségeket felosztania. Ez a beállítás csak a nagyvállalati szerződés számlázási fiókok és a részlegek számára érhető el. |
| **Gyakoriság** | Használat alapú, egyszeri és ismétlődő költségek felosztania. |
| **Meter** | Költségek felosztania által az Azure használati díjában. Ez a beállítás csak az Azure-használat esetén érhető el. Vásárlás és a piactér használati állapotúként lesz megjelölve **nincs megadva** vagy **ki nem osztott**. |
| **Közzétevő típusa** | Az AWS, az Azure Marketplace-en és költségek felosztania. |
| **Foglalás** | A Foglalás felosztania költségeket. Minden használatot, amely nem tartalmazza a Foglalás állapotúként lesz megjelölve **nincs megadva**. |
| **Erőforrás** | Felosztania költségek erőforrás szerint. Minden vásárláshoz állapotúként lesz **nincs megadva**, mivel azokat már alkalmazza a nagyvállalati szerződéssel rendelkező/Használatalapú számlázási fiók, vagy profil szint számlázási MCA.  |
| **Erőforráscsoport** | Felosztania költségeket. Ez a beállítás csak a nem klasszikus használati érhető el. Klasszikus erőforrások felhasználásának állapotúként lesz megjelölve **más**, és a vásárlások állapotúként lesz **nincs megadva**. |
| **Erőforrás típusa** | Díjak erőforrás típusa szerint felosztania. Ez a beállítás csak a nem klasszikus használati érhető el. Klasszikus erőforrások felhasználásának állapotúként lesz megjelölve **más**, és a vásárlások állapotúként lesz **nincs megadva**. |
| **Szolgáltatásnév** vagy **mérőszám kategóriája** | Az Azure-szolgáltatás által felosztania költség. Ez a beállítás csak az Azure-használat esetén érhető el. Vásárlás és a piactér használati állapotúként lesz megjelölve **nincs megadva** vagy **ki nem osztott**. |
| **Szolgáltatási szint** vagy **fogyasztásmérő alkategóriája** | Azure-használatát mérő subclassification szerint lebontva költség. Ez a beállítás csak az Azure-használat esetén érhető el. Vásárlás és a piactér használati állapotúként lesz megjelölve **nincs megadva** vagy **ki nem osztott**. |
| **Előfizetés** | Előfizetés-felosztania költségeket. Minden vásárláshoz állapotúként **nincs megadva**. |
| **Tag** | Költségek felosztania által egy adott címke kulcsával címkeértékeit. |

Feltételek kapcsolatos további információkért lásd: [megismerheti az Azure-beli használat és költségek fájlmegosztás használt kifejezések](../billing/billing-understand-your-usage.md).


## <a name="saving-and-sharing-customized-views"></a>Mentése és megosztása személyre szabott nézetek

Mentse, és testre szabott nézetek megosztja másokkal, az Azure portal Irányítópultjára költségelemzés rögzítésével vagy hivatkozás költségelemzés másolja. 

Költségelemzés rögzít, válassza a gombostű ikonra a jobb felső sarokban. Rögzítés költségelemzés csak a fő diagram és táblázat nézet menti. Ossza meg másokkal hozzáférést biztosíthat a csempét az irányítópulton. Vegye figyelembe, hogy ez csak az Irányítópult konfigurációja közös, és nem hozzáférést biztosítani másoknak az alapul szolgáló adatokat. Ha nem rendelkezik hozzáféréssel a költségek, de egy megosztott irányítópulthoz hozzáféréssel rendelkező, látni fogja a egy "hozzáférés megtagadva" üzenet.

Elemzés mutató hivatkozás megosztása, jelölje be **megosztása** a panel tetején. Jelennek meg egy egyéni URL-címet, amely megnyitja az adott nézet adott hatókörre. Ha nem rendelkezik költség eléréséhez, és az URL-cím lekérése, látni fogja a egy "hozzáférés megtagadva" üzenet. 

Az egyes támogatott hatókörök költségek hozzáférésének engedélyezésére vonatkozó további tudnivalókért tekintse át a [megismerése és együttműködnek a hatókörök](understand-work-scopes.md).

## <a name="automation-and-offline-analysis"></a>Automation és a kapcsolat nélküli elemzéshez

Vannak olyan helyzetek, amikor meg kell letölteni az adatokat további elemzés céljából, vonja össze a saját adataival, vagy integrálják őket, a saját rendszerekhez. A Cost Management több különböző lehetőséget kínál. Kiindulási pontként Ha szüksége kaphat belül költségelemzés, például egy ad-hoc magas szintű összegzés nézet létrehozásához a van szüksége. Ezután töltse le a kiválasztásával **exportálása** , és válassza **adatok letöltése CSV** vagy **töltse le az adatokat az Excelbe**. Az Excel letöltés a további környezet, amellyel a letöltést, például a hatókör létrehozása, a lekérdezések a konfiguráció teljes, és a date nézeten létrehozott tartalmazza.

Ha a teljes, unaggregated adatkészlet van szüksége, töltse le a számlázási fiók. Ezt követően a szolgáltatások listája a portál bal oldali navigációs panelen, lépjen a **Költségkezelés + számlázás**. Válassza ki a számlázási fiók, ha van ilyen. Lépjen a **használat és költségek**, majd válassza ki a **letöltése** ikonra a kívánt számlázási időszak.

A megközelítést fogadó költségadatok automatizálásához. Használja a [Query API](/rest/api/cost-management/query) részletesebb elemzéshez dinamikus szűréshez, csoportosítás, és összesítés vagy használja a [UsageDetails API](/rest/api/consumption/usageDetails) esetében a teljes adatkészlet unaggregated. A következő API-k általánosan (elérhetővé tétel GA) verzió a 2019-01-01. Használat **2019-04-01-preview** előzetes verziójával a foglalást és a Marketplace-en történt vásárlások viszont ezen API-k eléréséhez. 

Ha például az alábbiakban a összesített nézete az amortizált költségek típus szerinti bontásban díj (használat, beszerzési vagy visszatérítés), közzétevő típusa (Azure-ban vagy a Marketplace-en), a erőforráscsoportot (üres vásárlásokra) és a Foglalás (üres, ha nem alkalmazható).

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

És ha nem kell az összesítést, és igény szerint a teljes, nyers adatkészlet:

```
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

Ha van szüksége, amelyek felhalmozott beszerzések megjelenítése, módosíthatják a tényleges költségek **típus**/**metrika** való **ActualCost**. Ezen API-k kapcsolatos további információkért lásd: a [lekérdezés](/rest/api/cost-management/query) és [UsageDetails](/rest/api/consumption/usageDetails) API dokumentációja. Vegye figyelembe, hogy a közzétett docs a végleges verzió. Azonban mindkettő működik, ugyanezt a *2019-04-01-preview* kívül az új típus vagy mérőszám attribútum és a módosított tulajdonságnevek API-verzió. (További információ az alábbi tulajdonságnevek.)
 
Felügyeleti API-k munkahelyi költsége a fenti erőforrások összes hatókörökön belül: erőforráscsoport, az előfizetés és a felügyeleti csoporthoz Azure RBAC hozzáférés, nagyvállalati szerződés számlázási fiókok (regisztrációk), részlegek és regisztrációs fiókok segítségével a Nagyvállalati portál elérésére. További tudnivalók a hatókörök, beleértve a hatókör-azonosító meghatározása, és kezelheti a hozzáférést, a [megismerése és együttműködnek a hatókörök](understand-work-scopes.md).

## <a name="next-steps"></a>További lépések

Lépjen tovább az első oktatóanyaghoz, amely a költségvetés létrehozását és kezelését mutatja be.

> [!div class="nextstepaction"]
> [Költségvetések létrehozása és kezelése](tutorial-acm-create-budgets.md)
