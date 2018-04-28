---
title: Az Azure Analysis Services Diganostic naplózási |} Microsoft Docs
description: További tudnivalók az Azure Analysis Services diagnosztikai naplózásának beállítása.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 396ee2fc132abb5fbfd29bf5fffe9b1388b9a964
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="setup-diagnostic-logging"></a>Diagnosztikai naplózás beállítása

Bármely Analysis Services megoldás egyik fontos része az figyeli, hogyan működnek a kiszolgálók. A [Azure-erőforrás diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md), figyelheti és elküldheti a naplófájlokat ide [Azure Storage](https://azure.microsoft.com/services/storage/), adatfolyamként küldje el azokat [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), és exportálja azokat a [napló Elemzés](https://azure.microsoft.com/services/log-analytics/), a szolgáltatás a [Azure](https://www.microsoft.com/cloud-platform/operations-management-suite). 

![Diagnosztikai naplózás tárolási, az Event Hubs vagy Naplóelemzési](./media/analysis-services-logging/aas-logging-overview.png)


## <a name="whats-logged"></a>Mi kerül?

Kiválaszthatja **motor**, **szolgáltatás**, és **metrikák** kategóriák.

### <a name="engine"></a>Motor

Kiválasztása **motor** naplózza az összes [Xevent](https://docs.microsoft.com/sql/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Nem választhat ki egyéni események. 

|Az XEvent kategóriák |esemény neve  |
|---------|---------|
|Biztonsági naplózás    |   Naplózási bejelentkezés      |
|Biztonsági naplózás    |   Kijelentkezési naplózása      |
|Biztonsági naplózás    |   Naplózási kiszolgáló indítása és leállítása      |
|Folyamatban lévő jelentések     |   Folyamatban van a jelentés kezdő      |
|Folyamatban lévő jelentések     |   Folyamatban van a jelentés záró      |
|Folyamatban lévő jelentések     |   Folyamatban van a jelentés aktuális      |
|Lekérdezések     |  Lekérdezés kezdete       |
|Lekérdezések     |   Lekérdezés vége      |
|Parancsok     |  A parancs kezdete       |
|Parancsok     |  A parancs vége       |
|Hibák és figyelmeztetések     |   Hiba      |
|Ismertetők     |   Záró felderítése      |
|Értesítés     |    Értesítés     |
|Munkamenet     |  Munkamenet inicializálása       |
|Zárolások    |  Holtpont       |
|A lekérdezés feldolgozása     |   A VertiPaq SE lekérdezés kezdete      |
|A lekérdezés feldolgozása     |   A VertiPaq SE lekérdezés vége      |
|A lekérdezés feldolgozása     |   A VertiPaq SE lekérdezés gyorsítótár-egyezés      |
|A lekérdezés feldolgozása     |   A közvetlen lekérdezési kezdete      |
|A lekérdezés feldolgozása     |  A közvetlen lekérdezési vége       |

### <a name="service"></a>Szolgáltatás

|Művelet neve  |Akkor következik be, amikor  |
|---------|---------|
|CreateGateway     |   Felhasználó konfigurál egy átjáró kiszolgálón      |
|ResumeServer     |    A kiszolgáló folytatása     |
|SuspendServer    |   A kiszolgáló szüneteltetésére      |
|DeleteServer     |    Kiszolgáló törlése     |
|RestartServer    |     Felhasználó újraindítja a kiszolgálót SSMS vagy a Powershellen keresztül    |
|GetServerLogFiles    |    Felhasználó exportálja a kiszolgáló naplóját a PowerShell segítségével     |
|ExportModel     |   Felhasználó exportálja a portálon modell nyissa meg a Visual Studio használatával     |

### <a name="all-metrics"></a>Minden metrikák

A metrika-kategória naplózza az azonos [kiszolgálói metrikák](analysis-services-monitor.md#server-metrics) metrikák jelennek meg.

## <a name="setup-diagnostics-logging"></a>Diagnosztikai naplózás beállítása

### <a name="azure-portal"></a>Azure Portal

1. A [Azure-portálon](https://portal.azure.com) > kiszolgáló, kattintson a **diagnosztikai naplók** a bal oldali navigációs, majd **a diagnosztika bekapcsolásához**.

    ![Az Azure-portálon az Azure Cosmos DB diagnosztikai naplózás bekapcsolása](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. A **diagnosztikai beállítások**, adja meg a következő beállításokat: 

    * **Név**. Adjon meg egy nevet, a naplók létrehozásához.

    * **Archív tárfiókba**. Használja ezt a beállítást, meglévő tárfiókot csatlakozni kell. Lásd: [hozzon létre egy tárfiókot](../storage/common/storage-create-storage-account.md). Kövesse az utasításokat egy erőforrás-kezelő, általános célú fiók létrehozásához, majd válassza ki a tárfiók vissza ezt a lapot a portálon. Az újonnan létrehozott tárfiók megjelenik a legördülő menü néhány percig is eltarthat.
    * **Az eseményközpontok felé adatfolyam**. Használja ezt a beállítást, egy meglévő Eseményközpont névtér és esemény-központot csatlakozni kell. További tudnivalókért lásd: [hozzon létre egy Event Hubs névtér és egy eseményközpontot, az Azure portál használatával](../event-hubs/event-hubs-create.md). Térjen vissza erre az oldalra a portálon, és válassza ki az Event Hubs-névteret és a házirend nevét.
    * **Naplóelemzési küldése**. Használja ezt a beállítást, vagy használjon meglévő munkaterülethez vagy hozzon létre egy új Naplóelemzési munkaterület lépéseit követve [hozzon létre egy új munkaterületet](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) a portálon. A naplók megtekintése a Naplóelemzési további információkért lásd: [Naplóelemzési a naplók megtekintése](#view-in-loganalytics).

    * **Motor**. Ezt a beállítást Xevent bejelentkezni. Ha egy tárfiókkal van archiválás, válassza a diagnosztikai naplók megőrzési időtartama. Nincsenek autodeleted naplói a megőrzési időszak lejárta után.
    * **Szolgáltatás**. Ezt a beállítást a szolgáltatási szint események naplózása. Ha archiválni egy tárfiókot, válassza a diagnosztikai naplók megőrzési időtartama. Nincsenek autodeleted naplói a megőrzési időszak lejárta után.
    * **Metrikák**. Válassza ezt a beállítást, a részletes adatok tárolására [metrikák](analysis-services-monitor.md#server-metrics). Ha archiválni egy tárfiókot, válassza a diagnosztikai naplók megőrzési időtartama. Nincsenek autodeleted naplói a megőrzési időszak lejárta után.

3. Kattintson a **Save** (Mentés) gombra.

    Ha hibaüzenet jelenik meg, amely szerint "nem sikerült frissíteni a diagnosztikai \<munkaterület neve >. Az előfizetés \<előfizetés-azonosító > microsoft.insights használandó nincs regisztrálva. " Kövesse a [hibaelhárítása Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) a fiók regisztrálására vonatkozó utasításokat ismételje meg ezt az eljárást.

    Ha szeretné módosítani a diagnosztikai naplók mentési módjának bármikor a jövőben, ezen a lapon a következő beállításokat módosíthatja térhet vissza.

### <a name="powershell"></a>PowerShell

Az alábbiakban az alapvető parancsok az első állapotra vált. Ha azt szeretné, hogy a PowerShell használatával történő tárfiókot naplózás beállításával kapcsolatos részletes súgó, tekintse meg az a cikk későbbi részében.

Metrikák és a PowerShell használatával naplózás diagnosztika engedélyezéséhez használja a következő parancsokat:

- Ahhoz, hogy a tárfiók-diagnosztikai naplók tárolására, az alábbi parancsot használja:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   A tárfiók azonosítója az erőforrás-azonosítója a tárfiók, ahol szeretné elküldeni a naplókat.

- Adatfolyamként való küldése a diagnosztikai naplók eseményközpontokba való engedélyezéséhez az alábbi parancsot használja:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Az Azure Service Bus Szabályazonosító egy ilyen formátumú karakterláncot:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Ahhoz, hogy a küldő diagnosztikai naplókat a Naplóelemzési munkaterület, az alábbi parancsot használja:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Az erőforrás-azonosítója a Naplóelemzési munkaterület a következő paranccsal szerezheti be:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Ezek a paraméterek ahhoz, hogy több kimenet beállításai kombinálhatja.

### <a name="rest-api"></a>REST API

Megtudhatja, hogyan [diagnosztikai beállítások módosítása a Azure REST API használatával](https://msdn.microsoft.com/library/azure/dn931931.aspx). 

### <a name="resource-manager-template"></a>Resource Manager-sablon

Megtudhatja, hogyan [engedélyezze a diagnosztikai beállításokat a erőforrás létrehozásakor a Resource Manager-sablon](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="manage-your-logs"></a>A naplók kezelése

Érhetők el naplók általában naplózás beállításához néhány órán belül. A tárfiók naplófájljait Önnek kell kezelnie:

* Az Azure szabványos hozzáférés-vezérlési módszereivel korlátozhatja a naplókhoz való hozzáférést, így megvédheti azokat.
* Törölje azokat a naplókat, amelyeket nem kíván megőrizni a tárfiókban.
* Győződjön meg arról, a megőrzési idő beállítása, a régi naplókat a tárfiók törlődik.

## <a name="view-logs-in-log-analytics"></a>A Naplóelemzési naplók megtekintése

Xevent típusú eseményekhez a Log Analyticshez egymás melletti elemzéshez integrált metrikák és a kiszolgáló események. A Naplóelemzési is beállítható úgy, hogy más Azure-szolgáltatások diagnosztikai naplózási adatok átfogó képet biztosít az architektúrák közötti eseményeket fogadni.

Naplóelemzési a diagnosztikai adatok megtekintéséhez nyissa meg a napló lapon kattintson a bal oldali vagy felügyelet alább látható módon.

![Keresési beállítások jelentkezzen be az Azure-portálon](./media/analysis-services-logging/aas-logging-open-log-search.png)

Most, hogy az adatok gyűjtésének engedélyezése **naplófájl-keresési**, kattintson a **összegyűjtött adatok**.

A **típus**, kattintson a **AzureDiagnostics**, és kattintson a **alkalmaz**. AzureDiagnostics-motor és a szolgáltatás eseményeket tartalmazza. Figyelje meg, a Log Analytics-lekérdezés létrehozása a azonnali. A EventClass\_s mező tűnhet, hogy ismeri, ha a helyszíni naplózás xevent típusú eseményekhez használt xEvent nevét tartalmazza.

Kattintson a **EventClass\_s** vagy az esemény nevét és a Naplóelemzési egyik továbbra is hozhat létre lekérdezést. Győződjön meg arról, a lekérdezések későbbi használatra menti.

Győződjön meg arról, hogy egy webhely nyújt bővített lekérdezés, dashboarding és riasztási képességek gyűjtött adatokat a Naplóelemzési.

### <a name="queries"></a>Lekérdezések

Nincsenek több száz lekérdezéseket is használhatja. Az alábbiakban néhány az első lépésekhez.
Az új naplófájl-keresési lekérdezés nyelv használatával kapcsolatos további tudnivalókért lásd: [ismertetése napló keres a Naplóelemzési](../log-analytics/log-analytics-log-search-new.md). 

* Lekérdezés vissza lekérdezések Azure Analysis Services, amelyek több mint öt perc (300 000 milliszekundum) végrehajtásához szükség volt elküldve.

    ```
    search * | where ( Type == "AzureDiagnostics" ) | where ( EventClass_s == "QUERY_END" ) | where toint(Duration_s) > 300000
    ```

* Replikák kibővítési azonosításához.

    ```
    search * | summarize count() by ServerName_s
    ```
    Amikor kibővítési lehetőséget használ, csak olvasható replika azonosíthatja, mert a kiszolgálónév\_s mezőértékek rendelkezik a replika példányszámának a névhez. Az erőforrás mezőben az Azure-erőforrás neve, amely megfelel a kiszolgáló nevét, a felhasználók számára. A IsQueryScaleoutReadonlyInstance_s mező értéke igaz a replikákat.



> [!TIP]
> Rendelkezik egy megosztani kívánt kiváló Naplóelemzési lekérdezést? Ha egy GitHub-fiók, hozzáadhatja a cikkhez. Kattintson az imént **szerkesztése** , a jobb felső részén ezen a lapon.


## <a name="tutorial---turn-on-logging-by-using-powershell"></a>Az oktatóanyag – a PowerShell használatával naplózás bekapcsolása
Gyors ebben az oktatóanyagban mint az Analysis Services-kiszolgálót hoz létre a tárfiók ugyanabban az előfizetésben és erőforráscsoportban. Ezt követően az Set-azurermdiagnosticsetting parancshoz a naplózás, kimenetének küldése az új tárfiók diagnosztika bekapcsolásához.

### <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez a következőket kell rendelkeznie:

* Egy meglévő Azure Analysis Services-kiszolgálóhoz. Egy kiszolgáló-erőforrás létrehozása, lásd: [kiszolgáló létrehozása az Azure-portálon](analysis-services-create-server.md), vagy [az Azure Analysis Services-kiszolgáló létrehozása a PowerShell használatával](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Csatlakozás az előfizetésekhez

Indítson el egy Azure PowerShell-munkamenetet, és jelentkezzen be az Azure-fiókjába az alábbi paranccsal:  

```powershell
Connect-AzureRmAccount
```

Az előugró böngészőablakban adja meg az Azure-fiókja felhasználónevét és jelszavát. Az Azure PowerShell megkeresi az összes olyan előfizetést, amely ehhez a fiókhoz van rendelve, és alapértelmezés szerint kiválasztja az elsőt.

Ha több előfizetése van, előfordulhat, hogy meg kell adnia azt, amelyikkel az Azure Key Vault tárolóját létrehozta. Írja be az alábbi parancsot a fiókhoz tartozó előfizetések megjelenítéséhez:

```powershell
Get-AzureRmSubscription
```

Ezt követően az az Azure Analysis Services-fiókkal jelentkezik tartozó előfizetés megadásához írja be:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Ha a fiókjához társított több előfizetéssel rendelkezik, fontos adja meg az előfizetést.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Új tárfiók létrehozása a naplóknak

Meglévő tárfiók használhatja a naplókat, feltéve, hogy ugyanazt az előfizetést, mint a kiszolgáló. Ebben az oktatóanyagban létrehozhat egy új tárfiókot dedikált Analysis Services naplókba. Megkönnyítheti, tárolja a tárfiókadatok nevű változóban **sa**.

Is használják, amely tartalmazza az Analysis Services-kiszolgálónak ugyanabban az erőforráscsoportban. Értékeit helyettesítse `awsales_resgroup`, `awsaleslogs`, és `West Central US` saját értékekkel:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>A naplók server fiók azonosítása

A fióknév nevű változó beállítása **fiók**, ahol a ResourceName a fiók nevét.

```powershell
$account = Get-AzureRmResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Naplózás engedélyezése

A naplózás engedélyezéséhez használja a Set-AzureRmDiagnosticSetting parancsmaggal együtt a változókat az új tárfiókot, fiókja és a kategória. Futtassa a következő parancsot, a beállítás a **-engedélyezve** jelzőt **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

A kimeneti kell kinéznie:

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

Ez megerősíti, hogy naplózási most már engedélyezve van a kiszolgáló, a tárolási fiók információ mentése.

Is beállíthat adatmegőrzési a naplók, a korábbi naplók automatikusan törlődnek. Például a megőrzési házirend használatával be **- RetentionEnabled** jelzőt **$true**, és állítsa be **- RetentionInDays** paramétert **90**. A naplófájlok 90 napnál régebbi automatikusan törlődnek.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>További lépések

További információ [Azure-erőforrás diagnosztikai naplózás](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

Lásd: [Set-AzureRmDiagnosticSetting](https://docs.microsoft.com/powershell/module/azurerm.insights/Set-AzureRmDiagnosticSetting) PowerShell súgójában.
