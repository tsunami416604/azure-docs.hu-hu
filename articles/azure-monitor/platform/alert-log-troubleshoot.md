---
title: Az Azure Monitor riasztások hibaelhárítása |} A Microsoft Docs
description: Gyakori problémák, hibák, és az Azure-beli naplóriasztási szabály megoldására.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 0c7189f1d43a114532b30b0c1aabe6f7cd4402d8
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578713"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Az Azure Monitor riasztások hibaelhárítása  

## <a name="overview"></a>Áttekintés

Ez a cikk bemutatja, hogyan állítson be az Azure Monitor riasztások leggyakoribb problémáinak megoldásához. Gyakori kérdés a funkció- vagy naplóriasztások konfigurációját megoldásokat is biztosít. 

Az előfizetési időszak **Naplóriasztások** ismerteti, hogy a napló lekérdezés alapján fire riasztások egy [Log Analytics-munkaterület](../learn/tutorial-viewdata.md) vagy [Application Insights](../../azure-monitor/app/analytics.md). További információ funkciót, terminológia és-típusok a [Naplóriasztások – áttekintés](../platform/alerts-unified-log.md).

> [!NOTE]
> Ez a cikk nem tekinti esetekben, amikor az Azure Portalon látható és a szabály által aktivált riasztás és a egy társított művelet (ok) ban által végrehajtott értesítést. Ezekben az esetekben, olvassa el a részleteket a cikkben a [Műveletcsoportok](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>Riasztás nem aktiválódik.

Az alábbiakban néhány gyakori okáról miért egy konfigurált [riasztási szabály az Azure monitorban](../platform/alerts-log.md) állapota nem jelenik meg [, *aktivált* várt](../platform/alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Naplók adatok betöltési ideje

Riasztás rendszeres időközönként fut a lekérdezés alapján [Log Analytics](../learn/tutorial-viewdata.md) vagy [Application Insights](../../azure-monitor/app/analytics.md). Az Azure Monitor világszerte több ezer ügyfelünk különböző forrásokból származó adatok több terabájt feldolgozza, mert a szolgáltatás ki van téve a változó késleltetés. További információkért lásd: [adatok betöltési idő, az Azure Monitor naplóira](../platform/data-ingestion-time.md).

Adatok Adatbetöltési késés csökkentése érdekében, a rendszer megvárja, és újrapróbálkozik a többször is feldolgozza a riasztási lekérdezés Ha megtalálja, hogy a szükséges adatok még nem elemezhető. A rendszer állítsa ezzel exponenciálisan növelve várakozási időt tartalmaz. A napló riasztási csak eseményindítók után az adatok érhető el, így azok késleltetés lassú lekérdezések naplóját adatbetöltés okozhatja. 

### <a name="incorrect-time-period-configured"></a>Helytelen időtartam konfigurálva

A cikkben leírtak szerint [naplóriasztások terminológiája](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)az idő, a megadott időszak konfigurációs meghatározza az időtartományt a lekérdezés. A lekérdezés csak a idő ezen tartományában jöttek létre rekordokat adja vissza. Adott időszakban korlátozza az adatokat, a visszaélések megelőzése érdekében naplólekérdezés beolvasott, és minden olyan alkalommal parancs megkerüli (például *ezelőtt*) napló lekérdezésben használt. Például ha az adott időszakban 60 percre van beállítva, és a lekérdezés futtatásakor: 1:15-kor, csak a rekordok között 12:15-kor és 1:15-kor létrehozott szolgálnak a napló lekérdezés. Ha a napló-lekérdezést használ hasonló idő parancsot *ezelőtt (1d)*, a lekérdezés még mindig csak használja a 12:15-kor és 1:15-kor közötti, mert az adott időszakban, hogy interval.* értékre van állítva

Ezért mindig ellenőrizze a konfiguráció megfelel az adott időintervallumban a lekérdezést. A példához korábban említettük, ha a napló lekérdezés *ezelőtt (1d)* ahogyan a Zöld jelölő, majd az adott időszakban kell állítani 24 órás vagy 1440 perc (mint a vörös), a lekérdezés végrehajtása szánt biztosításához.

![Adott időszakban](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Beállítás riasztások mellőzése

A 8. lépés a cikkben leírtak szerint [egy riasztási szabály létrehozása az Azure Portalon](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal), riasztások adjon meg egy **riasztások mellőzése** elindítása és az értesítési műveletek elrejtését konfigurált mennyisége eltelt idő. Ennek eredményeképpen előfordulhat, hogy úgy gondolja, hogy a riasztást a actuality volt, de a rendszer mellőzte az nem aktiválódik.  

![Riasztások mellőzése](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Helytelen metrikamérési riasztási szabályt

**Metrikus egység naplóriasztások** riasztások, amelyek speciális képességek és a egy korlátozott riasztási lekérdezési szintaxis altípusa vannak. Egy metrikamérési riasztási szabály kell lennie egy metrika idősorozat; kimeneti a lekérdezés egy különálló táblázat, hogy egyenlő méretű időszakoknak, valamint a megfelelő összesített értékeket. Ezenkívül felhasználók is választja, hogy a további változókat AggregatedValue mellett a táblázatban. Ezek a változók rendezi a táblát is használható. 

Tegyük fel például, egy metrikamérési riasztási szabály úgy lett konfigurálva, mint:

- lekérdezés:: `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- 6 órás időtartammal
- az 50 küszöbérték
- három egymás utáni incidensek a riasztási logika
- Összesített esetén $table céltárhelyként

Mivel a parancs tartalmazza *... összegzés szempontja* és a két változót (timestamp & $table), a rendszer úgy dönt, hogy $table *összesített esetén*. Az eredményként kapott tábla mező szerint rendezi *$table* alább látható módon, és ezután megvizsgálja az egyes táblatípus (például az availabilityResults) több AggregatedValue megtekintheti, ha 3 vagy több egymást követő megsértése történt.

![Több értékkel rendelkező metrika mérési lekérdezés végrehajtása](media/alert-log-troubleshoot/LogMMQuery.png)

Mint *összesített esetén* van meghatározva, *$table*, az adatok (ahogy (piros); $table oszlop alapján van rendezve, majd azt a csoportot, és keresse meg típusú *összesített esetén* mező (vagyis) a $table Példa: availabilityResults egy diagram/entitás (a legyenek kiemelve a narancssárga) is figyelembe veszi a értékei. Az érték diagram/entitás riasztási szolgáltatás ellenőrzi (a bemutatott zöld) előforduló három egymás utáni incidensek mely a riasztás első aktiválódik a "availabilityResults" érték. Hasonlóképpen ha három egymás utáni incidensek - $table semmilyen más érték a egy másik riasztási értesítés akkor aktiválódik, ugyanarra a dologra; a riasztási szolgáltatás automatikusan rendezési egy diagram/entitás (ahogy narancssárga) szereplő értékek időpontig.

Most tegyük fel, metrikamérési riasztási szabály módosítva lett, és a lekérdezés: `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` többi előtt, beleértve a riasztási logika három egymás utáni incidensek esetén is ugyanaz, mint a fennmaradó a config. "Aggregate során" lehetőség alapértelmezés szerint ebben az esetben lesz: időbélyeg. Mivel csak egy érték van megadva, a lekérdezés *... összegzés szempontja* (vagyis) *időbélyeg*; korábbi példához hasonló, a kimeneti lenne az alábbi képen szemléltetett módon végrehajtásának végén.

   ![Metrika mérési lekérdezés-végrehajtás egyetlen értékkel](media/alert-log-troubleshoot/LogMMtimestamp.png)

*Összesített esetén* van definiálva az időbélyeget, az adatok a rendezett *időbélyeg* oszlopot (ahogy (piros); majd azt csoportosítás időbélyeg például: értékei `2018-10-17T06:00:00Z` egy diagram vagy szervei (például minősülnek kiemeli a narancssárga). Az érték diagram/entitás riasztási szolgáltatás megtalálja, nem egymás utáni incidensek előforduló (az egyes időbélyegző-érték csak egy bejegyzést tartalmaz), és ezért riasztási rendszer soha nem aktiválódott beolvasása. Ezért ebben az esetben felhasználói kell vagy:

- Adjon hozzá egy helyőrző a változó vagy egy létező változó (például $table) megfelelően rendezési kész konfigurált "Aggregate esetén" mező használatával
- (Vagy) konfigurálja újra a riasztási logika alapján használandó riasztási szabályt *megsértésére teljes* inkább megfelelően

## <a name="log-alert-fired-unnecessarily"></a>Feleslegesen aktivált riasztás

Részletes következő miért néhány általános oka egy konfigurált [riasztási szabály az Azure monitorban](../platform/alerts-log.md) megtekintve indítható [Azure Alerts](../platform/alerts-managing-alert-states.md), amikor nem a várt fired.

### <a name="alert-triggered-by-partial-data"></a>Részleges adatok által aktivált riasztás

Kockázatszámító a Log Analytics és az Application Insights Analytics vonatkoznak rá a feldolgozási késedelmeket és -feldolgozási; amely miatt a megadott naplóriasztás-lekérdezés futtatásakor - időpontjában lehet egy esetet folyamatban van a rendelkezésre álló adatok nem vagy csak bizonyos adatok legyenek elérhetők. További információkért lásd: [adatok betöltési idő jelentkezzen be az Azure Monitor](../platform/data-ingestion-time.md).

A riasztási szabály konfigurációjától függően előfordulhat gépeltünk Gyújtóegységek esetén nincs vagy részleges naplók riasztási végrehajtás időpontjában. Ezekben az esetekben azt javasoljuk, hogy a riasztási lekérdezés vagy a konfiguráció módosítását. 

Például, ha a riasztási szabály úgy van beállítva, ha analytics-lekérdezések eredményeinek száma kisebb, mint 5 indít, majd a riasztást aktivál, ha nem adatok (nulla rekord) vagy a részleges eredményeket (egy rekord). Az adatok feldolgozási késleltetés után azonban ugyanabból a lekérdezés az összes adat biztosíthatnak 10 rekordok eredménye.

### <a name="alert-query-output-misunderstood"></a>Böngésző riasztási lekérdezés kimenete

A logic a log analytics-lekérdezések riasztásokhoz adja meg. Az elemzési lekérdezés big data- és matematikai függvények használhatja.  A riasztási szolgáltatás megadott időszakra vonatkozó adatokat tartalmazó megadott időközönként hajtja végre a lekérdezést. A riasztási szolgáltatás lehetővé teszi, hogy a megadott lekérdezés változás is történt a kiválasztott riasztási típus alapján. Ez a változás a "Lekérdezés futtatandó" szakaszában tekinthet meg *jellogika konfigurálása* képernyőn, ahogy az alábbi: ![Végrehajtandó lekérdezés](media/alert-log-troubleshoot/LogAlertPreview.png)

Mi látható az **végrehajtandó lekérdezés** mező el a napló riasztási szolgáltatás futtatható. A megadott lekérdezés, valamint a timespan keresztül futtathatja [Analytics-portál](../log-query/portals.md) vagy a [szövegelemzési API](https://docs.microsoft.com/rest/api/loganalytics/) Ha szeretné-e megismerni, mi a riasztási lekérdezés kimeneti lehet, mielőtt ténylegesen a riasztás létrehozásához.

## <a name="log-alert-was-disabled"></a>Riasztás le lett tiltva.

Az alábbiakban néhány ok, ami [riasztási szabály az Azure monitorban](../platform/alerts-log.md) Azure Monitor letilthatja.

### <a name="resource-on-which-alert-was-created-no-longer-exists"></a>Erőforrás, amelyen a riasztás létrehozása már nem létezik

Az Azure monitorban létrehozott naplóriasztási szabályok a céloznia egy adott erőforrás, például egy Azure Log Analytics-munkaterületet, az Azure Application Insights alkalmazást és Azure-erőforrás. És a naplózási riasztási szolgáltatás futtassa a megadott cél a szabályban megadott elemzési lekérdezés. Azonban a szabály létrehozása után a felhasználók folytassa az Azure-ból törlésére vagy áthelyezésére az Azure-ban – a riasztási szabály célját belül. A riasztási szabály célját már nem érvényes, mert a szabály végrehajtása sikertelen lesz.

Ezekben az esetekben az Azure Monitor a riasztás letiltásához, és győződjön meg, hogy az ügyfelek nem lesznek számlázva szükségtelen, ha a szabály maga nem hajtható végre, folyamatosan jelentős ideig például hetente. A pontos idő, amikor a riasztási szabály letiltotta az Azure Monitor segítségével a felhasználók talál [Azure-tevékenységnapló](../../azure-resource-manager/resource-group-audit.md). Az Azure-tevékenységnapló a riasztási szabály le van tiltva, az Azure-ban, ha új esemény kerül az Azure-tevékenységnapló.

A riasztási szabály letiltása miatt a folyamatos; az Azure-tevékenységnapló minta-esemény Az alábbiakban látható.

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

### <a name="query-used-in-log-alert-is-not-valid"></a>A napló riasztási használt lekérdezés nem érvényes

Minden egyes létrehozott a konfigurálás részeként az Azure Monitor riasztási szabály adjon meg egy analytics-lekérdezés a riasztási szolgáltatás rendszeres időközönként hajtja végre. Míg az elemzési lekérdezés előfordulhat, hogy helyes szintaxis a szabály létrehozásakor vagy frissítésekor idején. Néhány eset egy időszakon belül, a lekérdezés adja meg a naplóban lévő riasztási szabály szintaxishibát fejlesztheti és hatására a szabály végrehajtásának indítása sikertelen. Néhány miért egy riasztási szabály megadott analytics lekérdezési nyelven fejleszthet hibák leggyakoribb okai a következők:

- Lekérdezés írt [több erőforrást futtathatja](../log-query/cross-workspace-query.md) és a egy vagy több, a megadott erőforrás már nem létezik.
- Nincs az adatfolyam az analytics platform, amely miatt már nem a [lekérdezés-végrehajtás biztosít hiba](https://dev.loganalytics.io/documentation/Using-the-API/Errors) , nem szerepel megjeleníthető adat a megadott lekérdezés.
- Változások a [lekérdezési nyelv](https://docs.microsoft.com/azure/kusto/query/) rendelkezik a következő parancsokat, és funkciók módosított formában. Ezért a korábban megadott lekérdezés a riasztási szabály már nem érvényes.

A kell lennie figyelmezteti a felhasználót a ezt a viselkedést először keresztül [az Azure Advisor](../../advisor/advisor-overview.md). Egy javaslat a konkrét naplófájlokból riasztási szabály az Azure Advisor, a közepes hatású és leírás szerint "A napló riasztási szabályt, hogy a figyelés javításnak" magas rendelkezésre állású kategóriában lesz hozzáadva. Ha az Azure Advisor biztosító ajánlott érték hét nap után a megadott riasztási szabály a riasztási lekérdezés nem javítani. Ezután az Azure Monitor a riasztás letiltásához, és győződjön meg, hogy az ügyfelek nem lesznek számlázva szükségtelen, ha a szabály maga nem hajtható végre, folyamatosan jelentős ideig például hetente.

A pontos idő, amikor a riasztási szabály letiltotta az Azure Monitor segítségével a felhasználók talál [Azure-tevékenységnapló](../../azure-resource-manager/resource-group-audit.md). Az Azure-tevékenységnapló Ha a riasztási szabály le van tiltva, az Azure - esemény kerül be a Azure-tevékenységnapló.

## <a name="next-steps"></a>További lépések

- Ismerje meg [Naplóriasztások az Azure-riasztások](../platform/alerts-unified-log.md)
- Tudjon meg többet [Application Insights](../../azure-monitor/app/analytics.md)
- Tudjon meg többet [lekérdezések naplózását](../log-query/log-query-overview.md)
