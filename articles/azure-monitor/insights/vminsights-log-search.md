---
title: Naplók lekérdezése Azure Monitor for VMsról (előzetes verzió) | Microsoft Docs
description: Azure Monitor for VMs a megoldás metrikákat és naplózási adatokat gyűjt, és ez a cikk ismerteti a rekordokat, és példákat tartalmaz a lekérdezésekre.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/10/2019
ms.openlocfilehash: 7363f1ec11974dab3e0c0149c18ac4f0bf1c86ee
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555188"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms-preview"></a>Naplók lekérdezése Azure Monitor for VMsról (előzetes verzió)
A Azure Monitor for VMs a teljesítmény-és a kapcsolatok mérőszámait, a számítógép-és feldolgozási leltári adatokat, valamint az állapotadatok adatait és a Azure Monitor Log Analytics munkaterületére továbbítja azokat.  Ezek az adatAzure Monitor [lekérdezésekhez](../../azure-monitor/log-query/log-query-overview.md) érhetők el. Ezeket az információkat olyan forgatókönyvekre alkalmazhatja, amelyek tartalmazzák az áttelepítés megtervezését, a kapacitás elemzését, a felderítést és az igény szerinti teljesítménnyel kapcsolatos hibaelhárítást.

## <a name="map-records"></a>Rekordok leképezése
A rendszer óránként létrehoz egy rekordot minden egyedi számítógéphez és folyamathoz, valamint a folyamat vagy számítógép indításakor vagy Azure Monitor for VMs Térkép funkciójának indításakor generált rekordokon kívül. Ezek a rekordok a következő táblákban található tulajdonságokkal rendelkeznek. A ServiceMapComputer_CL-események mezői és értékei a ServiceMap Azure Resource Manager API-ban lévő számítógép-erőforrás mezőire képezhetők le. A ServiceMapProcess_CL-események mezői és értékei a ServiceMap Azure Resource Manager API-ban a folyamat erőforrás mezőire mutatnak. A ResourceName_s mező megegyezik a megfelelő Resource Manager-erőforrásban található Name mezővel. 

Az egyedi folyamatok és számítógépek azonosításához belsőleg generált tulajdonságok tartoznak:

- Számítógép: a *ResourceId* vagy a *ResourceName_s* használatával egyedileg azonosíthatja a számítógépeket egy log Analytics munkaterületen belül.
- Folyamat: a *ResourceId* használatával egyedileg azonosíthatja a folyamatokat egy log Analytics munkaterületen belül. A *ResourceName_s* egyedi azon számítógép kontextusában, amelyen a folyamat fut (MachineResourceName_s) 

Mivel a megadott időtartományban több rekord is létezhet egy adott folyamat és számítógép esetében, a lekérdezések több rekordot is visszaadhatnak ugyanahhoz a számítógéphez vagy folyamathoz. Ha csak a legújabb rekordot szeretné felvenni, adja hozzá a következőt: "| deduplikáció ResourceId "a lekérdezéshez.

### <a name="connections-and-ports"></a>Kapcsolatok és portok
A kapcsolatok Metrikái szolgáltatás két új táblát vezet be Azure Monitor naplókba – VMConnection és VMBoundPort. Ezek a táblák a számítógépek kapcsolatairól (bejövő és kimenő), valamint a rajtuk nyitott/aktív kiszolgáló-portokról nyújtanak információt. A ConnectionMetrics API-kon keresztül is elérhetők, amelyek lehetővé teszik egy adott metrika egy adott időszakra való beszerzését. A figyelő szoftvercsatornán való *elfogadást* EREDMÉNYEZő TCP-kapcsolatok bejövőek, míg az adott IP-címhez *való csatlakozással* és a porttal való kapcsolat kimenő. A kapcsolatok irányát az Direction tulajdonság jelképezi, amely beállítható **bejövő** vagy **kimenő**értékre. 

A táblázatokban szereplő rekordok a Dependency Agent által jelentett adatokból jönnek létre. Minden rekord egy 1 perces időszakra vonatkozó megfigyelést jelöl. A TimeGenerated tulajdonság az időintervallum kezdetét jelzi. Minden rekord tartalmaz információt a megfelelő entitás, azaz a csatlakozás vagy a port, valamint az entitáshoz társított metrikák azonosítására. Jelenleg csak a TCP protokollt használó hálózati tevékenységek jelentik a jelentést. 

#### <a name="common-fields-and-conventions"></a>Gyakori mezők és konvenciók 
A következő mezők és konvenciók a VMConnection és a VMBoundPort egyaránt érvényesek: 

- Számítógép: a jelentéskészítő gép teljes tartományneve 
- Ügynökazonosító: a Log Analytics ügynökkel rendelkező gép egyedi azonosítója  
- Machine (gép): a ServiceMap által elérhetővé tett gép Azure Resource Manager erőforrásának neve. Az *m-{GUID}* formában van, ahol a *GUID* ugyanaz a GUID, mint a ügynökazonosító  
- Folyamat: a ServiceMap által elérhetővé tett folyamat Azure Resource Manager erőforrásának neve. A *p-{hex string}* formátumú. A folyamat egyedi a számítógép hatókörén belül, és egyedi folyamat-azonosítót állít elő a gépek között, kombinálja a gép és a folyamat mezőit. 
- ProcessName: a jelentéskészítési folyamat végrehajtható fájljának neve.
- Az összes IP-cím IPv4 kanonikus formátumú karakterlánc, például *13.107.3.160* 

A költségeket és a bonyolultságot a kapcsolati rekordok nem jelölik az egyes fizikai hálózati kapcsolatokat. A fizikai hálózati kapcsolatok több logikai kapcsolatba vannak csoportosítva, amely az adott táblázatban látható.  Ez azt jelenti, hogy a *VMConnection* táblában lévő rekordok logikai csoportosítást jelölnek, nem pedig a megfigyelt egyedi fizikai kapcsolatokat. Egy adott egyperces időszakban a következő attribútumok esetében azonos értéket megosztó fizikai hálózati kapcsolatok egyetlen logikai rekordba vannak összesítve a *VMConnection*-ben. 

| Tulajdonság | Leírás |
|:--|:--|
|Irány |A kapcsolatok iránya, az érték *bejövő* vagy *kimenő* . |
|Gép |A számítógép teljes tartományneve |
|Folyamat |Folyamatok vagy csoportok identitása, a kapcsolatok kezdeményezése/elfogadása |
|SourceIp |A forrás IP-címe |
|DestinationIp |A cél IP-címe |
|DestinationPort |A célhely portszáma |
|Protocol (Protokoll) |A kapcsolathoz használt protokoll.  Az értékek a *TCP*. |

A csoportosítás hatásának kiszámításához a csoportosított fizikai kapcsolatok számával kapcsolatos információk a rekord következő tulajdonságaiban vannak megadva:

| Tulajdonság | Leírás |
|:--|:--|
|LinksEstablished |A jelentéskészítési idő ablakában létrehozott fizikai hálózati kapcsolatok száma |
|LinksTerminated |A jelentéskészítési idő ablakában leállított fizikai hálózati kapcsolatok száma |
|LinksFailed |Azon fizikai hálózati kapcsolatok száma, amelyek sikertelenek voltak a jelentéskészítési idő ablakában. Ez az információ jelenleg csak a kimenő kapcsolatok esetében érhető el. |
|LinksLive |A jelentéskészítési idő ablakának végén megnyitott fizikai hálózati kapcsolatok száma|

#### <a name="metrics"></a>Metrikák

A kapcsolatok számának mérőszámai mellett az adott logikai kapcsolatban vagy hálózati porton küldött és fogadott adatok mennyiségére vonatkozó információk is szerepelni fog a rekord alábbi tulajdonságaiban:

| Tulajdonság | Leírás |
|:--|:--|
|BytesSent |A jelentéskészítési idő ablakában elküldhető bájtok teljes száma |
|BytesReceived |A jelentéskészítési idő ablakában fogadott bájtok teljes száma |
|Válaszok |A jelentéskészítési idő ablakában megfigyelt válaszok száma. 
|ResponseTimeMax |A jelentéskészítési idő ablakában megfigyelt legnagyobb válaszidő (ezredmásodpercben). Ha nincs érték, a tulajdonság üres.|
|ResponseTimeMin |A jelentéskészítési idő ablakában megfigyelt legkisebb válaszidő (ezredmásodpercben). Ha nincs érték, a tulajdonság üres.|
|ResponseTimeSum |A jelentéskészítési idő ablakában megfigyelt összes válaszidő (ezredmásodperc) összege. Ha nincs érték, a tulajdonság üres.|

A jelentett harmadik adattípus a válaszidő – mennyi időt vesz igénybe a hívó a kapcsolaton keresztül küldött kérések feldolgozására, és a távoli végpont által válaszol. A jelentett válaszidő az alapul szolgáló alkalmazás protokolljának valós válaszideje. A rendszer a heurisztikus használatával számítja ki a fizikai hálózati kapcsolat forrás-és cél vége közötti adatáramlás megfigyelése alapján. Elméletileg az a különbség, hogy a kérelem utolsó bájtja mikor hagyja el a küldőt, valamint azt az időpontot, amikor a válasz utolsó bájtja vissza fog érkezni. Ez a két időbélyeg a kérések és válaszok eseményeinek egy adott fizikai kapcsolatban való kiválasztására szolgál. A kettő közötti különbség az egyetlen kérelem válaszideje. 

A szolgáltatás ezen első kiadásában az algoritmus egy olyan közelítés, amely az adott hálózati kapcsolathoz használt tényleges alkalmazási protokolltól függően különböző sikereket eredményezhet. Például az aktuális megközelítés jól működik a kérelem-válasz alapú protokollok, például a HTTP (S) esetében, de nem működik egyirányú vagy üzenetsor-alapú protokollokkal.

Néhány fontos szempontot figyelembe kell venni:

1. Ha egy folyamat ugyanazon az IP-címen, de több hálózati adapteren keresztül fogad kapcsolatokat, a rendszer minden egyes csatolóhoz külön rekordot fog jelenteni. 
2. A helyettesítő karakteres IP-címmel rendelkező rekordok nem tartalmaznak tevékenységet. A szolgáltatás tartalmazza azt a tényt, hogy a gépen lévő port nyitva van a bejövő forgalom számára.
3. A részletesség és az adatmennyiség csökkentése érdekében a helyettesítő karakteres IP-címekkel rendelkező rekordok kimaradnak, ha egy adott IP-címmel rendelkező egyező rekord van (ugyanahhoz a folyamathoz, porthoz és protokollhoz). Ha nincs megadva helyettesítő IP-rekord, a IsWildcardBind Record tulajdonsága az adott IP-címmel "true" (igaz) értékre lesz állítva, amely jelzi, hogy a port a jelentéskészítő gép összes felületén elérhetővé válik.
4. Azokat a portokat, amelyek csak egy adott csatolón vannak kötve, a IsWildcardBind *hamis*értékre van állítva.

#### <a name="naming-and-classification"></a>Elnevezés és besorolás
A kényelem érdekében a kapcsolatok távoli végének IP-címét a RemoteIp tulajdonság tartalmazza. A bejövő kapcsolatok esetében a RemoteIp ugyanaz, mint a SourceIp, míg a kimenő kapcsolatok esetében ugyanaz, mint a DestinationIp. A RemoteDnsCanonicalNames tulajdonság a gép által a RemoteIp számára jelentett DNS-kanonikus neveket jelöli. A RemoteDnsQuestions és a RemoteClassification tulajdonságok későbbi használatra vannak fenntartva. 

#### <a name="geolocation"></a>Földrajzi hely
A *VMConnection* az egyes kapcsolatok távoli végére vonatkozó térinformatikai információkat is tartalmaz a rekord alábbi tulajdonságaiban: 

| Tulajdonság | Leírás |
|:--|:--|
|RemoteCountry |A RemoteIp üzemeltető ország/régió neve.  Például *Egyesült Államok* |
|RemoteLatitude |A térinformatikai szélesség. Például *47,68* |
|RemoteLongitude |A térinformatikai hosszúság. Például: *-122,12* |

#### <a name="malicious-ip"></a>Kártékony IP-cím
A *VMConnection* táblában lévő összes RemoteIp-tulajdonságot a rendszer az ismert kártékony tevékenységgel rendelkező IP-címek készletében ellenőrzi. Ha a RemoteIp rosszindulatúként van azonosítva, a következő tulajdonságok lesznek feltöltve (ezek üresek, ha az IP nem minősül rosszindulatúnak) a rekord következő tulajdonságaiban:

| Tulajdonság | Leírás |
|:--|:--|
|MaliciousIp |A RemoteIp címe |
|IndicatorThreadType |Az észlelt veszélyforrás az alábbi értékek egyike, a *botnet*, a *C2*, a *CryptoMining*, a *Darknet*, a *DDos*, a *MaliciousUrl*, a *malware*, az *adathalászat*, a *proxy*, a *PUA*, *a List*.   |
|Leírás |A megfigyelt fenyegetés leírása. |
|TLPLevel |A forgalmi lámpa protokoll (TLP) szintje az egyik definiált érték, a *fehér*, a *zöld*, a *sárga*és a *vörös*. |
|megbízhatósági |Az értékek *0 – 100*. |
|Súlyosság |Az értékek *0 – 5*, ahol az *5* a legsúlyosabb, a *0* pedig egyáltalán nem súlyos. Az alapértelmezett érték *3*.  |
|FirstReportedDateTime |Az első alkalommal, amikor a szolgáltató jelentette a kijelzőt. |
|LastReportedDateTime |A mutató a folyamat utolsó időpontjában volt látható. |
|IsActive |Azt jelzi, hogy a kijelzők inaktiválva vannak-e *igaz* vagy *hamis* értékkel. |
|ReportReferenceLink |Egy adott megfigyelhető jelentéshez kapcsolódó jelentések hivatkozásai. |
|additionalInformation |További információkat biztosít a megfigyelt fenyegetésről. |

### <a name="ports"></a>Portok 
Azok a portok, amelyek aktívan elfogadják a bejövő forgalmat, vagy képesek fogadni a forgalmat, de a jelentéskészítési idő ablakában inaktívak, a VMBoundPort táblába íródnak.  

A VMBoundPort összes rekordját a következő mezők azonosítják: 

| Tulajdonság | Leírás |
|:--|:--|
|Folyamat | Azon folyamat (vagy folyamatok csoportjai) identitása, amelyekhez a port társítva van.|
|IP | Port IP-címe (lehet helyettesítő IP-cím, *0.0.0.0*) |
|Port |A portszám |
|Protocol (Protokoll) | A protokoll.  Példa: *TCP* vagy *UDP* (csak a *TCP* jelenleg támogatott).|
 
Az identitás egy port a fenti öt mezőből származik, és a PortId tulajdonságban tárolódik. Ezzel a tulajdonsággal gyorsan megtalálhatja az adott port rekordjait az idő során. 

#### <a name="metrics"></a>Metrikák 
A portok rekordjai közé tartoznak a hozzájuk társított kapcsolatokra vonatkozó metrikák. Jelenleg a következő metrikákat mutatjuk be (az egyes mérőszámok részleteit az előző szakaszban találja): 

- BytesSent és BytesReceived 
- LinksEstablished, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Néhány fontos szempontot figyelembe kell venni:

- Ha egy folyamat ugyanazon az IP-címen, de több hálózati adapteren keresztül fogad kapcsolatokat, a rendszer minden egyes csatolóhoz külön rekordot fog jelenteni.  
- A helyettesítő karakteres IP-címmel rendelkező rekordok nem tartalmaznak tevékenységet. A szolgáltatás tartalmazza azt a tényt, hogy a gépen lévő port nyitva van a bejövő forgalom számára. 
- A részletesség és az adatmennyiség csökkentése érdekében a helyettesítő karakteres IP-címekkel rendelkező rekordok kimaradnak, ha egy adott IP-címmel rendelkező egyező rekord van (ugyanahhoz a folyamathoz, porthoz és protokollhoz). Helyettesítő karakteres IP-rekord kihagyása esetén az adott IP-címmel rendelkező rekord *IsWildcardBind* tulajdonsága *igaz*értékre lesz állítva.  Ez azt jelzi, hogy a port a jelentéskészítő gép minden felületén elérhető. 
- Azokat a portokat, amelyek csak egy adott csatolón vannak kötve, a IsWildcardBind *hamis*értékre van állítva. 

### <a name="servicemapcomputer_cl-records"></a>ServiceMapComputer_CL-rekordok
A *ServiceMapComputer_CL* rendelkező rekordok a függőségi ügynökkel rendelkező kiszolgálók leltározási adataival rendelkeznek. Ezek a rekordok a következő táblázatban szereplő tulajdonságokkal rendelkeznek:

| Tulajdonság | Leírás |
|:--|:--|
| Type (Típus) | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | A munkaterületen belüli gép egyedi azonosítója |
| ResourceName_s | A munkaterületen belüli gép egyedi azonosítója |
| ComputerName_s | A számítógép teljes tartományneve |
| Ipv4Addresses_s | A kiszolgáló IPv4-címeinek listája |
| Ipv6Addresses_s | A kiszolgáló IPv6-címeinek listája |
| DnsNames_s | DNS-nevek tömbje |
| OperatingSystemFamily_s | Windows vagy Linux |
| OperatingSystemFullName_s | Az operációs rendszer teljes neve  |
| Bitness_s | A gép bitszáma (32 bites vagy 64 bites)  |
| PhysicalMemory_d | A fizikai memória (MB) |
| Cpus_d | A processzorok száma |
| CpuSpeed_d | A processzor sebessége (MHz)|
| VirtualizationState_s | *ismeretlen*, *fizikai*, *virtuális*, *hypervisor* |
| VirtualMachineType_s | *HyperV*, *VMware*stb. |
| VirtualMachineNativeMachineId_g | A virtuális gép azonosítója, amelyet a hypervisor rendel hozzá |
| VirtualMachineName_s | A virtuális gép neve |
| BootTime_t | A rendszerindítási idő |

### <a name="servicemapprocess_cl-type-records"></a>ServiceMapProcess_CL típusú rekordok
A *ServiceMapProcess_CL* rendelkező rekordok a függőségi ügynökkel rendelkező kiszolgálókon a TCP-kapcsolattal rendelkező folyamatok leltározási adataival rendelkeznek. Ezek a rekordok a következő táblázatban szereplő tulajdonságokkal rendelkeznek:

| Tulajdonság | Leírás |
|:--|:--|
| Type (Típus) | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | A munkaterületen belüli folyamat egyedi azonosítója |
| ResourceName_s | Egy folyamat egyedi azonosítója azon a gépen belül, amelyen fut|
| MachineResourceName_s | A gép erőforrásának neve |
| ExecutableName_s | A folyamat végrehajtható fájljának neve |
| StartTime_t | A folyamat készletének kezdési ideje |
| FirstPid_d | A folyamat első PID-je |
| Description_s | A folyamat leírása |
| CompanyName_s | A vállalat neve |
| InternalName_s | A belső név |
| ProductName_s | A termék neve |
| ProductVersion_s | A termék verziószáma |
| FileVersion_s | A fájl verziója |
| CommandLine_s | A parancssor |
| ExecutablePath_s | A végrehajtható fájl elérési útja |
| WorkingDirectory_s | A munkakönyvtár |
| userName | Az a fiók, amelyben a folyamat végre van hajtva |
| UserDomain | Az a tartomány, amelyben a folyamat végrehajtás alatt áll |

## <a name="sample-log-searches"></a>Naplókeresési minták

### <a name="list-all-known-machines"></a>Az összes ismert gép listázása
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="when-was-the-vm-last-rebooted"></a>Mikor volt utoljára újraindítva a virtuális gép
```kusto
let Today = now(); ServiceMapComputer_CL | extend DaysSinceBoot = Today - BootTime_t | summarize by Computer, DaysSinceBoot, BootTime_t | sort by BootTime_t asc`
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Azure-beli virtuális gépek összefoglalása rendszerkép, hely és SKU alapján
```kusto
ServiceMapComputer_CL | where AzureLocation_s != "" | summarize by ComputerName_s, AzureImageOffering_s, AzureLocation_s, AzureImageSku_s`
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Az összes felügyelt számítógép fizikai memória-kapacitásának listázása.
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s`
```

### <a name="list-computer-name-dns-ip-and-os"></a>A számítógép nevének, DNS-címének, IP-címének és operációs rendszerének listázása.
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s`
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Az összes folyamat megkeresése az "SQL" paranccsal a parancssorban
```kusto
ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Számítógép (legfrissebb rekord) keresése erőforrás neve alapján
```kusto
search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Számítógép keresése (legfrissebb rekord) IP-cím szerint
```kusto
search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>A megadott gépen lévő összes ismert folyamat listázása
```kusto
ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="list-all-computers-running-sql-server"></a>Az összes SQL Server rendszert futtató számítógép listázása
```kusto
ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s`
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>A curl összes egyedi termék-verziójának listázása az adatközpontban
```kusto
ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s`
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>A CentOS-t futtató összes számítógép számítógépcsoport létrehozása
```kusto
ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s`
```

### <a name="bytes-sent-and-received-trends"></a>Küldött és fogadott bájtok száma
```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart`
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Mely Azure-beli virtuális gépek továbbítják a legtöbb bájtot
```kusto
VMConnection | join kind=fullouter(ServiceMapComputer_CL) on $left.Computer == $right.ComputerName_s | summarize count(BytesSent) by Computer, AzureVMSize_s | sort by count_BytesSent desc`
```

### <a name="link-status-trends"></a>Kapcsolatok állapotának trendjei
```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize  dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart`
```

### <a name="connection-failures-trend"></a>A sikertelen kapcsolatok trendje
```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart`
```

### <a name="bound-ports"></a>Kötött portok
```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>A nyitott portok száma a gépek között
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>A munkaterületen a megnyitott portok számával szerzi be a folyamatokat
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Az egyes portok összesített viselkedése
Ezzel a lekérdezéssel a portok tevékenység alapján, például a legtöbb bejövő/kimenő forgalomtal rendelkező portok, a legtöbb kapcsolattal rendelkező portok használhatók.
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>A kimenő kapcsolatok összefoglalása a számítógépek csoportjából
```kusto
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="next-steps"></a>Következő lépések
* Ha a Azure Monitor naplóbeli lekérdezések írásakor új, tekintse át a következő [témakört: log Analytics használata](../../azure-monitor/log-query/get-started-portal.md) a Azure Portal a naplók írásához.
* További információ a [keresési lekérdezések írásához](../../azure-monitor/log-query/search-queries.md).
