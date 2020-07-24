---
title: A Azure Monitor naplózási értesítéseinek hibakeresése | Microsoft Docs
description: Gyakori hibák, hibák és megoldások az Azure-beli naplózási riasztási szabályokhoz.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: b8edbbc397a56f4fcf5b3ae070f04ca61659d98d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87045342"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>A Azure Monitor naplózási értesítéseinek hibakeresése  

Ebből a cikkből megtudhatja, Hogyan oldhatók meg a naplózási riasztásokkal kapcsolatos gyakori problémák Azure Monitor. Emellett megoldásokat kínál a naplók működésével és konfigurációjával kapcsolatos gyakori problémákra.

A *log-riasztások* kifejezés azokat a szabályokat ismerteti, amelyek az [Azure log Analytics-munkaterületen](../log-query/get-started-portal.md) vagy az [Azure Application Insights](../log-query/log-query-overview.md)-ban való naplózási lekérdezésen alapulnak. További információ a [naplózási riasztásokban](../platform/alerts-unified-log.md)szereplő funkciókról, fogalmakról és típusokról Azure monitor.

> [!NOTE]
> Ez a cikk nem vizsgálja azokat az eseteket, amelyekben a Azure Portal riasztási szabályt váltott ki, és egy kapcsolódó műveleti csoport nem hajt végre értesítést. Ilyen esetekben tekintse meg a következő témakör részleteit: [műveleti csoportok létrehozása és kezelése a Azure Portalban](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>A napló riasztása nem tűz

Íme néhány gyakori ok, amiért egy konfigurált [naplózási riasztási szabály állapota Azure monitor](../platform/alerts-log.md) nem a [várt módon *fired* ](../platform/alerts-managing-alert-states.md)jelenik meg.

### <a name="data-ingestion-time-for-logs"></a>Naplók adatfeldolgozási ideje

A naplózási riasztás időszakonként [log Analytics](../log-query/get-started-portal.md) vagy [Application Insights](../log-query/log-query-overview.md)alapján futtatja a lekérdezést. Mivel Azure Monitor több ezer ügyfelet dolgoz fel több ezer ügyféltől a különböző forrásokból a világ bármely részén, a szolgáltatás a különböző késések miatt nem alkalmas. További információ: [adatfeldolgozási idő Azure monitor naplókban](../platform/data-ingestion-time.md).

A késések enyhítése érdekében a rendszer többször is megvárja és újrapróbálkozik a riasztási lekérdezéssel, ha úgy találja, hogy a szükséges adatmennyiség még nincs betöltve. A rendszer exponenciálisan növekszik a várakozási idő beállítása. A naplózási riasztás csak az elérhető adatmennyiség után aktiválódik, így a késés a naplózási adatmennyiség lassú betöltése miatt lehet.

### <a name="incorrect-time-period-configured"></a>Helytelen időtartam konfigurálva

A következő témakörben leírtak szerint: a [naplózási riasztások terminológiája](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)című cikk a konfigurációban megadott időtartamot határozza meg a lekérdezés időtartománya alapján. A lekérdezés csak azokat a rekordokat adja vissza, amelyek ezen a tartományon belül lettek létrehozva.

Az időszak korlátozza a naplózási lekérdezéshez beolvasott adatvesztést a visszaélések megakadályozása érdekében, és megkerüli a naplózási lekérdezésekben használt idő (például a **ago**) parancsot. Ha például az időszak 60 percre van állítva, és a lekérdezés a 1:15 ÓRAKOR fut, akkor a rendszer csak a 12:15 PM és az 1:15 közötti rekordokat használja a napló lekérdezéséhez. Ha a naplózási lekérdezés olyan időparancst használ, mint az **ago (1d)**, a lekérdezés továbbra is csak 12:15 pm és 1:15 PM közötti adatmennyiséget használ, mivel az adott időszak az adott intervallumra van beállítva.

Győződjön meg arról, hogy a konfigurációban szereplő időszak megfelel a lekérdezésnek. A korábban bemutatott példa esetén, ha a napló lekérdezése az **ago (1d)** és a zöld jelölő használatával van megadva, az időtartamot 24 órára vagy 1 440 percre kell beállítani (piros színnel jelölve). Ez a beállítás biztosítja, hogy a lekérdezés a kívánt módon fusson.

![Időtartam](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>A riasztások mellőzése beállítás be van állítva

A [napló riasztási szabály létrehozásáról](../platform/alerts-log.md#create-a-log-alert-rule-with-the-azure-portal)szóló cikk 8. lépésében leírtak szerint Azure Portal a naplózási riasztások lehetőséget biztosítanak a **riasztások letiltására** , amely letiltja az aktiválási és értesítési műveleteket a beállított időtartamra. Ennek eredményeképpen előfordulhat, hogy a riasztás nem tűz. Valójában nem volt tűz, de letiltották.  

![Riasztások mellőzése](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>A metrika mértékének riasztási szabálya helytelen

A *metrikai mérési naplók* a riasztások altípusai, amelyek különleges képességekkel és korlátozott riasztási lekérdezési szintaxissal rendelkeznek. A metrikák mérési naplójának riasztására vonatkozó szabályhoz a lekérdezés kimenetének metrikus idősorozatra van szüksége. Ez azt eredményezi, hogy a kimenet olyan tábla, amely különböző, azonos méretű időszakokkal és megfelelő összesített értékekkel együtt.

További változókat is megadhat a táblában a **AggregatedValue**mellett. Ezeket a változókat a tábla rendezésére használhatja.

Tegyük fel például, hogy egy mérőszám mérési naplójának riasztására vonatkozó szabály a következőként lett konfigurálva:

- Lekérdezés`search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- 6 órás időtartam
- 50 küszöbértéke
- Három egymást követő jogsértés riasztási logikája
- **Összesítés** a **$Table** kiválasztásakor

Mivel a parancs **összefoglalót tartalmaz... és két** változót biztosít (**timestamp** és **$Table**), a rendszer az **összesítéshez** **$Table** választ. A rendszeren a **$Table** mező alapján rendezi az eredmény táblát, ahogy az alábbi képernyőképen is látható. Ezt követően a több **AggregatedValue** -példányt is megtekinti az egyes táblákhoz (például **availabilityResults**), hogy ellenőrizze, hogy három vagy több egymást követő megsértés történt-e.

![Metrikus mérési lekérdezés végrehajtása több értékkel](media/alert-log-troubleshoot/LogMMQuery.png)

Mivel az **Összesítés** a **$tableon**van meghatározva, az adatokat egy **$Table** oszlop szerint rendezi a rendszer (piros színnel jelölve). Ezután csoportosítjuk és megkeresjük a mező **összesítésének** típusait.

A **$Table**esetében például a **availabilityResults** értékei egyetlen mintaterületnek/entitásnak tekintendők (narancssárga színnel jelölve). Ebben az értékben a mintaterület/entitás esetében a riasztási szolgáltatás három egymást követő jogsértést keres (zöld színnel jelezve). A szabálysértések riasztást váltanak ki a tábla értékének **availabilityResults**.

Hasonlóképpen, ha három egymást követő szabálysértés fordul elő a **$Table**bármely más értéke esetén, a rendszer egy másik riasztási értesítést indít el ugyanarra a dologra. A riasztási szolgáltatás automatikusan rendezi az értékeket egy adott parcellában/entitásban (narancssárga színnel jelölve).

Most tegyük fel, hogy a metrikák mérési naplójának riasztására vonatkozó szabály módosult, és a lekérdezés volt `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` . A konfiguráció többi része a korábbinál is megmaradt, beleértve a három egymást követő szabálysértés esetén a riasztási logikát. Ebben az esetben az **összesítési** lehetőség alapértelmezés szerint **timestamp** . A lekérdezésben csak egy érték van megadva az **összegzéshez... (azaz** **időbélyeg**). A korábbi példához hasonlóan a végrehajtás végén lévő kimenet a következőképpen lesz látható.

   ![Metrikus mérési lekérdezés végrehajtása az egyes szám értékkel](media/alert-log-troubleshoot/LogMMtimestamp.png)

Mivel az **Összesítés** az **időbélyegző**alapján van definiálva, az adatokat az **időbélyegző** oszlop szerint rendezi a rendszer (piros színnel jelölve). Ezt követően **időbélyegző**alapján csoportosítjuk. Például a rendszer a következő értékeket `2018-10-17T06:00:00Z` fogja figyelembe venni: egy mintaterület/entitás (narancssárga színnel jelölve). Ebben az értékben a mintaterület/entitás esetében a riasztási szolgáltatás nem talál egymást követő szabálysértéseket (mivel minden **időbélyeg** -érték csak egy bejegyzést tartalmaz). Így a riasztás soha nem aktiválódik. Ilyen esetben a felhasználónak vagy a következőket kell tennie:

- Vegyen fel egy dummy változót vagy egy meglévő változót (például **$Table**) a megfelelő rendezéshez az **összesítési** mező alapján.
- Konfigurálja újra a riasztási szabályt úgy, hogy az a **teljes megszegésen** alapuló riasztási logikát használja.

## <a name="log-alert-fired-unnecessarily"></a>A naplózási riasztás szükségtelen

A [Azure monitor konfigurált naplózási riasztási szabály](../platform/alerts-log.md) váratlanul indítható az [Azure-riasztásokban](../platform/alerts-managing-alert-states.md)való megtekintésekor. Az alábbi szakaszok néhány gyakori okot ismertetnek.

### <a name="alert-triggered-by-partial-data"></a>A riasztások részlegesen aktiválva

A Log Analytics és Application Insights a betöltési késések és a feldolgozás hatálya alá esnek. Ha napló-riasztási lekérdezést futtat, akkor előfordulhat, hogy nem áll rendelkezésre adathozzáférés, vagy csak néhány rendelkezésre álló adattal rendelkezik. További információ: [adatfeldolgozási idő naplózása Azure monitorban](../platform/data-ingestion-time.md).

A riasztási szabály konfigurálásának módjától függően előfordulhat, hogy a rendszer a riasztások végrehajtásának időpontjában nem tartalmaz adatmennyiséget vagy részleges adatnaplót. Ilyen esetekben javasoljuk, hogy módosítsa a riasztás lekérdezését vagy konfigurációját.

Ha például úgy konfigurálja a naplózási riasztási szabályt, hogy az elemzési lekérdezés eredményeinek száma kevesebb, mint 5, a riasztás akkor aktiválódik, ha nincs adat (nulla rekord) vagy részleges eredmény (egy rekord). Az adatok betöltésének késleltetése után azonban a teljes adatmennyiséggel rendelkező lekérdezés 10 rekordot eredményezhet.

### <a name="alert-query-output-is-misunderstood"></a>A riasztási lekérdezés kimenete nem értelmezhető.

Egy elemzési lekérdezésben megadja a naplózási riasztások logikáját. Az elemzési lekérdezés különböző big dataeket és matematikai függvényeket használhat. A riasztási szolgáltatás a megadott időszakra vonatkozóan az adataival megadott időközönként futtatja a lekérdezést. A riasztási szolgáltatás a riasztás típusa alapján finom módosításokat végez a lekérdezésben. Ezt a **változást a következő témakörben** tekintheti meg: a **jel logikai beállítása** képernyő

![Végrehajtandó lekérdezés](media/alert-log-troubleshoot/LogAlertPreview.png)

A **végrehajtandó lekérdezés** mező a naplózási riasztások szolgáltatás futtatására szolgál. Ha szeretné megismerni, hogy a riasztási lekérdezés kimenete milyen lehet a riasztás létrehozása előtt, a megadott lekérdezést és a TimeSpan az [Analytics-portálon](../log-query/log-query-overview.md) vagy az [elemzési API](/rest/api/loganalytics/)-n keresztül futtathatja.

## <a name="log-alert-was-disabled"></a>A napló riasztása le lett tiltva

Az alábbi részekben felsorolunk néhány okot, amiért Azure Monitor letilthatja a [napló riasztási szabályát](../platform/alerts-log.md).

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>A riasztást létrehozó erőforrás már nem létezik

A Azure Monitor céljaként létrehozott riasztási szabályok egy adott erőforráshoz, például egy Azure Log Analytics-munkaterülethez, egy Azure Application Insights alkalmazáshoz és egy Azure-erőforráshoz tartoznak. A naplózási riasztási szolgáltatás ezután futtatja a megadott célra vonatkozó szabályban megadott elemzési lekérdezést. A szabályok létrehozása után azonban a felhasználók gyakran az Azure-ból törölnek, vagy az Azure-ban helyezik át őket – a napló riasztási szabályának célját. Mivel a riasztási szabály célja már nem érvényes, a szabály végrehajtása meghiúsul.

Ilyen esetekben a Azure Monitor letiltja a napló riasztását, és gondoskodik arról, hogy ne legyen feleslegesen számlázva, ha a szabály nem futtatható folyamatosan jelentős időszakra (például egy hétre). Megtudhatja, mikor Azure Monitor letiltotta a naplózási riasztást az [Azure-tevékenység naplóján](../../azure-resource-manager/management/view-activity-logs.md)keresztül. Ha Azure Monitor letiltja a napló riasztási szabályát, az Azure-tevékenység naplójában megjelenik egy esemény.

Az Azure-beli tevékenység naplójának következő mintája olyan riasztási szabályt tartalmaz, amely folyamatos meghibásodás miatt le van tiltva.

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>A naplózási riasztásban használt lekérdezés érvénytelen.

A Azure Monitor a konfiguráció részeként létrehozott összes naplózási riasztási szabálynak meg kell határoznia egy elemzési lekérdezést, amelyet a riasztási szolgáltatás rendszeresen fog futni. Az elemzési lekérdezés helyes szintaxissal rendelkezhet a szabályok létrehozásakor vagy frissítésekor. Bizonyos esetekben azonban előfordulhat, hogy a napló riasztási szabályában megadott lekérdezés szintaktikai hibákat tud kialakítani, és a szabály végrehajtásának sikertelenségét okozhatja. Néhány gyakori ok, amiért a naplózási riasztási szabályban megadott elemzési lekérdezés a következőkhöz tud hibákat kialakítani:

- A lekérdezés [több erőforrás között fut](../log-query/cross-workspace-query.md). Egy vagy több megadott erőforrás már nem létezik.
- A [metrikai típus naplózási riasztása](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) konfigurálva van egy riasztási lekérdezés, amely nem felel meg a szintaxis normáinak
- Az elemzési platformhoz nem történt adatfolyam. A [lekérdezés végrehajtása hibát jelez](https://dev.loganalytics.io/documentation/Using-the-API/Errors) , mert a megadott lekérdezéshez nem tartozik információ.
- A [lekérdezés nyelvének](/azure/kusto/query/) változásai a parancsok és függvények módosított formátumát tartalmazzák. Így a riasztási szabályban korábban megadott lekérdezés már nem érvényes.

[Azure Advisor](../../advisor/advisor-overview.md) figyelmeztet erre a viselkedésre. A rendszer javaslatot tesz az adott napló riasztási szabályának Azure Advisor, a közepes hatású magas rendelkezésre állás kategóriája és a "riasztási szabály kijavítása a figyeléshez" című cikk leírásával.

> [!NOTE]
> Ha a naplózási riasztási szabályban lévő riasztási lekérdezés nem kerül kijavításra, miután a Azure Advisor hét napig megadta a javaslatot, Azure Monitor letilthatja a naplózási riasztást, és gondoskodhat arról, hogy a szabály nem számítható fel feleslegesen, ha a szabály nem futtatható folyamatosan egy jókora (7 nap). Megtalálhatja azt a pontos időt, amikor a Azure Monitor letiltotta a napló riasztási szabályát, ha egy eseményt keres az Azure-beli [tevékenység naplójában](../../azure-resource-manager/management/view-activity-logs.md).

## <a name="alert-rule-quota-was-reached"></a>Elérte a riasztási szabály kvótáját

Az előfizetés és az erőforrás naplózott keresési riasztási szabályainak száma az [itt](https://docs.microsoft.com/azure/azure-monitor/service-limits)ismertetett kvóta-korlátozások hatálya alá tartozik.

### <a name="recommended-steps"></a>Javasolt lépések
    
Ha elérte a kvóta korlátját, a következő lépések segíthetnek a probléma megoldásában.

1. Próbálja meg törölni vagy letiltani a napló keresési riasztási szabályait, amelyeket a rendszer már nem használ.
2. Ha növelni kívánja a kvótakorlátot, hozzon létre egy támogatási kérést, és adja meg a következő információkat:

    - Azon előfizetések azonosítói, amelyeknél növelni kívánja a kvótakorlátokat
    - A kvóta növelésének oka
    - A kvóta növelésének erőforrás-típusa: **log Analytics**, **Application Insights** stb.
    - Kért kvótakorlát


### <a name="to-check-the-current-usage-of-new-log-alert-rules"></a>Az új naplózási riasztási szabályok aktuális használatának ellenõrzése
    
#### <a name="from-the-azure-portal"></a>Az Azure Portalról

1. Nyissa meg a *Riasztások* képernyőt, és kattintson a *Riasztási szabályok kezelése* elemre
2. Az *Előfizetés* legördülő vezérlővel szűrjön rá a szóban forgó előfizetésre
3. NE szűrjön konkrét erőforráscsoport, erőforrástípus vagy erőforrás nevére
4. A *jel típusa* legördülő menüben válassza a "keresés naplózása" lehetőséget.
5. Ellenőrizze, hogy az *Állapot* legördülő vezérlő Engedélyezve értére van-e állítva
6. A naplók keresési riasztási szabályainak teljes száma a szabályok lista felett jelenik meg

#### <a name="from-api"></a>Küldő API

- PowerShell – [Get-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/get-azscheduledqueryrule?view=azps-3.7.0)
- REST API – [List by subscription](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/listbysubscription)

## <a name="next-steps"></a>További lépések

- További információ a [log-riasztásokról az Azure-ban](../platform/alerts-unified-log.md).
- További információ a [Application Insightsról](../log-query/log-query-overview.md).
- További információ a [naplók lekérdezéséről](../log-query/log-query-overview.md).
