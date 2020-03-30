---
title: Az Azure Monitor naplók telepítésének tervezése | Microsoft dokumentumok
description: Ez a cikk ismerteti a szempontokat és javaslatokat az ügyfelek számára, akik egy munkaterület üzembe helyezésére készülnek az Azure Monitorban.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/20/2019
ms.openlocfilehash: e493b07814821496f941a4b81402ba0b49acbede
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248826"
---
# <a name="designing-your-azure-monitor-logs-deployment"></a>Az Azure Monitor-naplók központi telepítésének tervezése

Az Azure Monitor [a naplóadatokat](data-platform-logs.md) egy Log Analytics-munkaterületen tárolja, amely egy Azure-erőforrás és egy tároló, ahol az adatok gyűjtése, összesítése és felügyeleti határként szolgál. Bár azure-előfizetésében egy vagy több munkaterületet is üzembe helyezhet, számos szempontot kell figyelembe vennie annak érdekében, hogy a kezdeti üzembe helyezés megfelel-e az irányelveinknek, hogy költséghatékony, kezelhető és méretezhető legyen. a szervezet igényeinek megfelelő központi telepítést.

A munkaterületen lévő adatok táblákba vannak rendezve, amelyek mindegyike különböző típusú adatokat tárol, és az adatokat létrehozó erőforráson alapuló egyedi tulajdonságkészlettel rendelkezik. A legtöbb adatforrás a saját tábláiba ír a Log Analytics-munkaterületen.

![Példa munkaterületi adatmodellre](./media/design-logs-deployment/logs-data-model-01.png)

A Log Analytics munkaterület a következőket biztosítja:

* Az adattárolás földrajzi helye.
* Az adatok elkülönítése azáltal, hogy a különböző felhasználók hozzáférési jogokat az egyik ajánlott tervezési stratégiák.
* Az olyan beállítások konfigurálásának hatóköre , mint a [tarifacsomag](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [a megőrzés](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)és [az adatplafon](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#manage-your-maximum-daily-data-volume).

Ez a cikk részletes áttekintést nyújt a tervezési és áttelepítési szempontokról, a hozzáférés-vezérlés áttekintéséről, valamint az informatikai szervezet számára ajánlott tervezési megvalósítások megértéséről.



## <a name="important-considerations-for-an-access-control-strategy"></a>Fontos szempontok a hozzáférés-vezérlési stratégiához

A szükséges munkaterületek számának azonosítását az alábbi követelmények közül egy vagy több befolyásolja:

* Ön egy globális vállalat, és az adatok szuverenitása vagy megfelelőségi okokból adott régiókban tárolt naplóadatokra van szüksége.
* Az Azure-t használja, és a kimenő adatátviteli díjak elkerülése érdekében ugyanabban a régióban szeretne létesíteni egy munkaterületet, amelyikben az általa kezelt Azure-erőforrás is található.
* Több részleget vagy üzleti csoportot kezel, és azt szeretné, hogy mindegyik saját adatait lássa, másokadatait azonban nem. Emellett nincs üzleti követelmény a konszolidált több osztály vagy üzleti csoport nézethez.

Az informatikai szervezetek ma egy központosított, decentralizált vagy a két struktúra közötti hibridet követve modelleződnek. Ennek eredményeképpen a következő munkaterület-telepítési modelleket gyakran használják a szervezeti struktúrák egyikének leképezésére:

* **Központosított:** Az összes napló egy központi munkaterületen van tárolva, és egyetlen csapat felügyeli, az Azure Monitor pedig csapatonként differenciált hozzáférést biztosít. Ebben a forgatókönyvben könnyen kezelhető, erőforrások közötti keresés és keresztkorreláció naplók. A munkaterület jelentősen növekedhet az előfizetés több erőforrásából gyűjtött adatok mennyiségétől függően, és további felügyeleti többletterheléssel a hozzáférés-vezérlés fenntartása érdekében a különböző felhasználók számára. Ez a modell az úgynevezett "hub és küllő".
* **Decentralizált:** Minden csapat saját munkaterülettel rendelkezik, amelyet egy saját és kezelt erőforráscsoportban hoztak létre, és a naplóadatok erőforrásonként vannak elkülönítve. Ebben a forgatókönyvben a munkaterület biztonságos maradhat, és a hozzáférés-vezérlés összhangban van az erőforrás-hozzáféréssel, de nehéz keresztkorrelációnaplók. Azok a felhasználók, akiknek széles körű képet kell kaphatniuk számos erőforrásról, nem tudják értelmesen elemezni az adatokat.
* **Hibrid:** A biztonsági naplózásmegfelelőségi követelmények tovább bonyolítják ezt a forgatókönyvet, mivel számos szervezet mindkét telepítési modellt párhuzamosan valósítja meg. Ez általában egy összetett, költséges és nehezen karbantartható konfigurációt eredményez a naplók lefedettségének hiányosságaival.

Amikor a Log Analytics-ügynökök et használja az adatok gyűjtésére, az ügynök telepítésének megtervezéséhez meg kell értenie a következőket:

* Ha adatokat szeretne gyűjteni a Windows-ügynököktől, [beállíthatja, hogy az egyes ügynökök egy vagy több munkaterületnek jelentsenek,](../../azure-monitor/platform/agent-windows.md)még akkor is, ha a System Center Operations Manager felügyeleti csoportjának jelent. A Windows-ügynök legfeljebb négy munkaterületet jelenthet.
* A Linux-ügynök nem támogatja a több-homing, és csak jelentést egyetlen munkaterületre.

Ha a System Center Operations Manager 2012 R2 vagy újabb verziót használja:

* Minden Operations Manager felügyeleti csoport [csak egy munkaterülethez csatlakoztatható.](../platform/om-agents.md) 
* A felügyeleti csoportnak jelentést tevő Linux-számítógépeket úgy kell konfigurálni, hogy közvetlenül a Log Analytics-munkaterületnek jelentsenek. Ha a Linux-számítógépek már közvetlenül egy munkaterületre jelentenek, és az Operations Manager segítségével szeretné figyelni őket, az alábbi lépésekkel [jelentheti az Operations Manager felügyeleti csoportjának.](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group) 
* Telepítheti a Log Analytics Windows-ügynök a Windows számítógépen, és azt jelentést mind Operations Manager integrált munkaterületet, és egy másik munkaterületet.

## <a name="access-control-overview"></a>A hozzáférés-vezérlés áttekintése

A szerepköralapú hozzáférés-vezérlés (RBAC) segítségével a felhasználók és csoportok csak annyi hozzáférést adhatnak a felhasználóknak és csoportoknak, amennyi a munkaterületfigyelési adatokkal való munkához szükséges. Ez lehetővé teszi, hogy egyetlen munkaterület használatával igazodjon az informatikai szervezet működési modelljéhez az összes erőforráson engedélyezett gyűjtött adatok tárolásához. Például hozzáférést biztosít az Azure virtuális gépeken (Virtuális gépeken) üzemeltetett infrastruktúra-szolgáltatásokért felelős csapatának, és ennek eredményeképpen csak a virtuális gépek által létrehozott naplókhoz férhetnek hozzá. Ez az új erőforrás-környezet napló modell t követi. Ennek a modellnek az alapja az Azure-erőforrás által kibocsátott minden naplórekord, automatikusan ehhez az erőforráshoz társítva van. A naplók egy központi munkaterületre kerülnek, amely tiszteletben tartja a hatókört és az RBAC-ot az erőforrások alapján.

A felhasználó által hozzáféréssel rendelkező adatokat az alábbi táblázatban felsorolt tényezők kombinációja határozza meg. Mindegyik az alábbi szakaszokban található.

| Tényező | Leírás |
|:---|:---|
| [Hozzáférési mód](#access-mode) | Az a módszer, amelyet a felhasználó a munkaterület eléréséhez használ.  Meghatározza a rendelkezésre álló adatok hatókörét és az alkalmazott hozzáférés-vezérlési módot. |
| [Hozzáférés-vezérlési mód](#access-control-mode) | A munkaterület beállítása, amely meghatározza, hogy az engedélyek alkalmazása munkaterület vagy erőforrás szintjén legyen-e érvényben. |
| [Engedélyek](manage-access.md) | A munkaterület vagy erőforrás egyéni vagy felhasználói csoportjaira alkalmazott engedélyek. Azt határozza meg, hogy a felhasználó milyen adatokhoz férhet hozzá. |
| [Táblázat szintű RBAC](manage-access.md#table-level-rbac) | Választható részletes engedélyek, amelyek hozzáférési módjuktól vagy hozzáférés-vezérlési módjuktól függetlenül minden felhasználóra vonatkoznak. Azt határozza meg, hogy a felhasználó mely adattípusokhoz férhet hozzá. |

## <a name="access-mode"></a>Hozzáférési mód

A *hozzáférési mód* arra utal, hogy a felhasználó hogyan fér hozzá a Log Analytics-munkaterülethez, és meghatározza az elérhető adatok hatókörét. 

A felhasználóknak két lehetőségük van az adatok elérésére:

* **Munkaterületi környezet:** Megtekintheti az összes naplót a munkaterületen, amelyhez engedéllyel rendelkezik. Az ebben a módban lévő lekérdezések hatóköre a munkaterület összes táblájának összes adatára kiterjed. Ez a hozzáférési mód, amikor a naplók érhetők el a munkaterület, mint a hatókör, például amikor **kiválasztja naplók** az **Azure Monitor** menüben az Azure Portalon.

    ![Log Analytics-környezet a munkaterületről](./media/design-logs-deployment/query-from-workspace.png)

* **Erőforrás-környezet:** Amikor egy adott erőforrás, erőforráscsoport vagy előfizetés munkaterületét éri el, például amikor az Azure Portalon egy erőforrás menünaplók lehetőséget **választja,** csak az összes olyan táblában tekintheti meg a naplókat, amelyekhez hozzáférése van. Az ebben a módban lévő lekérdezések hatóköre csak az adott erőforráshoz társított adatokra terjed ki. Ez a mód a részletes RBAC-ot is engedélyezi.

    ![Analytics-környezet naplózása az erőforrásból](./media/design-logs-deployment/query-from-resource.png)

    > [!NOTE]
    > A naplók csak akkor érhetők el az erőforrás-környezet lekérdezésekhez, ha megfelelően társítva voltak a megfelelő erőforráshoz. Jelenleg a következő erőforrások nak vannak korlátai:
    > - Az Azure-on kívüli számítógépek
    > - Service Fabric
    > - Application Insights
    >
    > A lekérdezés futtatásával és az Önt érdeklő rekordok vizsgálatával ellenőrizheti, hogy a naplók megfelelően vannak-e társítva az erőforrásukhoz. Ha a megfelelő erőforrásazonosító a [_ResourceId](log-standard-properties.md#_resourceid) tulajdonságban található, akkor az adatok az erőforrás-központú lekérdezések számára érhetők el.

Az Azure Monitor automatikusan meghatározza a megfelelő módot attól függően, hogy milyen környezetből hajtja végre a naplókeresést. A hatókör mindig a Log Analytics bal felső részében jelenik meg.

### <a name="comparing-access-modes"></a>Hozzáférési módok összehasonlítása

Az alábbi táblázat összefoglalja a hozzáférési módokat:

| | Munkaterület-környezet | Erőforrás-környezet |
|:---|:---|:---|
| Kinek szánták az egyes modelleket? | Központi adminisztráció. Azok a rendszergazdák, akiknek konfigurálniuk kell az adatgyűjtést, valamint azok a felhasználók, akiknek sokféle erőforráshoz kell hozzáférnie. Jelenleg is szükséges a felhasználók számára, akiknek szükségük van az Azure-on kívüli erőforrások naplóihoz. | Jelentkező csapatok. A figyelt Azure-erőforrások rendszergazdái. |
| Mire van szüksége a felhasználónak a naplók megtekintéséhez? | A munkaterület engedélyei. Lásd: **Munkaterület-engedélyek** a [Hozzáférés kezelése munkaterületi engedélyekkel című](manage-access.md#manage-access-using-workspace-permissions)témakörben. | Az erőforráshoz való hozzáférés olvasása. Lásd: **Erőforrás-engedélyek** az [Access kezelése az Azure-engedélyek használatával című](manage-access.md#manage-access-using-azure-permissions)témakörben. Az engedélyek örökölhetők (például a tartalmazó erőforráscsoporttól), vagy közvetlenül hozzárendelhetők az erőforráshoz. Az erőforrás naplóihoz tartozó engedélyek automatikusan hozzá lesznek rendelve. |
| Mi az engedélyek hatóköre? | Munkaterület. A munkaterülettel rendelkező felhasználók lekérdezhetik a munkaterület összes naplóját olyan táblákból, amelyekhez engedéllyel rendelkeznek. Lásd: [Tábla hozzáférés-vezérlés](manage-access.md#table-level-rbac) | Azure-erőforrás. A felhasználó lekérdezheti a naplókat bizonyos erőforrásokról, erőforráscsoportokról vagy előfizetésről, amelyekhez bármely munkaterületről hozzáférnek, de más erőforrások naplóit nem kérdezheti le. |
| Hogyan férhet hozzá a felhasználói naplók? | <ul><li>**Naplók indítása az** Azure **Monitor** menüből.</li></ul> <ul><li>**Naplók indítása** **a Log Analytics-munkaterületekből.**</li></ul> <ul><li>Az Azure Monitor [munkafüzetek ből.](../visualizations.md#workbooks)</li></ul> | <ul><li>**Naplók indítása** az Azure-erőforrás menüjéből</li></ul> <ul><li>**Naplók indítása az** Azure **Monitor** menüből.</li></ul> <ul><li>**Naplók indítása** **a Log Analytics-munkaterületekből.**</li></ul> <ul><li>Az Azure Monitor [munkafüzetek ből.](../visualizations.md#workbooks)</li></ul> |

## <a name="access-control-mode"></a>Hozzáférés-vezérlési mód

A *hozzáférés-vezérlési mód* minden munkaterületen olyan beállítás, amely meghatározza a munkaterülethez szükséges engedélyek meghatározását.

* **Munkaterületi engedélyek megkövetelése**: Ez a vezérlőmód nem engedélyezi a részletes RBAC-ot. Ahhoz, hogy egy felhasználó hozzáférhessen a munkaterülethez, engedélyt kell kapnia a munkaterülethez vagy adott táblákhoz.

    Ha egy felhasználó a munkaterület-környezet módban fér hozzá a munkaterülethez, hozzáférhet bármely táblában lévő összes adathoz, amelyhez hozzáférést kapott. Ha egy felhasználó az erőforrás-környezet módban fér hozzá a munkaterülethez, akkor csak az adott erőforrás adataihoz férhet hozzá bármely táblában, amelyhez hozzáférést kapott.

    Ez a 2019 márciusa előtt létrehozott összes munkaterület alapértelmezett beállítása.

* **Erőforrás- vagy munkaterület-engedélyek használata:** Ez a vezérlőmód lehetővé teszi a részletes RBAC használatát. A felhasználók csak az Azure-engedélyek `read` hozzárendelésével megtekinthető erőforrásokhoz társított adatokhoz kaphatnak hozzáférést. 

    Amikor egy felhasználó munkaterület-környezet módban éri el a munkaterületet, a munkaterületi engedélyek érvényesek. Amikor egy felhasználó erőforrás-környezet módban éri el a munkaterületet, a rendszer csak az erőforrásengedélyeket ellenőrzi, a munkaterületi engedélyeket pedig figyelmen kívül hagyja. Engedélyezze az RBAC-t egy felhasználó számára, ha eltávolítja őket a munkaterületi engedélyekből, és engedélyezi az erőforrás-engedélyek felismerését.

    Ez a 2019 márciusa után létrehozott összes munkaterület alapértelmezett beállítása.

    > [!NOTE]
    > Ha egy felhasználó csak erőforrás-engedélyekkel rendelkezik a munkaterülethez, akkor csak erőforrás-környezet módban férhet hozzá a munkaterülethez, feltéve, hogy a munkaterület-hozzáférési mód az **Erőforrás- vagy munkaterület-engedélyek használata**beállításra van beállítva.

Ha tudni szeretné, hogyan módosíthatja a hozzáférés-vezérlési módot a portálon, a PowerShell használatával vagy egy Erőforrás-kezelő sablon használatával, olvassa el a [Hozzáférés-vezérlési mód konfigurálása című témakört.](manage-access.md#configure-access-control-mode)

## <a name="ingestion-volume-rate-limit"></a>A betöltési hangerő korlátja

Az Azure Monitor egy nagy méretű adatszolgáltatás, amely több ezer ügyfelet szolgál ki, akik havonta több terabájtnyi adatot küldenek növekvő ütemben. Az alapértelmezett betöltési sebesség küszöbértéke munkaterületenként **6 GB/perc.** Ez egy hozzávetőleges érték, mivel a tényleges méret adattípusok on-val változhat a napló hosszától és tömörítési arányától függően. Ez a korlát nem vonatkozik az ügynököktől vagy az [adatgyűjtő API-tól küldött adatokra.](data-collector-api.md)

Ha az adatokat nagyobb sebességgel küldi el egyetlen munkaterületre, a rendszer eldob bizonyos adatokat, és 6 óránként egy eseményt küld a munkaterület *Operation* táblájának, miközben a küszöbértéket továbbra is túllépi. Ha a betöltési kötet továbbra is meghaladja a sebességkorlátot, vagy várhatóan hamarosan eléri, támogatási kérelem megnyitásával kérheti a munkaterület növelését.
 
Ha értesítést szeretne kapni egy ilyen eseményről a munkaterületen, hozzon létre egy [naplóriasztási szabályt](alerts-log.md) a következő lekérdezés használatával, amely riasztási logikai alapot biztosít a nullánál nagyobb számú eredményre.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


## <a name="recommendations"></a>Javaslatok

![Példa erőforrás-környezet tervre](./media/design-logs-deployment/workspace-design-resource-context-01.png)

Ez a forgatókönyv egyetlen munkaterület-tervet fed le az informatikai szervezetek előfizetésében, amelyet nem korlátoz az adatok szuverenitása vagy a jogszabályi megfelelőség, vagy le kell képeznie azokat a régiókat, amelyeken belül az erőforrások üzembe helyezése van. Lehetővé teszi a szervezetek biztonsága és az informatikai rendszergazdai csapatok számára, hogy kihasználják az Azure hozzáférés-kezeléssel és a biztonságosabb hozzáférés-vezérléssel való jobb integrációt.

Minden erőforrás, figyelési megoldások és insights, például az Application Insights és az Azure Monitor virtuális gépek, támogató infrastruktúra és a különböző csapatok által fenntartott alkalmazások vannak konfigurálva, hogy továbbítsa az összegyűjtött naplóadatokat az informatikai szervezetek központosított megosztott munkaterületet. Az egyes csoportok felhasználói hozzáférést kapnak az erőforrások naplóihoz, amelyekhez hozzáférést kaptak.

Miután üzembe helyezte a munkaterületarchitektúrát, ezt az Azure-erőforrásokon kényszerítheti az [Azure Policy használatával.](../../governance/policy/overview.md) Ez lehetővé teszi a szabályzatok meghatározását és az Azure-erőforrások nak való megfelelésbiztosítását, hogy az összes erőforrásnaplójukat egy adott munkaterületre küldhessék. Az Azure virtuális gépekkel vagy a virtuálisgép-méretezési csoportokkal például használhatja a munkaterület-megfelelőséget és a jelentéseredményeket kiértékelő meglévő szabályzatokat, illetve testre szabhatja a nem megfelelő megoldáshoz.  

## <a name="workspace-consolidation-migration-strategy"></a>Munkaterület-konszolidációs áttelepítési stratégia

Azon ügyfelek számára, akik már több munkaterületet telepítettek, és érdeklődnek az erőforrás-környezet hozzáférési modell konszolidálása iránt, javasoljuk, hogy növekményes megközelítést alkalmazzon az ajánlott hozzáférési modellre való áttelepítéshez, és ne kísérelje meg ezt elérni. gyorsan vagy agresszíven. A tervezés, áttelepítés, ellenőrzés és a kivonás egy ésszerű ütemtervet követőszakaszos megközelítést követve elkerülheti a nem tervezett incidenseket vagy a felhőbeli műveletekre gyakorolt váratlan hatásokat. Ha megfelelőségi vagy üzleti okokból nem rendelkezik adatmegőrzési házirenddel, fel kell mérnie, hogy mennyi ideig őrizze meg az adatokat abban a munkaterületen, amelyből a folyamat során áttelepíti az adatokat. Miközben újrakonfigurálja az erőforrásokat, hogy jelentést tegyenek a megosztott munkaterületnek, szükség szerint elemezheti az eredeti munkaterületen lévő adatokat. Az áttelepítés befejezése után, ha az adatmegőrzési időszak vége előtt az eredeti munkaterületen szeretné megőrizni az adatokat, ne törölje azt.

A modellre való áttérés megtervezésekor vegye figyelembe a következőket:

* Ismerje meg, hogy milyen iparági szabályozásoknak és belső szabályzatoknak kell megfelelnie az adatmegőrzéssel kapcsolatban.
* Győződjön meg arról, hogy az alkalmazáscsapatok a meglévő erőforrás-környezet funkción belül dolgozhatnak.
* Azonosítsa az erőforrásokhoz az alkalmazáscsapatok számára biztosított hozzáférést, és tesztelje a fejlesztői környezetben az éles környezetben való megvalósítás előtt.
* Konfigurálja a munkaterületet az **Erőforrás- vagy munkaterületi engedélyek használata**engedélyezéséhez.
* A munkaterület olvasására és lekérdezésére vonatkozó alkalmazáscsoportok engedélyének eltávolítása.
* Engedélyezze és konfigurálja a figyelési megoldásokat, például az Azure Monitor tárolók és/vagy az Azure Monitor virtuális gépekhez, az Automation-fiók(ok), és a felügyeleti megoldások, például a frissítéskezelés, a start/stop virtuális gépek, stb, az eredeti munkaterületen üzembe helyezett.

## <a name="next-steps"></a>További lépések

Az ebben az útmutatóban ajánlott biztonsági engedélyek és vezérlők megvalósításához tekintse át [a naplókhoz való hozzáférés kezelését.](manage-access.md)
