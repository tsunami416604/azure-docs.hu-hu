---
title: Azure Update Management-naplók lekérdezése
description: Ez a cikk azt ismerteti, hogyan lehet lekérdezni Update Management naplóit a Log Analytics munkaterületen.
services: automation
ms.subservice: update-management
ms.date: 01/10/2020
ms.topic: conceptual
ms.openlocfilehash: 5a1979b0e714f35694999c04e1f890b710d54ac9
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867067"
---
# <a name="query-update-records-for-update-management-in-azure-monitor-logs"></a>Azure Monitor naplók Update Management frissítési rekordjainak lekérdezése

A Update Management-megoldásban megadott részletek mellett a Log Analytics munkaterületen tárolt naplókra is rákereshet. A megoldás lap bal oldali ablaktábláján válassza a **naplók**lehetőséget. Megnyílik a **napló keresése** oldal.

Azt is megtudhatja, hogyan szabhatja testre a lekérdezéseket, és hogyan használhatja őket különböző ügyfelekről, és többet is megtalálhat: [log Analytics Search API dokumentációja](https://dev.loganalytics.io/).

## <a name="update-records"></a>Update típusú rekordok

A Update Management által összegyűjtött rekordok Windows és Linux rendszerű virtuális gépek esetén, valamint a naplók keresési eredményei között megjelenő adattípusok. A következő szakaszok ezeket a rekordokat ismertetik.

### <a name="required-updates"></a>Szükséges frissítések

Létrejön egy `RequiredUpdate` típusú rekord, amely a gép által igényelt frissítéseket jelképezi. Ezek a rekordok a következő táblázatban szereplő tulajdonságokkal rendelkeznek:

| Tulajdonság | Leírás | 
|----------|-------------|
| Computer | A jelentéskészítő gép teljes tartományneve. |
| KBID | A Windows Update szolgáltatáshoz tartozó Tudásbázis-cikk azonosítója. |
| ManagementGroupName | A Operations Manager felügyeleti csoport vagy Log Analytics munkaterület neve. | 
| Termék | Azok a termékek, amelyekhez a frissítés alkalmazható. | 
| PublishDate | Az a dátum, amikor a frissítés készen áll a letöltésre és telepítésre Windows Update. |
| Kiszolgáló | | 
| SourceHealthServiceId | A Log Analytics Windows-ügynök AZONOSÍTÓját jelképező egyedi azonosító. |
| SourceSystem | *OperationsManager* | 
| TenantId | Azure Active Directory szervezetének példányát jelképező egyedi azonosító. | 
| TimeGenerated | A rekord létrehozásának dátuma és időpontja. | 
| Type (Típus) | *Update* | 
| UpdateClassification | Az alkalmazható frissítések típusát jelöli. Windows esetén:<br> *Kritikus frissítések*<br> *Biztonsági frissítések*<br> *Kumulatív frissítések*<br> *Szolgáltatáscsomag*<br> *Szervizcsomagok*<br> *Definíciófrissítések*<br> *Eszközök*<br> *Frissítések*. Linux esetén:<br> *Kritikus és biztonsági frissítések*<br> *Egyéb* |
| UpdateSeverity | A biztonsági rés súlyossági besorolása. Az értékek a következők:<br> *Kritikus*<br> *Fontos*<br> *Közepes*<br> *Alacsony* |
| UpdateTitle | A frissítés címe.|

### <a name="update"></a>Frissítés

Létrejön egy `Update` típusú rekord, amely az elérhető frissítéseket és a gép telepítési állapotát jelöli. Ezek a rekordok a következő táblázatban szereplő tulajdonságokkal rendelkeznek:

| Tulajdonság | Leírás | 
|----------|-------------|
| ApprovalSource | Csak a Windows operációs rendszerre vonatkozik. Az érték *Microsoft Update*. |
| Approved | *Igaz* vagy *hamis* |
| Osztályozás | *Frissítések* |
| Computer | A jelentéskészítő gép teljes tartományneve. |
| ComputerEnvironment | *Azure* vagy *nem Azure*. |
| MSRCBulletinID | Biztonsági közlemény AZONOSÍTÓjának száma | 
| MSRCSeverity | A biztonsági rés súlyossági besorolása. Az értékek a következők:<br> *Kritikus*<br> *Fontos*<br> *Közepes*<br> *Alacsony* |  
| KBID | A Windows Update szolgáltatáshoz tartozó Tudásbázis-cikk azonosítója. |
| ManagementGroupName | A Operations Manager felügyeleti csoport vagy Log Analytics munkaterület neve. |
| UpdateID | A szoftverfrissítés egyedi azonosítója. |
| RevisionNumber | Egy frissítés adott változatának verziószáma. |
| Választható | *Igaz* vagy *hamis* | 
| RebootBehavior | A frissítés telepítése/eltávolítása után történő újraindítási viselkedés. |
| _ResourceId | Annak az erőforrásnak az egyedi azonosítója, amelyhez a rekord társítva van. |
| Type (Típus) | *Update* |
| VMUUID | A virtuális gép egyedi azonosítója. |
| MG | A felügyeleti csoport vagy Log Analytics munkaterület egyedi azonosítója. | 
| TenantId | Azure Active Directory szervezetének példányát jelképező egyedi azonosító. | 
| SourceSystem | *OperationsManager* | 
| TimeGenerated | A rekord létrehozásának dátuma és időpontja. | 
| SourceComputerId | A forrásoldali számítógépet jelképező egyedi azonosító. | 
| Cím | A frissítés címe. |
| PublishedDate (UTC) | Az a dátum, amikor a frissítés készen áll a letöltésre és telepítésre Windows Update.  |
| UpdateState | A frissítés aktuális állapota. | 
| Termék | Azok a termékek, amelyekhez a frissítés alkalmazható. |
| SubscriptionId | Az Azure-előfizetés egyedi azonosítója. | 
| ResourceGroup | Azon erőforráscsoport neve, amelyben az erőforrás tagja. | 
| ResourceProvider | Megadja az erőforrás-szolgáltatót. | 
| Erőforrás | Az erőforrás neve. | 
| ResourceType | Az erőforrástípus neve. | 

### <a name="update-agent"></a>Ügynök frissítése

Létrejön egy `UpdateAgent` típusú rekord, amely a számítógépen lévő frissítési ügynök részleteit tartalmazza. Ezek a rekordok a következő táblázatban szereplő tulajdonságokkal rendelkeznek:

| Tulajdonság | Leírás | 
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | | 
| AutomaticUpdateEnabled | | 
| Computer | A jelentéskészítő gép teljes tartományneve. |
| DaySinceLastUpdateBucket | | 
| ManagementGroupName | A Operations Manager felügyeleti csoport vagy Log Analytics munkaterület neve. |
| OSVersion | Az operációs rendszer verziója. |
| Kiszolgáló | |
| SourceHealthServiceId | A Log Analytics Windows-ügynök AZONOSÍTÓját jelképező egyedi azonosító. |
| SourceSystem | *OperationsManager* | 
| TenantId | Azure Active Directory szervezetének példányát jelképező egyedi azonosító. |
| TimeGenerated | A rekord létrehozásának dátuma és időpontja. |
| Type (Típus) | *Update* | 
| WindowsUpdateAgentVersion | A Windows Update ügynök verziója. |
| WSUSServer | Hibák megjelenítése, ha a Windows Update ügynöknek problémája van a hibaelhárításhoz. |

### <a name="update-deployment-status"></a>Központi telepítés állapotának frissítése 

Létrejön egy `UpdateRunProgress` típusú rekord, amely frissíti a számítógép által ütemezett központi telepítés állapotát. Ezek a rekordok a következő táblázatban szereplő tulajdonságokkal rendelkeznek:

| Tulajdonság | Leírás | 
|----------|-------------|
| Computer | A jelentéskészítő gép teljes tartományneve. |
| ComputerEnvironment | *Azure* vagy *nem Azure*. | 
| CorrelationId | A frissítéshez tartozó runbook-feladatok egyedi azonosítója. |
| EndTime | Az az idő, amikor a szinkronizálási folyamat befejeződött. | 
| ErrorResult | Windows Update hibakód jön létre, ha egy frissítés telepítése sikertelen. | 
| InstallationStatus | Egy frissítés lehetséges telepítési állapota az ügyfélszámítógépen, *folyamatban*, *sikeres*, *részben meghiúsult*. |
| KBID | A Windows Update szolgáltatáshoz tartozó Tudásbázis-cikk azonosítója. | 
| ManagementGroupName | A Operations Manager felügyeleti csoport vagy Log Analytics munkaterület neve. |
| OSType | Megadja az operációs rendszer, a *Windows* vagy a *Linux*típusát. | 
| Termék | Azok a termékek, amelyekhez a frissítés alkalmazható. |
| Erőforrás | Az erőforrás neve. | 
| ResourceId | Annak az erőforrásnak az egyedi azonosítója, amelyhez a rekord társítva van. |
| ResourceProvider | Megadja az erőforrás-szolgáltatót. | 
| ResourceType | Az erőforrástípus neve. | 
| SourceComputerId | A forrásoldali számítógépet jelképező egyedi azonosító. | 
| SourceSystem | *OperationsManager* |
| StartTime | A frissítés ütemezett telepítésének időpontját. |
| SubscriptionId | Az Azure-előfizetés egyedi azonosítója. | 
| SucceededOnRetry | Azt mutatja, hogy a frissítés végrehajtása sikertelen volt-e az első kísérlet során, és az aktuális művelet újrapróbálkozási kísérlet. |
| TimeGenerated | A rekord létrehozásának dátuma és időpontja. |
| Cím | A frissítés címe. |
| Type (Típus) | *UpdateRunProgress* |
| UpdateId | A szoftverfrissítés egyedi azonosítója. |
| VMUUID | A virtuális gép egyedi azonosítója. |
| _ResourceId | Annak az erőforrásnak az egyedi azonosítója, amelyhez a rekord társítva van. |

### <a name="update-summary"></a>Frissítés összegzése 

Létrejön egy `UpdateSummary` típusú rekord, amely a számítógép által készített frissítési összegzést biztosít. Ezek a rekordok a következő táblázatban szereplő tulajdonságokkal rendelkeznek:

| Tulajdonság | Leírás | 
|----------|-------------|
| Computer | A jelentéskészítő gép teljes tartományneve. |
| ComputerEnvironment | *Azure* vagy *nem Azure*. | 
| CriticalUpdatesMissing | A hiányzó kritikus frissítések száma. | 
| ManagementGroupName | A Operations Manager felügyeleti csoport vagy Log Analytics munkaterület neve. |
| NETRuntimeVersion | A .NET-keretrendszer telepített verziója a Windows rendszerű számítógépen. |
| OldestMissingSecurityUpdateBucket | | 
| OldestMissingSecurityUpdateInDays | |
| OsVersion | Az operációs rendszer verziója. |
| OtherUpdatesMissing | Hiányzó észlelt frissítések száma. |
| Erőforrás |  Az erőforrás neve. | 
| ResourceGroup | Azon erőforráscsoport neve, amelyben az erőforrás tagja. |
| ResourceId | Annak az erőforrásnak az egyedi azonosítója, amelyhez a rekord társítva van. |
| ResourceProvider | Megadja az erőforrás-szolgáltatót. |
| ResourceType | Az erőforrástípus neve. |
| RestartPending | *Igaz* vagy *hamis*. |
| SecurityUpdatesMissing | A hiányzó biztonsági frissítések száma.| 
| SourceComputerId | A virtuális gép egyedi azonosítója. |
| SourceSystem | *OpsManager* | 
| SubscriptionId | Az Azure-előfizetés egyedi azonosítója. |
| TimeGenerated | A rekord létrehozásának dátuma és időpontja. |
| TotalUpdatesMissing | A hiányzó frissítések teljes száma. | 
| Type (Típus) | *UpdateSummary típusú* |
| VMUUID | A virtuális gép egyedi azonosítója. |
| WindowsUpdateAgentVersion | A Windows Update ügynök verziója. |
| WindowsUpdateSetting | Megjeleníti a Windows Update ügynök állapotát. Lehetséges értékek:<br> *Ütemezett telepítés*<br> *Értesítés a telepítés előtt*<br> A nem kifogástalan állapotú WUA-ügynök által visszaadott hiba. | 
| WSUSServer | Hibák megjelenítése, ha a Windows Update ügynöknek problémája van a hibaelhárításhoz. |
| _ResourceId | Annak az erőforrásnak az egyedi azonosítója, amelyhez a rekord társítva van. |

## <a name="sample-queries"></a>Mintalekérdezések

A következő szakaszokban példákat talál a Update Management gyűjtött frissítési rekordokra.

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Győződjön meg arról, hogy a nem Azure-beli gépek vannak bekészítve

Annak ellenőrzéséhez, hogy a közvetlenül csatlakoztatott gépek Azure Monitor-naplókkal kommunikálnak-e, futtassa a következő naplók egyikét.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Windows rendszerű számítógépen a következő információkat tekintheti meg az ügynök kapcsolatának ellenőrzéséhez Azure Monitor naplók használatával:

1. A vezérlőpulton nyissa meg a **Microsoft monitoring Agent ügynököt**. Az **Azure log Analytics** lapon az ügynök a következő üzenetet jeleníti meg: **a Microsoft monitoring Agent sikeresen csatlakozott a log Analyticshoz**.
2. Nyissa meg a Windows eseménynaplót. Nyissa meg az **Application and Services Logs\Operations Manager alkalmazást** , és keressen rá a 3000-es azonosítójú eseményre és a 5002-es azonosítójú eseményre **.** Ezek az események jelzik, hogy a számítógép regisztrálva van a Log Analytics-munkaterületen, és konfigurációt kap.

Ha az ügynök nem tud kommunikálni Azure Monitor naplókkal, és az ügynök úgy van konfigurálva, hogy tűzfalon vagy proxykiszolgálón keresztül kommunikáljon az internettel, ellenőrizze, hogy a tűzfal vagy a proxykiszolgáló megfelelően van-e konfigurálva. A tűzfal vagy a proxykiszolgáló megfelelő konfigurálásának ellenőrzéséhez tekintse meg a [hálózati konfiguráció Windows-ügynökhöz](../azure-monitor/platform/agent-windows.md) vagy [a Linux-ügynök hálózati konfigurációja](../log-analytics/log-analytics-agent-linux.md)című témakört.

> [!NOTE]
> Ha a linuxos rendszerek proxy vagy Log Analytics átjáróval való kommunikációra vannak konfigurálva, és a megoldás előkészítését végzi, frissítse a *proxy. conf* engedélyeit, hogy a omiuser csoport olvasási engedélyt adjon a fájlhoz a következő parancsok használatával:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Az újonnan hozzáadott Linux-ügynökök a felmérés végrehajtása után **frissülő** állapotot mutatnak. A folyamat akár hat órát is igénybe vehet.

Annak ellenőrzéséhez, hogy egy Operations Manager felügyeleti csoport kommunikál-e Azure Monitor naplókkal, tekintse meg az [Operations Manager integráció ellenőrzése Azure monitor naplókkal](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor)című témakört.

### <a name="single-azure-vm-assessment-queries-windows"></a>Egyetlen Azure-beli VM-értékelő lekérdezés (Windows)

Cserélje le a VMUUID értéket a lekérdezni kívánt virtuális gép virtuális géphez tartozó GUID azonosítóra. A következő lekérdezésnek a Azure Monitor naplókban való futtatásával megkeresheti a VMUUID: `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Hiányzó frissítések összegzése

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Hiányzó frissítések listája

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

### <a name="single-azure-vm-assessment-queries-linux"></a>Egyetlen Azure-beli VM-értékelő lekérdezés (Linux)

Egyes Linux-disztribúciók esetében a [endianness](https://en.wikipedia.org/wiki/Endianness) nem egyezik a Azure Resource Manager és a Azure monitor naplókban tárolt VMUUID értékkel. A következő lekérdezés mindkét endianness egyezést keres. Cserélje le a VMUUID értékeket a GUID big-endian és little-endian formátumára, hogy megfelelően visszaadja az eredményeket. A következő lekérdezésnek a Azure Monitor naplókban való futtatásával megkeresheti a VMUUID: `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Hiányzó frissítések összegzése

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Hiányzó frissítések listája

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

### <a name="multi-vm-assessment-queries"></a>Több virtuális gépre kiterjedő értékelési lekérdezések

#### <a name="computers-summary"></a>Számítógépek összegzése

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

#### <a name="missing-updates-summary"></a>Hiányzó frissítések összegzése

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="computers-list"></a>Számítógépek listája

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

#### <a name="missing-updates-list"></a>Hiányzó frissítések listája

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="next-steps"></a>Következő lépések

* A részletes frissítési információk megtekintéséhez használja a naplóbeli kereséseket [Azure monitor naplókban](../log-analytics/log-analytics-log-searches.md) .
* [Riasztások létrehozása](automation-tutorial-update-management.md#configure-alerts) a központi telepítés állapotának frissítéséhez.
