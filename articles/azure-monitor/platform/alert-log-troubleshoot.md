---
title: Naplóriasztások elhárítása az Azure Monitorban | Microsoft dokumentumok
description: Gyakori problémák, hibák és az Azure-beli naplózási riasztási szabályok megoldásai.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: acb9784b745fa90fc9cd264162930020e6d64751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249034"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Naplóriasztások elhárítása az Azure Monitorban  

Ez a cikk bemutatja, hogyan oldhatja meg azokat a gyakori problémákat, amelyek akkor fordulhatnak elő, amikor naplóriasztásokat állít be az Azure Monitorban. Emellett megoldásokat is kínál a naplóriasztások működésével vagy konfigurációjával kapcsolatos gyakori problémákra.

A *naplóriasztások* kifejezés olyan szabályokat ír le, amelyek az [Azure Log Analytics-munkaterületen](../learn/tutorial-viewdata.md) vagy az Azure Application Insightsban naplólekérdezés alapján [jönnek.](../../azure-monitor/app/analytics.md) További információ a funkciókról, a terminólógiáról és a típusokról az [Azure Monitor naplóriasztásai ban.](../platform/alerts-unified-log.md)

> [!NOTE]
> Ez a cikk nem veszi figyelembe azokat az eseteket, amikor az Azure Portalon megjelenik egy riasztási szabály aktivált, és egy értesítés nem hajt végre egy társított műveletcsoport. Ilyen esetekben tekintse meg a [műveletcsoportok létrehozása és kezelése az Azure Portalon](../platform/action-groups.md)című témakörben található részleteket.

## <a name="log-alert-didnt-fire"></a>A naplóriasztás nem tűz

Íme néhány gyakori oka annak, hogy az Azure Monitorban egy konfigurált [naplóriasztási szabály](../platform/alerts-log.md) állapota miért nem [jelenik meg a várt *módon.* ](../platform/alerts-managing-alert-states.md)

### <a name="data-ingestion-time-for-logs"></a>A naplók adatbetöltési ideje

A naplóriasztás rendszeres időközönként futtatja a lekérdezést a [Log Analytics](../learn/tutorial-viewdata.md) vagy az [Application Insights](../../azure-monitor/app/analytics.md)alapján. Mivel az Azure Monitor több terabájtnyi adatot dolgoz fel több ezer ügyféltől a világ különböző forrásaiból, a szolgáltatás különböző időbeli késésekre hajlamos. További információ: [Adatok betöltési ideje az Azure Monitor naplóiban.](../platform/data-ingestion-time.md)

A késések csökkentése érdekében a rendszer megvárja, és újrapróbálkozik a riasztási lekérdezés többször, ha úgy találja, a szükséges adatok még nem lett betöltése. A rendszer exponenciálisan növekvő várakozási idővel rendelkezik. A naplóriasztás csak az adatok rendelkezésre állása után aktiválódik, így a késleltetés a naplóadatok lassú betöltése miatt következhet be.

### <a name="incorrect-time-period-configured"></a>Helytelen időszak konfigurálva

A [naplóriasztások terminológiájáról](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)szóló cikkben leírtak szerint a konfigurációban megadott időtartam határozza meg a lekérdezés időtartományát. A lekérdezés csak az ezen a tartományon belül létrehozott rekordokat adja vissza.

Az időtartam korlátozza a naplólekérdezésbe beolvasott adatokat a visszaélések megelőzése érdekében, és megkerüli a naplólekérdezésben használt bármely alkalommal **(például ezelőtt).** Ha például az időtartam 60 percre van állítva, és a lekérdezés 1:15-kor fut, a naplólekérdezéshez csak a 12:15 és 13:15 között létrehozott rekordok lesznek használatban. Ha a naplólekérdezés a **ezelőttihez (1d)** hasonló időparancsot használ, a lekérdezés továbbra is csak 12:15 és 13:15 óra közötti adatokat használ, mert az időtartam az adott intervallumra van beállítva.

Ellenőrizze, hogy a konfigurációban lévő időszak megegyezik-e a lekérdezéssel. A korábban bemutatott példában, ha a naplólekérdezés **ezelőtt (1d)** használja a zöld jelölővel, az időtartamot 24 órára vagy 1440 percre kell állítani (pirossal jelölve). Ez a beállítás biztosítja, hogy a lekérdezés a kívánt szerint fut.

![Időtartam](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>A Riasztások letiltása beállítás be van állítva

Amint azt a cikk [creating a log alert rule in the Azure portal](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)8. **Suppress Alerts** Ennek eredményeként, azt gondolhatja, hogy a riasztás nem tűz. Valójában, ez nem tűz, de elnyomták.  

![Riasztások letiltása](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>A metrikamérési riasztási szabály helytelen

*Metrikamérési napló riasztások* egy altípusa a napló riasztások, amelyek speciális képességekkel és a korlátozott riasztási lekérdezés szintaxisa. A metrikamérési napló riasztási szabály a lekérdezés kimenetét metrika-idősorozatnak kell tekinteni. Ez azt, hogy a kimenet egy különálló, egyenlő méretű időszakokkal rendelkező táblázat, valamint a megfelelő összesített értékekkel.

A táblázatban további változók is lehetnek az **AggregatedValue**mellett. Ezek a változók a tábla rendezésére használhatók.

Tegyük fel például, hogy egy metrikamérési naplóriasztási szabály a következőként lett konfigurálva:

- Lekérdezés e`search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- 6 órás időszak
- 50-es küszöbérték
- Riasztási logika három egymást követő jogsértésesetén
- **Összesített a** **kiválasztott $table**

Mivel a parancs magában **foglalja össze ... **két változóval **(időbélyeg** gel és **$table)** a rendszer az **Összesített időpont** **$table** választja. A rendszer az eredménytáblát a **$table** mező szerint rendezi, ahogy az a következő képernyőképen látható. Ezután megvizsgálja az egyes táblatípusok (például **availabilityResults)** több **AggregatedValue-példányát,** hogy lássa, három vagy több egymást követő szabályszegés történt-e.

![Metrikamérési lekérdezés végrehajtása több értékkel](media/alert-log-troubleshoot/LogMMQuery.png)

Mivel az **Összesített kapcsolat** **$table**van definiálva, az adatok **egy $table** oszlopra vannak rendezve (piros sal jelölve). Ezután csoportosítjuk és keressük az **Összesített adatok** mező típusait.

$table esetében **$table**például az **elérhetőségi értékekAz eredmények** egy telek/entitásként lesznek tekintve (narancssárga jelzésben). Ebben az értékábrázolásban/entitásban a riasztási szolgáltatás három egymást követő jogsértést ellenőriz (zöld színnel jelölve). A rések riasztást váltanak ki a tábla **értékének elérhetőségérőlEredmények.**

Hasonlóképpen, ha három egymást követő megsértése történik bármely más érték **$table,** egy másik riasztási értesítés aktiválódik az azonos dolog. A riasztási szolgáltatás automatikusan rendezi az értékeket egy telek/entitás (narancssárga) idő szerint.

Most tegyük fel, hogy a metrikamérési napló riasztásának szabálya módosult, és a lekérdezés a `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`. A konfiguráció többi része ugyanaz maradt, mint korábban, beleértve a riasztási logikát három egymást követő szabályszegésesetén. Ebben az esetben az **Összesített beállítás** alapértelmezés szerint **időbélyeg.** Csak egy érték van megadva a lekérdezés **összegző ... (azaz** **időbélyeg).** A korábbi példához hasonlóan a végrehajtás végén lévő kimenet is a következőképpen látható.

   ![Metrikus mérési lekérdezés végrehajtása egyedi értékkel](media/alert-log-troubleshoot/LogMMtimestamp.png)

Mivel az **Összesített időpont** **időbélyegen**van definiálva, az adatok az **időbélyeg** oszlopban vannak rendezve (piros jel). Ezután **az időbélyeg**szerint csoportosítunk. Például az `2018-10-17T06:00:00Z` értékek egy telek/entitásként lesznek tekintve (narancssárga jelzés). Ebben az értékábrázolásban/entitásban a riasztási szolgáltatás nem talál egymást követő incidenseket (mivel minden **időbélyeg-értéknek** csak egy bejegyzése van). Tehát a riasztás soha nem aktiválódik. Ilyen esetben a felhasználónak:

- Adjon hozzá egy dummy változót vagy egy meglévő változót (például **$table)** a helyes rendezéshez az **Összesített állapot** mező használatával.
- Konfigurálja újra a riasztási szabályt, hogy a **teljes megsértésen** alapuló riasztási logika legyen.

## <a name="log-alert-fired-unnecessarily"></a>Szükségtelenül kilőtt naplóriasztás

Előfordulhat, hogy az Azure Monitorban egy konfigurált [naplóriasztási szabály](../platform/alerts-log.md) váratlanul aktiválódik, amikor megtekinti azt az [Azure Alerts](../platform/alerts-managing-alert-states.md)szolgáltatásban. A következő szakaszok néhány gyakori okot ismernek.

### <a name="alert-triggered-by-partial-data"></a>Részleges adatok által kiváltott riasztás

A Log Analytics és az Application Insights betöltési késedelmek és feldolgozás oka a naplózás. Naplóriasztási lekérdezés futtatásakor előfordulhat, hogy nem áll rendelkezésre adat, vagy csak néhány adat érhető el. További információ: [Adatbetöltési idő naplózása az Azure Monitorban című témakörben.](../platform/data-ingestion-time.md)

Ariasztási szabály konfigurálásának módjától függően előfordulhat, hogy a riasztás végrehajtásakor nincsenek adatok vagy részleges adatok a naplókban. Ilyen esetekben azt javasoljuk, hogy módosítsa a riasztási lekérdezésvagy konfiguráció.

Ha például úgy konfigurálja a naplóriasztási szabályt, hogy akkor aktiválódjon, ha egy elemzési lekérdezés ből származó eredmények száma 5-nél kisebb, a riasztás akkor aktiválódik, ha nincs adat (nulla rekord) vagy részleges eredmény (egy rekord). De az adatok betöltési késleltetése után ugyanaz a lekérdezés teljes adatokkal 10 rekord eredménye lehet.

### <a name="alert-query-output-is-misunderstood"></a>A riasztási lekérdezés kimenetét félreértik

Adja meg a naplóriasztások logikáját egy elemzési lekérdezésben. Az elemzési lekérdezés különböző big data- és matematikai függvényeket használhat. A riasztási szolgáltatás a lekérdezést egy megadott időszakra megadott adatokkal megadott időközönként futtatja. A riasztási szolgáltatás finom módosításokat hajt végre a lekérdezésen a riasztás típusa alapján. Ezt a módosítást a **Végrehajtandó lekérdezés** szakaszban tekintheti meg a **Jellogika konfigurálása** képernyőn:

![Végrehajtandó lekérdezés](media/alert-log-troubleshoot/LogAlertPreview.png)

A **végrehajtandó lekérdezés** mező a naplóriasztási szolgáltatás futtatása. Ha szeretné megtudni, hogy mi lehet a riasztási lekérdezés kimenete a riasztás létrehozása előtt, futtathatja a megadott lekérdezést és az időtartamot az [Analytics portálon](../log-query/portals.md) vagy az [Analytics API-n](https://docs.microsoft.com/rest/api/loganalytics/)keresztül.

## <a name="log-alert-was-disabled"></a>A naplóriasztás le van tiltva

A következő szakaszok felsorolnak néhány okot, amiért az Azure Monitor letilthatja a [naplóriasztási szabályt.](../platform/alerts-log.md)

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>Az az erőforrás, amelyhez a riasztást létrehozták, már nem létezik

Az Azure Monitorban létrehozott naplóriasztási szabályok egy adott erőforrást céloznak meg, például egy Azure Log Analytics-munkaterületet, egy Azure Application Insights-alkalmazást és egy Azure-erőforrást. A naplóriasztási szolgáltatás ezután futtatja a szabályban megadott elemzési lekérdezést. De a szabály létrehozása után a felhasználók gyakran továbblépnek az Azure-ból – vagy az Azure-on belülre – a naplóriasztási szabály célpontjába. Mivel a riasztási szabály célja már nem érvényes, a szabály végrehajtása sikertelen lesz.

Ilyen esetekben az Azure Monitor letiltja a naplóriasztást, és biztosítja, hogy nem kell fizetnie feleslegesen, ha a szabály nem futtatható folyamatosan jelentős ideig (például egy hétig). Megtudhatja, hogy az Azure Monitor pontosan mikor tiltotta le a naplóriasztást az [Azure Activity Log szolgáltatáson](../../azure-resource-manager/management/view-activity-logs.md)keresztül. Az Azure-tevékenységnaplóban egy esemény kerül hozzáadásra, amikor az Azure Monitor letiltja a naplóriasztási szabályt.

A következő mintaesemény az Azure-tevékenységnaplóban egy riasztási szabály, amely le van tiltva egy folyamatos hiba miatt.

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

### <a name="query-used-in-a-log-alert-is-not-valid"></a>A naplóriasztásban használt lekérdezés érvénytelen

Az Azure Monitorban a konfiguráció részeként létrehozott minden egyes naplóriasztási szabálynak meg kell adnia egy elemzési lekérdezést, amelyet a riasztási szolgáltatás rendszeresen futtatni fog. Előfordulhat, hogy az elemzési lekérdezés szintaxisa a szabály létrehozásakor vagy a frissítéskor helyes. De néha egy bizonyos idő alatt a naplóriasztási szabályban megadott lekérdezés szintaktikai problémákat okozhat, és a szabály végrehajtása sikertelen lehet. Néhány gyakori oka annak, hogy a naplóriasztási szabályban megadott elemzési lekérdezések hibákat okozhatnak:

- A lekérdezés úgy van megírva, hogy [több erőforráson fusson.](../log-query/cross-workspace-query.md) És egy vagy több a megadott erőforrások már nem létezik.
- [Metrikamérési típusú napló riasztás](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) konfigurálva van egy riasztási lekérdezés nem felel meg a szintaxis normák
- Nem történt adatfolyam az elemzési platformra. A [lekérdezés végrehajtása hibát ad,](https://dev.loganalytics.io/documentation/Using-the-API/Errors) mert nincsadat a megadott lekérdezéshez.
- A [lekérdezési nyelv](https://docs.microsoft.com/azure/kusto/query/) módosításai közé tartozik a parancsok és függvények módosított formátuma. Így a riasztási szabály korábbi lekérdezése már nem érvényes.

[Az Azure Advisor](../../advisor/advisor-overview.md) figyelmezteti Erre a viselkedésre. A rendszer javaslatot ad hozzá az Azure Advisor adott naplóriasztási szabályához, a közepes hatással rendelkező magas rendelkezésre állás kategóriájába, valamint a "Naplóriasztási szabály javítása a figyelés biztosítása érdekében" leírásra vonatkozóan. Ha a naplóriasztási szabályban szereplő riasztási lekérdezést nem javítja a rendszer, miután az Azure Advisor hét napos ajánlást adott, az Azure Monitor letiltja a naplóriasztást, és biztosítja, hogy a rendszer szükségtelenül ne számlázhassa, ha a szabály nem futtatható folyamatosan egy jókora ideig ( mint egy hét).

Az Azure Monitor pontosan azt az időpontot állapíthatja meg, amikor az Azure-figyelő letiltotta a naplóriasztási szabályt, ha megkeres egy eseményt az [Azure-tevékenységnaplóban.](../../azure-resource-manager/management/view-activity-logs.md)

## <a name="next-steps"></a>További lépések

- Ismerje meg [a naplóriasztásokat az Azure-ban.](../platform/alerts-unified-log.md)
- További információ az [Application Insights](../../azure-monitor/app/analytics.md)ról.
- További információ a [naplólekérdezésekről.](../log-query/log-query-overview.md)
