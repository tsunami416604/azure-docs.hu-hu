---
title: Az Azure Monitor riasztások hibaelhárítása |} A Microsoft Docs
description: Gyakori problémák, hibák, és naplóriasztási szabály az Azure-beli megoldásuk.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 1c7712fc2ce55a3d22995bb119a9ee485a064903
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683390"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Az Azure Monitor riasztások hibaelhárítása  

Ez a cikk bemutatja, hogyan, előfordulhat, hogy amikor a beállítása az Azure Monitor riasztások leggyakoribb hibák elhárításához. Funkció vagy naplóriasztások konfigurációját a gyakori problémák megoldását is tartalmazza. 

Az előfizetési időszak *naplóriasztások* ismerteti, hogy a napló lekérdezés alapján fire riasztások egy [Azure Log Analytics-munkaterület](../learn/tutorial-viewdata.md) vagy a [Azure Application Insights](../../azure-monitor/app/analytics.md). További információ funkciót, terminológia és-típusok a [Naplóriasztások az Azure Monitor](../platform/alerts-unified-log.md).

> [!NOTE]
> Ez a cikk nem tekinti a esetben, ha az Azure portal megjeleníti a riasztási szabály elindítva, és értesítést nem társított műveletcsoport végzi. Ezekben az esetekben a részleteket lásd: [létrehozása és kezelése az Azure Portalon Műveletcsoportok](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>Riasztás nem aktiválódik.

Az alábbiakban néhány gyakori okáról miért állapota egy konfigurált [riasztási szabály az Azure monitorban](../platform/alerts-log.md) nem jelenít meg [, *aktivált* várt](../platform/alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Naplók adatok betöltési ideje

Naplóriasztás rendszeres időközönként fut a lekérdezés alapján [Log Analytics](../learn/tutorial-viewdata.md) vagy [Application Insights](../../azure-monitor/app/analytics.md). Az Azure Monitor világszerte több ezer ügyfelünk különböző forrásokból származó adatok több terabájt feldolgozza, mert a szolgáltatás az ki van téve a változó idő késéseket. További információkért lásd: [adatok betöltési ideje az Azure Monitor naplóira](../platform/data-ingestion-time.md).

Késések csökkentése érdekében, a rendszer megvárja, és újrapróbálkozik a többször is feldolgozza a riasztási lekérdezés Ha megtalálja, hogy a szükséges adatok még nem elemezhető. A rendszer állítsa ezzel exponenciálisan növelve várakozási időt tartalmaz. A riasztás akkor aktiválódik, csak után az adatok rendelkezésre áll, így a késleltetés lassú Adatbetöltési naplóadatok okozhatja. 

### <a name="incorrect-time-period-configured"></a>Helytelen időtartam konfigurálva

A cikkben leírtak szerint [naplóriasztások terminológiája](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), az adott időszakban, a konfigurációban megadott meghatározza az időtartományt a lekérdezés. A lekérdezés csak ezen tartományában jöttek létre rekordokat adja vissza. 

Az adott időszakban korlátozza a visszaélések megelőzése érdekében egy napló lekérdezés beolvasni az adatokat, és azt minden olyan alkalommal parancs megkerüli (például **ezelőtt**) a napló lekérdezésben használt. Például ha az adott időszakban 60 percre van beállítva, és a lekérdezés futtatásakor: 1:15-kor, csak a rekordok között 12:15-kor és 1:15-kor létrehozott szolgálnak a napló lekérdezés. Ha a napló-lekérdezést használ hasonló idő parancsot **ezelőtt (1d)**, a lekérdezés még mindig csak használja a 12:15-kor és 1:15-kor adatait, mert az időszak hibagyakorisága értékre van állítva.

Ellenőrizze, hogy az adott időszakban, a konfiguráció megfelel-e a lekérdezést. A példához korábban Ha a napló lekérdezés **ezelőtt (1d)** a Zöld jelölő, az időszakot kell megadni 24 órás vagy 1440 perc (vörös jelzi). Ez a beállítás biztosítja, hogy a lekérdezés megfelelően fut-e.

![Időtartam](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Beállítás riasztások mellőzése

A 8. lépés a cikkben leírtak szerint [egy riasztási szabály létrehozása az Azure Portalon](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal), riasztások adjon meg egy **riasztások mellőzése** elindítása és az értesítési műveletek elrejtését konfigurálva összeget idő. Ennek eredményeképpen előfordulhat, hogy úgy gondolja, hogy a riasztás nem aktiválódik. Valójában a fejeződött aktiválódik, de a rendszer mellőzte az.  

![Riasztások felfüggesztése](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Helytelen metrikamérési riasztási szabályt

*Metrikus egység naplóriasztások* vannak, amelyek speciális képességek és a egy korlátozott riasztási lekérdezési szintaxis riasztások altípusa. Egy metrikamérési naplóriasztási szabály szükséges a lekérdezés kimenete egy metrika idősorozat kell. Azt jelenti kimenete egy különálló, egyenlő méretű időszakok és a megfelelő összesített értékeket tartalmazó tábla. 

Választhatja a tábla mellett további változókat rendelkezik **AggregatedValue**. Ezek a változók segítségével rendezi a táblát. 

Tegyük fel például, egy metrikamérési naplóriasztási szabály úgy lett konfigurálva, mint:

- A lekérdezés `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- 6 órás időtartammal
- az 50 küszöbérték
- három egymás utáni incidensek a riasztási logika
- **Összesített esetén** céltárhelyként **$table**

A parancs tartalmazza **összefoglalója... által** két változót, és (**időbélyeg** és **$table**), a rendszer úgy dönt, **$table** a **összesített esetén** . A rendszer az eredményként kapott tábla szerint rendezi a **$table** mező, az alábbi képernyőképen látható módon. Úgy tűnik, az a többszörös majd **AggregatedValue** tábla az egyes példányok (például **availabilityResults**) megtekintéséhez, hogy voltak-e legalább három egymás utáni incidensek.

![Metrikus egység a lekérdezés végrehajtása több értékkel rendelkező](media/alert-log-troubleshoot/LogMMQuery.png)

Mivel **összesített esetén** van meghatározva, **$table**, az adatok alapján vannak rendezve, van egy **$table** (vörös jelzi) oszlopban. Majd azt a csoportot, és típusú keresse meg a **összesített esetén** mező. 

Például **$table**, értékei **availabilityResults** fogja tekinteni egy diagram/entitás (a narancsszínnel jelzi). Az entitás/érték diagram a riasztási szolgáltatás ellenőrzi három egymás utáni incidensek (zöld jelzi). Az illetéktelen behatolásokat figyelmeztetnek, a tábla érték **availabilityResults**. 

Hasonlóképpen ha három egymás utáni incidensek fordulhat elő, semmilyen más érték **$table**, ugyanarra a dologra egy másik riasztási értesítés aktiválódik. A riasztási szolgáltatás automatikusan időpontja alapján rendezi az értékeket egy diagram/entitás (a narancsszínnel jelzi).

Most tegyük fel, hogy a szabály a metrikamérési riasztás módosítva lett, és a lekérdezés a(z) `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`. A további konfigurálást előtt, beleértve a riasztási logika három egymás utáni incidensek maradt megegyezik. A **összesített esetén** beállítás ebben az esetben akkor **időbélyeg** alapértelmezés szerint. Csak egy érték van megadva a lekérdezést a **összefoglalója... által** (azaz **időbélyeg**). Például a korábbi példában végrehajtási végén a kimenet a következő módon lenne.

   ![Metrikus egység lekérdezés-végrehajtás egyetlen értékkel](media/alert-log-troubleshoot/LogMMtimestamp.png)

Mivel **összesített esetén** van meghatározva, **időbélyeg**, az adatok alapján vannak rendezve, akkor a **időbélyeg** (vörös jelzi) oszlopban. Azt a csoportosítás akkor **időbélyeg**. Például értékei `2018-10-17T06:00:00Z` fogja tekinteni egy diagram/entitás (a narancsszínnel jelzi). Az entitás/érték diagram, a riasztási szolgáltatás megtalálja a nem egymás utáni incidensek (mivel minden egyes **időbélyeg** értéke csak egy bejegyzés). Ezért soha nem aktiválódik a riasztás. Ebben az esetben a felhasználó vagy kell:

- A helyőrző a változó vagy egy létező változó hozzáadása (például **$table**) megfelelően rendezés használatával a **összesített esetén** mező.
- Konfigurálja újra a riasztási szabály által használandó riasztási logika alapján **megsértésére teljes** helyette.

## <a name="log-alert-fired-unnecessarily"></a>Feleslegesen aktivált riasztás

Egy konfigurált [riasztási szabály az Azure monitorban](../platform/alerts-log.md) előfordulhat, hogy indítható váratlanul megtekintheti a [Azure Alerts](../platform/alerts-managing-alert-states.md). A következő szakaszokban néhány gyakori okáról.

### <a name="alert-triggered-by-partial-data"></a>Részleges adatok által aktivált riasztás

A log Analytics és az Application Insights a feldolgozási késedelmeket és feldolgozási vonatkoznak. Ha egy naplóriasztás-lekérdezés futtatásához, akkor előfordulhat, hogy nem érhetők el adatok, vagy csak bizonyos adatok érhetők el. További információkért lásd: [adatok betöltési idő jelentkezzen be az Azure Monitor](../platform/data-ingestion-time.md).

A riasztási szabály konfigurációjától függően misfiring fordulhat elő, ha nem áll fenn adatok vagy naplók az adatok csak részlegesen riasztási végrehajtás időpontjában. Ezekben az esetekben azt javasoljuk, hogy a riasztási lekérdezés vagy a konfiguráció módosítását. 

Például ha a riasztási szabály aktiválódik, ha analytics-lekérdezések eredményeinek száma kisebb, mint 5 konfigurálja, a riasztás akkor aktiválódik, ha nem adatok (nulla rekord) vagy a részleges eredményeket (egy rekord). Azonban az adatok feldolgozási késleltetés után ugyanabból a lekérdezés az összes adat biztosíthatnak 10 rekordok eredménye.

### <a name="alert-query-output-is-misunderstood"></a>Riasztáshoz kapcsolódó lekérdezés kimenete a böngésző

A logic a log analytics-lekérdezések riasztásokhoz adja meg. Az elemzési lekérdezés különböző big Data jellegű adatok és a matematika függvényekkel használható. A riasztási szolgáltatás a lekérdezés fut, az adatokat a megadott időtartam során megadott időközönként. A riasztási szolgáltatás finom módosítást hajt végre a lekérdezést a riasztási típus alapján. Megtekintheti, hogy ez a változtatás a **végrehajtandó lekérdezés** szakaszában a **jellogika konfigurálása** képernyőjén:

![Végrehajtandó lekérdezés](media/alert-log-troubleshoot/LogAlertPreview.png)

A **végrehajtandó lekérdezés** mező el a napló riasztási szolgáltatás futtatható. Ha szeretné megtudni, mi a riasztási lekérdezés kimeneti lehet, hogy a riasztás létrehozása előtt, a megadott lekérdezés és a timespan keresztül futtathatja a [Analytics-portál](../log-query/portals.md) vagy a [szövegelemzési API](https://docs.microsoft.com/rest/api/loganalytics/).

## <a name="log-alert-was-disabled"></a>Riasztás le lett tiltva.

Az alábbi szakaszok tartalmazzák okok miatt miért az Azure Monitor előfordulhat, hogy tiltsa le a [riasztási szabály](../platform/alerts-log.md).

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>Erőforrás, ahol a riasztás létre lett hozva már nem létezik

Az Azure monitorban létrehozott naplóriasztási szabályok a céloznia egy adott erőforrást, mint például az Azure Log Analytics-munkaterületet egy Azure Application Insights alkalmazást és egy Azure-erőforrás. A riasztás naplózási szolgáltatás analytics-lekérdezések, az adott cél a szabályban megadott futtat. De miután szabály létrehozása, felhasználók gyakran látogasson el Azure – törlésére vagy áthelyezésére az Azure – a riasztási szabály célját belül. A riasztási szabály célját már nem érvényes, mert a szabály végrehajtása sikertelen lesz.

Ezekben az esetekben az Azure Monitor letiltja a riasztás, és biztosítja, hogy nem a számlázás feleslegesen futtatásakor a szabály nem folyamatosan eloszlatása időszak (például hetente). A pontos idő, amikor az Azure Monitor-n keresztül a riasztás le van tiltva talál [Azure-tevékenységnapló](../../azure-resource-manager/resource-group-audit.md). Az Azure-tevékenységnapló új esemény kerül, amikor az Azure Monitor letiltja a riasztási szabály.

A következő minta esemény az Azure-tevékenységnapló-riasztási szabály, amely folyamatos hiba miatt le van tiltva van.

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>Egy riasztás a használt lekérdezés nem érvényes

Minden egyes létrehozott a konfigurálás részeként az Azure Monitor riasztási szabály analytics-lekérdezések, amelyek a riasztási szolgáltatás rendszeres időközönként fut, adjon meg. Az elemzési lekérdezés helytelen szintaxisú előfordulhat szabály létrehozása vagy frissítése során. De egyes esetekben egy időszakon belül, a riasztási szabály a megadott lekérdezés fejlesztheti szintaxishibát és hatására a szabály végrehajtása sikertelen. Néhány miért analytics-lekérdezések egy riasztási szabály megadott nyelven fejleszthet hibák leggyakoribb okai a következők:

- A lekérdezés írt [több erőforrást futtathatja](../log-query/cross-workspace-query.md). És a egy vagy több megadott erőforrás már nem létezik.
- Nincs az adatfolyam az analytics platform lett. A [lekérdezés-végrehajtás olyan hibaüzenetet ad](https://dev.loganalytics.io/documentation/Using-the-API/Errors) , mert nem szerepel megjeleníthető adat a megadott lekérdezés.
- Változások a [lekérdezési nyelvet](https://docs.microsoft.com/azure/kusto/query/) egy parancsok és a funkciók módosított formában tartalmazza. Ezért egy riasztási szabályt a korábban megadott lekérdezés már nem érvényes.

[Az Azure Advisor](../../advisor/advisor-overview.md) ezzel a viselkedéssel kapcsolatos figyelmeztetést jelenít meg. Egy javaslatot ad hozzá a meghatározott riasztási szabály az Azure Advisor, a magas rendelkezésre állású közepes hatás kategóriáját és leírását a "Javítás a riasztási szabály figyelési biztosítására." A riasztási szabály egy riasztási lekérdezés után az Azure Advisor által adott hét napig javaslat nem kijavíthatók, ha a Azure Monitor a riasztás letiltásához, és győződjön meg arról, hogy nem a számlázás feleslegesen a szabály nem üzemidejének folyamatosan egy méretezhető időszak) például egy hét).

A pontos idő, amikor az Azure Monitor letiltotta a riasztási szabály az eseményt keres annak [Azure-tevékenységnapló](../../azure-resource-manager/resource-group-audit.md).

## <a name="next-steps"></a>További lépések

- Ismerje meg [naplóriasztások az Azure-ban](../platform/alerts-unified-log.md).
- Tudjon meg többet [Application Insights](../../azure-monitor/app/analytics.md).
- Tudjon meg többet [lekérdezések naplózását](../log-query/log-query-overview.md).
