---
title: Az Azure Cosmos DB diagnosztikai naplózás |} Microsoft Docs
description: Ez az oktatóanyag használatával alakítsa ki Ismerkedés az Azure Cosmos DB-naplózás.
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
tags: azure-resource-manager
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: sngun
ms.openlocfilehash: 103d9d36ae1290f7af18be83f41bd9b83dbd3fbe
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-cosmos-db-diagnostic-logging"></a>Az Azure Cosmos DB diagnosztikai naplózás

Egy vagy több Azure Cosmos DB adatbázis használandó elindítása után is figyelni kívánt hogyan és mikor érhetők el az adatbázisok. Ez a cikk áttekintést nyújt a naplók az Azure platformon elérhető. Megismerheti, hogyan ellenőrzési célból segítségével diagnosztikai naplózás engedélyezése [Azure Storage](https://azure.microsoft.com/services/storage/), hogyan adatfolyam-bejegyzéseit, amelyek [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), és a naplók exportálása [Azure Naplóelemzés ](https://azure.microsoft.com/services/log-analytics/).

## <a name="logs-available-in-azure"></a>Az Azure-ban elérhető naplók

Döntésről bővebben figyelése Azure Cosmos DB fiókja, mielőtt most tisztázása naplózásának és figyelésének kapcsolatos néhány dolgot. Nincsenek naplók az Azure platformon különböző típusait. Nincsenek [Azure tevékenységi naplóit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [Azure diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), [Azure metrikák](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), események, szívverés-figyelés, műveletek naplókat, és így tovább. A naplók formátum van. A naplók teljes listáját megtekintheti [Azure Naplóelemzés](https://azure.microsoft.com/services/log-analytics/) az Azure portálon. 

A következő kép bemutatja a különböző Azure naplók:

![Különböző Azure naplói](./media/logging/azurelogging.png)

Az ábrán a **számítási erőforrásokat** határoz meg az Azure-erőforrások, amelynek érheti el a Microsoft vendég operációs rendszer. Például az Azure virtuális gépek, virtuális gép méretezési készletek, az Azure Tárolószolgáltatás, és így tovább figyelembe vett számítási erőforrásokat. A számítási erőforrások tevékenységi naplóit, a diagnosztikai naplók és a alkalmazásnaplók létrehozása. További tudnivalókért tekintse meg a [Azure figyelési: számítási erőforrásokat](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#azure-monitor-sources---compute-subset) cikk.

A **nem-számítási erőforrásokat** erőforrás, amelyben érheti el az alapul szolgáló operációs rendszer és az erőforrás közvetlenül együttműködni. Például hálózati biztonsági csoportok, a Logic Apps, és így tovább. Azure Cosmos-adatbázis nem számítási erőforrás. Nem számítási erőforrásokat a naplók megtekintéséhez a műveletnaplóban, vagy engedélyezze a diagnosztikai naplókat a portálon. További tudnivalókért tekintse meg a [Azure figyelési: nem számítási erőforrások](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#azure-monitor-sources---everything-else) cikk.

A műveletnapló rögzíti a műveleteket, Azure Cosmos DB egy előfizetés szintjén. Például ListKeys, írható DatabaseAccounts és egyéb műveleteket a rendszer naplózza. Diagnosztikai naplók nyújtanak részletesebb naplózás, és engedélyezi, hogy a naplófájl (létrehozása, olvasása, lekérdezés és így tovább) DataPlaneRequests és MongoRequests.


Ez a cikk azt összpontosítani a Azure tevékenységnapló, az Azure diagnosztikai naplók és az Azure metrikákat. Mi az a különbség a három naplók között? 

### <a name="azure-activity-log"></a>Azure tevékenységnapló

Az Azure tevékenységnapló egy előfizetési napló, amely történt az Azure-előfizetés szintű események betekintést nyújt. A műveletnapló vezérlő-vezérlősík események a felügyeleti kategória az előfizetések a jelentések. A műveletnapló segítségével meghatározhatja a "mi, ki, és mikor" az előfizetés bármely írja az erőforrások (PUT, POST, Törlés) művelet számára. A művelet és az egyéb kapcsolódó tulajdonságainak állapotának értelmezni is lehet. 

A műveletnapló eltér a diagnosztikai naplókat. A műveletnapló kívülről erőforráson műveleteire vonatkozó adatokat biztosít (az _vezérlő vezérlősík_). Azure Cosmos DB környezetben műveletek közé tartozik vezérlő vezérlősík gyűjtemény, listában kulcsok, törlési kulcsok, lista adatbázis, és létrehozása stb. Diagnosztikai naplók az erőforrás által kibocsátott, és adja meg az erőforrás működésével kapcsolatos adatokat (a _adatok vezérlősík_). Néhány példa a diagnosztikai naplófájl az adatok vezérlősík műveletek a következők: törlés, Insert és ReadFeed.

Tevékenységi naplóit (vezérlő vezérlősík műveletek) gazdagabb jellegűek lehetnek, és a teljes e-mail címét, valamint a hívó, hívó IP-cím, erőforrás neve, a művelet neve, a TenantId, több is tartalmazhat. A műveletnapló több tartalmaz [kategóriák](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) adatok. Az ezekben a kategóriákban sémákra teljes részletekért lásd: [Azure tevékenységnapló esemény séma](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema). Azonban diagnosztikai naplók nem korlátozó ideiglenesek, ezek a naplók a gyakran tisztító a személyazonosításra alkalmas adatokat. Előfordulhat, hogy a hívó IP-címét, de a rendszer eltávolítja az utolsó octant.

### <a name="azure-metrics"></a>Az Azure metrikák

[Az Azure metrikák](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) Azure telemetriai adatok legfontosabb típusa (más néven _teljesítményszámlálók_) az Azure erőforrások által kibocsátott. Metrikák lehetővé teszik, hogy az átviteli sebesség, tárolási, konzisztencia, rendelkezésre állás és a késés az Azure Cosmos DB erőforrások vonatkozó információk megtekintése. További információkért lásd: [figyelés és az Azure Cosmos DB a metrikák hibakeresés](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Az Azure diagnosztikai naplók

Az Azure diagnosztikai naplók az erőforrás által kibocsátott, és adja meg az erőforrás a művelet részletes, gyakori adatait. Ezek a naplók tartalmának erőforrástípusok szerint változik. Erőforrás-szintű diagnosztikai naplók is eltérnek a vendég operációs rendszer szintű diagnosztikai naplókat. Vendég operációs rendszer diagnosztikai naplók, hogy a virtuális gépek vagy más támogatott belül fut egy ügynök által gyűjtött erőforrástípus. Erőforrás-szintű diagnosztikai naplók szükséges nincs ügynök és a rögzítési erőforrás-specifikus adatok az Azure platformról magát. Vendég operációs rendszer szintű diagnosztikai naplók az operációs rendszer és a virtuális gépen futó alkalmazások adatait rögzíti.

![Diagnosztikai naplózás tárolási, az Event Hubs vagy Naplóelemzési](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Mi az Azure diagnosztikai naplók által naplózott?

* Minden hitelesített háttér kérések (TCP/REST) keresztül minden API-k naplózása, beleértve a hozzáférési engedélyeket, rendszerhibákból vagy hibás kérésekből adótó sikertelen kérelmeket is. A felhasználó által kezdeményezett Graph Cassandra és tábla API kérelmek támogatása jelenleg nem érhetők el.
* Műveletek az adatbázishoz, többek között CRUD-műveleteknek a dokumentumok, tárolók és adatbázisokat.
* Műveletek a kulcsait, többek között létrehozása, módosítása vagy törlése a kulcsokat.
* A 401-es választ eredményező, nem hitelesített kérelmek. Például kérelmek száma, amelyek nem rendelkeznek tulajdonosi jogkivonattal, helytelen formátumúak vagy lejártak vagy érvénytelen a jogkivonatuk.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Kapcsolja be a naplózás az Azure-portálon

Diagnosztikai naplózás engedélyezése, rendelkeznie kell a következőket:

* Egy meglévő Azure Cosmos DB fiók, adatbázis, és a tároló. Ezek az erőforrások létrehozásának, lásd: [adatbázisfiók létrehozása az Azure portál használatával](create-sql-api-dotnet.md#create-a-database-account), [Azure CLI minták](cli-samples.md), vagy [PowerShell-példák](powershell-samples.md).

Az Azure portálon diagnosztikai naplózás engedélyezése, tegye a következőket:

1. Az a [Azure-portálon](https://portal.azure.com), az Azure Cosmos DB a fiókhoz, válasszon **diagnosztikai naplók** a bal oldali navigációs, és válassza a **a diagnosztika bekapcsolásához**.

    ![Az Azure-portálon az Azure Cosmos DB diagnosztikai naplózás bekapcsolása](./media/logging/turn-on-portal-logging.png)

2. Az a **diagnosztikai beállítások** lapon esetén tegye a következőket: 

    * **Név**: Adjon meg egy nevet a naplók létrehozásához.

    * **Archív tárfiókba**: használja ezt a beállítást, meglévő tárfiókot csatlakozni kell. Új tárfiók létrehozása a portálon: [hozzon létre egy tárfiókot](../storage/common/storage-create-storage-account.md) és kövesse az utasításokat az Azure Resource Manager, az általános célú fiók létrehozásához. Ezt követően térjen vissza erre a lapra, jelölje be a tárfiók a portálon. Az újonnan létrehozott tárfiók megjelenik a legördülő menü néhány percig is eltarthat.
    * **Az eseményközpontok felé adatfolyam**: használja ezt a beállítást, egy meglévő Event Hubs névtér és esemény-központot csatlakozni kell. Az Event Hubs névtér létrehozásához lásd: [az Event Hubs névtér és az eseményközpont létrehozása az Azure portál használatával](../event-hubs/event-hubs-create.md). Ezt követően térjen vissza erre a lapra, a portálon, és válassza ki az Event Hubs-névteret és a házirend nevét.
    * **Naplóelemzési küldése**: használja ezt a beállítást, vagy használjon meglévő munkaterülethez vagy hozzon létre egy új Naplóelemzési munkaterület lépéseit követve [hozzon létre egy új munkaterületet](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) a portálon. A naplók megtekintése a Naplóelemzési kapcsolatos további információkért lásd: [Naplóelemzési a naplók megtekintése](#view-in-loganalytics).
    * **Naplófájl DataPlaneRequests**: válassza ezt a lehetőséget, hogy naplózza a háttér-kérelmeket platformról az alapul szolgáló Azure Cosmos DB elosztott SQL, a Graph, a MongoDB, a Cassandra és a tábla API fiókok. Ha egy tárfiókkal van archiválás, válassza a diagnosztikai naplók megőrzési időtartama. Nincsenek naplói automatikus törlése a megőrzési időszak lejárta után.
    * **Naplófájl MongoRequests**: válassza ezt a lehetőséget, hogy naplózza a kérelmeket a felhasználó által kezdeményezett, de a MongoDB API-fiókok Azure Cosmos DB előtérből érkező. Ha egy tárfiókkal van archiválás, válassza a diagnosztikai naplók megőrzési időtartama. Nincsenek naplói automatikus törlése a megőrzési időszak lejárta után.
    * **Metrika kérelmek**: válassza ezt a beállítást, a részletes adatok tárolására [Azure metrikák](../monitoring-and-diagnostics/monitoring-supported-metrics.md). Ha egy tárfiókkal van archiválás, válassza a diagnosztikai naplók megőrzési időtartama. Nincsenek naplói automatikus törlése a megőrzési időszak lejárta után.

3. Kattintson a **Mentés** gombra.

    Ha hibaüzenet jelenik meg, amely szerint "nem sikerült frissíteni a diagnosztikai \<munkaterület neve >. Az előfizetés \<előfizetés-azonosító > nem regisztrált microsoft.insights, "hajtsa végre a [hibaelhárítása Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) utasításokat követve a fiók regisztrálása, és ismételje meg ezt az eljárást.

    Ha szeretné módosítani a diagnosztikai naplók mentési módjának bármikor a jövőben, térjen vissza ezen a lapon, a fiókja a diagnosztikai beállításainak módosítása.

## <a name="turn-on-logging-by-using-azure-cli"></a>Naplózás bekapcsolása az Azure parancssori felület használatával

Azure parancssori felület használatával metrikák és diagnosztikai naplózás engedélyezéséhez használja a következő parancsokat:

- Ahhoz, hogy a tárfiók diagnosztikai naplók tárolására, az alábbi parancsot használja:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   A `resourceId` a Azure Cosmos DB-fiók neve. A `storageId` , amelyre szeretné elküldeni a naplókat a tárfiók neve.

- Adatfolyamként való küldése a diagnosztikai naplók eseményközpontokba való engedélyezéséhez az alábbi parancsot használja:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   A `resourceId` a Azure Cosmos DB-fiók neve. A `serviceBusRuleId` egy karakterlánc, ezt a formátumot:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Ahhoz, hogy a küldő diagnosztikai naplókat a Naplóelemzési munkaterületet, az alábbi parancsot használja:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Ezek a paraméterek ahhoz, hogy több kimenet beállításai kombinálhatja.

## <a name="turn-on-logging-by-using-powershell"></a>Naplózás bekapcsolása a PowerShell használatával

Diagnosztikai naplózás bekapcsolása a PowerShell használatával, van szüksége az Azure Powershell legalább 1.0.1-es verziója.

Az Azure PowerShell telepítésérről és az Azure-előfizetéssel való társításáról további információt [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása) című cikkben találhat.

Ha már telepítve van az Azure PowerShell és a PowerShell-konzol típus nem tudja, melyik verziót `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Csatlakozás az előfizetésekhez
Indítson el egy Azure PowerShell-munkamenetet, és jelentkezzen be az Azure-fiókjába az alábbi paranccsal:  

```powershell
Connect-AzureRmAccount
```

Az előugró böngészőablakban adja meg az Azure-fiókja felhasználónevét és jelszavát. Az Azure PowerShell beolvassa az összes olyan előfizetést, amely rendelve, ezt a fiókot, és alapértelmezés szerint az elsőt használja.

Ha egynél több előfizetéssel rendelkezik, akkor előfordulhat, hogy adja meg az adott előfizetés, amely az az Azure key vault létrehozásához használt. A fiókhoz tartozó előfizetések megtekintéséhez írja be a következő parancsot:

```powershell
Get-AzureRmSubscription
```

Ezt követően az az Azure Cosmos DB fiókkal, amely éppen naplózás tartozó előfizetés megadásához írja be a következő parancsot:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Ha a fiókjához társított egynél több előfizetéssel rendelkezik, fontos adja meg a használni kívánt előfizetést.
>
>

Azure PowerShell konfigurálásával kapcsolatos további információkért lásd: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Új tárfiók létrehozása a naplóknak
Bár meglévő tárfiókot a naplók ebben az oktatóanyagban, azt hozzon létre egy új tárfiókot, Azure Cosmos DB naplókat külön. Kényelmi célokat szolgál, a nevű változóban tároljuk a tárfiókadatok **sa**.

Az egyszerű kezelés, az ebben az oktatóanyagban ugyanabban az erőforráscsoportban, amely tartalmazza az Azure Cosmos DB adatbázisban vesszük. Helyettesítse a saját értékeit a **ContosoResourceGroup**, **contosocosmosdblogs**, és **északi középső Régiójában** paraméterek kulcsnevekkel:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Ha meglévő tárfiók használata mellett dönt, a fiók az Azure Cosmos DB előfizetés ugyanahhoz az előfizetéshez kell használja. A fiókot kell használnia a Resource Manager üzembe helyezési modellel, nem pedig a klasszikus üzembe helyezési modellben.
>
>

### <a id="identify"></a>A naplók Azure Cosmos DB-fiók azonosítása
A Azure Cosmos DB-fiók neve nevű változó beállítása **fiók**, ahol **ResourceName** Azure Cosmos DB fiók neve.

```powershell
$account = Get-AzureRmResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Naplózás engedélyezése
Azure Cosmos adatbázis naplózásának engedélyezéséhez használja a `Set-AzureRmDiagnosticSetting` parancsmagot az új tárfiókot, Azure Cosmos DB fiók és a naplózás engedélyezéséhez a kategória-változók. A következő parancsot, majd állítsa be a **-engedélyezve** jelzőt **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
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

A parancs kimenetében ellenőrzi, hogy ha információ mentése folyamatban van a tárfiók naplózás engedélyezve van az adatbázis számára.

Szükség esetén is beállíthatja az adatmegőrzési a naplók úgy, hogy a régebbi naplóit. A megőrzési házirend beállításához például a **- RetentionEnabled** jelző beállítása **$true**. Állítsa be a **- RetentionInDays** paramétert **90** , hogy a naplófájlok 90 napnál régebbi automatikusan törlődnek.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>A naplók elérése
Azure Cosmos-adatbázis, ha a naplókban a **DataPlaneRequests** kategória tárolódnak a **insights-logs--vezérlősík-kérelmek** a tárfiókot, amely a megadott tárolóhoz. 

Először hozzon létre egy változót a tároló nevéhez. A változó használja a rendszer az előírt lépéseket keresztül.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Minden ebben a tárolóban lévő blobok, írja be a következőt:

```powershell
Get-AzureStorageBlob -Container $container -Context $sa.Context
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

Mivel a kimenetből látható, a blobok az alábbi elnevezési: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

A dátum- és időértékek az UTC hivatkozási időzónát használják.

Ugyanazt a tárfiókot használható gyűjtött naplók tárolására több erőforrást, mert eléréséhez és az adott szükséges blobokat töltse le a teljes erőforrás-azonosító használhatja a blob neve. Nézzük meg, mielőtt azt tér ki az összes a blobok letöltése.

Elsőként hozzon létre egy mappát, amelybe letölti a blobokat. Példa:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Ezt követően listájának az összes, a blobok:  

```powershell
$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Ebben a listában keresztül átadhatja a `Get-AzureStorageBlobContent` parancs letölti a blobokat a célként megadott mappába:

```powershell
$blobs | Get-AzureStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

A második parancs futtatásakor a **/** a blob nevének elválasztó hoz létre egy teljes mapparendszert a rendeltetési mappára. A gyökérmappa-szerkezetében töltse le és fájlként a blobok tárolására szolgál.

A blobok egyenkénti letöltéséhez használjon helyettesítő elemeket. Példa:

* Ha több adatbázist, és szeretné, hogy csak egy adatbázis nevű naplók letöltéséhez **CONTOSOCOSMOSDB3**, használja a parancsot:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Ha több erőforrás-csoportok és szeretne egy erőforráscsoporthoz tartozó naplókat letölteni, használja a parancs `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Ha azt szeretné, töltse le a naplók július 2017 hónap, a paranccsal `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

A következő parancsok futtatásával is:

* Az adatbázis erőforrás diagnosztikai beállítások állapotának lekérése, a parancs segítségével `Get-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`.
* A naplózás letiltása a **DataPlaneRequests** kategória az adatbázis-fiók erőforrás paranccsal `Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`.


A BLOB visszaadott minden ezeket a lekérdezéseket szövegként tárolt és formázott JSON blob, az alábbi kódban látható módon:

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

Minden JSON BLOB adatokkal kapcsolatos további tudnivalókért lásd: [az Azure Cosmos DB naplók értelmezése](#interpret).

## <a name="manage-your-logs"></a>A naplók kezelése

Diagnosztikai naplók elérhetővé válnak a fiókjában két órán keresztül, hogy az Azure Cosmos DB művelet történt. A tárfiók naplófájljait Önnek kell kezelnie:

* Azure szabványos hozzáférés ellenőrzési módszereket használja a naplókhoz, és korlátozzák a ki férhet hozzá.
* Törölje azokat a naplókat, amelyeket nem kíván megőrizni a tárfiókban.
* A megőrzési idő tárfiókba archivált vezérlősík kérelmek van konfigurálva, a portál amikor a **napló DataPlaneRequests** beállítás be van jelölve. Ez a beállítás módosításához lásd [kapcsolja be a naplózás az Azure portálon](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-log-analytics"></a>A Naplóelemzési naplók megtekintése

Ha bejelölte a **küldeni a Naplóelemzési** lehetőséget, ha bekapcsolta a diagnosztikai naplózás diagnosztikai adatokat a gyűjteményből továbbíthatja a rendszer Naplóelemzési két órán belül. Megtekintésekor Naplóelemzési naplózás bekapcsolása után azonnal, akkor nem látható adatokat. Csak két óra várakozás után próbálja újra. 

A naplók megtekintéséhez előtt ellenőrizze, és tekintse meg, ha a munkaterületet frissítve lett az új Naplóelemzési lekérdezési nyelv használatával. Ellenőrzéséhez nyissa meg a [Azure-portálon](https://portal.azure.com), jelölje be **Naplóelemzési** a bal oldali, majd válassza ki a munkaterület neve a következő ábrán látható módon. A **OMS-munkaterület** lap jelenik meg:

![Naplóelemzési az Azure-portálon](./media/logging/azure-portal.png)

Ha a következő üzenet jelenik meg a a **OMS-munkaterület** lap, a munkaterület még nem frissítették, és az új nyelv használatával. Új lekérdezés nyelvének frissítésével kapcsolatos további információkért lásd: [Azure Naplóelemzési munkaterület frissítése új naplófájl-keresési](../log-analytics/log-analytics-log-search-upgrade.md). 

![A Naplóelemzési frissítése message](./media/logging/upgrade-notification.png)

A Naplóelemzési a diagnosztikai adatok megtekintéséhez nyissa meg a **naplófájl-keresési** lap bal oldali menüjében vagy a **felügyeleti** terület az oldal, a következő ábrán látható módon:

![Napló keresési beállítások az Azure-portálon](./media/logging/log-analytics-open-log-search.png)

Most, hogy az adatgyűjtés engedélyezését, futtassa a következő naplófájl-keresési példa az új lekérdezési nyelv használatával tekintse meg a 10 legutóbbi naplókat `AzureDiagnostics | take 10`.

![Minta naplóban keresse meg a 10 legutóbbi naplók](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Lekérdezések

Az alábbiakban néhány további lekérdezések, amelyek adhatja meg azokat a **naplófájl-keresési** jelölőnégyzetet, hogy az Azure Cosmos DB tárolók figyeléséhez nyújt segítséget. Ezeket a lekérdezéseket dolgozni a [új nyelvi](../log-analytics/log-analytics-log-search-upgrade.md). 

Az egyes napló keresés által visszaadott adatokat szerinti kapcsolatos további tudnivalókért lásd: [az Azure Cosmos DB naplók értelmezése](#interpret).

* Lekérdezni a diagnosztikai naplók az Azure Cosmos-Adatbázisból egy adott időszakra vonatkozóan:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Az lekérdezése a 10 legtöbb nemrég naplózott események:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Az összes műveletet, a művelet típusa szerint csoportosítva lekérdezése:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* A lekérdezés minden műveletnél szerint csoportosítva **erőforrás**:

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Az összes felhasználói tevékenységet, erőforrás szerint csoportosítva lekérdezése:

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
    > [!NOTE]
    > Ez a parancs tevékenységnapló nem diagnosztikai naplófájl szolgál.

* A lekérdezéshez, amelynek műveletek tovább tart, mint 3 ezredmásodperc:

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Lekérdezni, mely ügynök fut a műveletek:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* A lekérdezni a hosszú ideig futó műveletek végrehajtásakor volt:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

Az új naplófájl-keresési nyelv használatával kapcsolatos további információkért lásd: [megértése napló megkeresi a Naplóelemzési](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>A naplók értelmezése

Diagnosztikai adatok az Azure Storage és Naplóelemzési tárolt hasonló sémát használja. 

Az alábbi táblázatban minden naplóbejegyzés tartalmát.

| Az Azure Storage mező vagy tulajdonság | Napló Analytics tulajdonság | Leírás |
| --- | --- | --- |
| **Idő** | **TimeGenerated** | A dátum és idő (UTC), ha a művelet történt. |
| **resourceId** | **Erőforrás** | A Azure Cosmos DB fiók, amelynek a naplók engedélyezve vannak.|
| **Kategória** | **Kategória** | Az Azure Cosmos DB naplók **DataPlaneRequests** érték csak akkor áll rendelkezésre. |
| **OperationName** | **OperationName** | A művelet neve. Ez az érték a következő műveletek valamelyike lehet: frissítés, Olvasás, ReadFeed, törlése, a név felülírandó, végrehajtás, SqlQuery, lekérdezés, JSQuery, Head, HeadFeed vagy Upsert létrehozása.   |
| **Tulajdonságok** | n/a | Ez a mező tartalmát az alábbi sorokat ismerteti. |
| **activityId** | **activityId_g** | A naplózott műveletnek egyedi GUID azonosítója. |
| **userAgent** | **userAgent_s** | A kérelem végző felhasználói ügynök meghatározó karakterlánc. A formátum: {felhasználói ügynök neve} / {version}.|
| **resourceType** | **ResourceType** | Elért erőforrás típusa. Ez az érték a következő típusok egyike lehet: adatbázis, gyűjtemény, a dokumentum, melléklet, felhasználó, engedéllyel, tárolt eljárás, eseményindító, UserDefinedFunction vagy ajánlat. |
| **statusCode** | **statusCode_s** | A válasz a művelet állapotát. |
| **requestResourceId** | **ResourceId** | Az erőforrás-azonosítója, amely a kérelem vonatkozik. Az érték databaseRid, collectionRid vagy documentRid végrehajtott művelettől függően előfordulhat, hogy mutasson.|
| **clientIpAddress** | **clientIpAddress_s** | Az ügyfél IP-címét. |
| **requestCharge** | **requestCharge_s** | A művelet által használt RUs száma |
| **collectionRid** | **collectionId_s** | A gyűjtemény egyedi azonosítója.|
| **Időtartam** | **duration_s** | A művelet, a ticks időtartama. |
| **requestLength** | **requestLength_s** | A kérést, bájtban hosszát. |
| **responseLength** | **responseLength_s** | A válasz bájtban hosszát.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Ez az érték megadása kötelező Ha [erőforrás jogkivonatok](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) használnak a hitelesítéshez. Az érték az erőforrás-azonosító, a felhasználó mutat. |

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan engedélyezze a naplózást, valamint a metrikák és a napló kategóriák különböző Azure-szolgáltatás által támogatott, olvassa el is a [áttekintése a Microsoft Azure-ban mérőszámok](../monitoring-and-diagnostics/monitoring-overview-metrics.md) és [áttekintés az Azure diagnosztikai naplók ](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) cikkeket.
- Ezek a cikkek az event hubs megismeréséhez olvassa el:
   - [Mi az Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Olvasási [metrikák és diagnosztikai naplók letöltése Azure Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
- Olvasási [megértése napló megkeresi a Naplóelemzési](../log-analytics/log-analytics-log-search-new.md).
