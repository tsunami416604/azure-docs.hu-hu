---
title: A felhőalapú befektetések optimalizálása a Azure Cost Managementkal | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan érheti el a legtöbbet a Felhőbeli befektetésekből, csökkentheti a költségeit, és kiértékelheti, hogy a pénzét hogyan költi el
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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75988382"
---
# <a name="how-to-optimize-your-cloud-investment-with-azure-cost-management"></a>A felhőalapú befektetések optimalizálása Azure Cost Management

Az Azure Cost Managementtel megkapja azokat az eszközöket, amelyekkel tervezhet a költségekkel, illetve elemezheti és csökkentheti őket, hogy a legtöbbet hozhassa ki felhőszolgáltatásaiból. Ez a dokumentum módszert kínál a Cost Management módszerének kialakítására, és kiemeli a szervezete által felmerülő kihívásokkal kapcsolatos problémák megoldásához elérhető eszközöket. Az Azure-ral egyszerűen hozhat létre és helyezhet üzembe felhőmegoldásokat. Azonban a költségek minimalizálása érdekében fontos, hogy ezek a megoldások optimalizálva legyenek. A jelen dokumentumban ismertetett alapelvek és az eszközök használata segít meggyőződni arról, hogy a szervezet készen áll a sikerre.

## <a name="methodology"></a>Módszertan

A Cost Management szervezeti probléma, és olyan folyamatban lévő gyakorlatnak kell lennie, amely a Felhőbeli erőforrások pénzének megkezdése előtt kezdődik. A Cost Management és a költségek optimalizálásának sikeres megvalósításához a szervezetnek a következőket kell tennie:

- Készüljön fel a megfelelő eszközökre a siker érdekében
- A költségekért felelős
- A költségek optimalizálása érdekében tegye meg a megfelelő lépéseket

Az alább vázolt három kulcsfontosságú csoportot a szervezetében kell összehangolni, hogy a költségek sikeresen kezelhetők legyenek.

- **Pénzügy** – a szervezeten belüli költségvetési kérelmek jóváhagyásáért felelős személyek a felhőalapú kiadások előrejelzései alapján. Kifizetik a megfelelő számlát, és kiosztják a költségeket a különböző csapatoknak, hogy az elszámoltathatóságot vezessenek.
- **Vezetők** – az üzleti döntéshozók egy olyan cégnél, amelyeknek meg kell ismerniük a Felhőbeli kiadásokat, hogy megtalálják a legjobb kiadási eredményeket.
- **Alkalmazás-csapatok** – a felhőalapú erőforrásokat napi rendszerességgel kezelő mérnökök, a szervezet igényeinek megfelelő szolgáltatások fejlesztése. Ezeknek a csapatoknak rugalmasan kell megadniuk a legtöbb értéket a meghatározott költségvetésekben.

### <a name="key-principles"></a>Legfontosabb alapelvek

Az alábbiakban ismertetett alapelvek segítségével meghelyezheti a szervezetét a Felhőbeli Cost Management szolgáltatásban.

#### <a name="planning"></a>Tervezés

Az átfogó, előzetes tervezés lehetővé teszi, hogy a felhőhasználatot az adott üzleti igényeknek megfelelően szabja testre. Kérdezzen rá:

- Milyen üzleti problémákat oldok meg?
- Milyen használati mintákra számítok a saját erőforrásaim kapcsán?

A válaszok segítségével kiválaszthatja az Ön számára legmegfelelőbb ajánlatokat. Meghatározzák a használni kívánt infrastruktúrát, valamint azt, hogy miként használhatók az Azure hatékonyságának maximalizálására.

#### <a name="visibility"></a>Láthatóság

A jól strukturált Cost Management segít tájékoztatni mindenkit a felelősségük alá tartozó Azure-költségekről, illetve az általuk elköltött pénzről. Az Azure-ban olyan szolgáltatások vannak kialakítva, amelyek betekintést *nyújtanak a* pénzbe. Használja ki ezeket az eszközöket. Segítséget nyújthatnak a felhasznált erőforrások megtalálásához, a hulladék eltávolításához és a költségmegtakarítási lehetőségek maximalizálásához.

#### <a name="accountability"></a>Accountability

Jelöljön ki felelőst a szervezet költségeihez, hogy elszámoltathatók legyenek a csapatuk költségeiért. Hogy jobban átlássa a szervezet Azure-költségeit, az erőforrásait úgy kell szerveznie, hogy maximális betekintést nyerjen a költségek elosztásáról. A jó szervezés segít a költségek kezelésében és csökkentésében, valamint a hatékony pénzkiadás felelőseinek elszámoltatásában a szervezeten belül.

#### <a name="optimization"></a>Optimalizálás

Tegyen azért, hogy alacsonyabbak legyenek a költségei. Hozza ki a legtöbbet a megállapításokból, amelyeket a tervezés és a költségek jobb átláthatósága révén tett. A jelen dokumentum későbbi részében részletesen tárgyalt infrastruktúra-telepítési módosítások mellett érdemes megfontolni a vásárlási és licencelési optimalizálást is.

#### <a name="iteration"></a>Iteráció

A szervezetben mindenkinek részt kell vennie a költségkezelési életciklusban. Folyamatosan részt kell venniük a folyamatban a költségek optimalizálása érdekében. Legyen szigorú ezzel az iteratív folyamattal kapcsolatban, és tegye a felelős felhőszabályozás kulcsfontosságú feladatává a szervezetén belül.

![A legfontosabb alapelvek ábrája a láthatóságot, az elszámoltathatóságot és az optimalizálást mutatja](./media/cost-mgt-best-practices/principles.png)

## <a name="plan-with-cost-in-mind"></a>Tervezzen a költségeket szem előtt tartva

A felhőalapú erőforrások üzembe helyezése előtt mérje fel a következő elemeket:

- Az Azure-ajánlat, amely a legjobban megfelel az Ön igényeinek
- A használni kívánt erőforrások
- Mennyibe kerülhetnek

Az Azure olyan eszközöket biztosít, amelyek segítséget nyújtanak az értékelés folyamatában. Az eszközök hasznos segítséget nyújtanak a számítási feladatok végrehajtásához szükséges beruházásokhoz. Ezután kiválaszthatja a helyzetének legmegfelelőbb konfigurációját.

### <a name="azure-onboarding-options"></a>Az Azure bevezetési lehetőségei

Az első lépés a Cost Managementon belüli élmény maximalizálása, hogy kivizsgálja és eldöntse, melyik Azure-ajánlat a legmegfelelőbb Önnek. Gondolja át, hogyan tervezi használni az Azure-t a jövőben. Azt is gondolja át, hogyan konfigurálja a számlázási modellt. A döntés meghozatalakor vegye figyelembe a következő kérdéseket:

- Mennyi ideig szeretném használni az Azure-t? Tesztelem vagy megtervezem a hosszú távú infrastruktúra kiépítését?
- Hogyan szeretne fizetni az Azure-ral kapcsolatban? Kell-e fizetnem, ha kedvezményes árat fizetnem, vagy a hónap végén számlázunk?

Ha többet szeretne megtudni a különböző lehetőségekről, tekintse meg az [Azure megvásárlását](https://azure.microsoft.com/pricing/purchase-options/)ismertető témakört. Az alábbiakban a leggyakoribb számlázási modellek közül többet talál.

#### <a name="freehttpsazuremicrosoftcomfree"></a>[Ingyenes](https://azure.microsoft.com/free/)

- 12 hónapig ingyenes népszerű szolgáltatások
- $200 kredit a szolgáltatások 30 napig való megismeréséhez
- 25 + szolgáltatás mindig ingyenes

#### <a name="pay-as-you-gohttpsazuremicrosoftcomoffersms-azr-0003p"></a>[Használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p)

- Nincs minimum díj és kötelezettségvállalás
- Versenyképes díjszabás
- Csak a valóban használt funkciókért kell fizetni
- Bármikor lemondható

#### <a name="enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement"></a>[Nagyvállalati Szerződés](https://azure.microsoft.com/pricing/enterprise-agreement/)

- A kezdeti pénzügyi kötelezettségvállalások lehetőségei
- Hozzáférés az Azure csökkentett díjszabásához

## <a name="estimate-the-cost-of-your-solution"></a>A megoldás díjainak becslése

Az infrastruktúra üzembe helyezése előtt mérje fel, hogy Mennyibe kerül a megoldás. Az értékelés segít létrehozni egy költségvetést a szervezet számára a számítási feladatokhoz. Ezt követően az idő függvényében költségvetést is használhat a kezdeti becslés érvényességének meghatározásához. És összehasonlíthatja az üzembe helyezett megoldás tényleges költségeit.

### <a name="azure-pricing-calculator"></a>Azure díjkalkulátor

Az Azure díjszabási számológépe lehetővé teszi, hogy az Azure-szolgáltatások különböző kombinációit kombinálva megtekintse a költségek becslését. A megoldás a különböző Azure-módokon valósítható meg – az egyes lehetőségek befolyásolhatják a teljes kiadást. A Felhőbeli üzembe helyezés összes infrastruktúra-igényével kapcsolatos korai megoldás az eszköz leghatékonyabb használatát teszi lehetővé. Az Azure-ban becsült kiadásaira vonatkozó becslést nyújt.

További információ: [Azure díjszabási kalkulátor](https://azure.microsoft.com/pricing/calculator).

### <a name="azure-migrate"></a>Azure Migrate

Azure Migrate egy olyan szolgáltatás, amely a szervezet aktuális számítási feladatait vizsgálja a helyszíni adatközpontokban. Betekintést nyújt az Azure-beli helyettesítő megoldásokra. Először is az áttelepítés megvizsgálja a helyszíni gépeket annak meghatározására, hogy a Migrálás megvalósítható-e. Ezt követően a virtuális gépek méretezését javasolja az Azure-ban a teljesítmény maximalizálása érdekében. Végül egy Azure-alapú megoldáshoz is létrehoz egy költségbecslést.

További információ: [Azure Migrate](../../site-recovery/migrate-overview.md).

## <a name="analyze-and-manage-your-costs"></a>A költségek elemzése és kezelése

Tájékoztassa a szervezete költségeinek időbeli alakulásáról. A következő módszerekkel megfelelően értelmezheti és kezelheti a kiadásait.

### <a name="organize-and-tag-your-resources"></a>Erőforrások rendszerezése és címkézése

Vegye figyelembe az erőforrásokat a költségeket szem előtt tartva. Az előfizetések és erőforráscsoportok létrehozásakor gondolja át a kapcsolódó költségekért felelős csapatokat. Győződjön meg arról, hogy a jelentéskészítés szem előtt tartja a szervezetét. Az előfizetések és az erőforráscsoportok jó gyűjtőket biztosítanak a vállalaton belüli kiadások rendszerezéséhez és az azokhoz való hozzárendeléséhez. A címkék jó módot biztosítanak a költséghatékonyságra. A címkéket szűrőként használhatja. Az adatelemzéshez és a költségek kivizsgálásához használhatja őket a csoportosításhoz. Nagyvállalati Szerződés ügyfeleink létrehozhatnak részlegeket és helyezhetnek előfizetéseket. Az Azure-beli Cost-alapú szervezet segít megőrizni az érintett személyeket a szervezete számára, hogy csökkentsék a csapatuk költségeit.

### <a name="use-cost-analysis"></a>A Cost Analysis használata

A Cost Analysis lehetővé teszi a szervezeti költségek részletes elemzését azáltal, hogy a szokásos erőforrás-tulajdonságok használatával kihasználja a költségeket, és Kockázás azokat. Az elemzéshez tekintse át az alábbi gyakori kérdéseket. Ha rendszeresen válaszol ezekre a kérdésekre, könnyebben tájékozódhat, és további tudatos döntéseket hozhat.

- **Az aktuális hónap becsült költségei** – mennyit vettem fel eddig ebben a hónapban? Maradok a költségkeret alatt?
- **Anomáliák vizsgálata** – a rutin ellenőrzi, hogy a költségek a normál használat ésszerű skáláján belül maradnak-e. Mik a trendek? Vannak kiugró értékek?
- **Számla-egyeztetés** – a legújabb számlázott díja az előző hónapnál nagyobb? Hogyan változtatták meg a kiadási szokások a havi hónapot?
- **Belső** jóváírás – most, hogy tudom, mennyit kell fizetnem, hogyan kell lebontania ezeket a díjakat a szervezetem számára?

További információ: [Cost Analysis](quick-acm-cost-analysis.md).

### <a name="export-billing-data-on-a-schedule"></a>Számlázási információk exportálása ütemterv szerint

Importálnia kell a számlázási adatait egy külső rendszerbe, például egy irányítópultra vagy egy pénzügyi rendszerre? Automatikus exportálás beállítása az Azure Storage-ba, és a fájlok manuális letöltése havonta. Ezután egyszerűen beállíthatja az automatikus integrációt más rendszerekkel, hogy a számlázási adatait szinkronban tartsa.

További információ a számlázási adatok exportálásáról: [exportált adatok létrehozása és kezelése](tutorial-export-acm-data.md).

### <a name="create-budgets"></a>Költségvetések létrehozása

Miután azonosította és elemezte a kiadások mintáit, fontos, hogy megkezdje a saját és a csapatának korlátozását. Az Azure-költségvetések lehetővé teszi, hogy egy költség-vagy használaton alapuló költségvetést is beállítson számos küszöbértékkel és riasztással. Mindenképpen tekintse át a rendszeresen létrehozott költségvetéseket, hogy megtekintse a költségvetés leégésének előrehaladását, és szükség szerint módosítsa a módosításokat. Az Azure-költségvetések lehetővé teszik az automatizálási trigger konfigurálását is, ha a rendszer elérte az adott költségvetési küszöbértéket. Beállíthatja például, hogy a szolgáltatás leállítsa a virtuális gépeket. Az infrastruktúrát másik díjszabási szinten helyezheti át egy költségvetési triggerre válaszul.

További információ: Azure- [költségvetés](tutorial-acm-create-budgets.md).

A költségvetésen alapuló automatizálással kapcsolatos további információkért lásd: a [költségvetés-alapú automatizálás](../manage/cost-management-budget-scenario.md).

## <a name="act-to-optimize"></a>Optimalizálás
A kiadások optimalizálásához kövesse az alábbi módszereket.

### <a name="cut-out-waste"></a>Kivágási hulladék

Miután üzembe helyezte az infrastruktúrát az Azure-ba, fontos, hogy az valóban ki legyen használva. A legegyszerűbben úgy érhet el azonnali megtakarítást, ha áttekinti az erőforrásait, és eltávolítja azokat, amelyek nincsenek használva. Itt meg kell határoznia, hogy az erőforrásokat a lehető leghatékonyabb módon használják-e.

#### <a name="azure-advisor"></a>Azure Advisor

Azure Advisor egy olyan szolgáltatás, amely többek között a CPU-vagy hálózati használati szempontból alacsony kihasználtságú virtuális gépeket azonosítja. Onnan dönthet úgy, hogy a számítógépek futtatásának folytatásához a becsült költségek alapján leállítja vagy átméretezi a gépet. Az Advisor a fenntartott példányok vásárlására vonatkozó javaslatokat is tartalmaz. A javaslatok a virtuális gépek utolsó 30 napjától függenek. A javaslatok segítségével csökkentheti a kiadásokat.

További információ: [Azure Advisor](../../advisor/advisor-overview.md).

### <a name="size-your-vms-properly"></a>Virtuális gépek méretének megfelelő méretezése

A virtuális gépek méretezése jelentős hatással van a teljes Azure-díjszabásra. Előfordulhat, hogy az Azure-ban szükséges virtuális gépek száma nem felel meg a helyszíni adatközpontban jelenleg üzembe helyezett virtuális gépek számának. Győződjön meg arról, hogy a megfelelő méretet választotta a futtatni kívánt munkaterhelések számára.

További információt az [Azure IaaS: megfelelő méretezéssel és a költséghatékonysággal](https://azure.microsoft.com/resources/videos/azurecon-2015-azure-iaas-proper-sizing-and-cost/)foglalkozó témakörben talál.

### <a name="use-purchase-discounts"></a>Vásárlási kedvezmények használata

Az Azure számos kedvezményt biztosít, amelyeket a szervezetnek ki kell használnia a pénz megtakarítása érdekében.

#### <a name="azure-reservations"></a>Azure Reservations

Azure Reservations lehetővé teszi, hogy egy-vagy hároméves virtuális gép vagy SQL Database számítási kapacitás előfizetését. Az előzetes fizetés lehetővé teszi, hogy kedvezményt kapjon a használt erőforrásokhoz. Az Azure-foglalások jelentősen csökkenthetik a virtuális gépeket vagy az SQL-adatbázis számítási költségeit – akár 72%-ot az utólagos elszámolású árakon egy-vagy hároméves előzetes kötelezettségvállalással. A Reservations számlázási kedvezményt nyújt, és nincs hatással a virtuális gép vagy az SQL Database-adatbázisok futtatási állapotára.

További információ: [Mi a Azure Reservations?](../reservations/save-compute-costs-reservations.md)

#### <a name="use-azure-hybrid-benefit"></a>Használja az Azure Hybrid Benefitet

Ha már rendelkezik Windows Server-vagy SQL Server-licenccel a helyszíni üzemelő példányokban, a Azure Hybrid Benefit program használatával mentheti az Azure-ban. A Windows Server előnyei közé tartozik, hogy minden licenc magában foglalja az operációs rendszer költségét (legfeljebb két virtuális géphez), és csak az alap számítási költségeket kell kifizetnie. A meglévő SQL Server-licenccel akár 55%-ot is megtakaríthat a virtuálismag-alapú SQL Database-lehetőségek költségéből. Ilyen például az Azure Virtual Machinesben az SQL Server és az SQL Server Integration Services.

További információ: [Azure Hybrid Benefit megtakarítási kalkulátor](https://azure.microsoft.com/pricing/hybrid-benefit/).

### <a name="other-resources"></a>Egyéb háttéranyagok

Az Azure-ban olyan szolgáltatások is elérhetők, amelyek lehetővé teszik az Azure-beli többletkapacitás kihasználását a kedvezményes díjszabás érdekében. További információ: [alacsony prioritású virtuális gépek használata a Batch használatával](../../batch/batch-low-pri-vms.md).

## <a name="next-steps"></a>Következő lépések
- Ha még nem Cost Management, olvassa el a [Mi a Azure Cost Management?](../cost-management-billing-overview.md) című témakört, amelyből megtudhatja, hogyan segíti az Azure-kiadások monitorozását és szabályozását, valamint az erőforrások használatának optimalizálását.
