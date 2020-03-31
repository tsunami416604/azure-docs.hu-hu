---
title: Példák az Azure Monitor naplólekérdezési példái | Microsoft dokumentumok
description: Példák a naplólekérdezések az Azure Monitor a Kusto lekérdezési nyelv használatával.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2020
ms.openlocfilehash: 18cd74ac9298b7dd058de2b224f677ec0d8f2d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480283"
---
# <a name="azure-monitor-log-query-examples"></a>Példák az Azure Monitor naplólekérdezési példái
Ez a cikk különböző példákat tartalmaz a [Kusto lekérdezési nyelvet](/azure/kusto/query/) használó [lekérdezések](log-query-overview.md) különböző típusú naplóadatok lekéréséhez az Azure Monitorból. Az adatok konszolidálására és elemzésére különböző módszerek et alkalmaznak, így ezekkel a mintákkal azonosíthatja a saját igényeihez használható különböző stratégiákat.  

A mintákban használt különböző kulcsszavakról a [Kusto nyelvi referencia című](https://docs.microsoft.com/azure/kusto/query/) témakörben talál részleteket. Az Azure Monitor újabb használatával kapcsolatos [leckével megbeszélheti a lekérdezések létrehozását.](get-started-queries.md)

## <a name="events"></a>Események

### <a name="search-application-level-events-described-as-cryptographic"></a>"Kriptográfiai" néven leírt alkalmazásszintű események keresése
Ez a példa olyan rekordokat keres az **Események** táblában, amelyekben az **EventLog** _alkalmazás_ és **a RenderedDescription** _kriptográfiai elemeket_tartalmaz. Az elmúlt 24 óra rekordjait tartalmazza.

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription contains "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>Átadás-előkészítéshez kapcsolódó keresési események
Keresés az **Esemény** és **a SecurityEvents** táblákban az _átnemelő metódust megemlítő_rekordokért.

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Szívverés

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>Heti nézet diagramja az adatokat küldő számítógépek számáról

A következő példa a szívveréseket küldő különböző számítógépek számát ábrázolja minden héten.

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>Elavult számítógépek keresése

A következő példa azokat a számítógépeket találja, amelyek az utolsó napon aktívak voltak, de nem küldtek szívverést az elmúlt órában.

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>A legfrissebb szívveréses rekord beszerezni e számítógépenkénti IP-cím

Ez a példa az egyes számítógép IP-címének utolsó szívverésrekordját adja vissza.
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Védett állapotrekordok egyeztetése szívveréses rekordokkal

Ez a példa megkeresi a kapcsolódó védelmi állapotrekordokat és a szívverési rekordokat, amelyek a számítógépen és az időben egyeztetve vannak.
Megjegyzés: az időmező a legközelebbi percre lesz kerekítve. Ehhez futásidejű raktárhely számítást `round_time=bin(TimeGenerated, 1m)`használtunk: .

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>Kiszolgáló rendelkezésre állási aránya

A kiszolgáló rendelkezésre állási arányának kiszámítása szívverési rekordok alapján. Az elérhetőség meghatározása: "legalább 1 szívverés óránként".
Tehát, ha egy kiszolgáló 100 órából 98-at volt elérhető, a rendelkezésre állási arány 98%.

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


## <a name="multiple-data-types"></a>Több adattípus

### <a name="chart-the-record-count-per-table"></a>Táblázatonkénti rekordszám diagramja
A következő példa az elmúlt öt óra összes táblájának összes rekordját összegyűjti, és megszámolja, hogy hány rekord volt az egyes táblákban. Az eredmények egy idődiagramon jelennek meg.

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>Az elmúlt órában összegyűjtött összes napló megszámlálása típus szerint
A következő példa az elmúlt órában jelentett adatokat keresi meg, és **típus**szerint számolja az egyes táblarekordokat. Az eredmények sávdiagramon jelennek meg.

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>Az Azure diagnosztikai rekordjainak megszámlálása kategóriánként
Ez a példa az egyes egyedi kategóriák összes Azure diagnosztikai rekordját megszámolja.

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>Véletlenszerű rekord beírása minden egyes egyedi kategóriához
Ebben a példában egyetlen véletlenszerű Azure diagnosztikai rekordot kap minden egyes egyedi kategóriához.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>A legfrissebb rekord beszerezni kategóriánként
Ebben a példában az egyes egyedi kategóriákban a legújabb Azure diagnosztikai rekordot kapja meg.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>Hálózatfigyelés

### <a name="computers-with-unhealthy-latency"></a>Nem kifogástalan késleltetéssel rendelkező számítógépek
Ez a példa a nem kifogástalan késéssel rendelkező különálló számítógépek listáját hozza létre.

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>Teljesítmény

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>Számítógép-perf rekordok összeadása a memória és a processzor korrelációjához
Ez a példa korrelálja egy adott számítógép **perf** rekordjait, és két idődiagramot hoz létre, az átlagos PROCESSZORt és a maximális memóriát.

```Kusto
let StartTime = now()-5d;
let EndTime = now()-4d;
Perf
| where CounterName == "% Processor Time"  
| where TimeGenerated > StartTime and TimeGenerated < EndTime
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

### <a name="perf-cpu-utilization-graph-per-computer"></a>Perf CPU-kihasználtsági grafikon számítógépenként
Ez a példa kiszámítja és ábrázolja a _Contoso-val_kezdődő számítógépek processzorkihasználtságát.

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>Védelem állapota

### <a name="computers-with-non-reporting-protection-status-duration"></a>Nem jelentett védelmi állapotú számítógépek
Ez a példa azokat a számítógépeket sorolja fel, amelyek nem _jelentett_ védelmi állapotúak, és az ebben az állapotukban lévő időtartamot.

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Védett állapotrekordok egyeztetése szívveréses rekordokkal
Ez a példa megkeresi a kapcsolódó védelmi állapotrekordokat és a szívverési rekordokat a számítógépés az idő egyaránt.
Az időmező takarásban a legközelebbi percre kerekítve jelenik **meg.**

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>Biztonsági nyilvántartások

### <a name="count-security-events-by-activity-id"></a>Biztonsági események számlálása tevékenységazonosító szerint


Ez a példa a **Tevékenység** oszlop rögzített \<szerkezetére támaszkodik:\>-\<Azonosító neve\>.
A **Tevékenység** értéket két új oszlopra elemzi, és megszámolja az egyes **tevékenységazonosítók előfordulását.**

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>Engedélyekhez kapcsolódó biztonsági események megszámlálása
Ez a példa a **securityEvent** rekordok számát mutatja, amelyekben a **Tevékenység** oszlop az _Engedélyek_teljes kifejezést tartalmazza. A lekérdezés az elmúlt 30 percben létrehozott rekordokra vonatkozik.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>Olyan fiókok keresése, amelyek nem tudtak bejelentkezni biztonsági észleléssel rendelkező számítógépekről
Ez a példa megkeresi és megszámolja azokat a fiókokat, amelyek nem tudtak bejelentkezni olyan számítógépekről, amelyeken biztonsági észlelést azonosítunk.

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>Elérhetők a biztonsági adataim?
Az adatok feltárásának megkezdése gyakran az adatok rendelkezésre állásának ellenőrzésével kezdődik. Ez a példa a **SecurityEvent** rekordok számát mutatja az elmúlt 30 percben.

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>Elemzési tevékenység neve és azonosítója
Az alábbi két példa a **Tevékenység** oszlop \<rögzített\>-\<szerkezetére támaszkodik: Azonosító neve\>. Az első példa az **elemzési** operátor segítségével két új oszlophoz rendel értékeket: **activityID** és **activityDesc**.

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

Ez a példa a **felosztásos** operátort használja egy különálló értékekből álló tömb létrehozásához.
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>Explicit hitelesítő adatok folyamatai
A következő példa azokat a folyamatokat mutatja be, amelyek explicit hitelesítő adatokat használtak az elmúlt héten

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>A legnépszerűbb futó folyamatok

A következő példa az elmúlt három nap öt leggyakoribb folyamatának tevékenységi vonalát mutatja be.

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


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>Ismétlődő sikertelen bejelentkezési kísérletek keresése ugyanannál a fióktól a különböző IP-kből

A következő példa az elmúlt hat órában több mint öt különböző IP-ből származó, ugyanazon fiók sikertelen bejelentkezési kísérleteit találja meg.

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>A bejelentkezést nem sikerült felhasználói fiókok keresése 
A következő példa azokat a felhasználói fiókokat azonosítja, amelyek az utolsó nap során több mint ötször nem tudtak bejelentkezni, és mikor próbáltak utoljára bejelentkezni.

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

Az **illesztés**használatával ellenőrizhetjük, **hogy** ugyanazok a gyanús fiókok tudtak-e később sikeresen bejelentkezni.

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

Az `Usage` adattípus segítségével nyomon követheti a bevitt adatmennyiséget megoldás vagy adattípus szerint. A bevitt adatkötetek [számítógépes](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#data-volume-by-computer) vagy [Azure-előfizetéssel, erőforráscsoportokkal vagy erőforrásokkal](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#data-volume-by-azure-resource-resource-group-or-subscription)történő tanulmányozására más módszerek is léteznek.

#### <a name="data-volume-by-solution"></a>Adatmennyiség megoldásonként

A számlázható adatmennyiség megoldásonkénti megtekintéséhez használt lekérdezés az elmúlt hónapban (az utolsó részleges nap kivételével) a következő:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

Vegye figyelembe, `where IsBillable = true` hogy a záradék kiszűri az adattípusokat bizonyos megoldásokból, amelyeknél nincs betöltési díj.  A záradék `TimeGenerated` is csak annak biztosítása érdekében, hogy a lekérdezési élmény az Azure Portalon az alapértelmezett 24 órán túl. A Használati adattípus `StartTime` használatakor, és `EndTime` azokat az időkategóriákat jelöli, amelyekhez az eredmények megjelennek. 

#### <a name="data-volume-by-type"></a>Adatmennyiség típus szerint

Tovább részletezve megtekintheti az adattrendekadat-típusok alapján:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

Vagy ha meg szeretne látni egy táblázatot megoldás és az elmúlt hónapban,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by Solution, DataType
| sort by Solution asc, DataType asc
```

> [!NOTE]
> A Használati adattípus néhány mezője, miközben még a sémában van, elavult, és értékeik már nem lesznek feltöltve. Ezek **a következők: Számítógép,** valamint a betöltéssel kapcsolatos mezők (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** és **AverageProcessingTimeMs**.

## <a name="updates"></a>Frissítések

### <a name="computers-still-missing-updates"></a>A számítógépek még mindig hiányoznak a frissítésekből
Ez a példa azoknak a számítógépeknek a listáját jeleníti meg, amelyek néhány nappal ezelőtt egy vagy több kritikus frissítésből hiányoztak, és amelyekről még mindig hiányoznak a frissítések.

```Kusto
let ComputersMissingUpdates3DaysAgo = Update
| where TimeGenerated between (ago(30d)..ago(1h))
| where Classification !has "Critical" and UpdateState =~ "Needed"
| summarize makeset(Computer);
Update
| where TimeGenerated > ago(1d)
| where Classification has "Critical" and UpdateState =~ "Needed"
| where Computer in (ComputersMissingUpdates3DaysAgo)
| summarize UniqueUpdatesCount = dcount(Product) by Computer, OSType
```


## <a name="next-steps"></a>További lépések

- A nyelvvel kapcsolatos részleteket a [Kusto nyelvi hivatkozásban](/azure/kusto/query) találja.
- A [naplólekérdezések azure Monitorban történő írásával kapcsolatos leckében](get-started-queries.md)átkell mennie.