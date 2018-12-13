---
title: Az Azure Cost Managementbe a felhőbe való befektetéséből optimalizálása |} A Microsoft Docs
description: Ez a cikk segít a lehető leghatékonyabban ki a felhőbefektetések, csökkentse a költségeket, és értékeléséhez, ahol a pénzt töltött.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: 77a5b455e3ef86484119983af24b6461a2fba229
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074487"
---
# <a name="how-to-optimize-your-cloud-investment-with-azure-cost-management"></a>Az Azure Cost Managementbe a felhőbe való befektetéséből optimalizálása

Az Azure Cost Management segít azon eszközök tervezése, elemzése, és csökkentheti költségeit a felhőbe való befektetéséből maximalizálása érdekében. Ez a dokumentum a cost Management módszeres megközelítést biztosít, és kiemeli az Ön számára elérhető eszközöket, akkor kihívásaira a szervezet költség. Az Azure egyszerűen készíthet és helyezhet üzembe felhőalapú megoldásokat. Azonban fontos, hogy ezek a megoldások minimálisra csökkentheti a szervezet számára van optimalizálva. Az eszközök segítségével, győződjön meg arról, hogy a szervezet a jelen dokumentum és a használatával leírt elveket követő sikeres kész.

## <a name="methodology"></a>Módszer

A Cost management egy szervezeti probléma, és lehet egy folyamatban lévő eljárás, amely előtt a felhőbeli erőforrások előzetesen pénzt kezdődik. Sikeres végrehajtása Költségkezelés, és optimalizálhatja a költségeket, a szervezet kell:

- Készüljön fel a siker érdekében a megfelelő eszközökkel
- A költségek elszámolással
- Optimalizálhatja a költségeket a megfelelő művelet végrehajtása

Három fő csoportra, az alábbi, úgy kell beállítani, győződjön meg arról, hogy sikeresen kezelheti költségeit a szervezetben.

- **Pénzügyi** -költségvetési kérelmek jóváhagyása a szervezeten belül felelős személyek alapján a felhő költségeit, előrejelzéseket. Azok a megfelelő számlázási kell fizetnie, és rendelje hozzá költségek különböző csapatokkal együttműködve elszámoltathatóság.
- **Kezelők** – üzleti döntéshozók a szervezet, amely a felhő költségeit, a költségkeret-beállítási eredmények legjobb keresése ismernie kell.
- **Alkalmazásokért felelős csapatok** – a mérnökök folyamatosan, a felhőerőforrások kezelése a szervezet igényeinek szolgáltatások fejlesztésébe. Ezek a csoportok a rugalmasságot, amelyekkel értéket termelhet a legtöbb azok meghatározott költségvetések kell.

### <a name="key-principles"></a>Kulcsfontosságú alapelvek

Használja a szervezet Költségkezelés felhőbeli siker érdekében helyezze az alább leírt elveket.

#### <a name="planning"></a>Tervezés

Átfogó, induló megtervezése lehetővé teszi a felhőhasználat, az adott üzleti igényeinek megfelelő kódolási sebesség kiválasztását. Tegye fel magának:

- Milyen üzleti problémák léptek megoldására?
- Milyen használati minták tegye várható az erőforrásaim?

A válaszok segítségével válassza ki az ajánlatok, amelyek az Ön számára megfelelő. Az infrastruktúra használatát, és hogyan használható az Azure hatékonyságának maximalizálása érdekében határozzák meg.

#### <a name="visibility"></a>Láthatóság

Ha jól strukturált, a Cost Management segít személyek tájékoztatása az Azure-költségek vagy töltenek pénzt felelős azok. Az Azure kialakításának köszönhetően betekintést szolgáltatással rendelkezik *ahol* töltött a költséget takaríthat meg. Ezek az eszközök előnyeit. Keresse meg az erőforrásokat, amelyek használaton, távolítsa el a veszteséget és maximalizálja a költségtakarékos lehetőségek segíthetnek.

#### <a name="accountability"></a>Felelősségre vonhatóság

Attribútum költségeit a szervezetben, hogy a költségkeret-beállítási csapatuk kiszolgálófelügyeletért felelős személyek biztosítják. Teljes mértékben megérteni a szervezet Azure költségeit, kell szervezni költség tesznek elérhetővé; ilyenek betekintést maximalizálhatja az erőforrások. Jó szervezet kezeléséhez, és csökkentheti a költségeket, és tartsa lenyomva a hatékony költségeit a szervezetben kiszolgálófelügyeletért személyek segít.

#### <a name="optimization"></a>Optimalizálás

ACT a kiadások csökkentése érdekében. A legtöbbet hozhatja ki a tervezési és a növekvő költségek láthatósági keresztül begyűjtött eredmények alapján. Érdemes lehet megfontolni a beszerzési és licencelés optimalizálások infrastruktúra üzembe helyezési változásokkal, amelyek a jelen dokumentum részletesen taglalja.

#### <a name="iteration"></a>Iteráció

A szervezet minden tagja a költség-kezelés életciklusának kell végezhetnek. Az érintett töltheti a költségek optimalizálása érdekében van szükség. Szigorú iteratív erről a folyamatról, és, hogy a szervezet felelős felhőalapú irányítási kulcsfontosságú alapelve.

![Láthatóság, elszámoltathatóság és optimalizálás a kulcsfontosságú alapelvek ábrája](./media/cost-mgt-best-practices/principles.png)

## <a name="plan-with-cost-in-mind"></a>Költség szem megtervezése

A felhőbeli erőforrások üzembe helyezése, előtt mérje fel a következő elemek:

- Az Azure-ajánlat, amely az igényeinek legmegfelelőbb
- Az erőforrások használatához
- Akkor lehet, hogy mennyibe

Az Azure biztosít eszközöket a értékelés folyamatban. Az eszközöket adhat a befektetés szükséges ahhoz, hogy a számítási feladatokat érdemes. Ezután kiválaszthatja az ajánlott konfiguráció az adott helyzetben.

### <a name="azure-onboarding-options"></a>Azure előkészítési lehetőségek

Az első lépés a felhasználói élmény a Cost Management belül maximalizálása, hogy vizsgálja meg, és eldönteni, melyik Azure-ajánlat legjobban az Ön számára. Gondolja át hogyan tervezi használni az Azure a jövőben. Is vegye figyelembe, hogyan szeretné konfigurálni a számlázási modellt. A döntés meghozatalakor, vegye figyelembe a következő kérdéseket:

- Mennyi ideig tervezi használni az Azure? Vagyok tesztelése, vagy tervezhetem hosszabb távú infrastruktúra létrehozását?
- Hogyan szeretné az Azure díjat fizetni? Érdemes lehet fizessen elő az csökkentett vagy első számlázás a hónap végén?

A különböző lehetőségekkel kapcsolatos további tudnivalókért látogasson el a [Azure megvásárlása](https://azure.microsoft.com/pricing/purchase-options/). A leggyakoribb számlázási modellek közül több alábbi azonosítja.

#### <a name="freehttpsazuremicrosoftcomfree"></a>[Ingyenes](https://azure.microsoft.com/free/)

- 12 hónapig ingyenes népszerű szolgáltatások
- 200 USD kredit 30 napig szolgáltatások
- szolgáltatások több mint 25 mindig ingyenesek.

#### <a name="pay-as-you-gohttpsazuremicrosoftcomoffersms-azr-0003p"></a>[Használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p)

- Nincs minimum díj és kötelezettségvállalás
- Versenyképes díjszabás
- Csak a valóban használt funkciókért kell fizetni
- Bármikor lemondható

#### <a name="enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement"></a>[Nagyvállalati Szerződés](https://azure.microsoft.com/pricing/enterprise-agreement/)

- Előzetes pénzügyi kötelezettségvállalással lehetőségei
- Az Azure kedvezményes díjszabása a hozzáférést

## <a name="estimate-the-cost-of-your-solution"></a>A megoldás kiszámítása

Semmilyen infrastruktúra üzembe helyezése, előtt mérje fel, mekkora a megoldás díjait. Az értékelés segít a szervezet a számítási feladat előzetes díj költségvetés létrehozása. Ezután használhatja költségvetési idővel becslésére a kezdeti felmérést érvényességét. És összehasonlíthatja a tényleges költség az üzembe helyezett megoldás.

### <a name="azure-pricing-calculator"></a>Azure díjkalkulátor

Az Azure díjkalkulátorát keverheti a Azure-szolgáltatásokkal, tekintse meg a költségek becslése eltérő kombinációja lehetővé teszi. Valósítható meg a megoldás különböző módokon használatával az Azure-ban – a teljes költekezés mindegyike befolyásolhatja. Szem előtt tartva korai mind a felhőbeli üzemelő példány, az infrastruktúrához segít a leghatékonyabban az eszközzel. Ez segíthet beolvasása egy szilárd becsült becsült költségeit az Azure-ban.

További információkért lásd: a [Azure díjkalkulátorát](https://azure.microsoft.com/pricing/calculator).

### <a name="azure-migrate"></a>Azure Migrate

Az Azure Migrate egy szolgáltatása, amely a helyszíni adatközpontokban a szervezet aktuális számítási feladatok felmérésével. Biztosít, mit érdemes Azure helyettesítő megoldásokból betekintést. Első lépésként áttelepítése elemzi a helyszíni gépek migrálási megoldható meghatározásához. Ezt követően javaslatot tesz a legjobb teljesítmény érdekében az Azure-beli virtuális gép méretezése. Végül egy Azure-alapú megoldás egy költségbecslést is létrehoz.

További információkért lásd: [Azure Migrate](../migrate/migrate-overview.md).

## <a name="analyze-and-manage-your-costs"></a>A költségek kezelése és elemzése

Hogyan a szervezet költségek időbeli alakulásával tisztában megtartása. Az alábbi technikák segítségével megfelelően ismertetése, és kezelheti költségeit.

### <a name="organize-and-tag-your-resources"></a>Rendszerezése és megjelölése az erőforrások

Költség szem előtt az erőforrások rendszerezéséhez. Az előfizetések és -erőforráscsoportok létrehozása, gondolja át a kapcsolódó költségek felelős csapatok. Ellenőrizze, hogy a szervezet a jelentéskészítési tartja szem előtt. Az előfizetések és -erőforráscsoportok adja meg a helyes gyűjtők rendszerezése és attribútum költségeit a szervezetben. Címkék segítségével attribútum költség tartalmaznak. A címkék szűrőként is használhatja. És használhatja őket a csoportosítási szempont, ha az adatok elemzéséhez, és vizsgálja meg a költségek. Nagyvállalati szerződéssel rendelkező ügyfelek szervezeti egységek létrehozása és helyezze őket-előfizetések is. Költség-alapú szervezet az Azure-ban teszi lehetővé, az érintett személyeket a szervezetben csapatuk kiadások csökkentése a kiszolgálófelügyeletért.

### <a name="use-cost-analysis"></a>Használja a költségek elemzése

Költségelemzés lehetővé teszi a szervezeti költségeket részletes tovább szeletelve és darabolva a standard szintű erőforrás-tulajdonságok használatával költségeket, és elemezheti. Vegye figyelembe a következő gyakori kérdések az elemzés alapján. Rendszeres időközönként a kérdések megválaszolása segítségével több értesüljön, és további költségérzékeny döntések engedélyezése.

- **Becsült költség az aktuális hónaphoz** – mennyi rendelkezik felmerülő eddig ebben a hónapban? E marad a költségvetés alatt?
- **Vizsgálja meg a rendellenességeket** – győződjön meg arról, hogy a költségek normál használati elfogadható tartományon belül marad, rendszeres ellenőrzéseket végre. Mik azok a trendek? Vannak-e kiugró adatokat?
- **Egyeztetés a számlához** -van a legutóbb számlázott drágább, mint az előző hónapban? Hogyan változott a költségkeret szokások hónap hónapos over?
- **Belső költséghelyi elszámolás** – most, hogy tudom, hogy mennyit vagyok éppen fizetnem, hogyan kell ezeket a díjakat kell bontani a szervezetem számára?

További információkért lásd: [költségelemzés](quick-acm-cost-analysis.md).

### <a name="export-billing-data-on-a-schedule"></a>Ütemezés szerint számlázási adatok exportálása

Van szüksége a számlázási adatokat importálni külső rendszerek, például egy irányítópulton vagy pénzügyi rendszer? Állítsa be az Azure Storage automatikus export, és manuálisan tölt le fájlokat minden hónapban. Ezután könnyedén állíthat be más rendszerekkel való automatikus Integrációk szinkronban tartani az elszámolási adatok.

Számlázási adatok exportálása kapcsolatos további információkért lásd: [létrehozása és kezelése az exportált adatok](tutorial-export-acm-data.md).

### <a name="create-budgets"></a>Hozzon létre költségvetése

Azonosított, és a költségkeret mintáinak elemzése után fontos megkezdheti a saját maga és a csapatok számának korlátozása. Az Azure költségvetése lehetővé teszik, egy költség vagy a használat alapú költségvetés számos küszöbérték és riasztások. Ellenőrizze, hogy tekintse át a költségvetés írási legördülő hétről és módosítható igény szerint hoz létre rendszeresen költségvetése. Az Azure költségvetése lehetővé teszi egy automation-eseményindító konfigurálása egy adott költségvetési küszöbérték elérésekor. Például konfigurálhatja a virtuális gépek leállítása a szolgáltatás. Vagy az infrastruktúra áthelyezheti egy másik tarifacsomagra költségvetés eseményindító válaszul.

További információkért lásd: [Azure költségvetése](tutorial-acm-create-budgets.md).

Költségvetés-alapú automatizálásával kapcsolatos további információkért lásd: [költségvetés-alapú automatizálási](../billing/billing-cost-management-budget-scenario.md).

## <a name="act-to-optimize"></a>ACT optimalizálása
Használja a következő módokon optimalizálhatja a költségeket.

### <a name="cut-out-waste"></a>Vágja ki a veszteség

Miután telepítette az Azure-ban az infrastruktúra, fontos, hogy használatban van. Kezdjen el takarékoskodni azonnal a legegyszerűbb módja, hogy tekintse át az erőforrásokat, és távolítsa el azokat, amelyek nincsenek használatban. Itt ellenőrizze, ha az erőforrások lehető leghatékonyabb vannak használatban.

#### <a name="azure-advisor"></a>Azure Advisor

Az Azure Advisor egy szolgáltatása, amely, többek között azonosítja a virtuális gépek Processzor- vagy hálózati használati szempontból alacsony kihasználtság mellett futottak. Itt eldöntheti, vagy állítsa le, vagy a gép a becsült költség a továbbra is fut a gépek alapján. Az Advisor is biztosít a fenntartott példány vásárlására vonatkozó javaslatok. A javaslatok a virtuális gép használatát az elmúlt 30 nap alapulnak. Határoz meg, ha a javaslatok segítségével csökkentheti költségeit.

További információkért lásd: [az Azure Advisor](../advisor/advisor-overview.md).

### <a name="size-your-vms-properly"></a>A virtuális gépek megfelelő méretezése

Virtuálisgép-méretezési jelentős hatással van a teljes Azure költségeit. Szükséges Azure-beli virtuális gépek számát előfordulhat, hogy nem felel meg egy magnak mi jelenleg telepített egy helyszíni adatközpontban. Győződjön meg arról, hogy a válassza ki a futtatni kívánt számítási feladatokhoz megfelelő méretű.

További információkért lásd: [Azure IaaS: megfelelő méretezése és a költségek](https://azure.microsoft.com/resources/videos/azurecon-2015-azure-iaas-proper-sizing-and-cost/).

### <a name="use-purchase-discounts"></a>Vásárlás kedvezmények használata

Az Azure rendelkezik számos kedvezményeket, amellyel a szervezet előnyeit, pénzt takaríthat meg.

#### <a name="azure-reservations"></a>Az Azure-foglalások

Azure foglalások engedélyezése, hogy fizessen elő az egyéves vagy hároméves virtuális gép vagy SQL-adatbázis a számítási kapacitást. Előre kell fizetnie kedvezményt szeretne kapni a használt erőforrások tehetőek. Az Azure-foglalások jelentősen csökkentheti a virtuális gép vagy SQL-adatbázis számítási költségeket – akár 72 %-a – egyéves vagy hároméves előzetes kötelezettségvállalás, használatalapú fizetéssel. foglalások számlázási engedményt adja meg, és a virtuális gépek vagy az SQL Database-adatbázisok futási állapotát nem befolyásolják.

További információkért lásd: [Mik az Azure-foglalásokat?](../billing/billing-save-compute-costs-reservations.md).

#### <a name="use-azure-hybrid-benefit"></a>Az Azure Hybrid Benefit

Ha már rendelkezik Windows Server vagy SQL Server-licenceivel a helyszíni központi telepítések, használhatja az Azure Hybrid Benefit program mentése az Azure-ban. A Windows Server az értékelemmel minden egyes licenc fedezi az operációs rendszer (akár két virtuális gép) költségeit, és számítási csak kell fizetnie. Meglévő SQL Server-licencek használatával akár megtakarítást 55 a Virtuálismag-alapú SQL Database beállításai. az Azure Virtual Machines és az SQL Server Integration Services a lehetőségek között az SQL Server.

További információkért lásd: [Azure Hybrid Benefit – megtakarítási kalkulátor](https://azure.microsoft.com/pricing/hybrid-benefit/).

### <a name="other-resources"></a>Egyéb erőforrások

Azure-szolgáltatás, amely lehetővé teszi, hogy igénybe többletkapacitását az Azure kedvezményes szolgáltatásokat is tartalmaz. További információkért lásd: [alacsony prioritású virtuális gépek használata a Batch-Csel](../batch/batch-low-pri-vms.md).

## <a name="next-steps"></a>További lépések
- Ha most ismerkedik a Cost Management, olvassa el [Mi az Azure Cost Management?](overview-cost-mgt.md) megtudhatja, hogyan segít monitorozását és ellenőrzését, az Azure-költségeit, és optimalizálják az erőforrások felhasználását.
