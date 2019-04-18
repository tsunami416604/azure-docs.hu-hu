---
title: Diagnosztikai naplózás az Azure Cosmos DB-ben
description: Ismerje meg naplózása és figyelése, az Azure Cosmos DB-ben tárolt adatok különböző módjait.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 8839d7ea93bcb205b1900e63d3ab98394e72cd75
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58904865"
---
# <a name="diagnostic-logging-in-azure-cosmos-db"></a>Diagnosztikai naplózás az Azure Cosmos DB-ben 

Legalább egy Azure Cosmos DB-adatbázisok használatához elindítása után előfordulhat, hogy figyelni kívánt hogyan és mikor érhetők el az adatbázisok. Ez a cikk az Azure platformon elérhető naplók áttekintést nyújt. Megtudhatja, hogyan ellenőrzési célból elküldeni a naplókat a diagnosztikai naplózás engedélyezése [Azure Storage](https://azure.microsoft.com/services/storage/), naplók közvetítése [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), és a naplók exportálása [AzureMonitor-naplók](https://azure.microsoft.com/services/log-analytics/).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="logs-available-in-azure"></a>Az Azure-ban elérhető naplók

Tárgyaljuk figyelése az Azure Cosmos DB-fiókot, mielőtt pedig elmagyarázza néhány dolgot kapcsolatos naplózás és figyelés. Nincsenek naplók az Azure platform különböző típusú. Nincsenek [Azure-tevékenységnaplóinak](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [Azure diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), [az Azure-metrikák](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), események, szívverés-figyelés, műveletnaplók és így tovább. Nincs naplók áll. A naplók teljes listáját megtekintheti [naplózza az Azure Monitor](https://azure.microsoft.com/services/log-analytics/) az Azure Portalon. 

Az alábbi képen látható az Azure-naplók elérhető különböző típusa:

![Különböző Azure-naplók](./media/logging/azurelogging.png)

Az ábrán a **számítási erőforrások** képviseli, amelyhez a Microsoft a vendég operációs rendszer érheti el az Azure-erőforrások. Például az Azure Virtual Machines, a virtuális gép méretezési csoportokat, az Azure Container Service, és így tovább, számít a számítási erőforrások. A számítási erőforrások készítése a tevékenységnaplókat, diagnosztikai naplók és Alkalmazásnaplókat. További tudnivalókért tekintse meg a [figyelési adatok az Azure-ban forrásai](../azure-monitor/platform/data-sources.md) cikk.

A **nem – számítási erőforrásokat** erőforrást, amelyben érheti el az alapul szolgáló operációs rendszer és az ügyfelek közvetlenül az erőforrás. Például a hálózati biztonsági csoportok, a Logic Apps és így tovább. Az Azure Cosmos DB egy olyan nem számítási erőforrás. Naplók nem számítási erőforrások megtekintése a tevékenységnaplóban, vagy engedélyezze a diagnosztikai naplók a portálon. További tudnivalókért tekintse meg a [adatforrásokat az Azure monitorban](../azure-monitor/platform/data-sources.md) cikk.

A tevékenységnapló előfizetés szintjén a műveletek az Azure Cosmos DB rögzíti. Műveletek, mint a listkeys műveletének, az írási DatabaseAccounts és egyéb a rendszer naplózza. Diagnosztikai naplók adja meg a részletes naplózást, és lehetővé teszi, hogy a naplófájl (létrehozás, Olvasás, lekérdezés és stb) DataPlaneRequests és MongoRequests.


Ebben a cikkben koncentrálunk az Azure-tevékenységnapló, az Azure diagnosztikai naplók és az Azure-metrikák. Mi a különbség a három naplók? 

### <a name="azure-activity-log"></a>Azure-tevékenységnapló

Az Azure-tevékenységnapló egy előfizetési napló, amely az Azure-ban bekövetkezett események előfizetés-szintű betekintést nyújt. A tevékenységnapló jelentések az előfizetések alatt a felügyeleti kategória vezérlősík eseményeket. A tevékenységnapló segítségével állapítja meg, a "mit, ki, és mikor" írási műveletet (PUT, POST, DELETE) erőforrásokra az előfizetésben. A művelet és az egyéb releváns tulajdonságok állapotát is ismernie is. 

A tevékenységnapló eltér a diagnosztikai naplók. A tevékenységnapló adatait a műveletek kívülről erőforrás biztosít (az _vezérlősík_). Az Azure Cosmos DB-környezetben a vezérlési síkjával végzett műveletek magukban foglalják tároló, kulcsok listázása, delete kulcsok, lista adatbázis, és így tovább hozzon létre. Diagnosztikai naplók az erőforrás által kibocsátott vannak, és adja meg a műveletet az erőforrás kapcsolatos információk (a _adatsík_). Néhány példa a diagnosztikai napló, az adatok síkjával végzett műveletek a Delete, Insert és ReadFeed.

Tevékenységnaplók (vezérlési síkjával végzett műveletek) gazdagabb jellegűek lehetnek, és tartalmazhatnak a hívó, hívó IP-címe, erőforrás neve, művelet neve, a TenantId és további teljes e-mail-címét. A tevékenységnapló több tartalmaz [kategóriák](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) az adatok. Részletes információ a sémák szerializálása e kategóriák közül, lásd: [Azure-tevékenységnapló eseménysémája](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema). Azonban a diagnosztikai naplók lehet korlátozó jellegű ezeket a naplókat a gyakran eltávolítani a személyes adatok. Előfordulhat, hogy a hívó IP-címe, de az utolsó octant törlődik.

### <a name="azure-metrics"></a>Azure-metrikák

[Az Azure-metrikák](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) Azure telemetriai adat legfontosabb típusa (más néven _teljesítményszámlálók_), amely a legtöbb Azure-erőforrások által kibocsátott. Mérőszámok segítségével átviteli sebesség, storage, konzisztencia, rendelkezésre állás és a késés, az Azure Cosmos DB-erőforrások adatainak megtekintése. További információkért lásd: [megfigyelési és a hibakeresést az Azure Cosmos DB metrikákkal](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Az Azure diagnosztikai naplók

Azure diagnosztikai naplók az erőforrás által kibocsátott vannak, és adja meg a műveletet az erőforrás gazdag, gyakori adatait. Ezek a naplók a tartalom erőforrás típusa szerint változó. Diagnosztikai naplók erőforrásszintű is Vendég operációsrendszer-szintű diagnosztikai naplók különböznek. A vendég operációs rendszer a diagnosztikai naplók egy virtuális gépet, vagy más támogatott belül futó ügynök által gyűjtött erőforrástípus. Diagnosztikai naplók erőforrásszintű nincs ügynök és a rögzítés erőforrás-specifikus adatok az Azure platform maga szükséges. Vendég operációsrendszer-szintű diagnosztikai naplók rögzítése az operációs rendszer és a egy virtuális gépen futó alkalmazásból származó adatok.

![Diagnosztikai naplózás, Storage, az Event Hubs vagy az Azure Monitor-naplókba](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Mi az Azure diagnosztikai naplók naplózta?

* Az összes hitelesített háttérrendszer kérelmek (TCP/REST) minden API-k között van jelentkezve, beleértve a hozzáférési engedélyek, rendszerhibákból vagy hibás kérésekből adótó sikertelen kérelmeket is. Felhasználó által kezdeményezett grafikon, a Cassandra és a Table API kérelmek támogatása nem érhető el.
* Műveleteket végez az adatbázison, többek között a dokumentumokat, tárolók és adatbázisok CRUD-műveleteket.
* Műveletek, a fiók létrehozása, módosítása vagy törlése a kulcsokat is tartalmazó.
* A 401-es választ eredményező, nem hitelesített kérelmek. Ha például kérelmeket, amely nem rendelkezik tulajdonosi jogkivonattal, helytelen formátumúak vagy lejártak vagy token érvénytelen rendelkezik.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Az Azure Portalon naplózás bekapcsolása

Diagnosztikai naplózás engedélyezése a következő erőforrások kell rendelkeznie:

* Egy meglévő Azure Cosmos DB fiókot, adatbázist és tárolót. Ezek az erőforrások létrehozásával kapcsolatos útmutatóért lásd: [egy adatbázis-fiók létrehozása az Azure portal használatával](create-sql-api-dotnet.md#create-account), [Azure CLI-minták](cli-samples.md), vagy [PowerShell-minták](powershell-samples.md).

Diagnosztikai naplózás az Azure Portalon engedélyezéséhez kövesse az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com), az az Azure Cosmos DB-fiók, jelölje ki **diagnosztikai naplók** a bal oldali navigációs, és válassza ki a **diagnosztika bekapcsolása**.

    ![Kapcsolja be a diagnosztikai naplózás az Azure Cosmos DB az Azure Portalon](./media/logging/turn-on-portal-logging.png)

2. Az a **diagnosztikai beállítások** lapon, tegye a következőket: 

    * **Név**: Adjon meg egy nevet, a naplókhoz hozhat létre.

    * **Archiválás tárfiókba**: Használja ezt a beállítást, egy meglévő tárfiókot csatlakozni kell. Új tárfiók létrehozása a portálon: [hozzon létre egy tárfiókot](../storage/common/storage-create-storage-account.md) , és kövesse az utasításokat egy Azure Resource Managerrel, általános célú fiók létrehozásához. Ezt követően térjen vissza erre a lapra, válassza ki a tárfiókot a portálon. Eltarthat néhány percig, újonnan létrehozott tárfiókok jelennek meg a legördülő menüben.
    * **Az eseményközpontok felé Stream**: Használja ezt a beállítást, egy meglévő Event Hubs névtér és az eseményközpont csatlakozni kell. Event Hubs-névtér létrehozása: [Event Hubs-névtér és eseményközpont létrehozása az Azure portal használatával](../event-hubs/event-hubs-create.md). Ezt követően térjen vissza erre a lapra a portálra, válassza ki az Event Hubs névtér és a házirend nevét.
    * **Küldés a Log Analyticsnek**: Ez a beállítás használatához használja egy meglévő munkaterületet, vagy hozzon létre egy új Log Analytics-munkaterületet a lépéseket követve [hozzon létre egy új munkaterületet](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace) a portálon. A naplók megtekintése az Azure Monitor naplóira kapcsolatos további információkért tekintse meg az Azure Monitor naplóira naplók megtekintése.
    * **Bejelentkezés DataPlaneRequests**: Válassza ki ezt a beállítást, az alapul szolgáló Azure Cosmos DB elosztott platformot SQL, a Graph, a MongoDB, a Cassandra és a Table API-fiókok a háttér-kérelmek naplózása. Ha, még archiválás tárfiókba, kiválaszthatja a diagnosztikai naplók megőrzési időtartama. Naplók automatikusan törli a megőrzési időszak lejárta után is.
    * **Bejelentkezés MongoRequests**: Válassza ki ezt a beállítást, a felhasználó által kezdeményezett kérelem számára az Azure Cosmos DB API a mongodb-hez konfigurált Cosmos-fiókok az Azure Cosmos DB előtér történő bejelentkezést. Ha, még archiválás tárfiókba, kiválaszthatja a diagnosztikai naplók megőrzési időtartama. Naplók automatikusan törli a megőrzési időszak lejárta után is.
    * **Metrika kérelmek**: Ezt a beállítást a részletes adatok tárolására [az Azure-metrikák](../azure-monitor/platform/metrics-supported.md). Ha, még archiválás tárfiókba, kiválaszthatja a diagnosztikai naplók megőrzési időtartama. Naplók automatikusan törli a megőrzési időszak lejárta után is.

3. Kattintson a **Mentés** gombra.

    Ha hibaüzenet jelenik meg arról, hogy a "nem sikerült frissíteni a diagnosztikai \<munkaterület neve >. Az előfizetés \<előfizetés-azonosító > nincs regisztrálva a microsoft.insights, használjon "hajtsa végre a [Azure Diagnostics hibaelhárítása](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) regisztrálja a fiókot, és ismételje meg ezt az eljárást.

    Ha meg szeretné változtatni a módját a diagnosztikai naplók menti a bármikor a jövőben, térjen vissza ezen az oldalon módosíthatja a fiókját diagnosztikai napló beállításait.

## <a name="turn-on-logging-by-using-azure-cli"></a>Azure CLI-vel kapcsolja be a naplózást

Metrikák és diagnosztikai célú naplózásának engedélyezése az Azure CLI-vel, használja a következő parancsokat:

- Ahhoz, hogy a diagnosztikai naplók tárolása a storage-fiókban, használja ezt a parancsot:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name DiagStorage --resource <resourceId> --storage-account <storageAccountName> --logs '[{"category": "QueryRuntimeStatistics", "enabled": true, "retentionPolicy": {"enabled": true, "days": 0}}]'
   ```

   A `resource` az Azure Cosmos DB-fiók neve. A következő formátumban van az erőforrás "/subscriptions/`<subscriptionId>`/resourceGroups/`<resource_group_name>`/providers/Microsoft.DocumentDB/databaseAccounts/ < Azure_Cosmos_account_name >" a `storage-account` a tárfiók neve, mely kíván küldeni a naplókat. További naplófájlokat "MongoRequests" vagy "DataPlaneRequests" kategória paraméter értékét frissítésével bejelentkezhet. 

- Diagnosztikai naplók egy eseményközpontba streamelésének engedélyezéséhez, használja ezt a parancsot:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --event-hub-rule <eventHubRuleID> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

   A `resource` az Azure Cosmos DB-fiók neve. A `event-hub-rule` az event hub szabály azonosítója. 

- Ahhoz, hogy a küldő a diagnosztikai naplók a Log Analytics-munkaterülethez, használja ezt a parancsot:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --workspace <resource id of the log analytics workspace> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

Kombinálhatja ezeket a paramétereket, több kimeneti beállítások engedélyezéséhez.

## <a name="turn-on-logging-by-using-powershell"></a>Kapcsolja be a naplózást a PowerShell használatával

Diagnosztikai naplózás bekapcsolásához használja a Powershellt van szüksége az Azure Powershell legalább 1.0.1-es verziója.

Az Azure PowerShell telepítésérről és az Azure-előfizetéssel való társításáról további információt [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása) című cikkben találhat.

Ha már telepített Azure PowerShell-lel, és nem ismeri a verzió, a PowerShell-konzol típusból `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Csatlakozás az előfizetésekhez
Indítson el egy Azure PowerShell-munkamenetet, és jelentkezzen be az Azure-fiókjába az alábbi paranccsal:  

```powershell
Connect-AzAccount
```

Az előugró böngészőablakban adja meg az Azure-fiókja felhasználónevét és jelszavát. Az Azure PowerShell megkeresi az összes az előfizetést, amely, társítva ehhez a fiókhoz, és alapértelmezés szerint kiválasztja az elsőt.

Ha egynél több előfizetéssel rendelkezik, akkor előfordulhat, hogy adja meg az adott előfizetés létrehozása az Azure key vaultban használt. A fiókhoz tartozó előfizetések megtekintéséhez írja be a következő parancsot:

```powershell
Get-AzSubscription
```

Ezt követően az Azure Cosmos DB-fiókot, hogy bejelentkezik a tartozó előfizetés megadásához írja be a következő parancsot:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Ha a fiókjához tartozó egynél több előfizetéssel rendelkezik, fontos válassza ki a használni kívánt előfizetést.
>
>

Azure PowerShell-lel konfigurálásával kapcsolatos további információkért lásd: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview).

### <a id="storage"></a>Új tárfiók létrehozása a naplóknak
Bár ebben az oktatóanyagban használhatja egy meglévő tárfiókot a naplók, létrehozunk egy új tárfiókot, amely Azure Cosmos DB-naplók van kijelölve. Az egyszerűség kedvéért a nevű változóban tároljuk a tárfiók részleteit **sa**.

Az egyszerű, ebben az oktatóanyagban ugyanazt az erőforráscsoportot használjuk, az Azure Cosmos DB adatbázis tartalmazza. Helyettesítse be a saját értékeit a **ContosoResourceGroup**, **contosocosmosdblogs**, és **USA északi középső Régiója** paraméterek, amennyiben alkalmazhatók:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Ha úgy dönt, hogy használjon egy meglévő tárfiókot, a fiók ugyanahhoz az előfizetéshez kell használnia, az Azure Cosmos DB-előfizetés. A fiók is használnia kell, a Resource Manager üzemi modell, nem pedig a klasszikus üzemi modellben.
>
>

### <a id="identify"></a>A naplók az Azure Cosmos DB-fiók azonosítása
Állítsa be az Azure Cosmos DB-fiók nevét egy nevű változóhoz **fiók**, ahol **ResourceName** az Azure Cosmos DB-fiók neve.

```powershell
$account = Get-AzResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Naplózás engedélyezése
Az Azure Cosmos DB a naplózás engedélyezéséhez használja a `Set-AzDiagnosticSetting` parancsmagot az új tárfiókot, az Azure Cosmos DB-fiók és a naplózás engedélyezéséhez a kategória változókat. Futtassa a következő parancsot, és állítsa be a **-kompatibilis** jelzőt **$true**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

A parancs kimenete a következő mintát kell hasonlítania:

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

A parancs kimenete megerősíti, hogy a naplózás engedélyezve van az adatbázis és információk mentése folyamatban van a tárfiókhoz.

Igény szerint is beállíthat a megtartási házirend a naplók úgy, hogy a régebbi naplófájlok automatikusan törlődnek. Például állítsa be a megőrzési házirend a **- RetentionEnabled** jelző értékre **$true**. Állítsa be a **- RetentionInDays** paramétert **90** úgy, hogy a 90 napnál régebbi naplófájlok automatikusan törlődnek.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>A naplók elérése
Az Azure Cosmos DB-naplókban a a **DataPlaneRequests** kategóriába vannak tárolva a **insights-logs-adatok-adatsík-kérelmek** megadott storage-fiókban lévő tárolóba. 

Először hozzon létre egy változót a tároló nevéhez. Az útmutatóban használja a változót.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Összes ebben a tárolóban lévő blobok listázása, írja be:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

A parancs kimenete a következő mintát kell hasonlítania:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

Ahogy a kimenetből látható, a blobokat egy elnevezési konvenciót követi: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

A dátum- és időértékek az UTC hivatkozási időzónát használják.

Mivel ugyanazt a tárfiókot a naplók több erőforrás is használható, használhatja a teljesen minősített erőforrás-azonosító a blob nevében eléréséhez, és töltse le a konkrét blobokat. Mielőtt nézzük meg, hogyan töltheti le az összes BLOB ismerteti.

Elsőként hozzon létre egy mappát, amelybe letölti a blobokat. Példa:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Kérje le az összes BLOB listáját:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

A listát a keresztül a `Get-AzStorageBlobContent` paranccsal töltse le a a rendeltetési mappára:

```powershell
$blobs | Get-AzStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

A második parancs futtatásakor a **/** a blob nevének elválasztó alapján hoz létre egy teljes mapparendszert a rendeltetési mappára. A mappastruktúra töltse le és -fájlok formájában a blobok tárolására szolgál.

A blobok egyenkénti letöltéséhez használjon helyettesítő elemeket. Példa:

* Ha több adatbázist, és szeretné egy adatbázis nevű tartozó naplók letöltéséhez **CONTOSOCOSMOSDB3**, használja a parancsot:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Ha több erőforráscsoportok és a egy erőforráscsoporthoz tartozó naplók letöltéséhez szeretne, használja a parancsot `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Ha azt szeretné, töltse le a 2017 július hónapja naplóját, a paranccsal `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Is futtathatja a következő parancsokat:

* Az adatbázis-erőforrás diagnosztikai beállítások állapotának lekérdezése, a parancs segítségével `Get-AzDiagnosticSetting -ResourceId $account.ResourceId`.
* A naplózás letiltása az **DataPlaneRequests** kategória az adatbázis-fiók erőforrás a parancs használata `Set-AzDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`.


A blobok, az egyes lekérdezések visszaadott szövegként tárolt, formázott JSON-blobként, az alábbi kódban látható módon:

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

Az adatok az egyes JSON-blobok kapcsolatos további információkért lásd: [az Azure Cosmos DB naplóinak értelmezése](#interpret).

## <a name="manage-your-logs"></a>A naplók kezelése

Diagnosztikai naplók is elérhetők a fiók, amely az Azure Cosmos DB művelet történt kezdve két órán keresztül. A tárfiók naplófájljait Önnek kell kezelnie:

* Használja a standard szintű Azure hozzáférés-vezérlési módszereivel a naplókhoz, és korlátozzák a ki érheti el őket.
* Törölje azokat a naplókat, amelyeket nem kíván megőrizni a tárfiókban.
* A tárfiókhoz archivált adatsík kérelmek megőrzési ideje van konfigurálva, a portálon során a **Log DataPlaneRequests** beállítás be van jelölve. Ez a beállítás módosításához lásd [kapcsolja be a naplózás az Azure Portalon](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-azure-monitor-logs"></a>Naplók megtekintése az Azure Monitor naplóira

Ha bejelölte a **Küldés a Log Analyticsnek** van kapcsolva a diagnosztikai naplózás, diagnosztikai beállítást a tároló adatait az Azure Monitor naplóira két órán belül továbbítja. Ha az Azure Monitor naplóira naplózás bekapcsolása után azonnal, nem jelennek meg adatok. Mindössze két órás várakozási, és próbálkozzon újra. 

Mielőtt a naplók megtekintéséhez ellenőrizze, és tekintse meg, ha a Log Analytics-munkaterület frissítve lett az új Kusto-lekérdezés nyelv használatára. Ellenőrzéséhez nyissa meg a [az Azure portal](https://portal.azure.com)válassza **Log Analytics-munkaterületek** a left, majd válassza ki a munkaterület neve a következő képen látható módon. A **Log Analytics-munkaterület** lap jelenik meg:

![Az Azure Portalon az Azure Monitor naplóira](./media/logging/azure-portal.png)

>[!NOTE]
>Az OMS-munkaterületeket mostantól Log Analytics-munkaterületeknek nevezzük.  

Ha a következő üzenet jelenik meg a a **Log Analytics-munkaterület** oldalon, a munkaterület rendszertábla az új nyelv használatára. Az új lekérdezési nyelvre való frissítése. További információkért lásd: [Azure Log Analytics-munkaterület frissítése új naplókeresésre](../log-analytics/log-analytics-log-search-upgrade.md). 

![Az Azure Monitor naplóira üzenet frissítése](./media/logging/upgrade-notification.png)

A diagnosztikai adatok az Azure Monitor-naplók megtekintéséhez nyissa meg a **naplóbeli keresés** lapra a bal oldali menüben, vagy a **felügyeleti** terület az oldal a következő képen látható módon:

![Az Azure Portalon a napló keresési beállítások](./media/logging/log-analytics-open-log-search.png)

Most, hogy engedélyezte az adatgyűjtést, futtasson le a következő naplóbeli keresést, tekintse meg a 10 legújabb naplókat az új lekérdezési nyelv használatával `AzureDiagnostics | take 10`.

![Naplóbeli keresés minta a 10 legújabb naplókhoz](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Lekérdezések

Az alábbiakban néhány további lekérdezést, amely adhatja meg azokat a **naplóbeli keresés** jelölőnégyzetet, hogy segítséget nyújt az Azure Cosmos DB-tárolók monitorozásához. Ezek a lekérdezések használata a [új nyelv](../log-analytics/log-analytics-log-search-upgrade.md). 

Az egyes naplóbeli keresés által visszaadott adatok jelentését kapcsolatos további információkért lásd: [az Azure Cosmos DB naplóinak értelmezése](#interpret).

* Az összes Azure Cosmos DB-ből a diagnosztikai naplók lekérdezése egy megadott időszak:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Lekérdezni a 10 legtöbb nemrég naplózott események:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* A lekérdezés minden műveletre, művelet típusa szerint csoportosítva:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* A lekérdezés az összes művelet szerint csoportosítva **erőforrás**:

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* A lekérdezés az összes felhasználói tevékenység, erőforrás szerint csoportosítva:

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
    > [!NOTE]
    > Ez a parancs egy tevékenységnapló-, nem egy diagnosztikai napló szól.

* A lekérdezéshez, amelynek műveletek tovább tart, mint 3 idő ezredmásodpercben:

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Lekérdezése, mely ügynök fut a műveletek:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Lekérdezni a, ha a hosszú ideig futó műveletek lettek végrehajtva:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

Az új naplófájl-keresési nyelv használatával kapcsolatos további információkért lásd: [naplókeresések megismerése az Azure Monitor naplóira](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>A naplóinak értelmezése

Diagnosztikai adatok az Azure Storage és az Azure Monitor naplók tárolt hasonló sémával használja. 

A következő táblázat ismerteti a tartalom minden eseménynapló-bejegyzés.

| Az Azure Storage mezőjével vagy tulajdonságával | Az Azure Monitor-naplók tulajdonság | Leírás |
| --- | --- | --- |
| **idő** | **TimeGenerated** | Dátuma és időpontja (UTC), ha a művelet történt. |
| **resourceId** | **Erőforrás** | Az Azure Cosmos DB-fiókot, amelynek a naplói engedélyezve vannak.|
| **kategória** | **Kategória** | Az Azure Cosmos DB-naplók **DataPlaneRequests** az egyetlen elérhető érték. |
| **OperationName** | **OperationName** | A művelet neve. Ez az érték a következő műveletek bármelyike lehet: Hozzon létre, frissítés, olvasási, ReadFeed, törlés, csere, SQL-lekérdezés, lekérdezés, JSQuery, Head, HeadFeed vagy Upsert hajtható végre.   |
| **Tulajdonságok** | n/a | Ez a mező tartalma kövesse azokat a sorokat ismerteti. |
| **activityId** | **activityId_g** | A naplózott műveletnek egyedi GUID azonosítója. |
| **userAgent** | **userAgent_s** | Egy karakterlánc, amely meghatározza a kérést végrehajtó felhasználó ügyfélügynök. A formátum a következő {felhasználói ügynök neve} / {version}.|
| **requestResourceType** | **requestResourceType_s** | Elért erőforrás típusa. Ez az érték a következő erőforrás-típusok egyike lehet: Adatbázis, tároló, a dokumentum, melléklet, felhasználói, engedélyt, StoredProcedure, eseményindító, UserDefinedFunction vagy ajánlat. |
| **statusCode** | **statusCode_s** | A válasz állapota a műveletet. |
| **requestResourceId** | **ResourceId** | Az erőforrás-azonosító, amely a kérelem vonatkozik. Az érték databaseRid, collectionRid vagy documentRid végrehajtott művelettől függően előfordulhat, hogy mutasson.|
| **clientIpAddress** | **clientIpAddress_s** | Az ügyfél IP-cím. |
| **requestCharge** | **requestCharge_s** | A művelet által használt fenntartott egységek száma |
| **collectionRid** | **collectionId_s** | A gyűjtemény egyedi azonosítója.|
| **Időtartam** | **duration_s** | A művelet az órajelben időtartama. |
| **requestLength** | **requestLength_s** | A kérelem bájtban hossza. |
| **responseLength** | **responseLength_s** | A válasz bájtban hossza.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Ezt az értéket kötelező nem üres Ha [erőforrás-jogkivonatokat](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) hitelesítéshez használhatók. Az érték a felhasználó erőforrás-Azonosítójára mutat. |

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan engedélyezze a naplózást, valamint a különböző Azure-szolgáltatások által támogatott mérőszámokban és naplófájlokban kategóriák, olvassa el mind a [Microsoft Azure-ban mérőszámok áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md) és [áttekintése az Azure diagnosztikai naplók ](../azure-monitor/platform/diagnostic-logs-overview.md) cikkeket.
- Olvassa el ezeket a cikkeket az event hubs ismertetése:
   - [Mi az Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Olvasási [metrikák és diagnosztikai naplók letöltése az Azure Storage-ból](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
- Olvasási [naplókeresések megismerése az Azure Monitor naplóira](../log-analytics/log-analytics-log-search-new.md).
