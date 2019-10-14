---
title: Diagnosztikai naplózás a Azure Cosmos DBban
description: Ismerje meg a Azure Cosmos DB tárolt adatainak naplózásának és figyelésének különböző módjait.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 1e9f852d01d60ead9979b6b1190e285b35d5c312
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72294040"
---
# <a name="diagnostic-logging-in-azure-cosmos-db"></a>Diagnosztikai naplózás a Azure Cosmos DBban 

Miután elkezdte egy vagy több Azure Cosmos-adatbázis használatát, érdemes figyelnie, hogyan és mikor férhet hozzá az adatbázisaihoz. Ez a cikk áttekintést nyújt az Azure platformon elérhető naplókról. Megtudhatja, hogyan engedélyezheti a diagnosztikai naplózást figyelési célokra a naplók [Azure Storage](https://azure.microsoft.com/services/storage/)-ba való küldéséhez, a naplók továbbításához az [Azure-Event Hubs](https://azure.microsoft.com/services/event-hubs/), valamint a naplók [Azure monitor naplókba](https://azure.microsoft.com/services/log-analytics/)való exportálásának módját.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="logs-available-in-azure"></a>Az Azure-ban elérhető naplók

Mielőtt megbeszéljük, hogyan figyelheti Azure Cosmos DB-fiókját, a naplózással és a figyeléssel kapcsolatos néhány dolog tisztázása. Az Azure platform különböző típusú naplókat használ. Vannak [Azure-Tevékenységnaplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [Azure diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), Azure- [mérőszámok](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), események, szívverés-figyelés, operatív naplók stb. Rengeteg napló van. A naplókban [Azure monitor naplók](https://azure.microsoft.com/services/log-analytics/) teljes listáját láthatja a Azure Portal. 

Az alábbi képen az elérhető Azure-naplók különböző típusa látható:

![Különböző típusú Azure-naplók](./media/logging/azurelogging.png)

A rendszerképben a **számítási erőforrások** azokat az Azure-erőforrásokat képviselik, amelyekhez hozzáférhet a Microsoft vendég operációs rendszerhez. Például az Azure Virtual Machines, a virtuálisgép-méretezési csoportok, a Azure Container Service stb.) számítanak számítási erőforrásnak. A számítási erőforrások tevékenységi naplókat, diagnosztikai naplókat és alkalmazási naplókat hoznak. További információért tekintse meg az [Azure-beli monitorozási információk forrásait](../azure-monitor/platform/data-sources.md) .

A **nem számítási erőforrások** olyan erőforrások, amelyekben nem férhet hozzá az alapul szolgáló operációs rendszerhez, és közvetlenül az erőforrással dolgozhat. Például hálózati biztonsági csoportok, Logic Apps és így tovább. Azure Cosmos DB egy nem számítási erőforrás. A naplókat megtekintheti a nem számítási erőforrásokhoz a tevékenység naplójában, vagy engedélyezheti a diagnosztikai naplók lehetőséget a portálon. További információért tekintse meg Azure Monitor cikkben [található adatforrásokat](../azure-monitor/platform/data-sources.md) .

A tevékenység naplója a Azure Cosmos DB előfizetési szintjén rögzíti a műveleteket. A rendszer naplózza az olyan műveleteket, mint a Listkeys műveletének beolvasása, az írási DatabaseAccounts és egyebek. A diagnosztikai naplók részletesebb naplózást biztosítanak, és lehetővé teszik a DataPlaneRequests (létrehozás, olvasás, lekérdezés stb.) és a MongoRequests naplózását.


Ebben a cikkben az Azure-tevékenység naplóját, az Azure diagnosztikai naplóit és az Azure-metrikákat vesszük figyelembe. Mi a különbség a három napló között? 

### <a name="azure-activity-log"></a>Azure-tevékenység naplója

Az Azure-tevékenység naplója egy előfizetési napló, amely betekintést nyújt az Azure-ban bekövetkezett előfizetési szintű eseményekre. A műveletnapló a felügyeleti kategória alá tartozó előfizetések esetében szabályozza a vezérlési sík eseményeit. A tevékenység naplójának segítségével meghatározhatja a "mit, ki és mikor" kifejezést az előfizetésben lévő erőforrásokra vonatkozó írási műveletekhez (PUT, POST, DELETE). Emellett megismerheti a művelet állapotát és az egyéb kapcsolódó tulajdonságokat is. 

A tevékenység naplója különbözik a diagnosztikai naplóktól. A tevékenység naplója az erőforráson kívülről (a _vezérlő síkja_) származó műveletekre vonatkozó információkat biztosít. A Azure Cosmos DB kontextusban a vezérlési sík műveletei közé tartozik a tároló létrehozása, a kulcsok listázása, a kulcsok törlése, a listázási adatbázis stb. A diagnosztikai naplókat egy erőforrás bocsátja ki, és információt nyújt az adott erőforrás működéséről (az _adatsíkon_). Néhány példa a diagnosztikai naplóban található adatsík-műveletekre: delete, INSERT és ReadFeed.

A Tevékenységnaplók (vezérlési sík műveletei) lehetnek gazdagabb jellegűek, és tartalmazhatják a hívó, a hívó IP-címe, az erőforrás neve, a művelet neve és a TenantId teljes e-mail-címét. A tevékenység naplója több [adatkategóriát](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) tartalmaz. A kategóriák sémák kapcsolatos részletes információkért lásd: [Azure Activity napló esemény sémája](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema). A diagnosztikai naplók azonban lehetnek korlátozó jellegűek, mivel a személyes adatok gyakran el vannak távolítva ezekből a naplókból. Lehet, hogy a hívó IP-címe, de az utolsó octant el lesz távolítva.

### <a name="azure-metrics"></a>Azure-metrikák

Az [Azure-metrikák](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) a legtöbb Azure-erőforrás által kibocsátott Azure telemetria-adatok (más néven _teljesítményszámlálók_) legfontosabb típusai. A metrikák lehetővé teszik az adatátvitelsel, a tárolással, a következetességgel, a rendelkezésre állással és a Azure Cosmos DB erőforrások késésével kapcsolatos információk megtekintését. További információ: [monitorozás és hibakeresés a metrikákkal a Azure Cosmos db](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Azure-beli diagnosztikai naplók

Az Azure diagnosztikai naplókat egy erőforrás bocsátja ki, és az adott erőforrás működésével kapcsolatos gazdag és gyakori információkat biztosít. Ezeket a naplókat a rendszer kérés szerint rögzíti. A naplók tartalma az erőforrástípus alapján változik. Az erőforrás-szintű diagnosztikai naplók szintén eltérnek a vendég operációsrendszer-szintű diagnosztikai naplóktól. A vendég operációs rendszer diagnosztikai naplóit egy virtuális gépen vagy más támogatott erőforrás-típuson belül futó ügynök gyűjti. Az erőforrás szintű diagnosztikai naplók esetében nem szükséges az ügynök és az erőforrás-specifikus adatok rögzítése az Azure platformon. A vendég operációsrendszer-szintű diagnosztikai naplók az operációs rendszer és a virtuális gépen futó alkalmazások adatait rögzítik.

![Diagnosztikai naplózás a tárolási, Event Hubs vagy Azure Monitor naplókba](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Mit naplóz az Azure diagnosztikai naplók?

* Az összes API összes hitelesített háttérbeli kérelme (TCP/REST) naplózva van, beleértve a sikertelen kérelmeket a hozzáférési engedélyek, a rendszerhibák vagy a helytelen kérelmek miatt. A felhasználó által kezdeményezett Graph-, Cassandra-és Table API-kérelmek támogatása jelenleg nem érhető el.
* Maga az adatbázison végzett műveletek, beleértve az összes dokumentumra, tárolóra és adatbázisra vonatkozó SZIFILISZi műveleteket.
* A fiókok kulcsain végrehajtott műveletek, beleértve a kulcsok létrehozását, módosítását és törlését.
* A 401-es választ eredményező, nem hitelesített kérelmek. Például a nem rendelkezik tulajdonosi jogkivonattal vagy helytelen formátumú vagy lejárt kérelmekkel, vagy érvénytelen tokenrel rendelkezik.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>A naplózás bekapcsolása a Azure Portalban

A következő lépésekkel engedélyezheti a diagnosztikai naplózást a Azure Portalban:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

1. Navigáljon az Azure Cosmos-fiókjához. Nyissa meg a **diagnosztikai beállítások** ablaktáblát, majd kattintson a **diagnosztikai beállítás hozzáadása** lehetőségre.

    ![Azure Cosmos DB diagnosztikai naplózásának bekapcsolása a Azure Portal](./media/logging/turn-on-portal-logging.png)

1. A **diagnosztikai beállítások** lapon töltse ki az űrlapot a következő részletekkel: 

    * **Név**: adjon meg egy nevet a létrehozandó naplók számára.

    * A naplókat a következő szolgáltatásokban lehet tárolni:

      * **Archiválás egy Storage-fiókba**: Ha ezt a beállítást szeretné használni, egy meglévő Storage-fiókra van szüksége a kapcsolódáshoz. Ha új Storage-fiókot szeretne létrehozni a portálon, tekintse meg a [Storage-fiók létrehozása](../storage/common/storage-create-storage-account.md) című cikket. Ezután térjen vissza a portál Azure Cosmos db diagnosztikai beállítások ablaktáblájához, és válassza ki a Storage-fiókját. Eltarthat néhány percig, amíg az újonnan létrehozott Storage-fiókok megjelennek a legördülő menüben.

      * **Stream az Event hub**-ba: Ha ezt a beállítást szeretné használni, egy meglévő Event Hubs névtérre és az Event hub-ra van szükség a kapcsolódáshoz. Event Hubs névtér létrehozásához tekintse meg [a Event Hubs névtér és az Event hub létrehozása a Azure Portal használatával](../event-hubs/event-hubs-create.md)című témakört. Ezután térjen vissza ehhez az oldalhoz a portálon, és válassza ki az Event hub névterét és a házirend nevét.

      * **Küldés log Analyticsba**: Ha ezt a beállítást használja, használjon egy meglévő munkaterületet, vagy hozzon létre egy új log Analytics munkaterületet a portálon [egy új munkaterület létrehozásához](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace) szükséges lépéseket követve. 

   * A következő adatnaplózási műveleteket végezheti el:

      * **DataPlaneRequests**: válassza ezt a lehetőséget, ha a háttérbeli kérelmeket minden olyan API-hoz naplózni szeretné, amely tartalmazza az SQL, a Graph, a MongoDB, a Cassandra és a Table API fiókokat Azure Cosmos db. Ha egy Storage-fiókba végez archiválást, kiválaszthatja a diagnosztikai naplók megőrzési időtartamát. A rendszer automatikusan törli a naplókat a megőrzési időszak lejárta után. A következő JSON-adatok például a DataPlaneRequests használatával naplózott részletek kimenete. A fontos tulajdonságok a következők: Requestcharge, statusCode, clientIPaddress és partitionID:

       ```
       { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
       ```

      * **MongoRequests**: ezzel a beállítással a felhasználó által kezdeményezett kérelmeket naplózhatja az előtérből a MongoDB API-ra irányuló Azure Cosmos db kérések kiszolgálásához. A MongoDB-kérelmek megjelennek a MongoRequests és a DataPlaneRequests is. Ha egy Storage-fiókba végez archiválást, kiválaszthatja a diagnosztikai naplók megőrzési időtartamát. A rendszer automatikusan törli a naplókat a megőrzési időszak lejárta után. A következő JSON-adatok például a MongoRequests használatával naplózott részletek kimenete. A fontos tulajdonságok a következők: Requestcharge, műveleti kód:

       ```
       { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
       ```

      * **QueryRuntimeStatistics**: válassza ezt a lehetőséget a végrehajtott lekérdezési szöveg naplózásához.  A következő JSON-adatok például a QueryRuntimeStatistics használatával naplózott részletek kimenete:

       ```
       { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
       ```

      * **PartitionKeyStatistics**: Ez a napló a partíciós kulcsok statisztikáit jelenti. A statisztikák jelenleg a partíciós kulcsok tárolási méretével (KB) jelennek meg. A rendszer kibocsátja a naplót a legtöbb adattárolást elfoglaló első három partíciós kulcson.

       ```
       { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
       ```

      * **Metrikai kérelmek**: válassza ezt a lehetőséget, ha részletes adatokat szeretne tárolni az [Azure-mérőszámokban](../azure-monitor/platform/metrics-supported.md). Ha egy Storage-fiókba végez archiválást, kiválaszthatja a diagnosztikai naplók megőrzési időtartamát. A rendszer automatikusan törli a naplókat a megőrzési időszak lejárta után.

3. Kattintson a **Mentés** gombra.

    Ha olyan hibaüzenetet kap, amely szerint a "nem sikerült frissíteni a \<workspace > diagnosztikát. Az előfizetés \<subscription azonosítója > nincs regisztrálva a Microsoft. ininsights használatára, kövesse a következő témakört: a fiók regisztrálásához szükséges [Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) utasítások követése, majd próbálja megismételni a műveletet.

    Ha módosítani szeretné a diagnosztikai naplók mentésének módját a jövő bármely pontjára, térjen vissza erre a lapra, és módosítsa a fiókja diagnosztikai naplójának beállításait.

## <a name="turn-on-logging-by-using-azure-cli"></a>Naplózás bekapcsolása az Azure CLI használatával

A metrikák és diagnosztikai naplózás Azure CLI használatával történő engedélyezéséhez használja a következő parancsokat:

- A diagnosztikai naplók tárolási fiókban való tárolásának engedélyezéséhez használja a következő parancsot:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name DiagStorage --resource <resourceId> --storage-account <storageAccountName> --logs '[{"category": "QueryRuntimeStatistics", "enabled": true, "retentionPolicy": {"enabled": true, "days": 0}}]'
   ```

   A `resource` a Azure Cosmos DB fiók neve. Az erőforrás formátuma "/Subscriptions/`<subscriptionId>`/resourceGroups/`<resource_group_name>`/Providers/Microsoft. DocumentDB/databaseAccounts/< Azure_Cosmos_account_name >". a `storage-account` annak a Storage-fióknak a neve, amelyre el szeretné küldeni a naplókat. A többi naplót úgy is naplózhatja, hogy a "MongoRequests" vagy a "DataPlaneRequests" kategória-paraméter értékeit frissíti. 

- A diagnosztikai naplók Event hub-ba való továbbításának engedélyezéséhez használja a következő parancsot:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --event-hub-rule <eventHubRuleID> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

   A `resource` a Azure Cosmos DB fiók neve. A `event-hub-rule` az Event hub-szabály azonosítója. 

- A diagnosztikai naplók Log Analytics munkaterületre való küldésének engedélyezéséhez használja a következő parancsot:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --workspace <resource id of the log analytics workspace> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

Ezeket a paramétereket kombinálva több kimeneti beállítást is engedélyezhet.

## <a name="turn-on-logging-by-using-powershell"></a>A naplózás bekapcsolása a PowerShell használatával

Ha a PowerShell használatával szeretné bekapcsolni a diagnosztikai naplózást, szüksége lesz az Azure PowerShell-re a 1.0.1 minimális verziójával.

Az Azure PowerShell telepítésérről és az Azure-előfizetéssel való társításáról további információt [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása) című cikkben találhat.

Ha már telepítette a Azure PowerShellt, és nem ismeri a verziót, a PowerShell-konzolon írja be a következőt: `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Csatlakozás az előfizetésekhez
Indítson el egy Azure PowerShell-munkamenetet, és jelentkezzen be az Azure-fiókjába az alábbi paranccsal:  

```powershell
Connect-AzAccount
```

Az előugró böngészőablakban adja meg az Azure-fiókja felhasználónevét és jelszavát. Azure PowerShell lekéri a fiókhoz társított összes előfizetést, és alapértelmezés szerint az elsőt használja.

Ha egynél több előfizetéssel rendelkezik, előfordulhat, hogy meg kell adnia az Azure Key Vault létrehozásához használt konkrét előfizetést. A fiókhoz tartozó előfizetések megtekintéséhez írja be a következő parancsot:

```powershell
Get-AzSubscription
```

Ezt követően adja meg a naplózni kívánt Azure Cosmos DB fiókhoz tartozó előfizetést, írja be a következő parancsot:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Ha egynél több előfizetése van társítva a fiókjához, fontos megadnia a használni kívánt előfizetést.
>
>

További információ a Azure PowerShell konfigurálásáról: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

### <a id="storage"></a>Új tárfiók létrehozása a naplóknak
Habár meglévő Storage-fiókot is használhat a naplókhoz, ebben az oktatóanyagban egy új Storage-fiókot hozunk létre, amely Azure Cosmos DB naplókhoz van hozzárendelve. A kényelem érdekében a Storage-fiók adatait egy **sa**nevű változóban tároljuk.

A könnyebb kezelhetőség érdekében ebben az oktatóanyagban ugyanazt az erőforráscsoportot használjuk, amely az Azure Cosmos-adatbázist tartalmazza. Helyettesítse be a **ContosoResourceGroup**, a **CONTOSOCOSMOSDBLOGS**és az **USA északi középső** paramétereinek értékeit, a megfelelő módon:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Ha úgy dönt, hogy meglévő Storage-fiókot használ, a fióknak ugyanazt az előfizetést kell használnia, mint a Azure Cosmos DB előfizetése. A fióknak a klasszikus üzemi modell helyett a Resource Manager-alapú üzemi modellt is használnia kell.
>
>

### <a id="identify"></a>A naplók Azure Cosmos DB fiókjának azonosítása
Állítsa a Azure Cosmos DB fiók nevét egy **fiók**nevű változóra, ahol a **resourcename** a Azure Cosmos db fiók neve.

```powershell
$account = Get-AzResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Naplózás engedélyezése
A Azure Cosmos DB naplózásának engedélyezéséhez használja a `Set-AzDiagnosticSetting` parancsmagot az új Storage-fiókhoz, Azure Cosmos DB a fiókhoz és a naplózás engedélyezéséhez. Futtassa a következő parancsot, és állítsa az **-enabled** jelzőt **$truere**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

A parancs kimenete az alábbi példához hasonló:

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

A parancs kimenete megerősíti, hogy a naplózás már engedélyezve van az adatbázishoz, és a rendszer menti az adatokat a Storage-fiókba.

Igény szerint beállíthatja a naplók adatmegőrzési szabályát is, így a régebbi naplók automatikusan törlődnek. Például állítsa be az adatmegőrzési szabályzatot a **-RetentionEnabled** jelzővel **$true**értékre. Állítsa a **-RetentionInDays** paramétert **90** -re, hogy a 90 napnál régebbi naplók automatikusan törlődjenek.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>A naplók elérése
A **DataPlaneRequests** kategóriájának Azure Cosmos db naplóit a megadott Storage-fiókban található elemzések **– naplók-DataPlaneRequests** tároló tárolja. 

Először hozzon létre egy változót a tároló nevéhez. A változót a rendszer a teljes útmutatóban használja.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

A tároló összes blobjának listázásához írja be a következőt:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

A parancs kimenete az alábbi példához hasonló:

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

Ahogy az ebből a kimenetből látható, a Blobok a névadási konvenciót követik: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

A dátum- és időértékek az UTC hivatkozási időzónát használják.

Mivel ugyanaz a Storage-fiók használható több erőforráshoz tartozó naplók összegyűjtésére, a blob nevében lévő teljes erőforrás-AZONOSÍTÓval elérheti és letöltheti a szükséges blobokat. Mielőtt ezt megtenné, bemutatjuk, hogyan töltheti le az összes blobot.

Elsőként hozzon létre egy mappát, amelybe letölti a blobokat. Példa:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Ezután szerezze be az összes blob listáját:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

A Blobok a célmappába való letöltéséhez a `Get-AzStorageBlobContent` paranccsal áthelyezheti a listát:

```powershell
$blobs | Get-AzStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

A második parancs futtatásakor a blob-nevekben lévő **/ elválasztó** a célmappában teljes mappastruktúrát hoz létre. Ez a mappastruktúrát a Blobok fájlként való letöltésére és tárolására szolgál.

A blobok egyenkénti letöltéséhez használjon helyettesítő elemeket. Példa:

* Ha több adatbázisa van, és csak egy **CONTOSOCOSMOSDB3**nevű adatbázis naplóit szeretné letölteni, használja a parancsot:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Ha több erőforráscsoport van, és csak egy erőforráscsoporthoz szeretné letölteni a naplókat, használja a `-Blob '*/RESOURCEGROUPS/<resource group name>/*'` parancsot:

    ```powershell
    Get-AzStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Ha a 2017-es hónapra vonatkozó összes naplót szeretné letölteni, használja a `-Blob '*/year=2017/m=07/*'` parancsot:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

A következő parancsokat is futtathatja:

* Az adatbázis-erőforrás diagnosztikai beállításainak állapotának lekérdezéséhez használja a következő parancsot: `Get-AzDiagnosticSetting -ResourceId $account.ResourceId`.
* Ha le szeretné tiltani az **DataPlaneRequests** -kategória naplózását az adatbázis-fiók erőforrásához, használja az `Set-AzDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests` parancsot.


Az egyes lekérdezésekben visszaadott Blobok szövegként vannak tárolva, és JSON-blobként vannak formázva, ahogy az a következő kódban látható:

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

Az egyes JSON-blobokban lévő információk megismeréséhez tekintse meg [a Azure Cosmos db naplók értelmezése](#interpret)című témakört.

## <a name="manage-your-logs"></a>Naplók kezelése

A diagnosztikai naplókat a fiókjában a Azure Cosmos DB művelet elkészültének időpontjától számított két órán belül elérhetővé teszik. A tárfiók naplófájljait Önnek kell kezelnie:

* A naplók védelme érdekében szabványos Azure-hozzáférés-vezérlési módszereket használhat, és korlátozhatja, hogy ki férhet hozzá.
* Törölje azokat a naplókat, amelyeket nem kíván megőrizni a tárfiókban.
* A Storage-fiókba archivált adatsík-kérelmek megőrzési időtartamát a portálon lehet konfigurálni a **DataPlaneRequests** beállítás kiválasztásakor. A beállítás módosításához tekintse [meg a Azure Portal naplózásának bekapcsolása](#turn-on-logging-in-the-azure-portal)című témakört.


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-azure-monitor-logs"></a>Naplók megtekintése Azure Monitor naplókban

Ha a **küldés log Analytics** lehetőséget választotta a diagnosztikai naplózás bekapcsolásakor, a tárolóból származó diagnosztikai adatok két órán belül át lesznek továbbítva Azure monitor naplókba. Ha a naplózás bekapcsolását követően azonnal megtekinti Azure Monitor naplókat, akkor nem fog semmilyen információt látni. Várjon két órát, és próbálkozzon újra. 

A naplók megtekintése előtt ellenőrizze, hogy a Log Analytics-munkaterület frissítve lett-e az új Kusto lekérdezési nyelv használatára. Az ellenõrzéshez nyissa meg a [Azure Portal](https://portal.azure.com), válassza a bal szélen **log Analytics munkaterületek** lehetőséget, majd válassza ki a munkaterület nevét a következő képen látható módon. Megjelenik a **log Analytics munkaterület** lap:

![Naplók Azure Monitor a Azure Portal](./media/logging/azure-portal.png)

>[!NOTE]
>Az OMS-munkaterületeket mostantól Log Analytics-munkaterületeknek nevezzük.  

Ha a **log Analytics munkaterület** lapon a következő üzenet jelenik meg, a munkaterület nem lett frissítve az új nyelv használatára. Az új lekérdezési nyelvre való frissítéssel kapcsolatos további információkért lásd: [Azure log Analytics-munkaterület frissítése az új naplók keresésére](../log-analytics/log-analytics-log-search-upgrade.md). 

![Azure Monitor naplók frissítési üzenete](./media/logging/upgrade-notification.png)

Ha Azure Monitor naplókban szeretné megtekinteni a diagnosztikai adatait, nyissa meg az oldal bal oldali menüjéből vagy a lap **felügyeleti** területéről a **napló keresése** lapot, ahogy az a következő képen látható:

![A Azure Portal keresési beállításainak naplózása](./media/logging/log-analytics-open-log-search.png)

Most, hogy engedélyezte az adatgyűjtést, az új lekérdezési nyelv használatával futtassa a következő naplóbeli keresési példát, hogy megtekintse a 10 legutóbbi naplókat `AzureDiagnostics | take 10`.

![A 10 legutóbb használt napló mintájának keresése](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Lekérdezések

Az alábbiakban néhány további lekérdezést is megadhat, amelyek segítségével **figyelheti az Azure** Cosmos-tárolókat. Ezek a lekérdezések az [új nyelvvel](../log-analytics/log-analytics-log-search-upgrade.md)működnek. 

Az egyes naplók által visszaadott információk jelentésének megismeréséhez tekintse meg a [Azure Cosmos db naplók értelmezése](#interpret)című témakört.

* Azure Cosmos DB összes diagnosztikai naplójának lekérdezése egy adott időszakban:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* A 10 legutóbb naplózott esemény lekérdezése:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Az összes művelet lekérdezése művelet típusa szerint csoportosítva:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Az összes művelet lekérdezése **erőforrás**szerint csoportosítva:

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Az összes felhasználói tevékenység lekérdezése erőforrás szerint csoportosítva:

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
    > [!NOTE]
    > Ez a parancs egy tevékenység naplóra, nem pedig diagnosztikai naplóra szolgál.

* A 3 ezredmásodpercnél hosszabb műveletek lekérdezése:

    ```
    AzureDiagnostics | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* A műveletet futtató ügynök lekérdezése:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Lekérdezés a hosszú ideig futó műveletek végrehajtásakor:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , duration_s | render timechart
    ```

További információ az új napló keresési nyelvének használatáról: a [naplók keresésének megismerése Azure monitor naplókban](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>A naplók értelmezése

Az Azure Storage-ban és a Azure Monitor-naplókban tárolt diagnosztikai adatgyűjtés hasonló sémát használ. 

A következő táblázat ismerteti az egyes naplóbejegyzések tartalmát.

| Azure Storage-mező vagy-tulajdonság | Azure Monitor naplók tulajdonsága | Leírás |
| --- | --- | --- |
| **idő** | **TimeGenerated** | A művelet bekövetkeztekor megadott dátum és idő (UTC). |
| **resourceId** | **Erőforrás** | Az Azure Cosmos DB fiók, amely számára engedélyezve vannak a naplók.|
| **Kategória** | **Kategória** | Azure Cosmos DB naplók esetében a **DataPlaneRequests** az egyetlen elérhető érték. |
| **operationName** | **OperationName** | A művelet neve. Ez az érték a következő műveletek bármelyike lehet: létrehozás, frissítés, olvasás, ReadFeed, törlés, csere, végrehajtás, SqlQuery, lekérdezés, JSQuery, Head, HeadFeed vagy Upsert.   |
| **Tulajdonságok** | – | A mező tartalmát az alábbi sorok írják le. |
| **Tevékenységazonosító** | **activityId_g** | A naplózott művelet egyedi GUID azonosítója. |
| **userAgent** | **userAgent_s** | Egy karakterlánc, amely megadja a kérést végző ügyfél-felhasználói ügynököt. A formátum {User Agent Name}/{Version}.|
| **requestResourceType** | **requestResourceType_s** | Az elérni kívánt erőforrás típusa. Az érték a következő erőforrástípusok bármelyike lehet: adatbázis, tároló, dokumentum, melléklet, felhasználó, engedély, StoredProcedure, trigger, UserDefinedFunction vagy ajánlat. |
| **statusCode** | **statusCode_s** | A művelet válaszának állapota. |
| **requestResourceId** | **ResourceId** | A kérelemre vonatkozó resourceId. Az érték databaseRid, collectionRid vagy documentRid is mutathat a végrehajtott művelettől függően.|
| **clientIpAddress** | **clientIpAddress_s** | Az ügyfél IP-címe. |
| **requestCharge** | **requestCharge_s** | A művelet által használt RUs száma |
| **collectionRid** | **collectionId_s** | A gyűjtemény egyedi azonosítója.|
| **időtartama** | **duration_s** | A művelet időtartama ezredmásodpercben. |
| **requestLength** | **requestLength_s** | A kérelem hossza (bájt). |
| **responseLength** | **responseLength_s** | A válasz hossza (bájt).|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Ez az érték nem üres, ha [erőforrás-tokeneket](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) használ a hitelesítéshez. Az érték a felhasználó erőforrás-AZONOSÍTÓJÁRA mutat. |

## <a name="next-steps"></a>Következő lépések

- Ha szeretné megtudni, hogyan engedélyezheti a naplózást, valamint a különböző Azure-szolgáltatások által támogatott mérőszámokat és naplózási kategóriákat, olvassa el a [Microsoft Azure metrikáinak áttekintését](../monitoring-and-diagnostics/monitoring-overview-metrics.md) , valamint az [Azure diagnosztikai naplók áttekintését ismertető](../azure-monitor/platform/resource-logs-overview.md) cikket.
- Az Event hubok megismeréséhez olvassa el az alábbi cikkeket:
   - [Mi az Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- [Az Azure Storage-ból letölthető mérőszámok és diagnosztikai naplók](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs)olvashatók.
- Olvassa el [a Azure monitor naplókban végzett keresések ismertetése](../log-analytics/log-analytics-log-search-new.md)című témakört.
