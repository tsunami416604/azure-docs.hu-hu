---
title: Az Azure Update Management naplóinak lekérdezése
description: Ez a cikk azt ismerteti, hogyan lehet lekérdezni a loganalytics-munkaterület frissítési felügyelet naplóit.
services: automation
ms.subservice: update-management
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: 81e12e775306cc8637dedd534f50e8a14bc09a26
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743865"
---
# <a name="query-update-records-for-update-management-in-azure-monitor-logs"></a>Frissítési rekordok lekérdezése az Azure Figyelőnaplóiban az Update Management szolgáltatáshoz

Az Update Management megoldásban megadott részleteken kívül a Log Analytics-munkaterületen tárolt naplókon is kereshet. A megoldás lap bal oldali ablaktáblájában válassza a **Naplók**lehetőséget. Megnyílik **a Naplókeresés** lap.

Azt is megtudhatja, hogyan szabhatja testre a lekérdezéseket, vagy használhatja őket a különböző ügyfelekés több felkeresi: [Log Analytics search API dokumentáció](https://dev.loganalytics.io/).

## <a name="update-records"></a>Update típusú rekordok

A Windows és Linux rendszerű virtuális gépek frissítéskezelése által gyűjtött rekordok, valamint a naplókeresési eredményekben megjelenő adattípusok. A következő szakaszok ismertetik ezeket a rekordokat.

### <a name="required-updates"></a>Szükséges frissítések

Olyan típusú rekord `RequiredUpdate` jön létre, amely a számítógép által igényelt frissítéseket jelöli. Ezek a rekordok a következő táblázatban található tulajdonságokkal rendelkeznek:

| Tulajdonság | Leírás | 
|----------|-------------|
| Computer | A jelentéskészítő gép teljesen minősített tartományneve. |
| KBID | Tudásbáziscikk-azonosító a Windows-frissítéshez. |
| ManagementGroupName | Az Operations Manager felügyeleti csoport vagy a Log Analytics-munkaterület neve. | 
| Product | Azok a termékek, amelyekre a frissítés vonatkozik. | 
| PublishDate | A frissítés letöltésének és telepítésének dátuma a Windows Update szolgáltatásból. |
| Kiszolgáló | | 
| SourceHealthServiceId azonosító | A Log Analytics Windows-ügynökazonosítóját jelölő egyedi azonosító |
| SourceSystem | *OperationsManager* | 
| TenantId | Az Azure Active Directory szervezetpéldányát jelölő egyedi azonosító. | 
| TimeGenerated | A rekord létrehozásának dátuma és időpontja. | 
| Típus | *Frissítés* | 
| UpdateClassification (UpdateClassification) | Az alkalmazható frissítések típusát jelzi. Windows esetén:<br> *Kritikus frissítések*<br> *Biztonsági frissítések*<br> *Kumulatív frissítések*<br> *Funkciócsomagok*<br> *Szervizcsomagok*<br> *Definíciófrissítések*<br> *Eszközök*<br> *Frissítések*. Linux esetén:<br> *Kritikus vagy biztonsági frissítések*<br> *Egyéb* |
| UpdateSeverity | A biztonsági rés súlyossági besorolása. Az értékek a következők:<br> *Kritikus*<br> *Fontos*<br> *Mérsékelt*<br> *Alacsony* |
| UpdateTitle cím | A frissítés címe.|

### <a name="update"></a>Frissítés

Létrejön egy olyan `Update` bejegyzés, amely nek megfelelő enkre a rendelkezésre álló frissítéseket és a gép telepítési állapotát jelöli. Ezek a rekordok a következő táblázatban található tulajdonságokkal rendelkeznek:

| Tulajdonság | Leírás | 
|----------|-------------|
| ApprovalSource (Jóváhagyási forrás) | Csak a Windows operációs rendszerre vonatkozik. Az érték a *Microsoft Update*. |
| Approved | *Igaz* vagy *Hamis* |
| Osztályozás | *Frissítések* |
| Computer | A jelentéskészítő gép teljesen minősített tartományneve. |
| ComputerEnvironment (Számítógépkörnyezet) | *Azure* *vagy nem Azure*. |
| MSRCBulletinID | A biztonsági közlemény azonosítószáma | 
| MSRCSeverity | A biztonsági rés súlyossági besorolása. Az értékek a következők:<br> *Kritikus*<br> *Fontos*<br> *Mérsékelt*<br> *Alacsony* |  
| KBID | Tudásbáziscikk-azonosító a Windows-frissítéshez. |
| ManagementGroupName | Az Operations Manager felügyeleti csoport vagy a Log Analytics-munkaterület neve. |
| UpdateID | A szoftverfrissítés egyedi azonosítója |
| RevisionNumber | A frissítés adott változatának verziószáma. |
| Optional | *Igaz* vagy *Hamis* | 
| RebootBehavior | A frissítés telepítése/eltávolítása utáni újraindítási viselkedés. |
| _ResourceId | Annak az erőforrásnak az egyedi azonosítója, amelyhez a bejegyzés társítva van. |
| Típus | *Frissítés* |
| VMUUID | A virtuális gép egyedi azonosítója |
| MG | A felügyeleti csoport vagy a Log Analytics-munkaterület egyedi azonosítója | 
| TenantId | Az Azure Active Directory szervezetpéldányát jelölő egyedi azonosító. | 
| SourceSystem | *OperationsManager* | 
| TimeGenerated | A rekord létrehozásának dátuma és időpontja. | 
| SourceComputerId | A forrásszámítógépet jelölő egyedi azonosító | 
| Cím | A frissítés címe. |
| Közzétett dátum (UTC) | A frissítés letöltésének és telepítésének dátuma a Windows Update szolgáltatásból.  |
| UpdateState | A frissítés aktuális állapota. | 
| Product | Azok a termékek, amelyekre a frissítés vonatkozik. |
| SubscriptionId | Az Azure-előfizetés egyedi azonosítója. | 
| ResourceGroup | Annak az erőforráscsoportnak a neve, amelynek az erőforrás tagja. | 
| ResourceProvider | Megadja az erőforrás-szolgáltatót. | 
| Erőforrás | Az erőforrás neve. | 
| ResourceType | Az erőforrástípus neve. | 

### <a name="update-agent"></a>Ügynök frissítése

Létrejön egy rekord, amelynek típusa `UpdateAgent` létrejön, és amely a frissítési ügynök adatait tartalmazza a számítógépen. Ezek a rekordok a következő táblázatban található tulajdonságokkal rendelkeznek:

| Tulajdonság | Leírás | 
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | | 
| Automatikusfrissítés engedélyezve | | 
| Computer | A jelentéskészítő gép teljesen minősített tartományneve. |
| DaySinceLastUpdateBucket között | | 
| ManagementGroupName | Az Operations Manager felügyeleti csoport vagy a Log Analytics-munkaterület neve. |
| OSVersion | Az operációs rendszer verziója. |
| Kiszolgáló | |
| SourceHealthServiceId azonosító | A Log Analytics Windows-ügynökazonosítóját jelölő egyedi azonosító |
| SourceSystem | *OperationsManager* | 
| TenantId | Az Azure Active Directory szervezetpéldányát jelölő egyedi azonosító. |
| TimeGenerated | A rekord létrehozásának dátuma és időpontja. |
| Típus | *Frissítés* | 
| WindowsUpdateAgentVersion | A Windows Update ügynök verziója. |
| WSUSServer | Hibák megjelenítése, ha a Windows Update ügynöknek problémája van a hibaelhárításban. |

### <a name="update-deployment-status"></a>Központi telepítés állapotának frissítése 

Létrejön egy rekord, amely nek `UpdateRunProgress` típusa van, amely biztosítja az ütemezett központi telepítés gépenkénti központi telepítési állapotát. Ezek a rekordok a következő táblázatban található tulajdonságokkal rendelkeznek:

| Tulajdonság | Leírás | 
|----------|-------------|
| Computer | A jelentéskészítő gép teljesen minősített tartományneve. |
| ComputerEnvironment (Számítógépkörnyezet) | *Azure* *vagy nem Azure*. | 
| CorrelationId | A frissítés runrunjának egyedi azonosítója. |
| EndTime | A szinkronizálási folyamat lesiszélének időpontja. | 
| Hibaeredmény | A frissítés telepítésének sikertelenétől generált Windows Update-hibakód. | 
| InstallationStatus (Telepítési állapot) | Az ügyfélszámítógépen lévő frissítés lehetséges telepítési állapotai,<br> *NotStarted* - a feladat még nem aktiválódott.<br> *FailedToStart* - nem lehet elindítani a feladatot a gépen.<br> *Nem sikerült* - a feladat elindult, de kivétellel nem sikerült.<br> *InProgress* - folyamatban lévő feladat.<br> *MaintenanceWindowExceeded* – ha a végrehajtás még hátra van, de a karbantartási időszak intervalluma elérte.<br> *Sikeres -* a feladat sikerült.<br> *InstallFailed* - a frissítés telepítése nem sikerült.<br> *Nem foglalt*<br> *Kizárt* |
| KBID | Tudásbáziscikk-azonosító a Windows-frissítéshez. | 
| ManagementGroupName | Az Operations Manager felügyeleti csoport vagy a Log Analytics-munkaterület neve. |
| OSType | A *Windows* *vagy*Linux operációs rendszer típusát adja meg. | 
| Product | Azok a termékek, amelyekre a frissítés vonatkozik. |
| Erőforrás | Az erőforrás neve. | 
| ResourceId | Annak az erőforrásnak az egyedi azonosítója, amelyhez a bejegyzés társítva van. |
| ResourceProvider | Megadja az erőforrás-szolgáltatót. | 
| ResourceType | Az erőforrástípus neve. | 
| SourceComputerId | A forrásszámítógépet jelölő egyedi azonosító | 
| SourceSystem | *OperationsManager* |
| StartTime | A frissítés telepítésének ütemezése. |
| SubscriptionId | Az Azure-előfizetés egyedi azonosítója. | 
| SucceededOnRetry | Megmutatja, hogy a frissítés végrehajtása az első kísérletkor mikor sikerült, és az aktuális művelet újrapróbálkozási kísérlet. |
| TimeGenerated | A rekord létrehozásának dátuma és időpontja. |
| Cím | A frissítés címe. |
| Típus | *UpdateRunProgress* |
| UpdateId azonosító | A szoftverfrissítés egyedi azonosítója |
| VMUUID | A virtuális gép egyedi azonosítója |
| _ResourceId | Annak az erőforrásnak az egyedi azonosítója, amelyhez a bejegyzés társítva van. |

### <a name="update-summary"></a>Összegzés frissítése 

A rendszer olyan `UpdateSummary` típusú rekordot hoz létre, amely gépenként biztosítja a frissítés összegzését. Ezek a rekordok a következő táblázatban található tulajdonságokkal rendelkeznek:

| Tulajdonság | Leírás | 
|----------|-------------|
| Computer | A jelentéskészítő gép teljesen minősített tartományneve. |
| ComputerEnvironment (Számítógépkörnyezet) | *Azure* *vagy nem Azure*. | 
| CriticalUpdatesMissing | A hiányzó kritikus frissítések száma, amelyek érvényesek. | 
| ManagementGroupName | Az Operations Manager felügyeleti csoport vagy a Log Analytics-munkaterület neve. |
| NETRuntimeVersion | A Windows rendszerű számítógépen telepített . |
| OldestMissingSecurityUpdateBucket | Az értékek a következők:<br> *Legutóbbi,* ha az érték 30 napnál rövidebb<br> *30 nappal ezelőtt*<br> *60 nappal ezelőtt*<br> *90 nappal ezelőtt*<br> *120 nappal ezelőtt*<br> *150 nappal ezelőtt*<br> *180 nappal ezelőtt*<br> *Akkor a dl,* ha az érték 180 napnál nagyobb | 
| OldestMissingSecurityUpdateInDays | A legrégibb, nem telepített frissítéshez szükséges napok száma összesen. |
| OsVersion | Az operációs rendszer verziója. |
| OtherUpdatesMissing | Hiányzó észlelt frissítések száma. |
| Erőforrás |  Az erőforrás neve. | 
| ResourceGroup | Annak az erőforráscsoportnak a neve, amelynek az erőforrás tagja. |
| ResourceId | Annak az erőforrásnak az egyedi azonosítója, amelyhez a bejegyzés társítva van. |
| ResourceProvider | Megadja az erőforrás-szolgáltatót. |
| ResourceType | Az erőforrástípus neve. |
| Újraindításfüggő | *Igaz* vagy *Hamis*. |
| SecurityUpdatesMissing | A hiányzó biztonsági frissítések száma.| 
| SourceComputerId | A virtuális gép egyedi azonosítója |
| SourceSystem | *OpsManager* | 
| SubscriptionId | Az Azure-előfizetés egyedi azonosítója. |
| TimeGenerated | A rekord létrehozásának dátuma és időpontja. |
| TotalUpdatesMissing | A hiányzó frissítések teljes száma alkalmazható. | 
| Típus | *UpdateSummary* |
| VMUUID | A virtuális gép egyedi azonosítója |
| WindowsUpdateAgentVersion | A Windows Update ügynök verziója. |
| WindowsUpdateSetting | A Windows Update ügynök állapotát jeleníti meg. Lehetséges értékek:<br> *Ütemezett telepítés*<br> *Értesítés a telepítés előtt*<br> A nem sérült WUA-ügynök hibát adott vissza. | 
| WSUSServer | Hibák megjelenítése, ha a Windows Update ügynöknek problémája van a hibaelhárításban. |
| _ResourceId | Annak az erőforrásnak az egyedi azonosítója, amelyhez a bejegyzés társítva van. |

## <a name="sample-queries"></a>Mintalekérdezések

A következő szakaszok mintanapló-lekérdezéseket biztosítanak a frissítéskezeléshez gyűjtött frissítési rekordokhoz.

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Annak ellenőrzése, hogy a nem Azure-alapú gépek be vannak-e táblára

Annak ellenőrzéséhez, hogy a közvetlenül csatlakoztatott gépek kommunikálnak-e az Azure Monitor naplóival, futtassa az alábbi naplókeresések egyikét.

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

Windows rendszerű számítógépen a következő adatokat tekintheti meg az ügynökkapcsolat Azure Monitor-naplókkal való ellenőrzéséhez:

1. A Vezérlőpulton nyissa meg a **Microsoft Monitoring Agent programot.** Az **Azure Log Analytics** lapon az ügynök a következő üzenetet jeleníti meg: **A Microsoft Monitoring Agent sikeresen csatlakozott a Log Analytics hez.**
2. Nyissa meg a Windows eseménynaplóját. Nyissa meg **az Alkalmazás- és szolgáltatásnaplók\Operations Manager mappát,** és keresse meg a 3000-es és 5002-es eseményazonosítójú eseményt a **forrásszolgáltatás-összekötőből.** Ezek az események jelzik, hogy a számítógép regisztrálva van a Log Analytics-munkaterületen, és konfigurációt kap.

Ha az ügynök nem tud kommunikálni az Azure Monitor naplóival, és az ügynök tűzfalon vagy proxykiszolgálón keresztül kommunikál az internettel, ellenőrizze, hogy a tűzfal vagy a proxykiszolgáló megfelelően van-e konfigurálva. A tűzfal vagy a proxykiszolgáló megfelelő konfigurálásáról a [Hálózati konfiguráció a Windows ügynökhöz](../azure-monitor/platform/agent-windows.md) vagy a [Linux-ügynök hálózati konfigurációja](../log-analytics/log-analytics-agent-linux.md)című témakörből olvashat.

> [!NOTE]
> Ha a Linux-rendszerek proxyval vagy Log Analytics átjáróval való kommunikációra vannak konfigurálva, és ön vezeti be ezt a megoldást, frissítse a *proxy.conf* engedélyeket, hogy az omiuser csoport olvasási engedélyt adjon a fájlhoz a következő parancsok használatával:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Az újonnan hozzáadott Linux-ügynökök egy értékelés elvégzése után **frissített** állapotot mutatnak. A folyamat akár hat órát is igénybe vehet.

Annak ellenőrzéséhez, hogy egy Operations Manager-felügyeleti csoport kommunikál-e az Azure Monitor naplóival, olvassa el [az Operations Manager-integráció ellenőrzése az Azure Monitor-naplókkal.](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor)

### <a name="single-azure-vm-assessment-queries-windows"></a>Egyetlen Azure virtuálisgép-felmérési lekérdezések (Windows)

Cserélje le a VMUUID értékét a lekérdező virtuális gép virtuálisgép-GUID azonosítójára. Az Azure Monitor-naplókban a következő lekérdezés futtatásával használandó VMUUID-t találja:`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

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

### <a name="single-azure-vm-assessment-queries-linux"></a>Egyetlen Azure virtuálisgép-felmérési lekérdezések (Linux)

Egyes Linux-disztribúciók esetében az Azure Resource Managertől származó VMUUID-értékkel való [endianness](https://en.wikipedia.org/wiki/Endianness) eltérés van, és az Azure Monitor-naplókban tárolt adatok. A következő lekérdezés ellenőrzi az egyezést az endianness.The following query checks for a match on either endianness. Cserélje le a VMUUID értékeket a GUID big-endian és little-endian formátumára az eredmények megfelelő visszaadásához. Az Azure Monitor-naplókban a következő lekérdezés futtatásával használandó VMUUID-t találja:`Update | where Computer == "<machine name>"
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

### <a name="multi-vm-assessment-queries"></a>Többvirtuális gépre vonatkozó értékelési lekérdezések

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

## <a name="next-steps"></a>További lépések

* A részletes frissítési adatok megtekintéséhez használja a naplókereséseket az [Azure Monitor naplóiban.](../log-analytics/log-analytics-log-searches.md)
* [Riasztások létrehozása](automation-tutorial-update-management.md#configure-alerts) a frissítés telepítési állapotához.
