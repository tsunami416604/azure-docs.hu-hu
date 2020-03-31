---
title: Diagnosztikai naplózás az Azure Analysis Services számára | Microsoft dokumentumok
description: Bemutatja, hogyan kell beállítani az Azure erőforrás-diagnosztikai naplózást az Azure Analysis Services-kiszolgáló figyeléséhez.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0f13f297facedceb50920c0f6afca63fe1df0b48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266181"
---
# <a name="setup-diagnostic-logging"></a>Diagnosztikai naplózás beállítása

Az Analysis Services-megoldások fontos része a kiszolgálók teljesítményének figyelése. Az [Azure-erőforrásnaplók segítségével](../azure-monitor/platform/platform-logs-overview.md)figyelheti és elküldheti a naplókat az [Azure Storage-ba,](https://azure.microsoft.com/services/storage/)streamelheti őket az [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)szolgáltatásba, és exportálhatja őket az [Azure Monitor-naplókba.](../azure-monitor/azure-monitor-log-hub.md)

![Diagnosztikai naplózás a Storage, az Event Hubs vagy az Azure Monitor naplóiba](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Mi kerül naplózásra?

Kiválaszthatja **a Motor**, **Szolgáltatás**és **Metrikák** kategóriákat.

### <a name="engine"></a>Motor

A **Motor** kiválasztása naplózza az összes [xEvents eseményt.](https://docs.microsoft.com/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events) Nem jelölhet ki egyedi eseményeket. 

|XEvent kategóriák |Esemény neve  |
|---------|---------|
|Biztonsági naplózás    |   Naplózás bejelentkezése      |
|Biztonsági naplózás    |   Naplózás kijelentkezés      |
|Biztonsági naplózás    |   A naplózási kiszolgáló elindul és leáll      |
|Előrehaladási jelentések     |   Állapotjelentés kezdete      |
|Előrehaladási jelentések     |   Az állapotjelentés vége      |
|Előrehaladási jelentések     |   Aktuális állapotjelentés      |
|Lekérdezések     |  Lekérdezés kezdete       |
|Lekérdezések     |   Lekérdezés vége      |
|Parancsok     |  Parancs kezdete       |
|Parancsok     |  Parancs vége       |
|Hibák & figyelmeztetések     |   Hiba      |
|Felderítés     |   Fedezet vége      |
|Értesítés     |    Értesítés     |
|Munkamenet     |  A munkamenet inicializálása       |
|Zárolások    |  Holtpont       |
|Lekérdezés feldolgozása     |   VertiPaq SE lekérdezés kezdete      |
|Lekérdezés feldolgozása     |   VertiPaq SE lekérdezés vége      |
|Lekérdezés feldolgozása     |   VertiPaq SE lekérdezésgyorsítótár-egyezés      |
|Lekérdezés feldolgozása     |   Közvetlen lekérdezés kezdete      |
|Lekérdezés feldolgozása     |  Közvetlen lekérdezés vége       |

### <a name="service"></a>Szolgáltatás

|Művelet neve  |Akkor következik be, ha  |
|---------|---------|
|Önéletrajz-kiszolgáló     |    Kiszolgáló folytatása     |
|SuspendServer (Kiszolgáló felfüggesztése)    |   Kiszolgáló szüneteltetése      |
|DeleteServer kiszolgáló     |    Kiszolgáló törlése     |
|ÚjraindításServer    |     A felhasználó ssms-en vagy PowerShellen keresztül újraindítja a kiszolgálót    |
|GetServerLog-fájlok    |    A felhasználó exportálja a kiszolgálói naplót a PowerShellen keresztül     |
|Exportmodell     |   A felhasználó exportálja a modellt a portálon az Open in Visual Studio használatával     |

### <a name="all-metrics"></a>Minden mutató

A Metrikák kategória ugyanazokat a [kiszolgálói metrikákat](analysis-services-monitor.md#server-metrics) naplózza az AzureMetrics táblába. Ha [lekérdezéshorizontális-kiskálázást](analysis-services-scale-out.md) használ, és minden egyes olvasási kópiához külön metriát kell használnia, használja inkább az AzureDiagnostics táblát, ahol az OperationName megegyezik a **LogMetric.If**you're using query scale-out and need to separate metrics for each read replicas, use the AzureDiagnostics table instead, where **OperationName** is equal to LogMetric.

## <a name="setup-diagnostics-logging"></a>Diagnosztikai naplózás beállítása

### <a name="azure-portal"></a>Azure portál

1. Az [Azure Portal](https://portal.azure.com) > kiszolgálóján kattintson a diagnosztikai naplók **elemre** a bal oldali navigációs sávon, majd kattintson a **Diagnosztika bekapcsolása parancsra.**

    ![Diagnosztikai naplózás bekapcsolása az Azure Cosmos DB-hez az Azure Portalon](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. A **diagnosztikai beállítások** területen adja meg a következő beállításokat: 

    * **Név**. Adja meg a létrehozandó naplók nevét.

    * **Archiválás tárfiókba.** A beállítás használatához egy meglévő tárfiókra van szükség a csatlakozáshoz. Lásd: [Tárfiók létrehozása.](../storage/common/storage-create-storage-account.md) Kövesse az utasításokat egy Erőforrás-kezelő, általános célú fiók létrehozásához, majd válassza ki a tárfiókot a portálezen a lapján való visszatéréshez. Néhány percet igénybe vehet, hogy az újonnan létrehozott tárfiók megjelenjen a legördülő menüben.
    * **Streamelés egy eseményközpontba**. A beállítás használatához egy meglévő Event Hub-névtérre és eseményközpontra van szükség a csatlakozáshoz. További információt az [Event Hubs-névtér és eseményközpont létrehozása az Azure Portal használatával](../event-hubs/event-hubs-create.md) című témakörben talál. Ezután térjen vissza erre a lapra a portálon az Event Hub névtér és a házirend nevének kiválasztásához.
    * **Küldés az Azure Monitornak (Log Analytics-munkaterület).** A beállítás használatához használjon egy meglévő munkaterületet, vagy [hozzon létre egy új munkaterületi](../azure-monitor/learn/quick-create-workspace.md) erőforrást a portálon. A naplók megtekintéséről ebben a cikkben a [Naplók megtekintése a Log Analytics-munkaterületen](#view-logs-in-log-analytics-workspace) című témakörben olvashat bővebben.

    * **Motor**. Válassza ezt a lehetőséget az xEvents naplózásához. Ha egy tárfiókba archivál, kiválaszthatja a diagnosztikai naplók megőrzési időszakát. A naplók automatikusan törlődnek a megőrzési időszak lejárta után.
    * **Szolgáltatás**. Ezzel a beállítással naplózva a szolgáltatásszintű eseményeket. Ha tárfiókba archivál, kiválaszthatja a diagnosztikai naplók megőrzési időtartamát. A naplók automatikusan törlődnek a megőrzési időszak lejárta után.
    * **Mérőszámok**. Ezzel a beállítással részletes adatokat tárolhatja a [Metrikák ban.](analysis-services-monitor.md#server-metrics) Ha tárfiókba archivál, kiválaszthatja a diagnosztikai naplók megőrzési időtartamát. A naplók automatikusan törlődnek a megőrzési időszak lejárta után.

3. Kattintson a **Mentés** gombra.

    Ha a következő hibaüzenet jelenik meg: "Nem sikerült frissíteni a munkaterület nevének \<> diagnosztikai adatait. Az \<előfizetés-azonosító> nincs regisztrálva a microsoft.insights használatához." Kövesse az [Azure-diagnosztika hibaelhárítási utasításait](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) a fiók regisztrálásához, majd próbálkozzon újra ezzel az eljárással.

    Ha módosítani szeretné a diagnosztikai naplók mentésének módját a jövőben bármikor, visszatérhet erre a lapra a beállítások módosításához.

### <a name="powershell"></a>PowerShell

Itt vannak az alapvető parancsokat, hogy neked megy. Ha részletes segítséget szeretne a naplózás beállításához egy tárfiókba a PowerShell használatával, tekintse meg az oktatóanyag későbbi ebben a cikkben.

PowerShell használatával a diagnosztikát és a metrikákat az alábbi parancsok segítségével engedélyezheti:

- Az alábbi paranccsal engedélyezheti a diagnosztikai naplók tárfiókban való tárolását:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   A Storage-fiók azonosítója (storage account ID) annak a tárfióknak az erőforrás-azonosítója, amelybe a naplókat szeretné küldeni.

- Az alábbi parancs használatával engedélyezheti a diagnosztikai naplók streamelését egy eseményközpontba:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Az Azure Service Bus szabályazonosítója (rule ID) egy sztring az alábbi formátumban:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Az alábbi paranccsal engedélyezheti a diagnosztikai naplók Log Analytics-munkaterületre való küldését:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- A Log Analytics-munkaterület erőforrás-azonosítóját (ResourceId) az alábbi paranccsal kérheti le:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Ezeket a paramétereket kombinálhatja is, ha több kimeneti eredményt szeretne kapni.

### <a name="rest-api"></a>REST API

Ismerje meg, [hogyan módosíthatja a diagnosztikai beállításokat az Azure Monitor REST API használatával](https://docs.microsoft.com/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Resource Manager-sablon

Ismerje meg, [hogyan engedélyezheti a diagnosztikai beállításokat az erőforrás létrehozásánál a Resource Manager-sablon használatával](../azure-monitor/platform/diagnostic-settings-template.md). 

## <a name="manage-your-logs"></a>Naplók kezelése

A naplók általában a naplózás beállítását követő néhány órán belül érhetők el. A tárfiók naplófájljait Önnek kell kezelnie:

* Az Azure szabványos hozzáférés-vezérlési módszereivel korlátozhatja a naplókhoz való hozzáférést, így megvédheti azokat.
* Törölje azokat a naplókat, amelyeket nem kíván megőrizni a tárfiókban.
* Ügyeljen arra, hogy a megőrzési időszak, így a régi naplók törlődnek a tárfiókból.

## <a name="view-logs-in-log-analytics-workspace"></a>Naplók megtekintése a Log Analytics-munkaterületen

Metrikák és a kiszolgálóesemények integrálva vannak xEvents a Log Analytics munkaterületi erőforrás egymás melletti elemzés. A Log Analytics munkaterület e-alapú események fogadására is konfigurálható más Azure-szolgáltatásokból, amelyek átfogó képet nyújtanak a diagnosztikai naplózási adatokról az architektúrában.

A diagnosztikai adatok megtekintéséhez a Log Analytics munkaterületen nyissa meg a **Naplók a** bal oldali menüből.

![Keresési beállítások naplózása az Azure Portalon](./media/analysis-services-logging/aas-logging-open-log-search.png)

A lekérdezésszerkesztőben bontsa ki a **LogManagement** > **AzureDiagnostics csomópontot.** Az AzureDiagnostics motor- és szolgáltatáseseményeket tartalmaz. Figyelje meg, hogy a lekérdezés menet közben jön létre. Az EventClass\_s mező xEvent neveket tartalmaz, amelyek ismerősnek tűnhetnek, ha az xEvents-et használta a helyszíni naplózáshoz. Kattintson **az EventClass\_s** vagy az eseménynevek egyikére, és a Log Analytics munkaterület folytatja a lekérdezés összeállítását. A lekérdezéseket ajánlatos menteni későbbi használatra.

### <a name="example-queries"></a>Példa lekérdezésekre

#### <a name="example-1"></a>1. példa

A következő lekérdezés a modelladatbázis és -kiszolgáló minden lekérdezésbefejezési/frissítési záróeseményének időtartamát adja vissza. Ha kicsinyítés esetén az eredmények replika szerint oszlanak meg, mert a replikaszám szerepel ServerName_s. A RootActivityId_g szerinti csoportosítás csökkenti az Azure Diagnostics REST API-ból beolvasott sorok számát, és segít a [Log Analytics-díjkorlátokban](https://dev.loganalytics.io/documentation/Using-the-API/Limits)leírt korlátokon belül maradni.

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName" and DatabaseName_s =~ "MyDatabaseName" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs
| order by StartTime_t asc
```

#### <a name="example-2"></a>2. példa

A következő lekérdezés memóriát és QPU-felhasználást ad vissza a kiszolgálóhoz. Ha kicsinyítés esetén az eredmények replika szerint oszlanak meg, mert a replikaszám szerepel ServerName_s.

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where name_s == "memory_metric" or name_s == "qpu_metric"
| project ServerName_s, TimeGenerated, name_s, value_s
| summarize avg(todecimal(value_s)) by ServerName_s, name_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

#### <a name="example-3"></a>3. példa

A következő lekérdezés a Sorok olvasása/mp Analysis Services motor teljesítményszámlálóit adja vissza a kiszolgálóhoz.

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where parse_json(tostring(parse_json(perfobject_s).counters))[0].name == "Rows read/sec" 
| extend Value = tostring(parse_json(tostring(parse_json(perfobject_s).counters))[0].value) 
| project ServerName_s, TimeGenerated, Value
| summarize avg(todecimal(Value)) by ServerName_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

Több száz lekérdezést használhat. A lekérdezésekről az [Azure Monitor naplólekérdezéseinek első lépései](../azure-monitor/log-query/get-started-queries.md).


## <a name="turn-on-logging-by-using-powershell"></a>A naplózás bekapcsolása a PowerShell használatával

Ebben a gyors oktatóanyagban hozzon létre egy tárfiókot ugyanabban az előfizetési és erőforráscsoportban, mint az Analysis Service-kiszolgáló. Ezután a Set-AzDiagnosticSetting segítségével kapcsolja be a diagnosztikai naplózást, és küldje el a kimenetet az új tárfiókba.

### <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag befejezéséhez a következő erőforrásokkal kell rendelkeznie:

* Egy meglévő Azure Analysis Services-kiszolgáló. A kiszolgálói erőforrások létrehozásáról a [Kiszolgáló létrehozása az Azure Portalon](analysis-services-create-server.md)vagy az Azure Analysis [Services-kiszolgáló létrehozása a PowerShell használatával című](analysis-services-create-powershell.md)témakörben talál.

### <a name="aconnect-to-your-subscriptions"></a></a>Csatlakozás az előfizetésekhez

Indítson el egy Azure PowerShell-munkamenetet, és jelentkezzen be az Azure-fiókjába az alábbi paranccsal:  

```powershell
Connect-AzAccount
```

Az előugró böngészőablakban adja meg az Azure-fiókja felhasználónevét és jelszavát. Az Azure PowerShell megkeresi az összes olyan előfizetést, amely ehhez a fiókhoz van rendelve, és alapértelmezés szerint kiválasztja az elsőt.

Ha több előfizetése van, előfordulhat, hogy meg kell adnia azt, amelyikkel az Azure Key Vault tárolóját létrehozta. Írja be az alábbi parancsot a fiókhoz tartozó előfizetések megjelenítéséhez:

```powershell
Get-AzSubscription
```

Ezután adja meg a naplózza a naplózásalatt lévő Azure Analysis Services-fiókhoz társított előfizetést, írja be a következőt:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Ha több előfizetése van társítva a fiókjához, fontos megadni az előfizetést.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Új tárfiók létrehozása a naplóknak

Használhat egy meglévő tárfiókot a naplókhoz, feltéve, hogy ugyanabban az előfizetésben van, mint a kiszolgáló. Ebben az oktatóanyagban hozzon létre egy új tárfiókot az Analysis Services-naplók számára. A könnyebbik egyszerűsítés érdekében a tárfiók adatait egy **sa**nevű változóban tárolja.

Ugyanazt az erőforráscsoportot is használhatja, mint az Analysis Services-kiszolgálót tartalmazó. A `awsales_resgroup`, `awsaleslogs`és `West Central US` a saját értékeivel helyettesíthetőértékek:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>A naplók kiszolgálófiókjának azonosítása

Állítsa be a fiók nevét egy nevű **változóra,** ahol a ResourceName a fiók neve.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Naplózás engedélyezése

A naplózás engedélyezéséhez használja a Set-AzDiagnosticSetting parancsmamot az új tárfiók, a kiszolgálófiók és a kategória változóival együtt. Futtassa a következő parancsot, és az **-Enabled** jelzőt **$true:**

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

Az alábbi példában látható kimenethez hasonló eredményt kell kapnia:

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

Ez a kimenet megerősíti, hogy a naplózás engedélyezve van a kiszolgálón, és adatokat ment a tárfiókba.

A naplók megőrzési házirendje is beállítható, így a régebbi naplók automatikusan törlődnek. Állítsa be például az adatmegőrzési házirendet a **-RetentionEnabled** jelző használatával **$true,** és állítsa **a -RetentionInDays** paramétert **90-re.** A 90 napnál régebbi naplók automatikusan törlődnek.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>További lépések

További információ az [Azure-erőforrások diagnosztikai naplózásáról.](../azure-monitor/platform/platform-logs-overview.md)

Lásd: [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) a PowerShell súgójában.
