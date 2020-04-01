---
title: Naplók lekérdezése az Azure Monitor szolgáltatásból virtuális gépekhez
description: Az Azure Monitor virtuális gépek megoldás metrikák és naplóadatok gyűjtése, és ez a cikk ismerteti a rekordokat, és mintalekérdezéseket tartalmaz.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 61a71539dc034a216689eafd8991df60db96d2a4
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396926"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms"></a>Naplók lekérdezése az Azure Monitor szolgáltatásból virtuális gépekhez

Az Azure Monitor virtuális gépekhez gyűjti a teljesítmény- és kapcsolati metrikákat, a számítógép- és folyamatkészlet-adatokat, valamint az állapotadatokat, és továbbítja azokat az Azure Monitor Log Analytics-munkaterületére.  Ezek az adatok az Azure Monitor [lekérdezéséhez](../../azure-monitor/log-query/log-query-overview.md) érhetők el. Ezeket az adatokat olyan esetekre alkalmazhatja, amelyek tartalmazzák az áttelepítés tervezését, a kapacitáselemzést, a felderítést és az igény szerinti teljesítményhibaelhárítást.

## <a name="map-records"></a>Rekordok leképezése

Óránként egy rekord jön létre minden egyes egyedi számítógéphez és folyamathoz, a folyamat vagy számítógép indításakor létrehozott vagy az Azure Monitor for VMs Map szolgáltatásba való bedeszkázott rekordokon kívül. Ezek a rekordok a következő táblák ban találhatók. A ServiceMapComputer_CL események mezői és értékei a ServiceMap Azure Resource Manager API-ban a Machine erőforrás mezőihez vannak leképezve. A ServiceMapProcess_CL események mezői és értékei a ServiceMap Azure Resource Manager API Process erőforrásának mezőihez vannak leképezve. A ResourceName_s mező megegyezik a megfelelő Erőforrás-kezelő erőforrás névmezőjével. 

Vannak belsőleg létrehozott tulajdonságok, amelyek segítségével azonosíthatja az egyedi folyamatokat és számítógépeket:

- Számítógép: A *ResourceId* vagy *ResourceName_s* használatával egyedileg azonosíthatja a számítógépet a Log Analytics-munkaterületen belül.
- Folyamat: A *ResourceId* használatával egyedileg azonosíthatja a folyamatot a Log Analytics-munkaterületen belül. *ResourceName_s* annak a gépnek az összefüggésében egyedi, amelyen a folyamat fut (MachineResourceName_s) 

Mivel egy adott folyamathoz és számítógéphez több rekord is létezhet egy megadott időtartományban, a lekérdezések egynél több rekordot is visszaadhatnak ugyanahhoz a számítógéphez vagy folyamathoz. Ha csak a legutóbbi rekordot szeretné felvenni, adja hozzá `| summarize arg_max(TimeGenerated, *) by ResourceId` a lekérdezéshez.

### <a name="connections-and-ports"></a>Kapcsolatok és portok

A Kapcsolatmetrikák funkció két új táblát vezet be az Azure Monitor naplóiban – VMConnection és VMBoundPort. Ezek a táblák információt nyújtanak a számítógép (bejövő és kimenő) kapcsolatairól, valamint a nyitott/aktív kiszolgálóportokról. A ConnectionMetrics api-kon keresztül is elérhetővé teszi, amelyek lehetővé teszik egy adott metrika beszerzéséhez egy időablakban. A figyelőszoftver-csatorna *fogadásából* eredő TCP-kapcsolatok bejövőek, míg az adott IP-hez és *porthoz való csatlakozással* létrehozott tcp-kapcsolatok kimenők. A kapcsolat irányát az Irány tulajdonság jelöli, amely **be-** vagy **kimenőre**állítható. 

A táblákban lévő rekordok a függőségi ügynök által jelentett adatokból jönnek létre. Minden rekord egy 1 perces megfigyelést jelöl. Az időgenerált tulajdonság az időintervallum kezdetét jelzi. Minden rekord információkat tartalmaz az adott entitás, azaz a kapcsolat vagy a port azonosításához, valamint az entitáshoz társított mutatókat. Jelenleg csak az IPv4-en keresztüli TCP használatával előforduló hálózati tevékenységet jelenti a rendszer. 

#### <a name="common-fields-and-conventions"></a>Közös területek és egyezmények 

A következő mezők és konvenciók vonatkoznak a VMConnection és a VMBoundPort szolgáltatásra is: 

- Számítógép: Teljesen minősített tartománynév a jelentéskészítő gép 
- Ügynökazonosító: Egy gép egyedi azonosítója a Log Analytics-ügynökkel  
- Gép: Az Azure Resource Manager erőforrás neve a ServiceMap által elérhetővé tett gép. *M-{GUID}* formátumú, ahol a *GUID* azonosító ugyanaz, mint az AgentId azonosító  
- Folyamat: Az Azure Resource Manager erőforrás neve a ServiceMap által elérhetővé tett folyamathoz. *P-{hex string}* formátumból áll. A folyamat egyedi a gép hatókörén belül, és egyedi folyamatazonosítót hoz létre a gépek között, kombinálja a Gép és a Folyamat mezőket. 
- ProcessName: A jelentési folyamat végrehajtható neve.
- Minden IP-cím IPv4 kanonikus formátumú karakterlánc, például *13.107.3.160* 

A költségek és az összetettség kezelése érdekében a kapcsolatrekordok nem jelentenek egyéni fizikai hálózati kapcsolatokat. Több fizikai hálózati kapcsolat logikai kapcsolatba van csoportosítva, amely ezután megjelenik a megfelelő táblában.  Jelentés: *a VMConnection* tábla rekordjai logikai csoportosítást jelentenek, nem pedig a figyelt egyéni fizikai kapcsolatokat. A virtuális *gépkapcsolat*ban a következő attribútumokhoz azonos értékű fizikai hálózati kapcsolat egy adott egyperces időközben egyetlen logikai rekordba lesz összesítve. 

| Tulajdonság | Leírás |
|:--|:--|
|Irány |A kapcsolat iránya, az érték *bejövő* vagy *kimenő* |
|Gép |A számítógép FQDN |
|Folyamat |A folyamat vagy folyamatcsoportok azonosítása a kapcsolat kezdeményezése/elfogadása |
|SourceIp |A forrás IP-címe |
|DestinationIp (célip) |A cél IP-címe |
|Célport |A cél állomás portszáma |
|Protocol (Protokoll) |A kapcsolathoz használt protokoll.  Az értékek *tcp.* |

A csoportosítás hatásának figyelembevétele érdekében a csoportosított fizikai kapcsolatok számával kapcsolatos információk a bejegyzés következő tulajdonságai között szerepelnek:

| Tulajdonság | Leírás |
|:--|:--|
|LinkekAlapítva |A jelentési időszak alatt létrehozott fizikai hálózati kapcsolatok száma |
|LinkekSimisz |A jelentési időszak alatt leszakított fizikai hálózati kapcsolatok száma |
|KapcsolatokNem sikerült |Azoknak a fizikai hálózati kapcsolatoknak a száma, amelyek a jelentési időszak alatt meghibásodtak. Ez az információ jelenleg csak a kimenő kapcsolatokesetében érhető el. |
|LinkekLive |A jelentési időablak végén megnyitott fizikai hálózati kapcsolatok száma|

#### <a name="metrics"></a>Mérőszámok

A kapcsolatszám-mérőszámokon kívül az adott logikai kapcsolaton vagy hálózati porton küldött és fogadott adatok mennyiségére vonatkozó információk a bejegyzés következő tulajdonságai ban is szerepelnek:

| Tulajdonság | Leírás |
|:--|:--|
|Küldött bájt |A jelentési időablak alatt elküldött bájtok teljes száma |
|Fogadott bájt |A jelentési időablak során fogadott bájtok teljes száma |
|Válaszok |A jelentési időszak alatt megfigyelt válaszok száma. 
|VálaszIdőmax |A jelentési idő ablaka során megfigyelt legnagyobb válaszidő (ezredmásodperc). Ha nincs érték, a tulajdonság üres.|
|Válaszidőmin |A jelentési idő ablaka során megfigyelt legkisebb válaszidő (ezredmásodperc). Ha nincs érték, a tulajdonság üres.|
|VálaszIdőösszeg |A jelentési idő ablaka során megfigyelt összes válaszidő (ezredmásodperc) összege. Ha nincs érték, a tulajdonság üres.|

A jelentett adatok harmadik típusa a válaszidő – mennyi ideig várakozik a hívó a kapcsolaton keresztül küldött kérelem feldolgozására, és a távoli végpont által megválaszolandó válaszra. A jelentett válaszidő az alapul szolgáló alkalmazásprotokoll valódi válaszidejének becslése. Heurisztika alapján számítják ki a fizikai hálózati kapcsolat forrás- és célvége közötti adatáramlás megfigyelését. Fogalmilag ez a különbség a kérelem utolsó bájtja között, amikor elhagyja a feladót, és az az időpont, amikor a válasz utolsó bájtja visszaérkezik hozzá. Ez a két időbélyeg egy adott fizikai kapcsolat kérés- és válaszeseményeinek elhaolására szolgál. A köztük lévő különbség egyetlen kérelem válaszidejét jelöli. 

Ebben az első kiadásban ez a funkció, a mi algoritmus egy közelítés, amely működhet különböző mértékű siker függően a tényleges alkalmazás protokoll használt egy adott hálózati kapcsolat. A jelenlegi megközelítés például jól működik a kérelem-válasz alapú protokollok, például a HTTP(S) esetében, de nem működik egyirányú vagy üzenetsor-alapú protokollokkal.

Íme néhány fontos szempont, amelyet figyelembe kell venni:

1. Ha egy folyamat ugyanazon az IP-címen, de több hálózati csatolón keresztül fogadja a kapcsolatokat, a rendszer minden egyes kapcsolathoz külön rekordot jelent. 
2. A helyettesítő IP-című rekordok nem tartalmaznak tevékenységet. Ezek azért vannak benne, hogy képviseljék azt a tényt, hogy a számítógép egy portja nyitva áll a bejövő forgalom számára.
3. A részletesség és az adatmennyiség csökkentése érdekében a helyettesítő IP-című rekordok kimaradnak, ha egy adott IP-címmel rendelkező egyező rekord (ugyanahhoz a folyamathoz, porthoz és protokollhoz) van. Ha egy helyettesítő IP-rekord nincs megadva, az IsWildcardBind rekord tulajdonság a megadott IP-címmel "True" lesz állítva, jelezve, hogy a port a jelentéskészítő gép minden felületén elérhető.
4. Azok a portok, amelyek csak egy adott kapcsolaton vannak kötve, az IsWildcardBind értéke *Hamis*.

#### <a name="naming-and-classification"></a>Elnevezés és besorolás

Az egyszerűség kedvéért a kapcsolat távoli végének IP-címe a RemoteIp tulajdonság része. Bejövő kapcsolatok esetén a RemoteIp megegyezik a SourceIp protokollével, míg a kimenő kapcsolatok esetében ugyanaz, mint a DestinationIp. A RemoteDnsCanonicalNames tulajdonság a számítógép által a RemoteIp számára jelentett DNS gyűjtőneveket jelöli. A RemoteDnsQuestions és remoteClassification tulajdonságok későbbi használatra vannak fenntartva. 

#### <a name="geolocation"></a>Földrajzi hely

*A VMConnection* az egyes kapcsolatrekordok távoli végpontjainak földrajzi helymeghatározási adatait is tartalmazza a rekord következő tulajdonságai között: 

| Tulajdonság | Leírás |
|:--|:--|
|Távoli ország |A RemoteIp-et üzemeltető ország/régió neve.  Például *az Egyesült Államok* |
|Távoli szélesség |A földrajzi hely szélessége. Például *47,68* |
|Távoli Hosszúkásság |A helymeghatározás imázsának hosszúsága. Például *-122.12* |

#### <a name="malicious-ip"></a>Rosszindulatú IP-cím

A *VMConnection* tábla minden RemoteIp-tulajdonságát összeveti az ismert rosszindulatú tevékenységgel rendelkező IP-címekkel. Ha a RemoteIp rosszindulatúként van azonosítva, a következő tulajdonságok lesznek feltöltve (üresek, ha az IP nem tekinthető rosszindulatúnak) a bejegyzés következő tulajdonságaiban:

| Tulajdonság | Leírás |
|:--|:--|
|Rosszindulatú ip |A RemoteIp-cím |
|IndicatorThreadType típus |Az észlelt fenyegetésjelző a következő értékek egyike: *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos*, *MaliciousUrl*, *Malware*, *Phishing*, *Proxy*, *PUA*, *Watchlist*.   |
|Leírás |A megfigyelt fenyegetés leírása. |
|TLPSzint |A Traffic Light Protocol (TLP) szint az egyik definiált érték, *fehér,* *zöld,* *borostyán,* *piros.* |
|Megbízhatóság |Az értékek *0 – 100*. |
|Severity |Az értékek *0–5*, ahol az *5* a legsúlyosabb, a *0* pedig egyáltalán nem súlyos. Az alapértelmezett érték *3*.  |
|FirstReportedDateTime (Első jelentettdátumideje |Az első alkalommal, amikor a szolgáltató jelentette a mutatót. |
|LastReportedDateTime (LastReportedDateTime) |Az interflow utoljára látta a mutatót. |
|Aktív |Azt jelzi, hogy a jelzők *true* vagy *false* értékkel vannak inaktiválva. |
|JelentésHivatkozás |Egy adott megfigyelhetővel kapcsolatos jelentésekre mutató hivatkozások. |
|További információk |Adott esetben további információkat tartalmaz a megfigyelt fenyegetésről. |

### <a name="ports"></a>Portok 

A vmboundport-táblába olyan portok kerülnek a VMBoundPort táblába, amelyek aktívan fogadják a bejövő forgalmat, vagy potenciálisan fogadhatnak forgalmat, de a jelentési időablak alatt tétlenek.  

A VMBoundPort minden rekordját a következő mezők azonosítják: 

| Tulajdonság | Leírás |
|:--|:--|
|Folyamat | Annak a folyamatnak (vagy folyamatcsoportoknak) az identitása, amelyhez a port társítva van.|
|Ip | Port IP-cím (lehet helyettesítő IP, *0.0.0)* |
|Port |A port száma |
|Protocol (Protokoll) | A protokoll.  Példa: *tcp* vagy *udp* (jelenleg csak a *tcp* támogatott).|
 
A port identitása a fenti öt mezőből származik, és a PortId tulajdonságban van tárolva. Ezzel a tulajdonsággal gyorsan megtalálhatja egy adott port rekordjait az idő múlásával. 

#### <a name="metrics"></a>Mérőszámok 

A portbejegyzések olyan mutatókat tartalmaznak, amelyek a hozzájuk társított kapcsolatokat jelölik. Jelenleg a következő mutatók jelennek meg (az egyes mutatók részleteit az előző szakasz ismerteti): 

- Fogadott bájt és fogadott bájt 
- LinkekAlapítva, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Íme néhány fontos szempont, amelyet figyelembe kell venni:

- Ha egy folyamat ugyanazon az IP-címen, de több hálózati csatolón keresztül fogadja a kapcsolatokat, a rendszer minden egyes kapcsolathoz külön rekordot jelent.  
- A helyettesítő IP-című rekordok nem tartalmaznak tevékenységet. Ezek azért vannak benne, hogy képviseljék azt a tényt, hogy a számítógép egy portja nyitva áll a bejövő forgalom számára. 
- A részletesség és az adatmennyiség csökkentése érdekében a helyettesítő IP-című rekordok kimaradnak, ha egy adott IP-címmel rendelkező egyező rekord (ugyanahhoz a folyamathoz, porthoz és protokollhoz) van. Ha egy helyettesítő IP-rekord nincs megadva, az adott IP-címmel rendelkező rekord *IsWildcardBind* tulajdonsága *Igaz*értékre lesz állítva.  Ez azt jelzi, hogy a port a jelentéskészítő gép minden felületén elérhető. 
- Azok a portok, amelyek csak egy adott kapcsolaton vannak kötve, az IsWildcardBind értéke *Hamis*. 

### <a name="vmcomputer-records"></a>VMComputer rekordok

A *VMComputer* típusú rekordok készletadatokat rendelkeznek a függőségi ügynökkel rendelkező kiszolgálókhoz. Ezek a rekordok a következő táblázatban található tulajdonságokkal rendelkeznek:

| Tulajdonság | Leírás |
|:--|:--|
|TenantId | A munkaterület egyedi azonosítója |
|SourceSystem | *Insights* | 
|TimeGenerated | A rekord időbélyege (UTC) |
|Computer | A számítógép FQDN | 
|Ügynökazonosító | A Log Analytics-ügynök egyedi azonosítója |
|Gép | A ServiceMap által elérhetővé tett gép Azure Resource Manager-erőforrásának neve. Az *m-{GUID}* formátumból áll, ahol a *GUID* azonosító ugyanaz, mint az AgentID azonosító. | 
|DisplayName | Megjelenített név | 
|FullDisplayName (Teljes megjelenítésneve) | Teljes megjelenítendő név | 
|HostName | A tartománynév nélküli gép neve |
|BootTime | A gép indítási ideje (UTC) |
|TimeZone | A normalizált időzóna |
|VirtualizationState (VirtualizationState) | *virtuális,* *hipervizor*, *fizikai* |
|Ipv4Címek | IPv4-címek tömbje | 
|Ipv4Subnetmaszkok | IPv4-alhálózati maszkok tömbje (az Ipv4Addresses protokollal azonos sorrendben). |
|Ipv4DefaultGateways | IPv4-átjárók tömbje | 
|Ipv6Címek | IPv6-címek tömbje | 
|MacCímek | MAC-címek tömbje | 
|DnsNames (DnsNames) | A számítógéphez társított DNS-nevek tömbje. |
|DependencyAgentVersion | A számítógépen futó függőségi ügynök verziója. | 
|OperatingSystemFamily | *Linux*, *Windows* |
|OperatingSystemFullName | Az operációs rendszer teljes neve | 
|Fizikai memória | A fizikai memória megabájtban | 
|Cpu | Processzorok száma | 
|CpuSebesség | A CPU sebessége MHz-ben | 
|VirtualMachineType típus | *hyperv*, *vmware*, *xen* |
|VirtualMachineNativeId | A hipervizor által hozzárendelt virtuálisgép-azonosító | 
|VirtualMachineNativeName | A virtuális gép neve |
|VirtualMachineHypervisorId | A virtuális gépnek otthont adó hipervizor egyedi azonosítója |
|Hipervizortípusa | *hiperv* |
|HipervizorAzonosító | A hipervizor egyedi azonosítója | 
|Tárhelyszolgáltató | *Azure* |
|_ResourceId | Egy Azure-erőforrás egyedi azonosítója |
|AzureSubscriptionId | Az előfizetést azonosító globálisan egyedi azonosító | 
|AzureResourceGroup | Annak az Azure-erőforráscsoportnak a neve, amelynek a gép tagja. |
|AzureResourceName | Az Azure-erőforrás neve |
|AzureLocation | Az Azure-erőforrás helye |
|AzureUpdatetartomány | Az Azure frissítési tartományának neve |
|AzureFaultDomain | Az Azure-tartaléktartomány neve |
|AzureVmId | Az Azure virtuális gép egyedi azonosítója |
|AzureSize | Az Azure virtuális gép mérete |
|AzureImagePublisher | Az Azure vm-közzétevő neve |
|AzureImageOffering | Az Azure virtuálisgép-ajánlattípus neve | 
|AzureImageSku | Az Azure virtuális gép lemezképének termékváltozata | 
|AzureImageVersion | Az Azure virtuálisgép-lemezkép verziója | 
|AzureCloudServiceName | Az Azure felhőszolgáltatás ának neve |
|AzureCloudServiceDeployment | A felhőszolgáltatás telepítési azonosítója |
|AzureCloudServiceRoleName | Felhőszolgáltatás szerepkör neve |
|AzureCloudServiceRoleType | Cloud Service szerepkör típusa: *dolgozó* vagy *web* |
|AzureCloudServiceInstanceId | A Felhőszolgáltatás szerepkörpéldányának azonosítója |
|AzureVmScaleSetName | A virtuálisgép-méretezési csoport neve |
|AzureVmScaleSetDeployment | Virtuálisgép méretezési készletének központi telepítési azonosítója |
|AzureVmScaleSetResourceId | A virtuálisgép-méretezési csoport erőforrásának egyedi azonosítója.|
|AzureVmScaleSetInstanceId | A virtuálisgép-méretezési csoport egyedi azonosítója |
|AzureServiceFabricClusterId | Az Azure Service Fabric-fürt egyedi azonosítója | 
|AzureServiceFabricClusterName | Az Azure Service Fabric-fürt neve |

### <a name="vmprocess-records"></a>VMProcess rekordok

A *VMProcess* típusú rekordok készletadatokat rendelkeznek a TCP-hez kapcsolódó folyamatokhoz a függőségi ügynökkel rendelkező kiszolgálókon. Ezek a rekordok a következő táblázatban található tulajdonságokkal rendelkeznek:

| Tulajdonság | Leírás |
|:--|:--|
|TenantId | A munkaterület egyedi azonosítója |
|SourceSystem | *Insights* | 
|TimeGenerated | A rekord időbélyege (UTC) |
|Computer | A számítógép FQDN | 
|Ügynökazonosító | A Log Analytics-ügynök egyedi azonosítója |
|Gép | A ServiceMap által elérhetővé tett gép Azure Resource Manager-erőforrásának neve. Az *m-{GUID}* formátumból áll, ahol a *GUID* azonosító ugyanaz, mint az AgentID azonosító. | 
|Folyamat | A Szolgáltatásleképezés folyamat ának egyedi azonosítója. *P-{GUID}* formában van. 
|Végrehajtható név | A folyamat végrehajtható fájljának neve | 
|DisplayName | Megjelenítendő név feldolgozása |
|Szerepkör | Folyamatszerepkör: *webkiszolgáló*, *appServer*, *databaseServer*, *ldapServer*, *smbServer* |
|Csoport | Folyamatcsoport neve. Az ugyanabban a csoportban lévő folyamatok logikailag kapcsolódnak egymáshoz, pl. ugyanannak a terméknek vagy rendszerösszetevőnek a része. |
|StartTime | A folyamatkészlet kezdési időpontja |
|FirstPid (Első: Nagy- | A folyamatkészlet első PID-je |
|Leírás | A folyamat leírása |
|CompanyName | A vállalat neve |
|Belső név | A belső név |
|TermékNév | A termék neve |
|ProductVersion | A termék verziója |
|Fájlverzió | A fájl verziója |
|Végrehajtható elérési út |A végrehajtható fájl elérési útja |
|Commandline | A parancssor |
|WorkingDirectory | A munkakönyvtár |
|Szolgáltatások | Szolgáltatások olyan tömbje, amelynek keretében a folyamat végrehajtásalatt áll |
|UserName (Felhasználónév) | Az a fiók, amely nek megfelelően a folyamat végrehajtásra kerül |
|UserDomain (Felhasználói tartomány) | Az a tartomány, amely alatt a folyamat végrehajtásalatt áll |
|_ResourceId | A munkaterületen belüli folyamat egyedi azonosítója |


## <a name="sample-map-queries"></a>Mintatérkép-lekérdezések

### <a name="list-all-known-machines"></a>Az összes ismert gép felsorolása

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="when-was-the-vm-last-rebooted"></a>Mikor volt a virtuális gép utoljára újraindul

```kusto
let Today = now(); VMComputer | extend DaysSinceBoot = Today - BootTime | summarize by Computer, DaysSinceBoot, BootTime | sort by BootTime asc
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Az Azure-beli virtuális gépek összefoglalása kép, hely és termékváltozat szerint

```kusto
VMComputer | where AzureLocation != "" | summarize by Computer, AzureImageOffering, AzureLocation, AzureImageSku
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Az összes felügyelt számítógép fizikai memóriakapacitásának felsorolása

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project PhysicalMemoryMB, Computer
```

### <a name="list-computer-name-dns-ip-and-os"></a>Számítógépnév, DNS, IP és operációs rendszer listázása

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project Computer, OperatingSystemFullName, DnsNames, Ipv4Addresses
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Az összes folyamat megkeresése az "sql" paranccsal a parancssorban

```kusto
VMProcess | where CommandLine contains_cs "sql" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Gép (legutóbbi rekord) keresése erőforrásnév szerint

```kusto
search in (VMComputer) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Gép (legutóbbi rekord) keresése IP-cím szerint

```kusto
search in (VMComputer) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>Az összes ismert folyamat listázása egy adott gépen

```kusto
VMProcess | where Machine == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-computers-running-sql-server"></a>Az SQL Server rendszert futtató összes számítógép listázása

```kusto
VMComputer | where AzureResourceName in ((search in (VMProcess) "*sql*" | distinct Machine)) | distinct Computer
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>A curl összes egyedi termékverziójának listázása az adatközpontomban

```kusto
VMProcess | where ExecutableName == "curl" | distinct ProductVersion
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>A CentOS rendszert futtató számítógépek számítógépcsoportjának létrehozása

```kusto
VMComputer | where OperatingSystemFullName contains_cs "CentOS" | distinct Computer
```

### <a name="bytes-sent-and-received-trends"></a>Elküldött és fogadott bájtok trendjei

```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Mely Azure-virtuális gépek küldik a legtöbb bájtot

```kusto
VMConnection | join kind=fullouter(VMComputer) on $left.Computer == $right.Computer | summarize count(BytesSent) by Computer, AzureVMSize | sort by count_BytesSent desc
```

### <a name="link-status-trends"></a>Kapcsolatállapot-trendek

```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart
```

### <a name="connection-failures-trend"></a>Csatlakozási hibák trendje

```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart
```

### <a name="bound-ports"></a>Kötött portok

```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>A gépek közötti nyitott portok száma

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>A munkaterület folyamatainak pontozása a megnyitott portok száma szerint

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Összesített viselkedés az egyes portokesetében

Ez a lekérdezés ezután a portok pontozására használható tevékenység, például a legtöbb bejövő/kimenő forgalmat bonyolító portok, a legtöbb kapcsolattal rendelkező portok
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Gépek egy csoportjának kimenő kapcsolatainak összegzése

```kusto
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(VMComputer
| summarize ips=makeset(todynamic(Ipv4Addresses)) by MonitoredMachine=AzureResourceName
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

## <a name="performance-records"></a>Teljesítményrekordok
Az *InsightsMetrics* típusú rekordok a virtuális gép vendég operációs rendszerének teljesítményadatait rendelkeznek. Ezek a rekordok a következő táblázatban található tulajdonságokkal rendelkeznek:


| Tulajdonság | Leírás |
|:--|:--|
|TenantId | A munkaterület egyedi azonosítója |
|SourceSystem | *Insights* | 
|TimeGenerated | Az érték beszedésének időpontja (UTC) |
|Computer | A számítógép FQDN | 
|Forrás | *vm.azm.ms* |
|Névtér | A teljesítményszámláló kategóriája | 
|Név | A teljesítményszámláló neve |
|Val között | Összegyűjtött érték | 
|Címkék | Kapcsolódó részleteket a rekordot. A különböző bejegyzéstípusokkal használt címkéket az alábbi táblázatban talál.  |
|Ügynökazonosító | Az egyes számítógépek ügynökének egyedi azonosítója |
|Típus | *InsightsMetrics* |
|_Erőforrásazonosító_ | A virtuális gép erőforrásazonosítója |

Az *InsightsMetrics* táblában jelenleg összegyűjtött teljesítményszámlálók az alábbi táblázatban találhatók:

| Névtér | Név | Leírás | Unit (Egység) | Címkék |
|:---|:---|:---|:---|:---|
| Computer    | Szívverés             | Számítógép szívverése                        | | |
| Memory (Memória)      | Elérhető MB           | Rendelkezésre álló memóriabájt                    | Bájt          | memorySizeMB - Teljes memóriaméret|
| Network (Hálózat)     | WriteBytesPerSecond   | Hálózat írási bájtjai másodpercenként            | BytesPerSecond | NetworkDeviceId - Az eszköz azonosítója<br>bájt - Összes elküldött bájt |
| Network (Hálózat)     | ReadBytesPerSecond    | Hálózati olvasási bájt másodpercenként             | BytesPerSecond | networkDeviceId - Az eszköz azonosítója<br>bájt - Összes fogadott bájt |
| Processzor   | KihasználtságSzázalék | Processzorkihasználtság százaléka          | Százalék        | totalCpus - Összes CPU |
| LogicalDisk | ÍrásperSz       | Logikai lemez másodpercenkénti írása            | CountPerSzekszekundum | mountId - A készülék csatlakoztatási azonosítója |
| LogicalDisk | Írási késedelmiségek        | Logikai lemez írási késleltetése ezredmásodperc    | Ezredmásodperc   | mountId - A készülék csatlakoztatási azonosítója |
| LogicalDisk | WriteBytesPerSecond   | Logikai lemez írása másodpercenkénti bájt       | BytesPerSecond | mountId - A készülék csatlakoztatási azonosítója |
| LogicalDisk | TransfersPerSecond (ÁtutalásperSz    | Logikai lemezátvitel másodpercenként         | CountPerSzekszekundum | mountId - A készülék csatlakoztatási azonosítója |
| LogicalDisk | TransferLatencyMs     | Logikai lemezátviteli késés ezredmásodperc | Ezredmásodperc   | mountId - A készülék csatlakoztatási azonosítója |
| LogicalDisk | Olvasásszekunduma        | Logikai lemezolvasás másodpercenként             | CountPerSzekszekundum | mountId - A készülék csatlakoztatási azonosítója |
| LogicalDisk | Olvasási késedelmiségek         | Logikai lemez olvasási késleltetése ezredmásodperc     | Ezredmásodperc   | mountId - A készülék csatlakoztatási azonosítója |
| LogicalDisk | ReadBytesPerSecond    | Logikai lemez olvasási bájtmásodpercenként        | BytesPerSecond | mountId - A készülék csatlakoztatási azonosítója |
| LogicalDisk | FreeSpacePercentage (FreeSpacePercentage)   | Logikai lemez szabad területszázaléka        | Százalék        | mountId - A készülék csatlakoztatási azonosítója |
| LogicalDisk | FreeSpaceMB között           | Logikai szabad lemezterület-bájtok             | Bájt          | mountId - A készülék csatlakoztatási azonosítója<br>diskSizeMB - Teljes lemezméret |
| LogicalDisk | BytesPerSecond        | Logikai lemezbájt másodpercenként             | BytesPerSecond | mountId - A készülék csatlakoztatási azonosítója |


## <a name="next-steps"></a>További lépések

* Ha most írja naplólekérdezések az Azure Monitorban, tekintse át, [hogyan használhatja](../../azure-monitor/log-query/get-started-portal.md) a Log Analytics az Azure Portalon naplólekérdezések írásához.

* További információ a [keresési lekérdezések írásáról.](../../azure-monitor/log-query/search-queries.md)
