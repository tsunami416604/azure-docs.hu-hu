---
title: Rövid útmutató – Az Azure-költségek feltérképezése költségelemzés használatával | Microsoft Docs
description: Ez a rövid útmutató bemutatja, hogyan vizsgálhatja meg és elemezheti vállalata Azure-költségeit a Költségelemzés szolgáltatás használatával.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: quickstart
ms.service: cost-management-billing
manager: micflan
ms.custom: seodec18
ms.openlocfilehash: f053b30d344e5372617a5bf98c087056c4fe2911
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76294150"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Gyorsútmutató: Költségek feltérképezése és elemzése költségelemzés használatával

Ahhoz, hogy érdemben szabályozhassa és optimalizálhassa Azure-költségeit, fontos, hogy tudja a vállalaton belül hol keletkeznek ezek a költségek. Emellett nem árt, ha tudja, mennyibe kerülnek az egyes szolgáltatások, és milyen környezeteket vagy rendszereket szolgálnak ki. A költségek teljes spektrumának ismerete elengedhetetlen a szervezet kiadásaiban fellépő mintázatok pontos megértéséhez. A kiadásoknál fellépő mintázatok segítségével költségszabályozó mechanizmusok, például költségkeretek léptethetők érvénybe.

Ebben a rövid útmutatóban költségelemzés használatával megvizsgálja és elemzi a szervezet költségeit. Az összesített költségeket szervezetekre lebontva jelenítheti meg, így jobban átláthatja, hogy az idő múlásával mely területeken keletkeznek költségei és felismerheti a kiadások trendjeit. Megtekintheti az egy adott időszak során felhalmozott költségeket is, így havi, negyedéves vagy akár éves költségtrendbecsléseket is készíthet a költségvetéshez viszonyítva. Költségvetés használatával könnyebb betartani a pénzügyi korlátozásokat. Ezen felül a költségvetés segít áttekinteni napi vagy havi szintű költségeit és elkülöníteni a rendhagyó kiadásokat. Az adatokat le is töltheti további elemzés vagy külső rendszerben való használat céljából.

Ezen rövid útmutató segítségével megtanulhatja a következőket:

- Költségek áttekintése a Költségelemzés használatával
- Költségnézetek testreszabása
- Költségelemzési adatok letöltése


## <a name="prerequisites"></a>Előfeltételek

A költségelemzés különböző Azure-fióktípusokat támogat. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](understand-cost-mgt-data.md). A költségadatok megtekintéséhez legalább olvasási jogosultsággal kell rendelkeznie az Azure-fiókjához.

További információért az Azure Cost Management adataihoz való hozzáférés hozzárendeléséről: [Adatokhoz való hozzáférés hozzárendelése](../../cost-management/assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

- Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="review-costs-in-cost-analysis"></a>Költségek áttekintése a Költségelemzés használatával

A költségek a költségelemzésben való áttekintéséhez nyissa meg a hatókört az Azure Portalon, majd válassza ki a **Költségelemzés** lehetőséget a menüben. Például lépjen az **Előfizetések** pontra, válasszon ki egy előfizetést a listában, majd válassza a **Költségelemzés** menüpontot. A **Hatókör** elemmel másik hatókörre válthat a költségelemzésben. További információ a hatókörökről: [A hatókörök ismertetése és használata](understand-work-scopes.md).

A választott hatókör az adatkonszolidációt és a költségadatokhoz való hozzáférés szabályozását határozza meg a költségkezelés során. A hatókörök használata során nem lehet egyszerre több hatókört is kiválasztani. Ehelyett egy magasabb szintű hatókört választ ki, amely alá több alacsonyabb szintű hatókör is tartozik, és szűrő alkalmazásával keresi meg a kívánt beágyazott hatóköröket. Ezt a megközelítést azért fontos megérteni, mert nem biztos, hogy minden személy rendelkezik hozzáféréssel egyetlen szülőhatókörhöz, amely több beágyazott hatókört is magába foglal.

A kezdő költségelemzési nézet a következő területeket foglalja magába.

**Halmozott költség nézet**: A költségelemzés előre definiált nézete. Minden nézet tartalmaz dátumtartományra, részletességre, csoportosítási szempontra és szűrésre vonatkozó beállításokat. Az alapértelmezett nézet az aktuális számlázási időszakra vonatkozó halmozott költségeket mutatja, de átválthat a többi beépített nézetre. További információ: [Költségnézetek testreszabása](#customize-cost-views).

**Tényleges költség**: Az aktuális hónap összes keletkező és a számlán megjelenő használati és vásárlási költségét mutatja.

**Előrejelzés**: A választott időszak összes előrevetített költségét jeleníti meg. (Az előrejelzés előzetes verzióban érhető el.)

**Költségkeret**: A kijelölt hatókör tervezett költségkeretét jeleníti meg, amennyiben az elérhető.

**Halmozott részletesség**: Megjeleníti az összes összesített napi kiadást a számlázási időszak kezdetétől számítva. Miután számlázási fiókjához vagy előfizetéséhez [létrehozta költségvetését](tutorial-acm-create-budgets.md), itt gyorsan, költségkeretéhez viszonyítottan áttekintheti kiadási trendjeit. A kurzort egy adott dátum felé helyezve megtekintheti az adott nap halmozott költségeit.

**Kimutatásdiagramok (fánkdiagramok)** : Dinamikus kimutatásokat tesznek lehetővé azzal, hogy a teljes költséget alapvető jellemzők alapján bontják le. Az aktuális hónap költségeit jelenítik meg a legnagyobbaktól a legkisebbekig. A kimutatásdiagramokat bármikor módosíthatja másik kimutatás kiválasztásával. A költségek alapértelmezetten a következő kategóriákba vannak sorolva: szolgáltatás (fogyasztásmérő kategóriája), hely (régió), valamint gyermekhatókör. Például a regisztrációs fiókok a számlázási fiókok alatt, az erőforráscsoportok az előfizetések alatt, az erőforrások pedig az erőforráscsoportok alatt jelennek meg.

![A költségelemzés kezdőnézete az Azure Portalon](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Költségnézetek testreszabása

A költségelemzés négy beépített nézettel rendelkezik, amelyek a leggyakoribb célokra vannak optimalizálva:

Nézet | A következőkhöz hasonló kérdésekre ad választ:
--- | ---
Halmozott költség | Mennyit költöttem eddig ebben a hónapban? Belül maradok-e a költségkeretemen?
Napi költség | Növekedett a napi költségszint az elmúlt 30 napban?
Díj szolgáltatás szerint | Hogyan változott a havi használat a legutóbbi három számla szerint?
Költségek erőforrások szerint | Eddig a folyó hónapban melyik erőforrások kerültek a legtöbbe?

![Nézetválasztó az erre a hónapra vonatkozó kiválasztást bemutató példával](./media/quick-acm-cost-analysis/view-selector.png)

Azonban sok esetben ennél mélyebb elemzésre is szükség lehet. A testreszabást az oldal tetején a dátum kiválasztásánál kezdhetjük.

Alapértelmezetten a költségelemzés az aktuális hónap adatait jeleníti meg. A dátumválasztóval gyorsan válthat néhány általános dátumtartomány között. Ilyen például az elmúlt hét nap, a múlt hónap, az aktuális év vagy egy egyéni dátumtartomány. A használatalapú előfizetéseknél a számlázási időszakon alapuló, a naptári hónaptól független dátumtartományok is elérhetők, például az aktuális számlázási időszak vagy az utolsó számla. A menü tetején az **<ELŐZŐ** és a **KÖVETKEZŐ>** hivatkozásokkal léphet az előző vagy a következő időszakra. Például az **<ELŐZŐ** hivatkozásra kattintva az **előző 7 nap** helyett a **8–14 nappal ezelőtti**, majd a **15–21 nappal ezelőtti** tartomány jelenik meg.

![Dátumválasztó az erre a hónapra vonatkozó kiválasztást bemutató példával](./media/quick-acm-cost-analysis/date-selector.png)

A költségelemzés alapértelmezetten a **Halmozott** költségeket jeleníti meg. A halmozott költségek az egyes napok költségét, valamint a megelőző napok költségeit foglalják magukba, tehát a napi költségek folyamatos halmozódásának nyomon követésére szolgálnak. A nézetet arra optimalizáltuk, hogy megvizsgálhassa, hogyan alakulnak költségei az adott időszakban a tervezett költségvetéshez képest.

Az előrejelzési diagram nézetén megállapíthatja, hogy fennáll-e a költségkeret-túllépés esélye. A potenciális költségkeret-túllépéseknél az előrevetített túlköltekezés pirossal jelenik meg. A diagramon ilyenkor egy figyelmeztető szimbólum is látható. Ha a mutatót a szimbólum fölé veszi, megjelenik a költségkeret-túllépés becsült dátuma.

![Lehetséges költségkeret-túllépést bemutató példa](./media/quick-acm-cost-analysis/budget-breach.png)

Emellett a **napi** nézet használatával az egyes napok költségeit is megtekintheti. A napi nézet nem jeleníti meg a növekedési trendeket. A nézet célja a szabálytalanságok, vagyis a kiugróan magas vagy alacsony értékek megjelenítése a napról napra jelentkező költségekben. Ha költségvetést is kiválasztott, a napi nézet a napi költségkeret becslését is megjeleníti.

Ha a napi költségei következetesen meghaladják a becsült napi költségkeretet, várhatóan a havi költségkeretét is túl fogja lépni. A becsült napi költségkeret egy olyan eszköz, amellyel könnyebben áttekintheti költségvetése kisebb alkotóelemeit. Ha napi költségei ingadozóak, a becsült napi költségkeret viszonya a havi költségkeretével kevésbé lesz pontos.

A legutóbbi költések napi nézete a költségek előrejelzésével így néz ki.
![Példa az aktuális hónap napi költségeit ábrázoló napi nézetre](./media/quick-acm-cost-analysis/daily-view.png)

Ha kikapcsolja a kiadások előrejelzését, a jövőbeli dátumok esetében nem látja a tervezett kiadásokat. Továbbá, ha a múltbeli időszakokra vonatkozó költségeket tekinti meg, a költség-előrejelzés nem jelenít meg költségeket.

Általánosságban elmondható, hogy a felhasznált erőforrásokra vonatkozó adatok vagy értesítések 8–12 órán belül jelennek meg.


**Csoportosítást** végezhet általános tulajdonságok alapján a költségek lebontásához és a legfontosabb közreműködők azonosításához. Az erőforráscímkék szerinti csoportosításhoz például válassza ki azt a címkekulcsot, amely alapján csoportosítani szeretne. A költségek az egyes címkeértékek szerint vannak lebontva egy külön szegmenssel az olyan erőforrások számára, amelyeken nincs alkalmazva az adott címke.

A legtöbb [Azure-erőforrás támogatja a címkézést](../../azure-resource-manager/management/tag-support.md). Ugyanakkor néhány címke nem érhető el a Költségkezelés és számlázás területen. Továbbá az erőforráscsoport-címkék sem támogatottak. A címkék támogatása a címke erőforráson való alkalmazása *utáni* jelentett használatra vonatkozik. A címkék nem jelennek meg visszamenőlegesen a költségösszesítések esetében.

További információért a költségadatok láthatóságának Azure-címkeszabályzat használatával történő javításáról tekintse meg a [Címkeszabályzatok áttekintése az Azure Cost Managementtel](https://www.youtube.com/watch?v=nHQYcYGKuyw) című videót.

Íme egy nézet arról, hogyan alakultak az Azure-szolgáltatások költségei az aktuális hónapban.

![Csoportosított napi halmozott nézet az Azure-szolgáltatások múlt hónapi költségeivel](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Alapértelmezés szerint a költségelemzés az összes keletkező és számlán megjelenő használati és vásárlási költséget, más néven a **Tényleges költséget** mutatja. A tényleges költség ideális arra, hogy egyeztetni tudja a számlát. A költségekben jelentkező kiugró vásárlások mindazonáltal aggodalmakra adhatnak okot, ha nem szeretne rendellenes kiadásokat vagy a költségekben jelentkező egyéb változásokat látni. A foglalásvásárlási költségek által okozott kiugró csúcsok kisimításához váltson **amortizált költségekre**.

![Váltson a tényleges és az amortizált költség között, hogy megtekinthesse a teljes időszakra leosztott, illetve a foglalást használó erőforrásokhoz rendelt foglalásvásárlásokat](./media/quick-acm-cost-analysis/metric-picker.png)

Az amortizált költség nézete a foglalásvásárlási díjakat napi adagokra osztja, és ezeket egyenletesen elosztja a foglalási időszak teljes tartamára. Például ahelyett, hogy január 1-én kiugrana egy 365 dollár értékű vásárlás, január 1-től december 31-ig minden nap egy 1 dollár értékű vásárlás jelenik meg. Az alapvető amortizáción felül ezek a költségek a foglalást használó konkrét erőforrásokhoz vannak rendelve, és közöttük vannak szétosztva. Ha például az 1 dolláros napi díj két virtuális gép között oszlik meg, két 0,50 dolláros napi díj jelenik meg. Ha a foglalás egyik része az adott napon nincs felhasználva, a megfelelő virtuális gépnél egy 0,50 dolláros díj látható, egy másik 0,50 dolláros díj pedig `UnusedReservation` díjtípussal jelenik meg. Vegye figyelembe, hogy a fel nem használt foglalási költségek csak akkor láthatók, ha az amortizált költségeket jeleníti meg.

Fontos megjegyezni, hogy a költségek megjelenítésének eltérései miatt a tényleges és az amortizált költség nézete eltérő végösszeget mutat. Általánosságban elmondható, hogy az amortizált költségek megtekintése esetén a foglalásvásárlást tartalmazó hónapok teljes költsége csökken, a foglalásvásárlásokat követő hónapok költségei pedig növekednek. Az amortizáció csak foglalásvásárlások esetén érhető el, és jelenleg az Azure Marketplace-beli vásárlásokra nem vonatkozik.

A következő képen az erőforráscsoportok nevei olvashatók. Csoportosíthat címkék alapján a teljes költség címkék szerinti megtekintéséhez, vagy használhatja a **Költség erőforrás szerint** nézetet egy adott erőforráshoz tartozó összes címke megtekintéséhez.

![A jelenlegi nézet összes adata az erőforráscsoport-nevek megjelenítésével](./media/quick-acm-cost-analysis/full-data-set.png)

Ha a költségeket egy adott attribútum szerint csoportosítja, csökkenő sorrendben megjelenik a tíz legfontosabb költségelem. Ha tíznél több ilyen elem van, akkor a kilenc legfontosabb költségelem mellett megjelenik egy **Egyéb** csoport is, amelyben az összes többi csoport együttesen szerepel. Címkék szerinti csoportosítás esetén egy **Címkézetlen** csoport jelenik meg azon költségekkel, amelyek nem rendelkeznek a címkekulccsal. Mindig a **Címkézetlen** csoport az utolsó, még akkor is, ha a címkézetlen költségek magasabbak a címkézett költségeknél. 10 vagy több címkeérték esetén a címkézetlen költségek az **Egyéb** csoport részei lesznek. Váltson táblanézetre, és módosítsa a részletességet **Nincs** értékre az összes érték ár szerinti csökkenő sorrendben való megjelenítéséhez.

A klasszikus virtuális gépek, hálózatkezelési és tárolási erőforrások nem osztanak meg részletes számlázási adatokat. Ezek a költségek csoportosításakor a **Klasszikus szolgáltatások** csoportba kerülnek.

A fő diagram alatti kimutatásdiagramok különböző csoportosításokat jelenítenek meg, így átfogó képet nyújtanak a teljes költségről a kiválasztott időszaknak és szűrőknek megfelelően. Válasszon ki egy tulajdonságot vagy címkét az összesített költségek bármely dimenzió alapján való megtekintéséhez.

![Példa kimutatásdiagramokra](./media/quick-acm-cost-analysis/pivot-charts.png)

Bármelyik nézet esetében megtekintheti a teljes adathalmazt. Az alkalmazott beállítások vagy szűrők hatással vannak a bemutatott adatokra. A teljes adathalmaz megtekintéséhez kattintson a **diagramtípus** listára, majd válassza ki a **Táblázat** nézetet.

![A jelenlegi nézet adatai táblázatos nézetben](./media/quick-acm-cost-analysis/chart-type-table-view.png)


## <a name="understanding-grouping-and-filtering-options"></a>Csoportosítási és szűrési lehetőségek ismertetése

Az alábbi táblázat felsorolja a leggyakoribb csoportosítási és szűrési lehetőségek némelyikét, valamint ismerteti, hogy mikor érdemes alkalmazni őket.

| Tulajdonság | A következő esetekben használja | Megjegyzések |
| --- | --- | --- |
| **Rendelkezésre állási zónák** | Az AWS-költségek lebontása rendelkezésreállási zóna szerint. | Csak AWS-hatókörök és felügyeleti csoportok esetén alkalmazható. Az Azure-beli adatok nem tartalmazzák a rendelkezésreállási zónát, így ezeknél a **Nem alkalmazható** érték jelenik meg. |
| **Számlázási időszak** | A PAYG-költségek lebontása a számlázási hónap szerint. | A **Számlázási időszak** segítségével pontos képet kaphat a számlázott PAYG-díjakról. Foglaljon bele további 2 napot a számlázási időszak előtt és után, ha egy egyéni dátumtartományra szűr. A pontos számlázási időszakra való korlátozás nem fog egyezni a számlával. A számlázási időszak összes számlájára vonatkozó költségek fognak megjelenni. Egy adott számlára a **Számlaazonosító** segítségével szűrhet. Csak PAYG-előfizetésekre alkalmazható, mivel az EA és az MCA számlázása a naptári hónapok szerint történik. Az EA-/MCA-fiókok esetében ugyanebből a célból naptári hónapok vagy havi részletesség is használható a dátumválasztóban. |
| **Díj típusa** | A használat, a vásárlás, a visszatérítés és a nem használt foglalási költségek lebontása. | A foglalásvásárlások és a visszatérítések csak a tényleges költségek használatakor érhetők el, amortizált költségek esetén nem. A fel nem használt foglalási költségek csak akkor érhetők el, ha az amortizált költségeket jeleníti meg. |
| **Részleg** | Költségek lebontása EA-részlegek szerint. | Csak az EA és a felügyeleti csoportok számára érhető el. A PAYG-előfizetéseknek nincs részlege, így **Nem alkalmazható** vagy **nem hozzárendelt** értéket fognak megjeleníteni. |
| **Regisztrációs fiók** | Költségek lebontása EA-fióktulajdonos szerint. | Csak az EA-számlázási fiókok, -részlegek és felügyeleti csoportok számára érhető el. A PAYG-előfizetések nem rendelkeznek EA regisztrációs fiókokkal, így **Nem alkalmazható** vagy **nem hozzárendelt** értéket fognak megjeleníteni. |
| **Gyakoriság** | A használaton alapuló, az egyszeri és az ismétlődő költségek lebontása. | |
| **Számlaazonosító** | Költségek lebontása kiállított számla alapján. | A ki nem számlázott díjak még nem rendelkeznek számlaazonosítóval, így az EA-költségek nem tartalmazzák a számla részleteit, valamint **Nem alkalmazható** értéket fognak megjeleníteni.  |
| **Fogyasztásmérő** | Költségek lebontása használatmérés alapján. | A vásárlások és a Marketplace-használat **Nem alkalmazható** értékként jelennek meg. A vásárlások azonosításához tekintse meg a **Díj típusa** értéket, a Marketplace-díjak azonosításához pedig a **Kiadó típusa** értéket. |
| **Művelet** | Az AWS-költségek lebontása műveletek szerint. | Csak AWS-hatókörök és felügyeleti csoportok esetén alkalmazható. Az Azure-beli adatok nem tartalmaznak műveletet, így ezeknél a **Nem alkalmazható** érték jelenik meg – használja helyette a **Fogyasztásmérő** lehetőséget. |
| **Díjszabási modell** | A költségek igény, foglalás vagy spot-használat szerinti lebontása. | A vásárlások **OnDemand** értékként jelennek meg. Ha **Nem alkalmazható** értéket lát, a **Foglalás** szerinti csoportosítással megállapíthatja, hogy foglalásról vagy igény szerinti használatról van-e szó, a **Díj típusa** szerintivel pedig azonosíthatja a vásárlásokat.
| **Szolgáltató** | Költségek lebontása AWS és Azure szerint. | Csak felügyeleti csoportok számára érhető el. |
| **Kiadó típusa** | Az AWS, az Azure és a Marketplace költségeinek lebontása. |  |
| **Foglalás** | Költségek lebontása foglalás szerint. | A foglaláshoz nem társított használat vagy vásárlások **Nem alkalmazható** értékként jelennek meg. A **Kiadó típusa** szerinti csoportosítással azonosíthatja az egyéb Azure-, AWS- vagy Marketplace-vásárlásokat. |
| **Erőforrás** | Költségek lebontása erőforrás szerint. | A vásárlások **Nem alkalmazható** értékként jelennek meg, mert egy EA-/PAYG-számlázási fiók vagy egy MCA-számlázási profil szintjén vannak alkalmazva, és nem egy adott erőforráshoz vannak társítva. A **Kiadó típusa** szerinti csoportosítással azonosíthatja az egyéb Azure-, AWS- vagy Marketplace-vásárlásokat. |
| **Erőforráscsoport** | Költségek lebontása erőforráscsoport szerint. | A vásárlások, az előfizetéshez nem társított bérlői erőforrások, az erőforráscsoporthoz nem üzembe helyezett előfizetési erőforrások, illetve a klasszikus erőforrások nem rendelkeznek erőforráscsoporttal, így **egyebek**, **klasszikus szolgáltatások**, **$system** vagy **nem alkalmazható** értéket mutatnak. |
| **Erőforrás típusa** | Költségek lebontása erőforrástípus szerint. | A vásárlások és a klasszikus szolgáltatások nem rendelkeznek Azure Resource Manager-erőforrástípussal, így **egyebek**, **klasszikus szolgáltatások** vagy **nem alkalmazható** értéket mutatnak. |
| **Erőforrás helye** | Költségek lebontása hely vagy régió szerint. | A vásárlások és a Marketplace-használat **nem hozzárendelt**, **ismeretlen**, **nem leképezett** vagy **nem alkalmazható** értékként jelenhetnek meg. |
| **Szolgáltatásnév** vagy **Fogyasztásmérő kategóriája** | Költségek lebontása az Azure-szolgáltatás szerint. | A vásárlások és a Marketplace-használat **nem alkalmazható** vagy **nem hozzárendelt** értékként jelennek meg. |
| **Szolgáltatásszint** vagy **Fogyasztásmérő alkategóriája** | A költségek lebontása az Azure fogyasztásmérőjének osztályozási alkategóriái alapján. | A vásárlások és a Marketplace-használat **nem alkalmazható** vagy **nem hozzárendelt** értékként jelennek meg. |
| **Előfizetés** | Költségek lebontása az Azure-előfizetés és az AWS csatolt fiókja alapján. | A vásárlások és a bérlői erőforrások **nem alkalmazható** értékként jelenhetnek meg. |
| **Tag** | Költségek lebontása egy adott címkekulcs címkeértékei alapján. | A címkék nem érhetők el a vásárlásokhoz, az előfizetésekhez nem társított bérlői erőforrásokhoz, az erőforráscsoporthoz nem üzembe helyezett előfizetési erőforrásokhoz vagy a klasszikus erőforrásokhoz. Vegye figyelembe, hogy egyes szolgáltatások nem tartalmaznak címkéket a használati adatokban. További információ [a címkék támogatásáról az egyes erőforrástípusokra vonatkozóan](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support). |

A kifejezésekkel kapcsolatos további információ: [Az Azure használati adatait és díjait tartalmazó fájl kifejezéseinek ismertetése](../understand/understand-usage.md).


## <a name="saving-and-sharing-customized-views"></a>Személyre szabott nézetek mentése és megosztása

A személyre szabott nézeteket a költségelemzés az Azure Portal irányítópultján való rögzítésével vagy a költségelemzésre mutató hivatkozás másolásával mentheti és oszthatja meg másokkal.

A költségelemzés rögzítéséhez kattintson a rögzítés ikonra a jobb felső sarokban. A költségelemzés rögzítése csak a fő diagramot vagy a táblanézetet fogja menteni. Az irányítópult megosztásával biztosítása másoknak is hozzáférést a csempéhez. Vegye figyelembe, hogy ez csak az irányítópult konfigurációját osztja meg, és nem biztosít hozzáférést a mögöttes adatokhoz. Ha nincs hozzáférése a költségekhez, de hozzáfér egy megosztott irányítópulthoz, a „hozzáférés megtagadva” üzenetet fog látni.

A költségelemzésre mutató hivatkozás megosztásához válassza a panel tetején lévő **Megosztás** lehetőséget. Egy egyéni URL-cím fog megjelenni, amely megnyitja az adott hatókörre vonatkozó nézetet. Ha nincs hozzáférése a költséghez, az URL-cím használatakor a "hozzáférés megtagadva" üzenet jelenik meg.

Az egyes támogatott hatókörök költségeihez való hozzáférés megadásával kapcsolatban tekintse meg [A hatókörök ismertetése és használata](understand-work-scopes.md) címe részt.

## <a name="automation-and-offline-analysis"></a>Automatizálás és offline elemzés

Előfordulhat, hogy további elemzés céljából le kell töltenie az adatait, egyesíteni kell őket a saját adataival, vagy integrálnia kell őket a saját rendszereibe. A Cost Management több különböző lehetőséget kínál. Ha egy olyan alkalmi jellegű, részletes összegzésre van szüksége, mint amilyen a költségelemzésben is elérhető, hozza létre a szükséges nézetet. Ezt követően töltse le az **Exportálás**, majd az **Adatok letöltése CSV-fájlba** vagy az  **Adatok letöltése Excel-fájlba** lehetőség kiválasztásával. Az Excel-fájlba történő letöltés további információt nyújt a letöltés létrehozására használt nézetről, például a hatókörről, a lekérdezéskonfigurációról, a teljes összegről és a létrehozás dátumáról.

Ha a teljes, nem összesített adatkészletre van szüksége, töltse le a számlázási fiókból. Ezután a portál bal oldali navigációs paneljén található szolgáltatáslistából válassza ki a **Költségkezelés + Számlázás** lehetőséget. Válassza ki a számlázási fiókját, ha van. Lépjen a **Felhasználás + díjak** oldalra, majd kattintson a kívánt számlázási időszak **Letöltés** ikonjára.

Hasonló módon automatizálhatja a költségadatok lekérését is. A [Query API](/rest/api/cost-management/query) dinamikus szűrést, csoportosítást és összesítést alkalmazó gazdagabb elemzésekhez használható, a [UsageDetails API](/rest/api/consumption/usageDetails) pedig a teljes, nem aggregált adatkészlethez. Az API-k általánosan elérhető (GA) verziója a 2019-01-01-es. Az API-kon belüli foglalás- és a Marketplace-vásárlások előzetes verziójának eléréséhez használja a **2019-04-01-preview** előzetes verziót.

A következő példa amortizált költségek összesített nézetét mutatja be díjtípusok (használat, vásárlás vagy visszatérítés), a kiadó típusa (Azure vagy Marketplace), az erőforráscsoport (a vásárlások esetében üres) és a foglalás (üres, ha nem alkalmazható) szerinti lebontásban.

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

Ha nincs szüksége összesítésre, és a teljes, nyers adathalmazt részesíti előnyben:

```
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

Ha azt szeretné, hogy a tényleges költségekben a vásárlások keletkezésük szerinti jelenjenek meg, módosítsa a **típus**/**metrikát** **ActualCost** értékre. Az ezekkel az API-kkal kapcsolatos további információért tekintse meg a [Query](/rest/api/cost-management/query) és a [UsageDetails](/rest/api/consumption/usageDetails) API-dokumentációt. Vegye figyelembe, hogy a közzétett dokumentumok az általánosan elérhető verzióra vonatkoznak. Ugyanakkor mindkettő alkalmazható a *2019-04-01-preview* API-verzióra az új típus/metrika attribútum és a módosított tulajdonságnevek kivételével. (A tulajdonságnevekkel kapcsolatos részleteket alább találja.)

A Cost Management API-k az erőforrások feletti összes hatókörben működnek: erőforráscsoport, előfizetés és felügyeleti csoport az Azure RBAC-hozzáférésen keresztül, EA számlázási fiókok (regisztrációk), részlegek és regisztrációs fiókok az EA-portálhozzáférésen keresztül. További információ a hatókörökről, beleértve a hatókör azonosítójának meghatározását és a hozzáférés-kezelést: [A hatókörök ismertetése és használata](understand-work-scopes.md).

## <a name="next-steps"></a>További lépések

Lépjen tovább az első oktatóanyaghoz, amely a költségvetés létrehozását és kezelését mutatja be.

> [!div class="nextstepaction"]
> [Költségvetések létrehozása és kezelése](tutorial-acm-create-budgets.md)
