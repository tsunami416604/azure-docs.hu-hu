---
title: A Azure Monitor naplók üzembe helyezésének megtervezése | Microsoft Docs
description: Ez a cikk azokat a szempontokat és javaslatokat ismerteti, amelyek a munkaterületek Azure Monitor való üzembe helyezésére készülnek.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/05/2019
ms.author: magoedte
ms.openlocfilehash: d2fadf6d0bf9b7422b6dbf7597a024d22b5d733f
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839324"
---
# <a name="designing-your-azure-monitor-logs-deployment"></a>A Azure Monitor naplók üzembe helyezésének megtervezése

A Azure Monitor egy Log Analytics-munkaterületen tárolja a [napló](data-platform-logs.md) adatokat, amely egy Azure-erőforrás, valamint egy olyan tároló, amelybe az adatok gyűjtése, összesítése és felügyeleti határként szolgál. Noha az Azure-előfizetésben egy vagy több munkaterületet is üzembe helyezhet, több szempontot is figyelembe kell vennie, hogy a kezdeti üzembe helyezése a költséghatékony, felügyelhető és méretezhető a szervezet igényeinek megfelelő üzembe helyezés.

A munkaterületen lévő adatkészletek táblázatokba vannak rendezve, amelyek mindegyike különböző típusú adattípusokat tárol, és saját tulajdonságokkal rendelkezik, amelyek az adatforrást generáló erőforráson alapulnak. A legtöbb adatforrás a saját tábláiba fog írni egy Log Analytics munkaterületen.

![Példa a munkaterület adatmodelljére](./media/design-logs-deployment/logs-data-model-01.png)

A Log Analytics munkaterület A következőket biztosítja:

* Az adattárolás földrajzi helye.
* Az adatelkülönítés azáltal, hogy az ajánlott tervezési stratégiák alapján különböző felhasználói hozzáférési jogosultságokat biztosít.
* A beállítások, például az [árképzési szintek](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) a megőrzési és [az](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap)adatkorláti konfiguráció hatóköre.

Ez a cikk részletes áttekintést nyújt a tervezési és áttelepítési megfontolásokról, a hozzáférés-vezérlés áttekintéséről, valamint az informatikai szervezet számára ajánlott tervezési megvalósítások megismeréséről.

## <a name="important-considerations-for-an-access-control-strategy"></a>A hozzáférés-vezérlési stratégia fontos szempontjai

A szükséges munkaterületek számának azonosításához az alábbi követelmények közül egyet vagy többet kell megadnia:

* Ön globális vállalat, és az adatszuverenitási vagy megfelelőségi okokból az adott régiókban tárolt adatnaplózási adataira van szüksége.
* Az Azure-t használja, és a kimenő adatátviteli díjak elkerülése érdekében ugyanabban a régióban szeretne létesíteni egy munkaterületet, amelyikben az általa kezelt Azure-erőforrás is található.
* Több részleget vagy üzleti csoportot is kezelhet, és azt szeretné, hogy mindegyikük láthassa a saját adatait, de másoktól nem. Emellett a konszolidált részleg vagy az üzleti csoport nézet esetében nem szükséges üzleti követelmény.

Az informatikai szervezetek jelenleg a következő modelleken alapulnak: központosított, decentralizált, vagy a hibrid mindkét struktúrában. Ennek eredményeképpen az alábbi munkaterület-telepítési modelleket gyakran használták a következő szervezeti struktúrák egyikének leképezésére:

* **Központosított**: A rendszer minden naplót egy központi munkaterületen tárol, és egyetlen csapat felügyeli, és a Azure Monitor a csapat számára differenciált hozzáférést biztosít. Ebben a forgatókönyvben könnyen kezelhető, kereshetők az erőforrások, és többek között a naplók. A munkaterület jelentős mértékben nőhet az előfizetés több erőforrása által gyűjtött adatok mennyiségétől függően, és további adminisztratív terhelést biztosít a hozzáférés-vezérlés különböző felhasználókhoz való fenntartásához.
* **Decentralizált**: Mindegyik csapat saját munkaterülettel rendelkezik, amelyet a saját és felügyelt erőforráscsoporthoz hoztak létre, és az adatnaplók erőforrásai elkülönítve vannak. Ebben az esetben a munkaterület biztonságos, és a hozzáférés-vezérlés konzisztens az erőforrás-hozzáféréssel, de a naplók összekapcsolását nehéz kezelni. A sok erőforrás széles körű nézetét igénylő felhasználóknak nem lehet értelmes módon elemezni az adatelemzést.
* **Hibrid**: A biztonsági naplózás megfelelőségi követelményei tovább bonyolítják ezt a forgatókönyvet, mivel számos szervezet párhuzamosan implementálja mindkét üzemi modellt. Ez gyakran összetett, költséges és nehezen karbantartható konfigurációt eredményez a naplók lefedettségének hiányában.

Ha a Log Analytics ügynököket használja az adatok gyűjtésére, az ügynök üzembe helyezésének megtervezéséhez a következőket kell megismernie:

* A Windows-ügynököktől származó adatok összegyűjtéséhez beállíthatja, hogy az [egyes ügynökök egy vagy több](../../azure-monitor/platform/agent-windows.md)munkaterületnek jelentsenek, még akkor is, ha System Center Operations Manager felügyeleti csoportnak jelentenek jelentést. A Windows-ügynök legfeljebb négy munkaterületet tud jelenteni.
* A Linux-ügynök nem támogatja a többsoros vezérlést, és csak egyetlen munkaterületre tud jelentést készíteni.

Ha System Center Operations Manager 2012 R2 vagy újabb verziót használ:

* Az egyes Operations Manager felügyeleti csoportok [csak egy](../platform/om-agents.md)munkaterülethez csatlakoztathatók. 
* A felügyeleti csoportnak jelentést küldő linuxos számítógépeket úgy kell konfigurálni, hogy közvetlenül egy Log Analytics munkaterületre jelentsenek. Ha a Linux rendszerű számítógépek már közvetlenül egy munkaterületre vannak bejelentve, és Operations Manager szeretné figyelni őket, kövesse az alábbi lépéseket [egy Operations Manager felügyeleti csoportnak](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group)való jelentéskészítéshez. 
* Telepítheti a Log Analytics Windows-ügynököt a Windows rendszerű számítógépre, és jelentést készíthet mind a munkaterülettel integrált Operations Manager, mind pedig egy másik munkaterületről.

## <a name="access-control-overview"></a>Hozzáférés-vezérlés áttekintése

A szerepköralapú hozzáférés-vezérlés (RBAC) segítségével a felhasználók és csoportok csak a munkaterületen lévő figyelési adathoz szükséges hozzáférés mennyiségét biztosíthatják. Ez lehetővé teszi, hogy egyetlen munkaterülettel igazítsa az informatikai szervezet működési modelljéhez, hogy az összegyűjtött adatok az összes erőforráson engedélyezve legyenek. Például hozzáférést biztosíthat az Azure Virtual Machines szolgáltatásban üzemeltetett infrastruktúra-szolgáltatásokért felelős csapatának, és ennek eredményeképpen csak a virtuális gépek által létrehozott naplókhoz férhet hozzá. Ez az új erőforrás-kontextusú naplózási modellt követi. Ennek a modellnek az alapja, hogy az Azure-erőforrás által kibocsátott összes naplófájl automatikusan társítva lesz ehhez az erőforráshoz. A naplók továbbítása egy központi munkaterületre történik, amely az erőforrások alapján a hatókört és a RBAC is figyelembe veszi.

A felhasználó által hozzáféréssel rendelkező adatok meghatározása az alábbi táblázatban felsorolt tényezők kombinációjával történik. Mindegyiket az alábbi szakasz ismerteti.

| Tényező | Leírás |
|:---|:---|
| [Hozzáférési mód](#access-mode) | A felhasználó által a munkaterület eléréséhez használt metódus.  Meghatározza az elérhető adatmennyiséget és az alkalmazott hozzáférés-vezérlési módot. |
| [Hozzáférés-vezérlési mód](#access-control-mode) | A munkaterületre vonatkozó beállítás, amely meghatározza, hogy a rendszer az engedélyeket a munkaterületen vagy az erőforrás szintjén alkalmazza-e. |
| [Engedélyek](manage-access.md#manage-accounts-and-users) | A munkaterülethez vagy erőforráshoz tartozó egyéni vagy felhasználói csoportokra alkalmazott engedélyek. Meghatározza, hogy a felhasználó milyen adatelérési pontokhoz férhet hozzá. |
| [Táblázat szintű RBAC](manage-access.md#table-level-rbac) | Választható részletes engedélyek, amelyek az összes felhasználóra érvényesek, függetlenül azok hozzáférési módjától vagy hozzáférés-vezérlési módjától. Meghatározza, hogy a felhasználó mely adattípusokhoz férhet hozzá. |

## <a name="access-mode"></a>Hozzáférési mód

A *hozzáférési mód* arra utal, hogy a felhasználó hogyan fér hozzá egy log Analytics munkaterülethez, és meghatározza az általuk elérhetővé vált adatmennyiséget. 

A felhasználók két lehetőség közül választhatnak az adatok eléréséhez:

* **Munkaterület – környezet**: A munkaterületen megtekintheti az összes olyan naplót, amelyhez jogosult. Az ebben a módban lévő lekérdezések hatóköre a munkaterület összes tábláján lévő összes értékre vonatkozik. Ez a hozzáférési mód akkor használható, ha a naplók a munkaterülettel való hozzáférés hatókörként vannak megadva, például ha a Azure Portal **Azure monitor** menüjében a **naplók** elemet választja.

    ![Log Analytics környezet a munkaterületről](./media/design-logs-deployment/query-from-workspace.png)

* **Erőforrás-környezet**: Ha egy adott erőforráshoz, erőforráscsoporthoz vagy előfizetéshez fér hozzá a munkaterülethez, például amikor kiválasztja a **naplók** elemet a Azure Portal erőforrás menüjéből, megtekintheti az adott erőforráshoz tartozó naplókat az összes olyan táblában, amelyhez hozzáfér. Az ebben a módban lévő lekérdezések hatóköre csak az adott erőforráshoz társított adatmennyiségre vonatkozik. Ez a mód lehetővé teszi a szemcsés RBAC használatát is.

    ![Log Analytics környezet erőforrásból](./media/design-logs-deployment/query-from-resource.png)

    > [!NOTE]
    > A naplók csak akkor érhetők el erőforrás-környezeti lekérdezésekhez, ha megfelelően vannak társítva az adott erőforráshoz. Jelenleg az alábbi erőforrások korlátai vannak:
    > - Az Azure-on kívüli számítógépek
    > - Service Fabric
    > - Application Insights
    >
    > Ellenőrizheti, hogy a naplók megfelelően vannak-e társítva az erőforráshoz egy lekérdezés futtatásával és a kívánt rekordok vizsgálatával. Ha a megfelelő erőforrás-azonosító a [_ResourceId](log-standard-properties.md#_resourceid) tulajdonságban van, akkor az adatok elérhetők az erőforrás-központú lekérdezések számára.

A Azure Monitor automatikusan meghatározza a megfelelő módot attól függően, hogy milyen kontextusban végzi a napló keresését. A hatókör mindig a Log Analytics bal felső részén jelenik meg.

### <a name="comparing-access-modes"></a>Hozzáférési módok összehasonlítása

A következő táblázat összefoglalja a hozzáférési módokat:

| | Munkaterület – környezet | Erőforrás-környezet |
|:---|:---|:---|
| Kik az egyes modellek? | Központi felügyelet. Azok a rendszergazdák, akiknek olyan adatgyűjtést és felhasználókat kell konfigurálniuk, akiknek számos erőforráshoz kell hozzáféréssel rendelkezniük. Jelenleg olyan felhasználók számára is szükséges, akiknek az Azure-on kívüli erőforrásokhoz is hozzá kell férniük. | Alkalmazás-csapatok. A figyelt Azure-erőforrások rendszergazdái. |
| Mire van szükség a felhasználók számára a naplók megtekintéséhez? | Engedélyeket a munkaterületre. Lásd: **munkaterület-engedélyek** a [fiókok és felhasználók kezeléséhez](manage-access.md#manage-accounts-and-users). | Olvasási hozzáférés az erőforráshoz. Lásd: **erőforrás-engedélyek** a [fiókok és felhasználók kezeléséhez](manage-access.md#manage-accounts-and-users). Az engedélyek örökölhető (például a tartalmazó erőforráscsoporthoz) vagy közvetlenül az erőforráshoz rendelve. A rendszer automatikusan hozzárendeli az erőforrás naplóihoz tartozó engedélyeket. |
| Mi az engedélyek hatóköre? | Munkaterület. A munkaterülethez hozzáféréssel rendelkező felhasználók a munkaterületen lévő összes naplót lekérhetik olyan táblákból, amelyekhez engedélyük van. Lásd: [Table Access Control](manage-access.md#table-level-rbac) | Azure-erőforrás. A felhasználó bármely munkaterületről lekérdezheti az egyes erőforrások, erőforráscsoportok vagy előfizetések naplóit, de más erőforrásokhoz nem tud naplókat lekérdezni. |
| Hogyan férhet hozzá a felhasználói naplókhoz? | <ul><li>**Naplók** indítása **Azure monitor** menüből.</li></ul> <ul><li>**Naplók** indítása **log Analytics**munkaterületekről.</li></ul> <ul><li>Azure Monitor [munkafüzetekből](../visualizations.md#workbooks).</li></ul> | <ul><li>**Naplók** indítása az Azure-erőforrás menüjéből</li></ul> <ul><li>**Naplók** indítása **Azure monitor** menüből.</li></ul> <ul><li>**Naplók** indítása **log Analytics**munkaterületekről.</li></ul> <ul><li>Azure Monitor [munkafüzetekből](../visualizations.md#workbooks).</li></ul> |

## <a name="access-control-mode"></a>Hozzáférés-vezérlési mód

A *hozzáférés-vezérlési mód* az egyes munkaterületeken olyan beállítás, amely meghatározza, hogy az adott munkaterülethez milyen engedélyek vannak meghatározva.

* **Munkaterület-engedélyek**megkövetelése: Ez a vezérlési mód nem engedélyezi a szemcsés RBAC. Ahhoz, hogy egy felhasználó hozzáférhessen a munkaterülethez, engedélyeket kell adni a munkaterülethez vagy adott táblákhoz.

    Ha a felhasználó a munkaterület környezetét követően fér hozzá a munkaterülethez, az összes olyan táblához hozzáférhet, amelyhez hozzáférést kapott. Ha a felhasználó az erőforrás-kontextus mód követése után fér hozzá a munkaterülethez, az adott erőforráshoz tartozó összes olyan táblában hozzáfér, amelyhez hozzáférést kapott.

    Ez az alapértelmezett beállítás az összes olyan munkaterülethez, amelyet a március 2019. előtt hoztak létre.

* **Erőforrás-vagy munkaterület-engedélyek használata**: Ez a vezérlési mód lehetővé teszi a szemcsés RBAC. A felhasználók csak olyan erőforrásokhoz férhetnek hozzá, amelyekhez az Azure `read` -engedély hozzárendelésével megtekinthetők. 

    Ha a felhasználó munkaterület-környezet módban fér hozzá a munkaterülethez, a rendszer a munkaterület engedélyeit alkalmazza. Ha a felhasználó erőforrás-kontextus módban fér hozzá a munkaterülethez, a rendszer csak az erőforrás-engedélyeket ellenőrzi, és a munkaterület-engedélyeket figyelmen kívül hagyja. A RBAC engedélyezése a felhasználók számára a munkaterület engedélyeiből való eltávolításával, valamint az erőforrás-engedélyek felismerésének engedélyezésével.

    Ez az alapértelmezett beállítás minden, a március 2019. után létrehozott munkaterülethez.

    > [!NOTE]
    > Ha egy felhasználó csak erőforrás-jogosultságokkal rendelkezik a munkaterülethez, csak az erőforrás-környezet mód használatával férhetnek hozzá a munkaterülethez, feltételezve, hogy a munkaterület-hozzáférési mód **erőforrás-vagy munkaterület-engedélyek használatára**van beállítva.

## <a name="recommendations"></a>Javaslatok

![Példa erőforrás-környezet kialakítására](./media/design-logs-deployment/workspace-design-resource-context-01.png)

Ez a forgatókönyv egyetlen munkaterület-kialakítást mutat be az IT-szervezet előfizetésében, amelyet nem korlátozza az adatszuverenitás vagy a szabályozások megfelelősége, vagy le kell képeznie azokat a régiókat, amelyekre az erőforrások telepítve vannak. Lehetővé teszi a szervezetek számára, hogy az Azure Access Management és a biztonságosabb hozzáférés-vezérlés révén jobban kihasználják a szervezet biztonsági és informatikai felügyeleti csapatait.

Az összes erőforrás, figyelési megoldás és elemzés, például a Application Insights és a Azure Monitor for VMs, a különböző csapatok által karbantartott infrastruktúra és alkalmazások támogatása úgy van konfigurálva, hogy az összegyűjtött naplózási adatokat az informatikai szervezeteknek továbbítsák központosított megosztott munkaterület. Az egyes csoportokban lévő felhasználók hozzáférést kapnak a naplókhoz azokhoz az erőforrásokhoz, amelyekhez hozzáférést kaptak.

A munkaterület architektúrájának üzembe helyezése után a [Azure Policy](../../governance/policy/overview.md)használatával kényszerítheti ki ezt az Azure-erőforrásokra. Lehetővé teszi a szabályzatok meghatározását és az Azure-erőforrások megfelelőségének biztosítását, így az összes diagnosztikai naplóját egy adott munkaterületre küldi el. Az Azure-beli virtuális gépekkel vagy a virtuálisgép-méretezési csoportokkal például olyan meglévő házirendeket használhat, amelyek kiértékelik a munkaterület megfelelőségét és a jelentés eredményeit, illetve testreszabhatók, ha nem megfelelőek.  

## <a name="workspace-consolidation-migration-strategy"></a>Munkaterület-konszolidáció áttelepítési stratégiája

Azon ügyfelek esetében, akik már több munkaterületet telepítettek, és érdeklik az erőforrás-kontextus hozzáférési modellbe való konszolidáció iránt, javasoljuk, hogy növekményes megközelítést használjon az ajánlott hozzáférési modellre való áttéréshez, és nem próbálja meg elérni ezt gyorsan vagy agresszíven. Egy ésszerű időkeretet követően a tervezés, az áttelepítés, az ellenőrzés és a kivonás szakaszos megközelítését követve elkerülheti a nem tervezett incidenseket, vagy nem várt hatással lehet a Felhőbeli műveletekre. Ha nem rendelkezik megfelelőségi vagy üzleti okokból adatmegőrzési szabályzattal, értékelnie kell a megfelelő időtartamot, hogy az adatok megmaradjanak a munkaterületen, amelyet a folyamat során migrál. A megosztott munkaterületre történő jelentéskészítéshez szükséges erőforrások újrakonfigurálásakor szükség szerint továbbra is elemezheti az eredeti munkaterületen tárolt adatmennyiséget. Ha az áttelepítés befejeződött, a megőrzési idő lejárta előtt ne törölje azt, ha az eredeti munkaterületen lévő adatok megtartására van irányadó.

A modellre való Migrálás megtervezése során vegye figyelembe a következőket:

* Ismerje meg, hogy milyen iparági előírásokat és belső szabályzatokat kell megtartania az adatmegőrzéssel kapcsolatban.
* Győződjön meg arról, hogy az alkalmazás-munkacsoportok működhetnek a meglévő erőforrás-környezeti funkciókon belül.
* Azonosítsa az alkalmazási csapatok erőforrásai számára biztosított hozzáférést, és tesztelje a fejlesztési környezetet az éles környezet megvalósítása előtt.
* Konfigurálja a munkaterületet az **erőforrás-vagy munkaterület-engedélyek használatának**engedélyezéséhez.
* Távolítsa el az alkalmazás-Teams engedélyt a munkaterület olvasásához és lekérdezéséhez.
* Engedélyezheti és konfigurálhatja azokat a figyelési megoldásokat, például a tárolók és/vagy Azure Monitor for VMs Azure Monitorét, az Automation-fiók (oka) t, valamint a felügyeleti megoldásokat, például a Update Management, a virtuális gépek üzembe helyezését és leállítását, illetve munkaterület.

## <a name="next-steps"></a>További lépések

Az útmutatóban ajánlott biztonsági engedélyek és vezérlőelemek megvalósításához tekintse át a [naplók hozzáférésének kezelése](manage-access.md)című témakört.