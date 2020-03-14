---
title: Diagnosztikai naplózás a Azure Analysis Serviceshoz | Microsoft Docs
description: Leírja, hogyan kell beállítani az Azure Resource Diagnostics naplózását a Azure Analysis Services-kiszolgáló figyeléséhez.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0f13f297facedceb50920c0f6afca63fe1df0b48
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266181"
---
# <a name="setup-diagnostic-logging"></a>Diagnosztikai naplózás beállítása

Fontos része annak az Analysis Services megoldással figyel, hogyan a kiszolgálók végzik. Az [Azure-erőforrás-naplók](../azure-monitor/platform/platform-logs-overview.md)segítségével figyelheti és elküldheti a naplókat az [Azure Storage](https://azure.microsoft.com/services/storage/)-ba, továbbíthatja őket az [Azure Event Hubsba](https://azure.microsoft.com/services/event-hubs/), és exportálhatja őket [Azure monitor naplókba](../azure-monitor/azure-monitor-log-hub.md).

![Diagnosztikai naplózás a tárolási, Event Hubs vagy Azure Monitor naplókba](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Mi kerül?

Kiválaszthatja a **motor**, a **szolgáltatás**és a **metrikák** kategóriáit.

### <a name="engine"></a>Adatbázismotor

A **motor** kiválasztásával naplózza az összes [xevent típusú eseményekhez](https://docs.microsoft.com/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Nem lehet kiválasztani az egyes eseményeket. 

|Az XEvent-kategóriák |Esemény neve  |
|---------|---------|
|Biztonsági naplózás    |   Bejelentkezés naplózása      |
|Biztonsági naplózás    |   Kijelentkezés naplózása      |
|Biztonsági naplózás    |   Naplózási kiszolgáló indítása és leállítása      |
|Végrehajtási jelentések     |   Folyamatban van a jelentés kezdete      |
|Végrehajtási jelentések     |   Az állapotjelentés vége      |
|Végrehajtási jelentések     |   Folyamatban van a jelentés aktuális      |
|Lekérdezések     |  Lekérdezés kezdete       |
|Lekérdezések     |   Lekérdezés vége      |
|Parancsok     |  A parancs kezdete       |
|Parancsok     |  A parancs vége       |
|Hibák és figyelmeztetések     |   Hiba      |
|Ismertetők     |   Fedezze fel a vége      |
|Értesítés     |    Értesítés     |
|Munkamenet     |  A munkamenet inicializálása       |
|Zárolások    |  Holtpont       |
|Lekérdezés-feldolgozás     |   VertiPaq SE lekérdezés kezdete      |
|Lekérdezés-feldolgozás     |   VertiPaq SE lekérdezés vége      |
|Lekérdezés-feldolgozás     |   VertiPaq SE lekérdezés gyorsítótár-egyezés      |
|Lekérdezés-feldolgozás     |   Közvetlen lekérdezés kezdete      |
|Lekérdezés-feldolgozás     |  Közvetlen lekérdezés vége       |

### <a name="service"></a>Szolgáltatás

|Művelet neve  |Akkor következik be, amikor  |
|---------|---------|
|ResumeServer     |    A kiszolgáló folytatása     |
|SuspendServer    |   A kiszolgáló szüneteltetésére      |
|DeleteServer     |    Kiszolgáló törlése     |
|RestartServer    |     Felhasználó indítja újra egy kiszolgálót az ssms-t vagy a Powershellen keresztül    |
|GetServerLogFiles    |    Felhasználó exportálja kiszolgálónapló PowerShell-lel     |
|ExportModel     |   Felhasználó exportálja egy modellt a portálon nyissa meg a Visual Studio használatával     |

### <a name="all-metrics"></a>Az összes metrikák

A metrikák kategória ugyanazokat a [kiszolgálói metrikákat](analysis-services-monitor.md#server-metrics) naplózza a AzureMetrics táblába. Ha a lekérdezési [felskálázást](analysis-services-scale-out.md) használja, és el kell különíteni a metrikákat az egyes olvasási replikák számára, használja helyette a AzureDiagnostics táblát, ahol a **OperationName** egyenlő a **LogMetric**.

## <a name="setup-diagnostics-logging"></a>Diagnosztikai naplózás beállítása

### <a name="azure-portal"></a>Azure Portal

1. [Azure Portal](https://portal.azure.com) >-kiszolgáló területen kattintson a bal oldali navigációs sávon a **diagnosztikai naplók** elemre, majd kattintson a **diagnosztika bekapcsolása**elemre.

    ![Kapcsolja be a diagnosztikai naplózás az Azure Cosmos DB az Azure Portalon](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. A **diagnosztikai beállítások**területen a következő beállításokat adhatja meg: 

    * **Név**. Adjon meg egy nevet, a naplókhoz hozhat létre.

    * **Archiválás egy Storage-fiókba**. Használja ezt a beállítást, egy meglévő tárfiókot csatlakozni kell. Lásd: [Storage-fiók létrehozása](../storage/common/storage-create-storage-account.md). Kövesse az utasításokat egy erőforrás-kezelő, általános célú fiók létrehozásához, majd válassza ki a tárfiókját, térjen vissza erre a lapra a portál által. Eltarthat néhány percig, újonnan létrehozott tárfiókok jelennek meg a legördülő menüben.
    * **Adatfolyam küldése az Event hub-** nak. Használja ezt a beállítást, egy meglévő Event Hubs névtér és az eseményközpont csatlakozni kell. További információ: [Event Hubs névtér és az Event hub létrehozása a Azure Portal használatával](../event-hubs/event-hubs-create.md). Ezután térjen vissza erre a lapra a portálra, válassza ki az Eseményközpont-névtér és a házirend nevét.
    * **Küldés Azure monitor (log Analytics munkaterületre)** . Ha ezt a beállítást szeretné használni, használjon egy meglévő munkaterületet, vagy [hozzon létre egy új munkaterület](../azure-monitor/learn/quick-create-workspace.md) -erőforrást a portálon. A naplók megtekintésével kapcsolatos további információkért tekintse meg a jelen cikk [naplók megtekintése log Analytics munkaterületen](#view-logs-in-log-analytics-workspace) című témakörét.

    * **Motor**. Ezzel a lehetőséggel Xevent bejelentkezni. Ha, még archiválás tárfiókba, kiválaszthatja a diagnosztikai naplók megőrzési időtartama. Naplók autodeleted, a megőrzési időszak lejárta után is.
    * **Szolgáltatás**. Ezt a beállítást a szolgáltatási szintű eseményeket. Ha archiválni egy tárfiókot, kiválaszthatja a diagnosztikai naplók megőrzési időtartama. Naplók autodeleted, a megőrzési időszak lejárta után is.
    * **Metrikák**. Válassza ezt a lehetőséget, ha részletes adatokat szeretne tárolni a [mérőszámokban](analysis-services-monitor.md#server-metrics). Ha archiválni egy tárfiókot, kiválaszthatja a diagnosztikai naplók megőrzési időtartama. Naplók autodeleted, a megőrzési időszak lejárta után is.

3. Kattintson a **Save** (Mentés) gombra.

    Ha olyan hibaüzenetet kap, amely szerint a "nem sikerült frissíteni a \<munkaterület neve > a diagnosztikát. Az előfizetés \<előfizetés-azonosító > nincs regisztrálva a Microsoft. bepillantások használatára. " Kövesse a fiók regisztrálásához [Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) útmutatást, majd próbálja megismételni a műveletet.

    Ha meg szeretné változtatni a módját a diagnosztikai naplók menti a bármikor a jövőben, visszatérhet ezt oldal beállításainak módosítására.

### <a name="powershell"></a>PowerShell

Az alábbiakban az alapszintű parancsokat bevezetésként. Ha azt szeretné, hogy a részletes súgó a naplózást, és a egy storage-fiók beállítása a PowerShell-lel, tekintse meg az oktatóanyag a cikk későbbi részében.

Metrikák és naplózás PowerShell-lel diagnosztika engedélyezéséhez használja a következő parancsokat:

- Ahhoz, hogy a diagnosztikai naplókat egy tárfiókban, használja ezt a parancsot:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   A tárfiók azonosítója a storage-fiók erőforrás-azonosító, ahol szeretné küldeni a naplókat.

- Diagnosztikai naplók egy eseményközpontba streamelésének engedélyezéséhez használja ezt a parancsot:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Az Azure Service Bus Szabályazonosító karakterláncnak a következő formátumban:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Ahhoz, hogy a küldő diagnosztikai naplók a Log Analytics-munkaterülethez, használja ezt a parancsot:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- A Log Analytics munkaterület erőforrás-Azonosítóját a következő paranccsal szerezheti be:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Kombinálhatja ezeket a paramétereket, több kimeneti beállítások engedélyezéséhez.

### <a name="rest-api"></a>REST API

Megtudhatja, hogyan [változtathatja meg a diagnosztikai beállításokat a Azure Monitor REST API használatával](https://docs.microsoft.com/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Resource Manager-sablon

Megtudhatja, hogyan [engedélyezheti a diagnosztikai beállításokat erőforrás-létrehozáshoz Resource Manager-sablon használatával](../azure-monitor/platform/diagnostic-settings-template.md). 

## <a name="manage-your-logs"></a>A naplók kezelése

Érhetők el naplók általában a naplózás beállítása néhány órán belül. A tárfiók naplófájljait Önnek kell kezelnie:

* Az Azure szabványos hozzáférés-vezérlési módszereivel korlátozhatja a naplókhoz való hozzáférést, így megvédheti azokat.
* Törölje azokat a naplókat, amelyeket nem kíván megőrizni a tárfiókban.
* Mindenképpen állítsa olyan megőrzési időszakot, így a régi naplókat törli a storage-fiókjában.

## <a name="view-logs-in-log-analytics-workspace"></a>Naplók megtekintése Log Analytics munkaterületen

A metrikák és a kiszolgálói események integrálva vannak a Log Analytics munkaterület-erőforrás Xevent típusú eseményekhez, egymás melletti elemzés céljából. A Log Analytics munkaterület úgy is konfigurálható, hogy más Azure-szolgáltatásokból származó eseményeket fogadjon, és átfogó képet kapjon a diagnosztikai naplózási adatokról az architektúrán belül.

A diagnosztikai adatok megtekintéséhez Log Analytics munkaterületen nyissa meg a **naplók** elemet a bal oldali menüben.

![Keresési beállítások jelentkezzen be az Azure Portalon](./media/analysis-services-logging/aas-logging-open-log-search.png)

A lekérdezés-szerkesztőben bontsa ki a **LogManagement** > **AzureDiagnostics**elemet. AzureDiagnostics motor és a szolgáltatások eseményeinek tartalmazza. Figyelje meg, hogy a rendszer menet közben hozza létre a lekérdezést. A EventClass\_s mező xEvent neveket tartalmaz, amelyek ismerősek lehetnek, ha a helyszíni naplózáshoz Xevent típusú eseményekhez használta. Kattintson a **EventClass\_s** vagy az egyik esemény nevére, és log Analytics a munkaterület folytatja a lekérdezés összeállítását. Mindenképpen mentse a lekérdezések későbbi használatra.

### <a name="example-queries"></a>Példák lekérdezésekre

#### <a name="example-1"></a>1\. példa

A következő lekérdezés a modell-adatbázis és-kiszolgáló minden lekérdezési End/Fresh End eseményének időtartamát adja vissza. Ha a méretezés ki van bontva, az eredmények a replika szerint vannak kiosztva, mivel a replika száma szerepel a ServerName_sban. Az RootActivityId_g csoportosítása csökkenti a Azure Diagnostics REST API lekért sorszámokat, és az [log Analytics díjszabásában](https://dev.loganalytics.io/documentation/Using-the-API/Limits)leírtaknak megfelelően segít a korlátokon belül maradni.

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

#### <a name="example-2"></a>2\. példa

A következő lekérdezés egy kiszolgáló memória-és QPU-felhasználását adja vissza. Ha a méretezés ki van bontva, az eredmények a replika szerint vannak kiosztva, mivel a replika száma szerepel a ServerName_sban.

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

#### <a name="example-3"></a>3\. példa

A következő lekérdezés visszaadja a-kiszolgáló másodpercenkénti olvasási/mp Analysis Services motorjának teljesítményszámlálói értékét.

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

Nincsenek lekérdezésekben használható több száz. A lekérdezésekkel kapcsolatos további tudnivalókért tekintse meg [az Ismerkedés a Azure monitor log lekérdezésekkel](../azure-monitor/log-query/get-started-queries.md)című témakört.


## <a name="turn-on-logging-by-using-powershell"></a>Kapcsolja be a naplózást a PowerShell használatával

Gyors ebben az oktatóanyagban mint az Analysis Services-kiszolgáló hoz létre egy tárfiókot, az ugyanabban az előfizetésben és erőforráscsoportban. Ezután a set-AzDiagnosticSetting használatával bekapcsolhatja a diagnosztikai naplózást, és elküldheti a kimenetet az új Storage-fiókba.

### <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez a következőket kell rendelkeznie:

* Egy meglévő Azure Analysis Services-kiszolgáló. A kiszolgálói erőforrások létrehozásával kapcsolatos utasításokért lásd: [kiszolgáló létrehozása Azure Portalban](analysis-services-create-server.md), vagy [Azure Analysis Services kiszolgáló létrehozása a PowerShell használatával](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>csatlakozhat az előfizetésekhez

Indítson el egy Azure PowerShell-munkamenetet, és jelentkezzen be az Azure-fiókjába az alábbi paranccsal:  

```powershell
Connect-AzAccount
```

Az előugró böngészőablakban adja meg az Azure-fiókja felhasználónevét és jelszavát. Az Azure PowerShell megkeresi az összes olyan előfizetést, amely ehhez a fiókhoz van rendelve, és alapértelmezés szerint kiválasztja az elsőt.

Ha több előfizetése van, előfordulhat, hogy meg kell adnia azt, amelyikkel az Azure Key Vault tárolóját létrehozta. Írja be az alábbi parancsot a fiókhoz tartozó előfizetések megjelenítéséhez:

```powershell
Get-AzSubscription
```

Ezt követően az Azure Analysis Services-fiókkal jelentkezik tartozó előfizetés megadásához írja be:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Ha a fiókjához társított több előfizetéssel rendelkezik, fontos adja meg az előfizetést.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Új tárfiók létrehozása a naplóknak

Használhatja egy meglévő tárfiókot a naplók, feltéve, hogy a kiszolgáló ugyanabban az előfizetésben. Ebben az oktatóanyagban egy új Storage-fiókot hoz létre, amely Analysis Services naplókhoz van hozzárendelve. Az egyszerűvé tétel érdekében a Storage-fiók adatait egy **sa**nevű változóban tárolja.

Is ugyanazt az erőforráscsoportot használjuk az Analysis Services-kiszolgáló tartalmazza. `awsales_resgroup`, `awsaleslogs`és `West Central US` helyettesítő értékei a saját értékekkel:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>A naplók server fiók azonosítása

Állítsa a fiók nevét egy **fiók**nevű változóra, ahol a resourcename a fiók neve.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Naplózás engedélyezése

A naplózás engedélyezéséhez használja a set-AzDiagnosticSetting parancsmagot az új Storage-fiók, a kiszolgálói fiók és a kategória változóinak együttes használatával. Futtassa a következő parancsot, amely az **-enabled** jelzőt **$true**:

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

Ez a kimenet megerősíti, hogy a naplózás mostantól engedélyezve van a kiszolgálón, és az adatok mentése a Storage-fiókba.

Beállíthat adatmegőrzési házirend a naplók, a régebbi naplófájlok automatikusan törlődnek. Például állítsa be az adatmegőrzési szabályzatot a **-RetentionEnabled** jelzővel **$truere**, és állítsa a **-RetentionInDays** paramétert **90**-re. 90 napnál régebbi naplófájlok automatikusan törlődnek.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Következő lépések

További információ az [Azure erőforrás-diagnosztika naplózásáról](../azure-monitor/platform/platform-logs-overview.md).

Lásd: [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) a PowerShell súgójában.
