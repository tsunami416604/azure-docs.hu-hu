---
title: Az Azure Monitor Log Analytics lekérdezési példa |} A Microsoft Docs
description: Példa a Kusto-nyelv használatával Log Analytics-lekérdezéseket.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/03/2018
ms.author: bwren
ms.openlocfilehash: d5cad3869e74f33a2d1a56352c658bb9c8f23db6
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52883107"
---
# <a name="log-analytics-query-examples"></a>Log Analytics lekérdezési példa
Ez a cikk példákat különböző [lekérdezések](../../azure-monitor/log-query/log-query-overview.md) használatával a [Kusto nyelvi](https://docs.microsoft.com/azure/kusto/query/) Log Analytics különféle típusú adatokat lekérdezni. Különböző módszerek használhatók konszolidálhatja és az adatok elemzése, így ezek a minták segítségével azonosíthatja a különböző stratégiákat használhatja a saját igényeinek megfelelően.  

Tekintse meg a [Kusto nyelvi referencia](https://docs.microsoft.com/azure/kusto/query/) használja ezeket a mintákat a különböző kulcsszavak részleteiért. Olvassa el a [leckében a lekérdezések létrehozásáról](get-started-queries.md) Ha most ismerkedik a Log Analytics szolgáltatásba.

## <a name="events"></a>Események

### <a name="search-application-level-events-described-as-cryptographic"></a>Alkalmazásszintű események keresése "Titkosítási" fürtkezelőben
Ez a példa olyan rekordokat keres az **Events** táblában, amelyekben az **EventLog** értéke _Application_, a **RenderedDescription** pedig tartalmazza a _cryptographic_ értéket. Az elmúlt 24 órából származó rekordokat tartalmazza.

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription == "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>Unmarshaling kapcsolatos események keresése
Keresés a táblákban **esemény** és **SecurityEvents** a rögzíti, hogy Dicséretben _unmashaling_.

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Szívverés

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>Adatokat küldő számítógépek száma a héten több mint hét nézetét mutató részletes diagram

Az alábbi példa diagram egyedi számítógépek számlálása, minden héten száma.

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>Elavult számítógépek keresése

Az alábbi példa a számítógépek, amelyek az elmúlt egy napon belül aktív volt, de nem küldött szívverés az elmúlt órában keresi meg.

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>A legújabb szívverés rekordot / számítógép IP-cím beszerzése

Ebben a példában minden egyes számítógép IP-cím utolsó szívverés rekordját adja vissza.
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Szívverés rekordok rekordokat védett állapota megfelelő

Ebben a példában megkeresi a kapcsolódó védelmi állapot-rekordok és -szívverés rekordok, mind a számítógépen, és az időt.
Vegye figyelembe, hogy az idő mezőt a legközelebbi egész percre lesz kerekítve. Futásidejű tároló számítási használtuk ehhez: `round_time=bin(TimeGenerated, 1m)`.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>Kiszolgáló rendelkezésre állási aránya

Kiszolgáló rendelkezésre állási aránya szívverés rekordok alapján számítja ki. "Óránként legalább 1 szívverés" rendelkezésre állási definiálható.
Így ha egy kiszolgáló elérhető 98 100 óra volt, a rendelkezésre állási díj 98 %.

```Kusto
let start_time=startofday(datetime("2018-03-01"));
let end_time=now();
Heartbeat
| where TimeGenerated > start_time and TimeGenerated < end_time
| summarize heartbeat_per_hour=count() by bin_at(TimeGenerated, 1h, start_time), Computer
| extend available_per_hour=iff(heartbeat_per_hour>0, true, false)
| summarize total_available_hours=countif(available_per_hour==true) by Computer 
| extend total_number_of_buckets=round((end_time-start_time)/1h)+1
| extend availability_rate=total_available_hours*100/total_number_of_buckets
```


## <a name="multiple-data-types"></a>Adattípusok

### <a name="chart-the-record-count-per-table"></a>Táblánként rekordszám mutató részletes diagram
Az alábbi példa minden tábla minden rekordján gyűjti össze az elmúlt 5 órában, és mindegyik táblázatban is hány rekordok száma. Az eredmények idődiagramját láthatók.

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>Száma típus szerint az elmúlt órában gyűjtött minden napló
Az alábbi példában minden az elmúlt órában jelentett keres, és minden tábla által a rekordokat számolja **típus**. Az eredmények jelennek meg a sávdiagramot oszlopdiagramra cseréli.

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>Kategória szerinti az Azure diagnostics-rekordok száma
Ebben a példában egyedi kategóriákhoz tartozó összes az Azure diagnostics-rekordok száma.

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>Egyedi kategóriákhoz tartozó véletlenszerű rekord beolvasása
Ebben a példában egy véletlenszerű az Azure diagnostics egyetlen rekord egyedi kategóriákhoz tartozó beolvasása.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>A legfrissebb rekordja kategória szerinti lekérése
Ebben a példában a legújabb Azure diagnostics-rekord lekéri az egyes egyedi.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>Hálózatfigyelés

### <a name="computers-with-unhealthy-latency"></a>Nem megfelelő állapotú késéssel rendelkező számítógépek
Ebben a példában nem megfelelő állapotú késés hoz létre különböző számítógépek listáját.

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>Teljesítmény

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>Csatlakozás teljesítményoptimalizált számítógéprekordok korrelációját, memória és CPU
Ebben a példában egy adott számítógép hátterében **teljesítményoptimalizált** rögzíti, és létrehozza a két idődiagramokon, az átlagos CPU és memória maximális mérete.

```Kusto
let StartTime = now()-5d;
let EndTime = now()-4d;
Perf
| where CounterName == "% Processor Time"  
| where TimeGenerated > StartTime and TimeGenerated < EndTime
and TimeGenerated < EndTime
| project TimeGenerated, Computer, cpu=CounterValue 
| join kind= inner (
   Perf
    | where CounterName == "% Used Memory"  
    | where TimeGenerated > StartTime and TimeGenerated < EndTime
    | project TimeGenerated , Computer, mem=CounterValue 
) on TimeGenerated, Computer
| summarize avgCpu=avg(cpu), maxMem=max(mem) by TimeGenerated bin=30m  
| render timechart
```

### <a name="perf-cpu-utilization-graph-per-computer"></a>Teljesítményoptimalizált CPU-kihasználtság graph számítógépenként
Ebben a példában számítja ki, és a CPU-kihasználtság számítógépek kezdődő diagramok _Contoso_.

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>Védelmi állapot

### <a name="computers-with-non-reporting-protection-status-duration"></a>A védelmi állapotának időtartama jelentéseket nem készítő számítógépek
Ez a példa felsorolja azokat a számítógépeket, amelyek védelmi állapota _Not Reporting_ volt, valamint az időtartamot, amíg ebben az állapotban voltak.

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Szívverés rekordok rekordokat védett állapota megfelelő
Ebben a példában megkeresi a kapcsolódó védelmi állapot-rekordok és -szívverés rekordok mind a számítógépen, és az időt.
Az idő mező lesz kerekítve a legközelebbi perces történő **bin**.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>Biztonsági rekordok

### <a name="count-security-events-by-activity-id"></a>Biztonsági események száma tevékenység azonosítója


Ebben a példában a rögzített szerkezete támaszkodik a **tevékenység** oszlop: \<azonosító\>-\<neve\>.
Azt elemzi a **tevékenység** érték két új oszlopot, és mindegyik előfordulását számolja **activityID**.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>Engedélyekkel kapcsolatos biztonsági események száma
Ez a példa az olyan **securityEvent** rekordok számát jelenít meg, ahol az **Activity** oszlop a teljes _Permissions_ kifejezést tartalmazza. A lekérdezés az elmúlt 30 percben létrehozott rekordokra vonatkozik.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>Keresse meg a fiókokat, jelentkezzen be egy biztonsági észlelés számítógépeken nem sikerült
Ebben a példában talál, és nem sikerült bejelentkezni számítógépekről, amelyeken hogy ezzel a biztonsági észlelés fiókok száma.

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>Saját biztonsági adatok érhetők el?
Feltárás gyakran az adatok rendelkezésre állásának ellenőrzése adatok indítása kezdődik. Ebben a példában a számát jeleníti meg **SecurityEvent** rögzíti az elmúlt 30 percben.

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>Elemezheti a tevékenység neve és azonosítója
A rögzített szerkezete támaszkodjon az alábbi két példában a **tevékenység** oszlop: \<azonosító\>-\<neve\>. Az első példában a **elemezni** operátor két új oszlop értékeit hozzárendelése: **activityID** és **activityDesc**.

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

Ez a példa a **felosztása** operátor számára hozzon létre egy külön értékek tömbje
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>Explicit hitelesítő folyamatok
Az alábbi példa bemutatja a kördiagram explicit hitelesítő adatok a múlt héten használt folyamatok

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>Felső futó folyamatok

Az alábbi példában a tevékenység az öt leggyakoribb folyamatok idő sorban jeleníti meg az elmúlt három napon.

```Kusto
// Find all processes that started in the last three days. ID 4688: A new process has been created.
let RunProcesses = 
    SecurityEvent
    | where TimeGenerated > ago(3d)
    | where EventID == "4688";
// Find the 5 processes that were run the most
let Top5Processes =
RunProcesses
| summarize count() by Process
| top 5 by count_;
// Create a time chart of these 5 processes - hour by hour
RunProcesses 
| where Process in (Top5Processes) 
| summarize count() by bin (TimeGenerated, 1h), Process
| render timechart
```


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>Keresse meg a sikertelen bejelentkezési kísérlet ismétlődő által ugyanazzal a fiókkal, a másik IP-címekről

Az alábbi példa megkeresi sikertelen bejelentkezési kísérlet alapján ugyanazt a fiókot több mint öt különböző IP-címekről származó az elmúlt hat óra.

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>Keresse meg a felhasználói fiókok, melyek nem sikerült bejelentkezni 
Az alábbi példában a felhasználói fiókok, amelyek több mint ötször az elmúlt egy napon belül, és amikor azok utolsó kísérlet bejelentkezés naplózása sikertelen volt, azonosítja.

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

Használatával **illesztési**, és **teszik** utasítások ellenőrizheti, hogy ha ugyanazt a gyanús fiókot később is tud sikeresen bejelentkezni.

```Kusto
let timeframe = 1d;
let suspicious_users = 
    SecurityEvent
    | where TimeGenerated > ago(timeframe)
    | where AccountType == 'User' and EventID == 4625 // 4625 - failed login
    | summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
    | where failed_login_attempts > 5
    | project-away Account1;
let suspicious_users_that_later_logged_in = 
    suspicious_users 
    | join kind=innerunique (
        SecurityEvent
        | where TimeGenerated > ago(timeframe)
        | where AccountType == 'User' and EventID == 4624 // 4624 - successful login,
        | summarize latest_successful_login=arg_max(TimeGenerated, Account) by Account
    ) on Account
    | extend was_login_after_failures = iif(latest_successful_login>latest_failed_login, 1, 0)
    | where was_login_after_failures == 1
;
suspicious_users_that_later_logged_in
```

## <a name="usage"></a>Használat

### <a name="calculate-the-average-size-of-perf-usage-reports-per-computer"></a>Teljesítményoptimalizált használati jelentések számítógépenként átlagosan méretének kiszámítása

Ebben a példában az átlagos mérete teljesítményoptimalizált használati jelentések egy számítógépen, az elmúlt 3 órában számítja ki.
Az eredmények sávdiagram látható.
```Kusto
Usage 
| where TimeGenerated > ago(3h)
| where DataType == "Perf" 
| where QuantityUnit == "MBytes" 
| summarize avg(Quantity) by Computer
| sort by avg_Quantity desc nulls last
| render barchart
```

### <a name="timechart-latency-percentiles-50-and-95"></a>Idődiagramját késés percentilisei 50 és 95

Ebben a példában kiszámítja és diagramok az idő az 50, és a 95. percentilisei jelentett **avgLatency** óránként az elmúlt 24 órában.

```Kusto
Usage
| where TimeGenerated > ago(24h)
| summarize percentiles(AvgLatencyInSeconds, 50, 95) by bin(TimeGenerated, 1h) 
| render timechart
```

### <a name="usage-of-specific-computers-today"></a>Használat az adott számítógépek még ma
Ez a példa lekéri **használati** számítógépnevekhez az előző nap, a karakterláncot tartalmazó adatok _ContosoFile_. Az eredmények alapján vannak rendezve **TimeGenerated**.

```Kusto
Usage
| where TimeGenerated > ago(1d)
| where  Computer contains "ContosoFile" 
| sort by TimeGenerated desc nulls last
```

## <a name="updates"></a>Frissítések

### <a name="computers-still-missing-updates"></a>Számítógépek továbbra is a hiányzó frissítések
Ebben a példában egy vagy több kritikus frissítéseket néhány napja nem voltak, és még mindig hiányoznak frissítések számítógépek listáját jeleníti meg.

```Kusto
let ComputersMissingUpdates3DaysAgo = Update
| where TimeGenerated between (ago(3d)..ago(2d))
| where  Classification == "Critical Updates" and UpdateState != "Not needed" and UpdateState != "NotNeeded"
| summarize makeset(Computer);

Update
| where TimeGenerated > ago(1d)
| where  Classification == "Critical Updates" and UpdateState != "Not needed" and UpdateState != "NotNeeded"
| where Computer in (ComputersMissingUpdates3DaysAgo)
| summarize UniqueUpdatesCount = dcount(Product) by Computer, OSType
```


## <a name="next-steps"></a>További lépések

- Tekintse meg a [Kusto nyelvi referencia](/azure/kusto/query) nyelv részleteiért.
- Végig egy [leckében a Log Analytics lekérdezések írásáról](get-started-queries.md).