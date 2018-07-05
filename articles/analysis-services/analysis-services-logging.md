---
title: Az Azure Analysis Services Diganostic naplózása |} A Microsoft Docs
description: Ismerje meg az Azure Analysis Services diagnosztikai naplózás beállítása.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: d19e45710aca3e1e18be6c4529da6474a97bc59f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449953"
---
# <a name="setup-diagnostic-logging"></a>Diagnosztikai naplózás beállítása

Fontos része annak az Analysis Services megoldással figyel, hogyan a kiszolgálók végzik. A [Azure erőforrás-diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md), figyelheti és a naplók küldése [Azure Storage](https://azure.microsoft.com/services/storage/), azokat a stream [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), és exportálhatja őket [napló Analytics](https://azure.microsoft.com/services/log-analytics/), amely egy, a szolgáltatás [Azure](https://www.microsoft.com/cloud-platform/operations-management-suite). 

![Diagnosztikai naplózás, Storage, az Event Hubs és a Log Analytics](./media/analysis-services-logging/aas-logging-overview.png)


## <a name="whats-logged"></a>Mi kerül?

Választhat **motor**, **szolgáltatás**, és **metrikák** kategóriák.

### <a name="engine"></a>Adatbázismotor

Kiválasztásával **motor** naplózza az összes [xevent típusú eseményekhez](https://docs.microsoft.com/sql/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Nem lehet kiválasztani az egyes eseményeket. 

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

A metrikák kategória naplók azonos [kiszolgáló metrikáinak](analysis-services-monitor.md#server-metrics) metrikák jelenik meg.

## <a name="setup-diagnostics-logging"></a>Diagnosztikai naplózás beállítása

### <a name="azure-portal"></a>Azure Portal

1. A [az Azure portal](https://portal.azure.com) > kiszolgáló, kattintson a **diagnosztikai naplók** a bal oldali navigációs, és kattintson a **diagnosztika bekapcsolása**.

    ![Kapcsolja be a diagnosztikai naplózás az Azure Cosmos DB az Azure Portalon](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. A **diagnosztikai beállítások**, adja meg a következő beállításokat: 

    * **Név**. Adjon meg egy nevet, a naplókhoz hozhat létre.

    * **Archiválás tárfiókba**. Használja ezt a beállítást, egy meglévő tárfiókot csatlakozni kell. Lásd: [hozzon létre egy tárfiókot](../storage/common/storage-create-storage-account.md). Kövesse az utasításokat egy erőforrás-kezelő, általános célú fiók létrehozásához, majd válassza ki a tárfiókját, térjen vissza erre a lapra a portál által. Eltarthat néhány percig, újonnan létrehozott tárfiókok jelennek meg a legördülő menüben.
    * **Az eseményközpontok felé Stream**. Használja ezt a beállítást, egy meglévő Event Hubs névtér és az eseményközpont csatlakozni kell. További tudnivalókért lásd: [hozzon létre egy Event Hubs-névtér és a egy eseményközpontot, az Azure portal használatával](../event-hubs/event-hubs-create.md). Ezután térjen vissza erre a lapra a portálra, válassza ki az Eseményközpont-névtér és a házirend nevét.
    * **Küldés a Log Analyticsnek**. Ez a beállítás használatához használja egy meglévő munkaterületet, vagy hozzon létre egy új Log Analytics-munkaterületet a lépéseket követve [hozzon létre egy új munkaterületet](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) a portálon. A naplók megtekintése a Log Analytics további információkért lásd: [naplók megtekintése a Log Analytics](#view-in-loganalytics).

    * **Motor**. Ezzel a lehetőséggel Xevent bejelentkezni. Ha, még archiválás tárfiókba, kiválaszthatja a diagnosztikai naplók megőrzési időtartama. Naplók autodeleted, a megőrzési időszak lejárta után is.
    * **Szolgáltatás**. Ezt a beállítást a szolgáltatási szintű eseményeket. Ha archiválni egy tárfiókot, kiválaszthatja a diagnosztikai naplók megőrzési időtartama. Naplók autodeleted, a megőrzési időszak lejárta után is.
    * **Metrikák**. Ezt a beállítást a részletes adatok tárolására [metrikák](analysis-services-monitor.md#server-metrics). Ha archiválni egy tárfiókot, kiválaszthatja a diagnosztikai naplók megőrzési időtartama. Naplók autodeleted, a megőrzési időszak lejárta után is.

3. Kattintson a **Save** (Mentés) gombra.

    Ha hibaüzenet jelenik meg arról, hogy a "nem sikerült frissíteni a diagnosztikai \<munkaterület neve >. Az előfizetés \<előfizetés-azonosító > nincs regisztrálva a microsoft.insights használata. " Kövesse a [Azure Diagnostics hibaelhárítása](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) utasításokat követve regisztrálja a fiókot, majd ismételje meg ezt az eljárást.

    Ha meg szeretné változtatni a módját a diagnosztikai naplók menti a bármikor a jövőben, visszatérhet ezt oldal beállításainak módosítására.

### <a name="powershell"></a>PowerShell

Az alábbiakban az alapszintű parancsokat bevezetésként. Ha azt szeretné, hogy a részletes súgó a naplózást, és a egy storage-fiók beállítása a PowerShell-lel, tekintse meg az oktatóanyag a cikk későbbi részében.

Metrikák és naplózás PowerShell-lel diagnosztika engedélyezéséhez használja a következő parancsokat:

- Ahhoz, hogy a diagnosztikai naplókat egy tárfiókban, használja ezt a parancsot:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   A tárfiók azonosítója a storage-fiók erőforrás-azonosító, ahol szeretné küldeni a naplókat.

- Diagnosztikai naplók egy eseményközpontba streamelésének engedélyezéséhez használja ezt a parancsot:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Az Azure Service Bus Szabályazonosító karakterláncnak a következő formátumban:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Ahhoz, hogy a küldő diagnosztikai naplók a Log Analytics-munkaterülethez, használja ezt a parancsot:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- A Log Analytics munkaterület erőforrás-Azonosítóját a következő paranccsal szerezheti be:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Kombinálhatja ezeket a paramétereket, több kimeneti beállítások engedélyezéséhez.

### <a name="rest-api"></a>REST API

Ismerje meg, hogyan [diagnosztikai beállítások módosítása az Azure Monitor REST API használatával](https://msdn.microsoft.com/library/azure/dn931931.aspx). 

### <a name="resource-manager-template"></a>Resource Manager-sablon

Ismerje meg, hogyan [erőforrás létrehozásakor a diagnosztikai beállítások engedélyezése Resource Manager-sablon használatával](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="manage-your-logs"></a>A naplók kezelése

Érhetők el naplók általában a naplózás beállítása néhány órán belül. A tárfiók naplófájljait Önnek kell kezelnie:

* Az Azure szabványos hozzáférés-vezérlési módszereivel korlátozhatja a naplókhoz való hozzáférést, így megvédheti azokat.
* Törölje azokat a naplókat, amelyeket nem kíván megőrizni a tárfiókban.
* Mindenképpen állítsa olyan megőrzési időszakot, így a régi naplókat törli a storage-fiókjában.

## <a name="view-logs-in-log-analytics"></a>Naplók megtekintése a Log Analyticsben

Metrikák és a kiszolgáló eseményeit integrálva vannak a Log Analytics xevent típusú eseményekhez egymás mellett elemzés céljából. A log Analytics az események fogadása az más Azure-szolgáltatásokat nyújtó holisztikus diagnosztikai naplózási adatok között az architektúra is konfigurálható.

A Log Analytics a diagnosztikai adatok megtekintéséhez nyissa meg a naplók keresése oldalát a bal oldali menüben, vagy a felügyeleti területen alább látható módon.

![Keresési beállítások jelentkezzen be az Azure Portalon](./media/analysis-services-logging/aas-logging-open-log-search.png)

Most, hogy engedélyezte az adatgyűjtést, a **naplóbeli keresés**, kattintson a **összegyűjtött adatok**.

A **típus**, kattintson a **AzureDiagnostics**, és kattintson a **alkalmaz**. AzureDiagnostics motor és a szolgáltatások eseményeinek tartalmazza. Figyelje meg, hogy a Log Analytics-lekérdezés jön létre a működés közbeni. A EventClass\_s mező tűnhet, hogy jól ismert, ha a helyszíni naplózás xevent típusú eseményekhez használt xEvent neveket tartalmaz.

Kattintson a **EventClass\_s** vagy az esemény nevét és a Log Analytics egyik továbbra is hozhat létre, amely a lekérdezést. Mindenképpen mentse a lekérdezések későbbi használatra.

Győződjön meg arról, a Log Analytics, amely egy webhely biztosít magas szintű dashboarding és riasztási funkciókat biztosít a gyűjtött adatok megtekintéséhez.

### <a name="queries"></a>Lekérdezések

Nincsenek lekérdezésekben használható több száz. Íme néhány a kezdéshez.
Az új naplók keresése lekérdezési nyelv használatával kapcsolatos további tudnivalókért lásd: [naplókereséseit ismertető a Log Analytics](../log-analytics/log-analytics-log-search-new.md). 

* Lekérdezés vissza a lekérdezések elküldése az Azure Analysis Servicesbe, amely több mint öt perc alatt (300 000 milliszekundum) befejezéséhez szükséges.

    ```
    search * | where ( Type == "AzureDiagnostics" ) | where ( EventClass_s == "QUERY_END" ) | where toint(Duration_s) > 300000
    ```

* Horizontális felskálázás replikák azonosításához.

    ```
    search * | summarize count() by ServerName_s
    ```
    Horizontális felskálázás használata esetén csak olvasható replikák azonosíthatja, mert a kiszolgálónév\_s mezőértékek rendelkezik a replika példány számot a névhez. Az erőforrás mező tartalmazza a Azure-erőforrás nevét, amely megfelel a kiszolgáló nevét, a felhasználók számára. A IsQueryScaleoutReadonlyInstance_s mező értéke igaz, a replikákat.



> [!TIP]
> Van egy nagyszerű Log Analytics-lekérdezés szeretné megosztani? Ha egy GitHub-fiókkal rendelkezik, ez a cikk a adhat hozzá. Ehhez egyszerűen kattintson **szerkesztése** , a jobb felső sarkában ezen a lapon.


## <a name="tutorial---turn-on-logging-by-using-powershell"></a>Oktatóanyag – a PowerShell-lel naplózás bekapcsolása
Gyors ebben az oktatóanyagban mint az Analysis Services-kiszolgáló hoz létre egy tárfiókot, az ugyanabban az előfizetésben és erőforráscsoportban. Ezt követően az Set-azurermdiagnosticsetting parancshoz, kapcsolja be a diagnosztikai naplózás, a kimeneti küldését az új tárfiókot.

### <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez a következőket kell rendelkeznie:

* Egy meglévő Azure Analysis Services-kiszolgáló. Egy kiszolgálói erőforrást létrehozásával kapcsolatos útmutatóért lásd: [hozzon létre egy kiszolgálót az Azure Portalon](analysis-services-create-server.md), vagy [Azure Analysis Services-kiszolgáló létrehozása a PowerShell használatával](analysis-services-create-powershell.md).

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

Ezt követően az Azure Analysis Services-fiókkal jelentkezik tartozó előfizetés megadásához írja be:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Ha a fiókjához társított több előfizetéssel rendelkezik, fontos adja meg az előfizetést.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Új tárfiók létrehozása a naplóknak

Használhatja egy meglévő tárfiókot a naplók, feltéve, hogy a kiszolgáló ugyanabban az előfizetésben. Ebben az oktatóanyagban létrehozhat egy új tárfiókot dedikált Analysis Services-naplókba. Megkönnyíti, tárolja a tárfiók részleteit nevű változóba **sa**.

Is ugyanazt az erőforráscsoportot használjuk az Analysis Services-kiszolgáló tartalmazza. Értékeit helyettesítse `awsales_resgroup`, `awsaleslogs`, és `West Central US` a saját értékeire:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>A naplók server fiók azonosítása

Állítsa be a fiók nevét egy nevű változóhoz **fiók**, ahol a ResourceName a fiók nevét.

```powershell
$account = Get-AzureRmResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Naplózás engedélyezése

A naplózás engedélyezéséhez használja a Set-AzureRmDiagnosticSetting parancsmaggal együtt a változókat az új tárfiókot, a kiszolgáló fiók és a kategória. Futtassa a következő parancsot, a beállítás a **-kompatibilis** jelzőt **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

A kimenet következőhöz hasonlóan kell kinéznie:

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

Ezzel megerősíti, hogy naplózás engedélyezve van a kiszolgálón, így az információk a storage-fiókot.

Beállíthat adatmegőrzési házirend a naplók, a régebbi naplófájlok automatikusan törlődnek. Például állítsa be a megtartási házirendet a **- RetentionEnabled** jelzőt **$true**, és állítsa be **- RetentionInDays** paramétert **90**. 90 napnál régebbi naplófájlok automatikusan törlődnek.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>További lépések

Tudjon meg többet [Azure-erőforrás diagnosztikai naplózás](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

Lásd: [Set-azurermdiagnosticsetting parancshoz](https://docs.microsoft.com/powershell/module/azurerm.insights/Set-AzureRmDiagnosticSetting) PowerShell súgójában.
