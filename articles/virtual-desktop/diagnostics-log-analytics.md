---
title: Windows rendszerű virtuális asztali diagnosztika log Analytics – Azure
description: A log Analytics használata a Windows rendszerű virtuális asztali diagnosztika szolgáltatással.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bd28117350913bc25f5bf7cec08d28683ad9daca
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020064"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Log Analytics használata a diagnosztikai szolgáltatáshoz

>[!IMPORTANT]
>Ez a tartalom a Spring 2020 frissítésre vonatkozik Azure Resource Manager Windows rendszerű virtuális asztali objektumokkal. Ha a Windows rendszerű virtuális 2019 asztalt Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md).
>
> A Windows rendszerű virtuális asztali Spring 2020 frissítése jelenleg nyilvános előzetes verzióban érhető el. Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. 
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Windows rendszerű virtuális asztali szolgáltatások számos más Azure-szolgáltatáshoz hasonló figyelési és riasztási [Azure monitor](../azure-monitor/overview.md) használnak. Ez lehetővé teszi, hogy a rendszergazdák egyetlen felületen azonosítsák a problémákat. A szolgáltatás felhasználói és rendszergazdai műveletekhez is létrehozza a tevékenység naplóit. Minden tevékenység naplója a következő kategóriákba tartozik:  

- Felügyeleti tevékenységek:
    - A Windows rendszerű virtuális asztali objektumok API-kkal vagy PowerShell-lel való módosítására irányuló kísérletek sikerességének nyomon követése. Lehet például, hogy valaki sikeresen létrehoz egy gazdagépet a PowerShell használatával?
- Hírcsatorna 
    - A felhasználók sikeresen előfizethetnek munkaterületekre? 
    - A felhasználók látják az Távoli asztal-ügyfélen közzétett összes erőforrást?
- Kapcsolatok: 
    - Amikor a felhasználók kezdeményezik és befejezik a kapcsolatot a szolgáltatással. 
- Gazdagép regisztrálása: 
    - Sikerült regisztrálni a munkamenet-állomást a szolgáltatásban a csatlakozáskor?
- Hibák 
    - A felhasználók az adott tevékenységekkel kapcsolatos problémákat tapasztalnak? Ez a funkció létrehozhat egy táblázatot, amely nyomon követi a tevékenység adatait mindaddig, amíg az információ csatlakoztatva van a tevékenységekhez.
- Ellenőrzőpontok  
    - Egy elért tevékenység élettartamának meghatározott lépései. Egy munkamenet során például egy felhasználó terheléselosztásra került egy adott gazdagépre, majd a felhasználó egy kapcsolat során jelentkezett be, és így tovább.

Azok a kapcsolatok, amelyek nem érik el a Windows virtuális asztalt, nem jelennek meg a diagnosztikai eredményekben, mert maga a diagnosztikai szerepkör-szolgáltatás a Windows virtuális asztal része. A Windows rendszerű virtuális asztali kapcsolattal kapcsolatos problémák akkor fordulnak elő, ha a felhasználó hálózati kapcsolati problémákba ütközik.

Azure Monitor lehetővé teszi a Windows rendszerű virtuális asztali adatelemzést és a virtuális gép (VM) teljesítményszámlálók áttekintését, mindezt egyetlen eszközön belül. Ebből a cikkből megtudhatja, hogyan engedélyezheti a diagnosztika eszközt a Windows rendszerű virtuális asztali környezet számára. 

>[!NOTE] 
>Az Azure-beli virtuális gépek monitorozásával kapcsolatos további információkért lásd: az [Azure Virtual Machines figyelése Azure monitorokkal](../azure-monitor/insights/monitor-vm-azure.md). Emellett [tekintse át a teljesítményszámláló küszöbértékeit](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds) , hogy jobban megértse a felhasználói élményt a munkamenet-gazdagépen.

## <a name="before-you-get-started"></a>A kezdés előtt

A Log Analytics használata előtt létre kell hoznia egy munkaterületet. Ehhez kövesse az alábbi két cikk utasításait:

- Ha inkább a Azure Portal használatát kívánja használni, tekintse meg [a log Analytics munkaterület létrehozása a Azure Portalban](../azure-monitor/learn/quick-create-workspace.md)című témakört.
- Ha inkább a PowerShellt szeretné megtekinteni, olvassa el [a log Analytics munkaterület létrehozása a PowerShell használatával](../azure-monitor/learn/quick-create-workspace-posh.md)című témakört.

Miután létrehozta a munkaterületet, kövesse a [Windows rendszerű számítógépek Összekapcsolásának Azure monitor](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key) a következő információk beszerzéséhez című témakör utasításait:

- A munkaterület azonosítója
- A munkaterület elsődleges kulcsa

Ezt az információt később a telepítési folyamat során kell megadnia.

Győződjön meg arról, hogy áttekinti az engedélyek kezelését Azure Monitor, hogy engedélyezze az adathozzáférést azok számára, akik figyelik és karbantartják a Windows rendszerű virtuális asztali környezetet. További információ: Ismerkedés [a szerepkörökkel, az engedélyekkel és a biztonsággal a Azure monitor](../azure-monitor/platform/roles-permissions-security.md). 

## <a name="push-diagnostics-data-to-your-workspace"></a>Diagnosztikai adatai leküldése a munkaterületre

A Windows rendszerű virtuális asztali objektumai diagnosztikai adatait leküldheti a munkaterülethez tartozó Log Analyticsba. Ezt a funkciót azonnal beállíthatja az objektumok első létrehozásakor.

Log Analytics beállítása egy új objektumhoz:

1. Jelentkezzen be a Azure Portalba, és lépjen a **Windows rendszerű virtuális asztalra**. 

2. Navigáljon az objektumra (például egy olyan gazdagépre, alkalmazás-csoportra vagy munkaterületre), amelynek a naplóit és eseményeit rögzíteni kívánja. 

3. A képernyő bal oldalán található menüben válassza a **diagnosztikai beállítások** lehetőséget. 

4. Válassza a **diagnosztikai beállítás hozzáadása** lehetőséget a képernyő jobb oldalán megjelenő menüben. 
   
    A diagnosztikai beállítások lapon megjelenő beállítások attól függően változnak, hogy milyen típusú objektumot szerkeszt.

    Ha például egy alkalmazáscsoport engedélyezi a diagnosztikát, az ellenőrzőpontok, a hibák és a felügyelet konfigurálásának lehetőségeit fogja látni. A munkaterületek esetében ezek a kategóriák olyan hírcsatornát állítanak be, amely nyomon követheti, hogy a felhasználók Mikor fizetnek az alkalmazások listájára. A diagnosztikai beállításokkal kapcsolatos további tudnivalókért lásd: [diagnosztikai beállítás létrehozása az erőforrás-naplók és-metrikák gyűjtéséhez az Azure-ban](../azure-monitor/platform/diagnostic-settings.md). 

     >[!IMPORTANT] 
     >Ne feledje, hogy minden egyes figyelni kívánt Azure Resource Manager-objektum esetében engedélyezze a diagnosztika engedélyezését. Az adatműveletek a diagnosztika engedélyezése után lesznek elérhetők a tevékenységekhez. Az első beállítás után néhány órát is igénybe vehet.  

5. Adja meg a beállítások konfigurációjának nevét, majd kattintson a **küldés log Analytics**lehetőségre. A használt névnek nem tartalmazhat szóközt, és meg kell felelnie az [Azure elnevezési konvencióinak](../azure-resource-manager/management/resource-name-rules.md). A naplók részeként kiválaszthatja a Log Analyticsba felvenni kívánt beállításokat, például az ellenőrzőpontot, a hibát, a felügyeletet és így tovább.

6. Kattintson a **Mentés** gombra.

>[!NOTE]
>Log Analytics lehetővé teszi az adatok adatfolyamként való továbbítását [Event Hubs](../event-hubs/event-hubs-about.md) vagy archiválását egy Storage-fiókba. A szolgáltatással kapcsolatos további tudnivalókért tekintse meg az [Azure monitoring-adatok továbbítása az Event hub](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) -ba és az Azure-beli [erőforrás-naplók archiválása a Storage-fiókba](../azure-monitor/platform/resource-logs-collect-storage.md)című 

## <a name="how-to-access-log-analytics"></a>Hozzáférés Log Analytics

Log Analytics munkaterületeket a Azure Portal vagy a Azure Monitorban érheti el.

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>Log Analytics elérése Log Analytics munkaterületen

1. Jelentkezzen be az Azure portálra.

2. **Log Analytics munkaterület**keresése. 

3. A szolgáltatások területen válassza a **log Analytics munkaterületek**lehetőséget. 
   
4. A listából válassza ki azt a munkaterületet, amelyet a Windows rendszerű virtuális asztali objektumhoz konfigurált.

5. A munkaterületen kattintson a **naplók**elemre. A menüparancsokat kiszűrheti a **keresési** függvénnyel. 

### <a name="access-log-analytics-on-azure-monitor"></a>Hozzáférési Log Analytics Azure Monitor

1. Bejelentkezés az Azure Portalra

2. Keresse meg és válassza a **figyelő**elemet. 

3. Válassza a **naplók**lehetőséget.

4. A lekérdezés hatókörének beállításához kövesse a naplózás lapon megjelenő utasításokat.  

5. Készen áll a diagnosztika lekérdezésére. Az összes diagnosztikai tábla "WVD" előtaggal rendelkezik.

## <a name="cadence-for-sending-diagnostic-events"></a>A diagnosztikai események küldésének ritmusa

Ha elkészült, a rendszer a diagnosztikai eseményeket a Log Analytics elküldi.

Log Analytics csak a közbenső állapotú jelentések a kapcsolódási tevékenységekhez:

- Elindítva: amikor egy felhasználó kiválaszt és csatlakoztat egy alkalmazást vagy egy asztalt az Távoli asztal-ügyfélben.
- Csatlakoztatva: Ha a felhasználó sikeresen csatlakozik ahhoz a virtuális géphez, amelyen az alkalmazást vagy az asztalt üzemelteti.
- Befejezett: Ha a felhasználó vagy a kiszolgáló leválasztja azt a munkamenetet, amelyen a tevékenység lezajlott.

## <a name="example-queries"></a>Példák lekérdezésekre

A következő példák azt mutatják be, hogy a diagnosztikai szolgáltatás hogyan állít elő jelentést a rendszer leggyakoribb tevékenységeihez.

A felhasználók által készített kapcsolatok listájának lekéréséhez futtassa a következő parancsmagot:

```kusto
WVDConnections 
| project-away TenantId,SourceSystem 
| summarize arg_max(TimeGenerated, *), StartTime =  min(iff(State== 'Started', TimeGenerated , datetime(null) )), ConnectTime = min(iff(State== 'Connected', TimeGenerated , datetime(null) ))   by CorrelationId 
| join kind=leftouter ( 
    WVDErrors 
    |summarize Errors=makelist(pack('Code', Code, 'CodeSymbolic', CodeSymbolic, 'Time', TimeGenerated, 'Message', Message ,'ServiceError', ServiceError, 'Source', Source)) by CorrelationId 
    ) on CorrelationId     
| join kind=leftouter ( 
   WVDCheckpoints 
   | summarize Checkpoints=makelist(pack('Time', TimeGenerated, 'Name', Name, 'Parameters', Parameters, 'Source', Source)) by CorrelationId 
   | mv-apply Checkpoints on 
    ( 
        order by todatetime(Checkpoints['Time']) asc 
        | summarize Checkpoints=makelist(Checkpoints) 
    ) 
   ) on CorrelationId 
| project-away CorrelationId1, CorrelationId2 
| order by  TimeGenerated desc 
```

A felhasználók hírcsatorna-tevékenységének megtekintése:

```kusto
WVDFeeds  
| project-away TenantId,SourceSystem  
| join kind=leftouter (  
    WVDErrors  
    |summarize Errors=makelist(pack('Code', Code, 'CodeSymbolic', CodeSymbolic, 'Time', TimeGenerated, 'Message', Message ,'ServiceError', ServiceError, 'Source', Source)) by CorrelationId  
    ) on CorrelationId      
| join kind=leftouter (  
   WVDCheckpoints  
   | summarize Checkpoints=makelist(pack('Time', TimeGenerated, 'Name', Name, 'Parameters', Parameters, 'Source', Source)) by CorrelationId  
   | mv-apply Checkpoints on  
    (  
        order by todatetime(Checkpoints['Time']) asc  
        | summarize Checkpoints=makelist(Checkpoints)  
    )  
   ) on CorrelationId  
| project-away CorrelationId1, CorrelationId2  
| order by  TimeGenerated desc 
```

Egyetlen felhasználóhoz tartozó összes kapcsolat megkeresése: 

```kusto
WVDConnections
|where UserName == "userupn" 
|take 100 
|sort by TimeGenerated asc, CorrelationId 
```
 

A naponta csatlakoztatott felhasználók számának megállapítása:

```kusto
WVDConnections 
|where UserName == "userupn" 
|take 100 
|sort by TimeGenerated asc, CorrelationId 
|summarize dcount(CorrelationId) by bin(TimeGenerated, 1d) 
```
 

Munkamenet időtartamának megkeresése felhasználónként:

```kusto
let Events = WVDConnections | where UserName == "userupn" ; 
Events 
| where State == "Connected" 
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated 
| join (Events 
| where State == "Completed" 
| project EndTime=TimeGenerated, CorrelationId) 
on CorrelationId 
| project Duration = EndTime - StartTime, ResourceAlias 
| sort by Duration asc 
```

Adott felhasználó hibáinak megkeresése:

```kusto
WVDErrors
| where UserName == "userupn" 
|take 100
```

Annak megállapítása, hogy egy adott hiba történt-e:

```kusto
WVDErrors 
| where CodeSymbolic =="ErrorSymbolicCode" 
| summarize count(UserName) by CodeSymbolic 
```

Egy hiba előfordulásának megkeresése az összes felhasználónál:

```kusto
WVDErrors 
| where ServiceError =="false" 
| summarize usercount = count(UserName) by CodeSymbolic 
| sort by usercount desc
| render barchart 
```

>[!NOTE]
>A legfontosabb hibaelhárítási táblázat a WVDErrors. Ezzel a lekérdezéssel megtudhatja, hogy milyen problémák merülhetnek fel a felhasználói tevékenységek, például a kapcsolatok vagy a hírcsatornák esetében, amikor egy felhasználó előfizet az alkalmazások vagy az asztali számítógépek listájára. A tábla megjeleníti a felügyeleti hibákat, valamint az állomások regisztrálásával kapcsolatos problémákat.
>
>Ha a nyilvános előzetes verzióban segítségre van szüksége a probléma megoldásához, győződjön meg arról, hogy a CorrelationID a hibaüzenetet adja meg. Győződjön meg arról is, hogy a szolgáltatási hiba értéke mindig azt mondja, ServiceError = "false". A "hamis" érték azt jelenti, hogy a probléma megoldásához a végén egy rendszergazdai feladat is feloldható. Ha a ServiceError = "true" (igaz) értéket adja meg, a problémát a Microsoftnak kell megadnia.

## <a name="next-steps"></a>További lépések 

A diagnosztikai szolgáltatás által azonosítható gyakori hibák áttekintése: a [problémák azonosítása és diagnosztizálása](diagnostics-role-service.md#common-error-scenarios).
