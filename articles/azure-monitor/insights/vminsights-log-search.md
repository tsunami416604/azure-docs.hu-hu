---
title: Hogyan lekérdezés naplók az Azure Monitor-beli virtuális gépek (előzetes verzió) |} A Microsoft Docs
description: Az Azure Monitor virtuális gépek megoldás metrikákat gyűjt, és a naplóadatokat, és ez a cikk ismerteti a rekordokat, és mintalekérdezéseket tartalmaz.
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
ms.date: 03/15/2019
ms.author: magoedte
ms.openlocfilehash: 12f8b3d9dd461dc5d09d76245aa02f0e1cefc343
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188968"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms-preview"></a>Az Azure Monitor naplók lekérdezni a virtuális gépek (előzetes verzió)
A virtuális gépek az Azure Monitor teljesítmény és a kapcsolati metrika, a számítógép és a folyamat leltáradatok és a állapotinformációkat gyűjt, és továbbítja azokat a Log Analytics-munkaterületet az Azure monitorban.  Ezek az adatok érhető el [lekérdezés](../../azure-monitor/log-query/log-query-overview.md) az Azure monitorban. Ezeket az adatokat, beleértve az áttelepítés megtervezése, kapacitáselemzési, felderítési és igény szerinti teljesítménnyel kapcsolatos hibaelhárítás forgatókönyveket is alkalmazhat.

## <a name="map-records"></a>Rekord leképezése
Egy rekord minden egyedi számítógép és a folyamat a rekordokat, amelyek akkor jönnek létre egy folyamatot, vagy a számítógép indításakor vagy bevezetve az Azure monitornak a virtuális gépek térkép funkció mellett óránként jön létre. Ezek a rekordok tulajdonságait az alábbi táblázatban rendelkezik. A mezők és értékek a mezők a ServiceMap Azure Resource Manager API-ban a gép erőforrás ServiceMapComputer_CL események leképezés. A mezők és értékek a ServiceMapProcess_CL események leképezés a mezőket a folyamat erőforrás a ServiceMap Azure Resource Manager API-ban. A ResourceName_s mezője megegyezik a megfelelő Resource Manager-erőforrás neve mezőjében. 

Belsőleg generált tulajdonságaiként segítségével egyedi folyamatokat és a számítógépek azonosításához:

- Számítógép: Használat *ResourceId* vagy *ResourceName_s* egy számítógépen belül a Log Analytics-munkaterület egyedi azonosításához.
- Folyamat: Használat *ResourceId* egy folyamatot egy Log Analytics-munkaterületen belül egyedi azonosításához. *ResourceName_s* egyedi, a gép, amelyen a folyamatot futtató (MachineResourceName_s) környezetében 

Több rekord megadott folyamat és a egy adott időtartományban számítógép is létezik, mivel a lekérdezések adhat vissza ugyanazon a számítógépen vagy folyamat egynél több rekordot. Adja meg a legújabb bejegyzést, adja hozzá a "|} a deduplikáció erőforrás-azonosító"a lekérdezéshez.

### <a name="connections-and-ports"></a>Kapcsolatok és portok
A kapcsolati metrika funkció az Azure Monitor naplóira - VMConnection és VMBoundPort két új táblázat mutatja be. Ezek a táblázatok ismertetik a kapcsolatokat a gép (bejövő és kimenő), valamint a kiszolgáló rajtuk nyílt/active-portok. ConnectionMetrics is, amelyek biztosítják az eszközöket egy adott időszakban egy adott metrika beszerzése a API-kon keresztül érhetők el. TCP-kapcsolatok eredő *elfogadásával* szoftvercsatorna figyel-e a bejövő, míg a által létrehozott *csatlakozás* egy adott IP-cím és port a kimenő. A kapcsolat irányát képviseli a iránya tulajdonság, amely lehet megadni **bejövő** vagy **kimenő**. 

Ezeknek a tábláknak rögzíti a függőségi ügynök által küldött adatokból jönnek létre. Minden rekord egy 1 perces időszakra megfigyelési jelöli. A TimeGenerated tulajdonság az időintervallum kezdetét jelzi. Minden rekord tartalmazza a megfelelő entitás, amely azonosító adatokat, kapcsolat vagy a port, valamint az adott entitás kapcsolódó metrikák. Jelenleg csak akkor fordul elő, a TCP-val IPv4 hálózati aktivitás jelentett. 

#### <a name="common-fields-and-conventions"></a>Általános mezők és konvenciók 
A következő mezőket és egyezmények a alkalmazni VMConnection és VMBoundPort is: 

- Számítógép: Teljes tartománynév gép Reporting 
- AgentID: A Log Analytics-ügynökkel rendelkező gép egyedi azonosítója  
- Számítógép: A gép ServiceMap által elérhetővé tett az Azure Resource Manager-erőforrás neve. A következő formában van *m – {GUID}*, ahol *GUID* ügynökazonosító, ugyanaz a GUID  
- Folyamat: A folyamat ServiceMap által elérhetővé tett az Azure Resource Manager-erőforrás neve. A következő formában van *p-{hexadecimális karakterlánc}*. Folyamat gép hatókörön belüli egyedi, és hozzon létre egy egyedi folyamat azonosítója gépek között, kombinálhatja a gép és a folyamat mezőket. 
- ProcessName: A jelentéskészítési folyamat végrehajtható fájljának nevét.
- Az összes IP-címek olyan karakterláncok IPv4 kanonikus formátumban, például *13.107.3.160* 

Kezelheti a költségeket és összetettséget, csatlakozási rekordjainak nem felelnek meg az egyes fizikai hálózati kapcsolatokat. Több fizikai hálózati kapcsolatot, amely megjelenik majd a megfelelő tábla logikai kapcsolatot vannak csoportosítva.  Ez azt jelenti, rögzíti a *VMConnection* tábla felel meg a logikai csoportosítása és nem az egyes fizikai kapcsolatokat betartását. Egyetlen logikai rekord összesíti ugyanazt az értéket a következő attribútumok megosztása során egy adott egyperces időszakban, a fizikai hálózati kapcsolatot a rendszer *VMConnection*. 

| Tulajdonság | Leírás |
|:--|:--|
|Irány |A kapcsolat irányát, értéke *bejövő* vagy *kimenő* |
|Gép |A számítógép teljes Tartományneve |
|Feldolgozás |Identitáskezelési folyamat vagy a csoportok a folyamatok, a kapcsolat kezdeményezése és elfogadása |
|SourceIp |A forrás IP-címe |
|DestinationIp |A cél-IP-cím |
|DestinationPort |A cél-port száma |
|Protokoll |A kapcsolathoz használt protokoll.  Értékek *tcp*. |

A fiókra a csoportosítás a hatását, csoportosított fizikai kapcsolatok számával kapcsolatos információkat a rekord a következő tulajdonságok:

| Tulajdonság | Leírás |
|:--|:--|
|LinksEstablished |A jelentéskészítési időszakban létesített fizikai hálózati kapcsolatok száma |
|LinksTerminated |A jelentéskészítési időszakban le lett állítva fizikai hálózati kapcsolatok száma |
|LinksFailed |A jelentéskészítési időszakban sikertelenül fizikai hálózati kapcsolatok száma. Ez az információ érhető el jelenleg csak a kimenő kapcsolatok számára. |
|LinksLive |Volt megnyitva a jelentéskészítési időtartomány végén található fizikai hálózati kapcsolatok száma|

#### <a name="metrics"></a>Mérőszámok

Mellett száma kapcsolati metrika adatmennyiség kapcsolatos információkat küldött, és a egy adott logikai kapcsolat fogadott, vagy hálózati port is szerepelnek a rekord a következő tulajdonságokat:

| Tulajdonság | Leírás |
|:--|:--|
|BytesSent |A jelentéskészítési időszakban elküldött bájtok száma |
|BytesReceived |A jelentéskészítési időszakban fogadott bájtok teljes száma |
|Válaszok |A jelentéskészítési időszakban megfigyelt válaszok száma. 
|ResponseTimeMax |A legnagyobb válaszideje (ezredmásodperc) észlelt a jelentéskészítési időszakban. Ha nincs érték a tulajdonság értéke üres.|
|ResponseTimeMin |A legkisebb válaszideje (ezredmásodperc) észlelt a jelentéskészítési időszakban. Ha nincs érték a tulajdonság értéke üres.|
|ResponseTimeSum |Az összes válaszidők összege a jelentéskészítési időszakban megfigyelt (ezredmásodperc). Ha nincs érték a tulajdonság értéke üres.|

A jelentett adatokat harmadik típus a válaszidő - mennyi ideig does egy hívó költségek feldolgozni és a távoli végpont célzottól kapcsolaton keresztül küldött kérés vár. A válaszidő jelentett igaz válaszideje, az alapul szolgáló protokoll becslése. Számított heurisztika alapján a megfigyelési adatok között a forrás és cél végén egy fizikai hálózati kapcsolatot a flow használatával. Elméleti szinten a kérelem utolsó bájtját elhagyja a küldő, és az idő a válasz utolsó bájtját érkezésekor vissza közötti különbség. E két időbélyegek ábrázolni a kérések és válaszok események egy adott fizikai kapcsolathoz szolgálnak. A különbség a kettő válaszideje, egyetlen kérelem jelöli. 

Ez a szolgáltatás első verziójában az algoritmus sikeres, a tényleges protokoll egy adott hálózati kapcsolathoz használt függően különböző fokú feltétlenül közelítés. Például az aktuális módszer jól működik a kérés-válasz alapú protokollokat, például a HTTP (S), de nem dolgozhat egyirányú, illetve üzenetet az üzenetsor-alapú protokollokat.

Az alábbiakban néhány fontos tudnivalók:

1. Ha egy folyamat ugyanazon IP-címen, de több hálózati adapterrel kapcsolatokat fogad, minden egyes kapcsolathoz külön rekord lesz jelentve. 
2. Helyettesítő karaktert tartalmazó IP-Címmel rendelkező rekordokat tartalmazni fogja nincs tevékenység. Nem foglalja azokat, amelyek arra, hogy a gépen port nyitott a bejövő forgalmat.
3. Részletességi és adatmennyiség csökkentése érdekében helyettesítő karaktert tartalmazó IP-Címmel rendelkező rekordok kimarad egy egyező rekordot (az ugyanabban a folyamatban, port és protokoll) esetén egy adott IP-címmel. Amikor egy helyettesítő karaktert tartalmazó IP-címrekordot, a rendszer a IsWildcardBind rekord tulajdonságot az adott IP-címmel rendelkező fog beállítani "True" jelzi, hogy a port van-e téve a jelentéskészítési gép minden felületen keresztül történik.
4. Csak egy adott illesztő a kötött portok van beállítva IsWildcardBind *hamis*.

#### <a name="naming-and-classification"></a>Elnevezési és besorolása
Az egyszerűség kedvéért egy kapcsolat a távoli vég IP-címét a RemoteIp tulajdonság szerepel. A bejövő kapcsolatok esetében RemoteIp megegyezik SourceIp, mint a kimenő kapcsolatokhoz megegyezik a DestinationIp. A RemoteDnsCanonicalNames tulajdonság jelöli a canonical DNS-nevek, a gép által jelentett RemoteIp. A RemoteDnsQuestions és RemoteClassification tulajdonság későbbi használatra vannak fenntartva. 

#### <a name="geolocation"></a>Földrajzi hely
*VMConnection* is tartalmaz földrajzi adatokat a távoli kapcsolat rekordokban végén a rekord a következő tulajdonságai: 

| Tulajdonság | Leírás |
|:--|:--|
|RemoteCountry |Az üzemeltető RemoteIp ország neve.  Ha például *Egyesült Államok* |
|RemoteLatitude |A földrajzi szélesség. Ha például *47.68* |
|RemoteLongitude |A földrajzi hosszúság. Ha például *-122.12* |

#### <a name="malicious-ip"></a>Kártékony IP-cím
Minden RemoteIp tulajdonság *VMConnection* tábla be van jelölve IP-címek összevetéssel az ismert kártékony tevékenységek. Ha a RemoteIp kártevőként azonosított a következő tulajdonságok lesz kitöltve (azok üres, ha a IP-cím nem számít rosszindulatú) a rekord a következő tulajdonságai:

| Tulajdonság | Leírás |
|:--|:--|
|MaliciousIp |A RemoteIp címe |
|IndicatorThreadType |Észlelt fenyegetés mutató a következő értékek egyike *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos* , *MaliciousUrl*, *kártevő*, *adathalász*, *Proxy*, *elleni*, *Megnézendők*.   |
|Leírás |Az észlelt fenyegetés leírása. |
|TLPLevel |Közlekedési lámpa protokoll (TLP) szint egyike a meghatározott értékeknek *fehér*, *zöld*, *sárga*, *Red*. |
|Megbízhatóság |Értékek a következők *0 – 100*. |
|Severity |Értékek a következők *0 – 5*, ahol *5* van a legsúlyosabb és *0* nem súlyos egyáltalán. Alapértelmezett érték *3*.  |
|FirstReportedDateTime |Először a szolgáltató jelenteni a kijelző. |
|LastReportedDateTime |A kijelző Interflow által látott utolsó időpontját. |
|IsActive |Azt jelzi, hogy a mutatók vannak inaktiválása az *igaz* vagy *hamis* értéket. |
|ReportReferenceLink |Egy adott rendszernek megfigyelhetőnek kapcsolatos jelentéseket mutató hivatkozásokat tartalmaz. |
|AdditionalInformation |További információkat biztosít, ha van ilyen, az észlelt fenyegetés kapcsolatban. |

### <a name="ports"></a>Portok 
Egy gép azon portjait, aktívan bejövő forgalmat fogadja el vagy potenciálisan fogadja el a forgalmat, de a jelentéskészítési időszakban üresjáratban a VMBoundPort táblában írja.  

Alapértelmezés szerint adatok nem szerepel ebben a táblában. Ahhoz, hogy ez a táblázat az írt adatok, küldjön egy e-mailek vminsights@microsoft.com munkaterület-Azonosítót és -munkaterület régiója együtt.   

Minden rekord VMBoundPort azonosíthatók a következő mezőket: 

| Tulajdonság | Leírás |
|:--|:--|
|Feldolgozás | Identitás, amellyel a port társítva folyamat (vagy a folyamatok csoportok).|
|IP | IP-cím-portot (lehet helyettesítő karaktert tartalmazó IP-Címek használatához *0.0.0.0*) |
|Port |A Port számát |
|Protokoll | A protokoll.  A példában *tcp* vagy *udp* (csak *tcp* jelenleg támogatott).|
 
Az identitás egy portot a fenti öt mezők származik, és a PortId tulajdonságban tárolja. Ez a tulajdonság segítségével gyorsan található rekordok egy adott portot időpontja között. 

#### <a name="metrics"></a>Mérőszámok 
A kapcsolatok társítva jelző mérőszámok a port rekordok tartalmazzák. Jelenleg a következő metrikák szerepelnek a jelentésben (mindegyik metrikát részleteit az előző szakaszban ismertetett): 

- BytesSent és BytesReceived 
- LinksEstablished, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Az alábbiakban néhány fontos tudnivalók:

- Ha egy folyamat ugyanazon IP-címen, de több hálózati adapterrel kapcsolatokat fogad, minden egyes kapcsolathoz külön rekord lesz jelentve.  
- Helyettesítő karaktert tartalmazó IP-Címmel rendelkező rekordokat tartalmazni fogja nincs tevékenység. Nem foglalja azokat, amelyek arra, hogy a gépen port nyitott a bejövő forgalmat. 
- Részletességi és adatmennyiség csökkentése érdekében helyettesítő karaktert tartalmazó IP-Címmel rendelkező rekordok kimarad egy egyező rekordot (az ugyanabban a folyamatban, port és protokoll) esetén egy adott IP-címmel. Ha egy helyettesítő karaktert tartalmazó IP-címrekordot, a rendszer a *IsWildcardBind* állítja be a rekord a meghatározott IP-címmel rendelkező tulajdonság *igaz*.  Ez azt jelzi, hogy a port van közzétéve a jelentéskészítő gép minden felületen keresztül történik. 
- Csak egy adott illesztő a kötött portok van beállítva IsWildcardBind *hamis*. 

### <a name="servicemapcomputercl-records"></a>ServiceMapComputer_CL records
Típussal rendelkező rekordok *ServiceMapComputer_CL* leltáradatokat a függőségi ügynök esetében van. Ezeket a rekordokat az alábbi táblázatban az jellemzőkkel rendelkeznek:

| Tulajdonság | Leírás |
|:--|:--|
| Typo | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | A munkaterületen belül a gépek egyedi azonosítója |
| ResourceName_s | A munkaterületen belül a gépek egyedi azonosítója |
| ComputerName_s | A számítógép teljes Tartományneve |
| Ipv4Addresses_s | Listáját a kiszolgáló az IPv4-címek |
| Ipv6Addresses_s | Listáját a kiszolgáló az IPv6-címek |
| DnsNames_s | DNS-nevek tömbje |
| OperatingSystemFamily_s | Windows vagy Linux rendszeren |
| OperatingSystemFullName_s | Az operációs rendszer teljes neve  |
| Bitness_s | Bitszámértékének a gép (32 bites vagy 64 bites)  |
| PhysicalMemory_d | A fizikai memória (MB) |
| Cpus_d | Processzorok száma |
| CpuSpeed_d | A CPU-sebesség MHz-ben|
| VirtualizationState_s | *ismeretlen*, *fizikai*, *virtuális*, *hipervizor* |
| VirtualMachineType_s | *Hyper-v*, *vmware*, és így tovább |
| VirtualMachineNativeMachineId_g | A hipervizor által hozzárendelt virtuális gép azonosítója |
| VirtualMachineName_s | A virtuális gép neve |
| BootTime_t | A rendszerindítás ideje |

### <a name="servicemapprocesscl-type-records"></a>ServiceMapProcess_CL Type records
Típussal rendelkező rekordok *ServiceMapProcess_CL* rendelkezik TCP-kapcsolattal rendelkező folyamatokat a Hardverleltár-adatait a függőségi ügynököt a kiszolgálón. Ezeket a rekordokat az alábbi táblázatban az jellemzőkkel rendelkeznek:

| Tulajdonság | Leírás |
|:--|:--|
| Typo | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | A munkaterületen belül a folyamat egyedi azonosítója |
| ResourceName_s | A gépen, amelyen fut a folyamat egyedi azonosítója|
| MachineResourceName_s | Az erőforrás nevét a gép |
| ExecutableName_s | A folyamat végrehajtható fájl neve |
| StartTime_t | A folyamat készlet kezdő időpont |
| FirstPid_d | Az első PID folyamatkészletben |
| Description_s | A folyamat leírása |
| CompanyName_s | A vállalat neve |
| InternalName_s | A belső név |
| ProductName_s | A termék nevét |
| ProductVersion_s | A termék verziója |
| FileVersion_s | A fájl verziója |
| CommandLine_s | A parancssorból |
| ExecutablePath z | A végrehajtható fájl elérési útja |
| WorkingDirectory_s | A munkakönyvtárban |
| Felhasználónév | A fiók, amely alatt a folyamat végrehajtása |
| UserDomain | A tartományhoz, amelyben a folyamat végrehajtása |

## <a name="sample-log-searches"></a>Naplókeresési minták

### <a name="list-all-known-machines"></a>Az összes ismert gépek listája
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="when-was-the-vm-last-rebooted"></a>Ha a virtuális gép utolsó újraindították
```kusto
let Today = now(); ServiceMapComputer_CL | extend DaysSinceBoot = Today - BootTime_t | summarize by Computer, DaysSinceBoot, BootTime_t | sort by BootTime_t asc`
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Kép, a hely és a Termékváltozat Azure virtuális gépek – összefoglalás
```kusto
ServiceMapComputer_CL | where AzureLocation_s != "" | summarize by ComputerName_s, AzureImageOffering_s, AzureLocation_s, AzureImageSku_s`
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>A fizikai memória-kapacitás az összes felügyelt számítógép listája.
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s`
```

### <a name="list-computer-name-dns-ip-and-os"></a>Számítógép neve, DNS, IP és az operációs rendszer.
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s`
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>A parancssorban keresse meg az "sql" összes folyamat
```kusto
ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Keresse meg a gép (legutóbbi rekord) erőforrás szerint
```kusto
search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Keresse meg a gép (legutóbbi rekord) IP-cím alapján
```kusto
search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>Egy megadott számítógép összes ismert folyamat listázása
```kusto
ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="list-all-computers-running-sql-server"></a>Az SQL Server rendszert futtató számítógépek listája
```kusto
ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s`
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Saját adatközpontban curl összes egyedi termék verziója listázása
```kusto
ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s`
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Az összes számítógép CentOS rendszerű számítógépcsoport létrehozása
```kusto
ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s`
```

### <a name="bytes-sent-and-received-trends"></a>Bájt küldött és fogadott trendek
```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart`
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Melyik Azure-beli virtuális gépek vannak továbbítaná a legtöbb bájt
```kusto
VMConnection | join kind=fullouter(ServiceMapComputer_CL) on $left.Computer == $right.ComputerName_s | summarize count(BytesSent) by Computer, AzureVMSize_s | sort by count_BytesSent desc`
```

### <a name="link-status-trends"></a>Kapcsolat állapota trendek
```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize  dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart`
```

### <a name="connection-failures-trend"></a>Kapcsolódási hibák trend
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

### <a name="number-of-open-ports-across-machines"></a>Gép megnyitott portok száma:
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>Nyílt pontszám a munkaterületen folyamatok rendelkeznek portok száma szerint
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Minden egyes porthoz összesített viselkedés
Ez a lekérdezés majd pontszámot rendelni az portok által használható tevékenységek, például a legtöbb bejövő/kimenő forgalom a portokon, a portok a legtöbb kapcsolatok
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>A kimenő kapcsolatok a gépek csoportból összefoglalója
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

## <a name="next-steps"></a>További lépések
* Ha most ismerkedik az Azure Monitor log-lekérdezések írásának módját, tekintse át a [használata a Log Analytics](../../azure-monitor/log-query/get-started-portal.md) log lekérdezéseket írhat az Azure Portalon.
* Ismerje meg [keresési lekérdezések írása](../../azure-monitor/log-query/search-queries.md).
