---
title: A felhővel kapcsolatos befektetés optimalizálása az Azure Cost Management használatával | Microsoft Docs
description: Ez a cikk segítséget nyújt a felhővel kapcsolatos befektetések leghatékonyabb kihasználásához, a költségek csökkentéséhez és annak kiértékeléséhez, mire költ pénzt.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: 47a53136a8f299e5c8654d122d8e319be4ae29db
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "75988382"
---
# <a name="how-to-optimize-your-cloud-investment-with-azure-cost-management"></a>A felhővel kapcsolatos befektetés optimalizálása az Azure Cost Management használatával

Az Azure Cost Management biztosítja azokat az eszközöket, amelyekkel tervezhet a költségekkel, illetve elemezheti és csökkentheti őket, hogy a legtöbbet hozhassa ki a felhővel kapcsolatos befektetéseiből. Ez a dokumentum módszertani megközelítést biztosít a költségkezeléshez, és felhívja a figyelmet a szervezet költségekkel kapcsolatos kihívásainak leküzdéséhez rendelkezésre álló eszközökre. Az Azure-ral egyszerűen hozhat létre és helyezhet üzembe felhőmegoldásokat. Azonban a költségek minimalizálása érdekében fontos, hogy ezek a megoldások optimalizálva legyenek. A jelen dokumentumban ismertetett alapelvek követésével és az eszközeink használatával biztosíthatja, hogy a szervezet készen álljon a sikerre.

## <a name="methodology"></a>Módszertan

A költségkezelés egy szervezeti probléma, amellyel már a felhőerőforrásokba való befektetés előtt el kell kezdeni foglalkozni. A költségkezelés sikeres megvalósításához és a költségek optimalizálásához a szervezetnek:

- Fel kell készülnie a sikerre a megfelelő eszközökkel
- Elszámoltathatónak kell lennie a költségek tekintetében
- Meg kell tennie a költségek optimalizálásához szükséges intézkedéseket

Az alábbi három fő csoportnak összehangoltan kell működnie a szervezeten belül ahhoz, hogy biztosítható legyen a költségkezelés sikeressége.

- **Pénzügyi részleg** – Azok a személyek, akik a költségvetési kérelmek jóváhagyásáért felelnek a szervezetben, a felhőköltség-előrejelzések alapján. Ők fizetik be a megfelelő számlákat, és az elszámoltathatóság ösztönzése érdekében költségeket rendelnek hozzá a különböző csapatokhoz.
- **Vezetők** – A szervezet üzleti döntéshozói. Tisztában kell lenniük a felhőköltségekkel a kiadások optimalizálásához.
- **Alkalmazásokat kezelő csapatok** – Azok a mérnökök, akik napi szinten kezelik a felhőerőforrásokat, és a szervezet igényeinek megfelelően fejlesztik a szolgáltatásokat. Ezeknek a csapatoknak rugalmasnak kell lenniük ahhoz, hogy a lehető legtöbbet hozhassák ki az adott költségvetésből.

### <a name="key-principles"></a>Alapelvek

Az alább ismertetett alapelvek segítségével a siker útjára állíthatja szervezetét a felhőköltség-kezelés terén.

#### <a name="planning"></a>Tervezés

Az átfogó, előzetes tervezés lehetővé teszi, hogy a felhőhasználatot az adott üzleti igényeknek megfelelően szabja testre. Tegye fel magának a következő kérdéseket:

- Milyen üzleti problémákat oldok meg?
- Milyen használati mintákra számítok a saját erőforrásaim kapcsán?

A válaszok segítségével kiválaszthatja az Ön számára legmegfelelőbb ajánlatokat. Meghatározzák a használni kívánt infrastruktúrát, valamint hogy az miként használható az Azure hatékonyságának maximalizálására.

#### <a name="visibility"></a>Láthatóság

A jól strukturált Cost Management segít tájékoztatni mindenkit a felelősségük alá tartozó Azure-költségekről, illetve az általuk elköltött pénzről. Egyes Azure-szolgáltatások segítségével betekintést nyerhet abba, hogy *mire* költ pénzt. Érdemes használni ezeket az eszközöket. Segíthetnek megtalálni a kihasználatlan erőforrásokat, megszüntetni a pazarlást és maximálisan kihasználni a költségmegtakarítási lehetőségeket.

#### <a name="accountability"></a>Elszámoltathatóság

Ossza fel a költségeket a szervezeten belül, hogy az egyes személyek elszámoltathatók legyenek a csapatuk költéseiért. Hogy jobban átlássa a szervezet Azure-költségeit, az erőforrásait úgy kell szerveznie, hogy maximális betekintést nyerjen a költségek elosztásáról. A jó szervezés segít a költségek kezelésében és csökkentésében, valamint a hatékony pénzkiadás felelőseinek elszámoltatásában a szervezeten belül.

#### <a name="optimization"></a>Optimalizálás

Tegyen azért, hogy alacsonyabbak legyenek a költségei. Hozza ki a legtöbbet a megállapításokból, amelyeket a tervezés és a költségek jobb átláthatósága révén tett. Érdemes fontolóra venni a dokumentum későbbi szakaszában ismertetett optimalizálási megoldások megvásárlását vagy licencelését, illetve az infrastruktúra üzembe helyezésének módosítását.

#### <a name="iteration"></a>Iteráció

A szervezetben mindenkinek részt kell vennie a költségkezelési életciklusban. Folyamatosan részt kell venniük a folyamatban a költségek optimalizálása érdekében. Legyen szigorú ezzel az iteratív folyamattal kapcsolatban, és tegye a felelős felhőszabályozás kulcsfontosságú feladatává a szervezetén belül.

![Az alapelveket (láthatóság, elszámoltathatóság és optimalizálás) bemutató ábra](./media/cost-mgt-best-practices/principles.png)

## <a name="plan-with-cost-in-mind"></a>Költségtudatos tervezés

A felhőerőforrások üzembe helyezése előtt értékelje ki a következőket:

- Az igényeinek legjobban megfelelő Azure-ajánlat
- A használni tervezett erőforrások
- Az ezekhez kapcsolódó, várható költségek

Az Azure biztosít olyan eszközöket, amelyek segítségére lehetnek az értékelési folyamatban. Ezek az eszközök áttekintést adhatnak a számítási feladatok végrehajtásához szükséges befektetésről. Ezt követően kiválaszthatja az adott helyzetnek legmegfelelőbb konfigurációt.

### <a name="azure-onboarding-options"></a>Előkészítési lehetőségek az Azure-ban

A Cost Management nyújtotta felhasználói élmény maximalizálásának első lépése annak megvizsgálása és eldöntése, hogy melyik Azure-ajánlat a legmegfelelőbb az Ön számára. Gondolja át, hogyan szeretné használni az Azure-t a jövőben. Azt is fontolja meg, hogyan szeretné konfigurálni a számlázási modellt. A döntés meghozatalakor vegye figyelembe a következő kérdéseket:

- Mennyi ideig tervezem használni az Azure-t? Csak tesztelem, vagy hosszú távon használt infrastruktúrát tervezek kiépíteni?
- Hogyan szeretnék fizetni az Azure használatáért? Előre fizessek, kedvezményes áron, vagy inkább a hó végi számlázást válasszam?

A különböző lehetőségekkel kapcsolatos további információkért lásd [az Azure megvásárlásával foglalkozó részt](https://azure.microsoft.com/pricing/purchase-options/). Alább néhány példát láthat a leggyakoribb számlázási modellekre.

#### <a name="freehttpsazuremicrosoftcomfree"></a>[Ingyenes](https://azure.microsoft.com/free/)

- 12 hónapig ingyenesen használható, népszerű szolgáltatások
- 200 dollár értékű kredit a szolgáltatások 30 napos használatához
- 25+ mindig ingyenes szolgáltatás

#### <a name="pay-as-you-gohttpsazuremicrosoftcomoffersms-azr-0003p"></a>[Használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p)

- Nincs minimális költési szint és elköteleződés
- Versenyképes díjszabás
- Csak a valóban használt funkciókért kell fizetni
- Bármikor lemondható

#### <a name="enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement"></a>[Nagyvállalati Szerződés](https://azure.microsoft.com/pricing/enterprise-agreement/)

- Lehetőségek az előzetes pénzügyi kötelezettségvállalásra
- Hozzáférés a kedvezményes Azure-díjszabáshoz

## <a name="estimate-the-cost-of-your-solution"></a>Költségbecslés az adott megoldáshoz

Az infrastruktúra üzembe helyezése előtt mérje fel, hogy mennyibe fog kerülni az adott megoldás. A kiértékelés segítségével előzetes költségvetést biztosíthat a szervezet számára a számítási feladathoz. Ezután az idővel folyamatosan frissített költségvetés segítségével megállapíthatja a kezdeti becslés érvényességét. Emellett a kezdeti becslést az üzembe helyezett megoldás tényleges költségével is összehasonlíthatja.

### <a name="azure-pricing-calculator"></a>Azure-díjkalkulátor

Az Azure-díjkalkulátor lehetővé teszi a különböző Azure-szolgáltatások kombinálását a költségbecslés megtekintéséhez. A megoldást különböző módokon valósíthatja meg az Azure-ban – ezek mindegyike hatással lehet a teljes költségre. Ha már a kezdeti szakaszban átgondolja a felhőbeli üzembe helyezés infrastrukturális igényeit, még hatékonyabban használhatja az eszközt. Az eszköz segítségével megbízható becslést adhat a becsült Azure-beli kiadásokról.

További információ: [Azure-díjkalkulátor](https://azure.microsoft.com/pricing/calculator).

### <a name="azure-migrate"></a>Azure Migrate

Az Azure Migrate egy olyan szolgáltatás, amely kiértékeli a szervezet aktuális számítási feladatait a helyszíni adatközpontokban. Betekintést nyújt az Ön számára abba, hogy mire lehet szüksége egy helyettesítő Azure-megoldástól. A Migrate először a helyszíni gépek vizsgálatát végzi el, hogy meghatározza a migrálás megvalósíthatóságát. Ezután javaslatot tesz egy Azure-beli virtuálisgép-méretre a teljesítmény maximalizálásához. Végül pedig megbecsüli az Azure-alapú megoldás költségét.

További információ: [Azure Migrate](../../site-recovery/migrate-overview.md).

## <a name="analyze-and-manage-your-costs"></a>Költségek elemzése és kezelése

Kövesse figyelemmel, hogyan változnak a szervezet költségei az idő múlásával. Használja az alábbi technikákat a költségek megfelelő értelmezéséhez és kezeléséhez.

### <a name="organize-and-tag-your-resources"></a>Erőforrások rendszerezése és címkézése

Rendszerezze költségtudatosan az erőforrásokat. Az előfizetések és erőforráscsoportok létrehozásakor gondoljon a kapcsolódó költségekért felelős csapatokra. Ügyeljen rá, hogy a jelentéskészítés során érvényesüljenek a szervezet szempontjai. Az előfizetések és az erőforráscsoportok kiválóan használható gyűjtők a költségek szervezeten belüli rendszerezéséhez és elosztásához. A címkék jó módszert kínálnak a költségek elosztására. A címkéket szűrőként is lehet használni. A címkéket csoportosításra is használhatja az adatok elemzésekor és a költségek vizsgálatakor. A Nagyvállalati Szerződéssel rendelkező ügyfelek részlegeket is létrehozhatnak, és előfizetéseket rendelhetnek azokhoz. Az Azure-beli költségalapú szervezés segít abban, hogy a szervezet megfelelő személyei folyamatosan elszámoltathatók maradjanak a csapataik költéseiért.

### <a name="use-cost-analysis"></a>Költségelemzés használata

A költségelemzés a költségek standard erőforrás-tulajdonságok segítségével történő felbontásával lehetővé teszi a szervezeti költségek részletes elemzését. Fontolja meg az alábbi gyakori kérdések elemzési útmutatóként való kezelését. Ha rendszeresen megválaszolja ezeket a kérdéseket, tájékozott maradhat, és költségtudatosabb döntéseket hozhat.

- **Az aktuális hónap becsült költségei** – Mennyi költségem keletkezett eddig ebben a hónapban? Belül maradok-e a költségkereten?
- **Anomáliák vizsgálata** – Végezzen rutinellenőrzéseket annak biztosításához, hogy a költségek a normál használat észszerű tartományán belül maradjanak. Mik a trendek? Vannak kiugró értékek?
- **Számla egyeztetése** – Meghaladják a legutóbb kiszámlázott költségeim az azt megelőző havit? Hogyan változtak a költési szokások hónapról hónapra?
- **Belső költséghelyi elszámolás** – Most, hogy tudom, mennyit számítottak fel, hogyan bontsam szét ezeket a díjakat a szervezet esetében?

További információért lásd a [költségelemzéssel foglalkozó részt](quick-acm-cost-analysis.md).

### <a name="export-billing-data-on-a-schedule"></a>A számlázási adatok exportálása ütemezés alapján

Importálnia kell a számlázási adatokat egy külső rendszerbe, például irányítópultba vagy pénzügyi rendszerbe? Állítson be automatikus exportálást az Azure Storage-ba, hogy ne kelljen minden hónapban manuálisan letöltenie a fájlokat. Ezután egyszerűen beállíthatja az automatikus integrációt más rendszerekkel a számlázási adatok szinkronizálásához.

További információ a számlázási adatok exportálásáról: [Exportált adatok létrehozása és kezelése](tutorial-export-acm-data.md).

### <a name="create-budgets"></a>Költségvetések létrehozása

A kiadásoknál fellépő mintázatok azonosítását és elemzését követően fontos, hogy elkezdjen korlátokat meghatározni saját maga és csapatai számára. Az Azure-költségvetések lehetővé teszik költség- vagy használatalapú költségvetések beállítását, több küszöbértékkel és riasztással. Tekintse át rendszeresen a létrehozott költségvetéseket, hogy figyelemmel kísérhesse azok kimerülését, és szükség esetén módosíthassa őket. Az Azure-költségvetések egy, a költségvetési küszöbérték elérésekor aktiválódó automatizálási trigger konfigurálását is lehetővé teszik. Konfigurálhatja például úgy a szolgáltatást, hogy az leállítsa a virtuális gépeket. Azt is megteheti, hogy a költségvetési triggerre reagálva módosítja az infrastruktúra tarifacsomagját.

További információ: [Azure-költségvetések](tutorial-acm-create-budgets.md).

A költségvetés-alapú automatizálásról további információért lásd a [költségvetés-alapú automatizálással foglalkozó részt](../manage/cost-management-budget-scenario.md).

## <a name="act-to-optimize"></a>Optimalizálási műveletek
A költést az alábbi módszerekkel optimalizálhatja.

### <a name="cut-out-waste"></a>Pazarlás megszüntetése

Miután üzembe helyezte az infrastruktúrát az Azure-ba, fontos, hogy az valóban ki legyen használva. A legegyszerűbben úgy érhet el azonnali megtakarítást, ha áttekinti az erőforrásait, és eltávolítja azokat, amelyek nincsenek használva. Ezt követően érdemes megvizsgálni, hogy a lehető leghatékonyabban használja-e az erőforrásokat.

#### <a name="azure-advisor"></a>Azure Advisor

Az Azure Advisor egy olyan szolgáltatás, amely többek között azonosítja a processzor- vagy hálózati használat szempontjából alacsony kihasználtságú virtuális gépeket. Ezután a gépek további használatának becsült költsége alapján eldöntheti, hogy leállítja vagy átméretezi az adott gépet. Az Advisor emellett javaslatokkal is szolgál a fenntartott példányok vásárlásával kapcsolatban. Ezek a javaslatok az elmúlt 30 nap virtuálisgép-használatán alapulnak. A javaslatok végrehajtásával csökkentheti a költéseit.

További információ: [Azure Advisor](../../advisor/advisor-overview.md).

### <a name="size-your-vms-properly"></a>Virtuális gépek megfelelő méretezése

A virtuális gépek méretezése jelentősen befolyásolja az Azure összköltségét. Előfordulhat, hogy az Azure esetében szükséges virtuális gépek száma nem egyezik meg a helyszíni adatközpontban üzembe helyezett virtuális gépek számával. Ügyeljen rá, hogy a megfelelő méretet válassza a futtatni tervezett számítási feladatokhoz.

További információért lásd az [Azure IaaS megfelelő méret- és költségbeállításával foglalkozó részt](https://azure.microsoft.com/resources/videos/azurecon-2015-azure-iaas-proper-sizing-and-cost/).

### <a name="use-purchase-discounts"></a>Vásárlási kedvezmények kihasználása

Az Azure számos kedvezményt kínál, amelyekkel a szervezet pénzt takaríthat meg.

#### <a name="azure-reservations"></a>Azure Reservations

Az Azure Reservations lehetővé teszi a virtuális gép vagy az SQL Database számítási kapacitásának kifizetését egy vagy három évre előre. Az előre fizetés kedvezményt nyújt a felhasznált erőforrásokra. Az Azure Reservationsszel jelentősen csökkenthetők a virtuális gép vagy az SQL Database számítási költségei, a használatalapú fizetéshez képest akár 72%-kal, egy- vagy hároméves előzetes kötelezettségvállalás esetén. A Reservations számlázási kedvezményt nyújt, és nincs hatással a virtuális gép vagy az SQL Database-adatbázisok futtatási állapotára.

További információ: [Mi az az Azure Reservations?](../reservations/save-compute-costs-reservations.md).

#### <a name="use-azure-hybrid-benefit"></a>Használja az Azure Hybrid Benefitet

Ha a helyszíni környezetekben Windows Server- vagy SQL Server-licencet használ, az Azure Hybrid Benefit programmal megtakarításra tehet szert az Azure-ban. A Windows Server előnyei közé tartozik, hogy minden licenc magában foglalja az operációs rendszer költségét (legfeljebb két virtuális géphez), és csak az alap számítási költségeket kell kifizetnie. A meglévő SQL Server-licenccel akár 55%-ot is megtakaríthat a virtuálismag-alapú SQL Database-lehetőségek költségéből. Ilyen például az Azure Virtual Machinesben az SQL Server és az SQL Server Integration Services.

További információért lásd az [Azure Hybrid Benefit megtakarításkalkulátorát ismertető részt](https://azure.microsoft.com/pricing/hybrid-benefit/).

### <a name="other-resources"></a>Egyéb erőforrások

Az Azure olyan szolgáltatást is biztosít, amellyel a kedvezményes díjszabás érdekében az Azure többletkapacitását kihasználó szolgáltatásokat hozhat létre. További információ: [Alacsony prioritású virtuális gépek használata a Batch szolgáltatással](../../batch/batch-low-pri-vms.md).

## <a name="next-steps"></a>További lépések
- Ha először használja a Cost Managementet, olvassa el a [Mi az Azure Cost Management?](../cost-management-billing-overview.md) című cikket, amely ismerteti, hogy milyen segítséget nyújt ez a szolgáltatás az Azure-költségek monitorozásához és szabályozásához, valamint az erőforrás-használat optimalizálásához.
