---
title: Az Azure Storage figyelése | Microsoft Docs
description: Ismerje meg, hogyan figyelheti az Azure Storage teljesítményét és rendelkezésre állását.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 5564634471045838dae3344dc883b6fdc203711e
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82722920"
---
# <a name="monitoring-azure-storage"></a>Az Azure Storage figyelése

Ha olyan kritikus fontosságú alkalmazásokkal és üzleti folyamatokkal rendelkezik, amelyek az Azure-erőforrásokra támaszkodnak, ezeket az erőforrásokat szeretné figyelni a rendelkezésre állás, a teljesítmény és a művelet szempontjából. Ez a cikk ismerteti az Azure Storage által generált figyelési információkat, valamint azt, hogy miként használhatók a Azure Monitor funkciói az adatriasztások elemzéséhez.

> [!NOTE]
> Az Azure Storage-naplók Azure Monitor nyilvános előzetes verzióban érhető el, és az előzetes teszteléshez minden nyilvános felhőalapú régióban elérhető. Az előzetes verzióra való regisztráláshoz tekintse meg [ezt a lapot](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).  Ez az előzetes verzió lehetővé teszi a Blobok (többek között a Azure Data Lake Storage Gen2), a fájlok, a várólisták, a táblák és a Premium Storage-fiókok általános célú v1 és általános célú v2 Storage-fiókokban való naplózását. A klasszikus Storage-fiókok nem támogatottak.

## <a name="monitor-overview"></a>Figyelő áttekintése

Az egyes tárolási erőforrások Azure Portal **áttekintő** lapja az erőforrás-használat rövid nézetét tartalmazza, beleértve a kérelmét és az óránkénti számlázási használatot. Ez hasznos információ, de csak kis mennyiségű figyelési adat érhető el. Az adatok némelyikét automatikusan gyűjti a rendszer, és elemzésre is elérhető, amint létrehozza a tárolási erőforrást. További típusú adatgyűjtést is engedélyezhet néhány konfigurációval.

## <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?
Az Azure Storage [Azure monitor](../../azure-monitor/overview.md) használatával hoz létre monitorozási adatokkal, amely az Azure teljes körű figyelési szolgáltatása, amely teljes körű funkciókat biztosít az Azure-erőforrások figyeléséhez más Felhőbeli és helyszíni erőforrásokon kívül. 

A cikkből megtudhatja [, hogyan figyelheti az Azure-erőforrásokat a Azure monitor,](../../azure-monitor/insights/monitor-azure-resource.md) amely leírja a következőket:

- Mi az Azure Monitor?
- A figyeléshez kapcsolódó költségek
- Az Azure-ban összegyűjtött adatok figyelése
- Adatgyűjtés konfigurálása
- Szabványos eszközök az Azure-ban a figyelési adatok elemzéséhez és riasztásához

Az alábbi részekben az Azure Storage-ból gyűjtött adatok leírásával, valamint az adatok gyűjtésének konfigurálását és az Azure-eszközökkel történő elemzését bemutató példákat találhat.

## <a name="monitoring-data-from-azure-storage"></a>Adatok figyelése az Azure Storage-ból

Az Azure Storage ugyanolyan típusú megfigyelési adatokat gyűjt, mint az Azure- [erőforrások monitorozásával](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)kapcsolatos egyéb Azure-erőforrások. Az Azure Storage által létrehozott naplók és mérőszámok részletes ismertetését lásd az [Azure Storage-figyelési adatokról szóló dokumentációban](monitor-storage-reference.md) .

A Azure Monitor metrikái és naplói csak Azure Resource Manager Storage-fiókokat támogatják. A Azure Monitor nem támogatja a klasszikus Storage-fiókokat. Ha metrikákat vagy naplókat szeretne használni egy klasszikus Storage-fiókon, át kell telepítenie Azure Resource Manager Storage-fiókba. Lásd: [áttelepítés Azure Resource Managerra](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

Ha szeretné, továbbra is használhatja a klasszikus metrikákat és naplókat. Valójában a klasszikus metrikák és naplók párhuzamosan érhetők el Azure Monitor metrikákkal és naplókkal. A támogatás addig marad érvényben, amíg az Azure Storage befejezi a szolgáltatást az örökölt metrikák és naplók esetében. 

### <a name="logs-in-azure-monitor-preview"></a>Naplók Azure Monitorban (előzetes verzió)

A naplóbejegyzések csak akkor jönnek létre, ha a szolgáltatás-végpontra irányuló kérések történnek. Ha például egy Storage-fiók egy tevékenységgel rendelkezik a blob-végponton, de nem a tábla vagy a várólista végpontján, akkor a rendszer csak a blob szolgáltatáshoz tartozó naplókat fogja létrehozni. Az Azure Storage-naplók részletes információkat tartalmaznak a tárolási szolgáltatásokkal kapcsolatos sikeres és sikertelen kérelmekről. Ezekkel az információkkal monitorozhatók az egyes kérelmek és diagnosztizálhatók a tárolási szolgáltatások problémái. A kéréseket a rendszer a lehető legjobb módon naplózza.

#### <a name="logging-authenticated-requests"></a>Hitelesített kérelmek naplózása

 A hitelesített kérések alábbi típusai vannak rögzítve:

- Sikeres kérelmek
- Sikertelen kérések, köztük az időtúllépések, torlódások, valamint a hálózati, hitelesítési és egyéb hibák
- Közös hozzáférésű jogosultságkódot vagy OAuth-hitelesítést használó sikertelen és sikeres kérések
- Elemzési adatokra irányuló kérések (a klasszikus naplózási adatok a **$logs** tárolóban, valamint a **$metric** táblákban szereplő osztály metrikájának adatai)

A tárolási szolgáltatás által kezdeményezett kérelmek (például a naplók létrehozása vagy törlése) nem kerülnek naplózásra. A naplózott adatok teljes listáját a [Storage naplózott műveletek és állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) és a [tárolási naplók formátuma](monitor-storage-reference.md) témakör ismerteti.

#### <a name="logging-anonymous-requests"></a>Névtelen kérelmek naplózása

 A rendszer a következő típusú névtelen kérelmeket naplózza:

- Sikeres kérelmek
- Kiszolgálóhibák
- Időtúllépési hibák az ügyfél és a kiszolgáló esetében
- 304-es (Nincs módosítva) hibakóddal ellátott sikertelen GET-kérések

Az összes többi sikertelen névtelen kérelmet nem naplózza a rendszer. A naplózott adatok teljes listáját a [Storage naplózott műveletek és állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) és a [tárolási naplók formátuma](monitor-storage-reference.md) témakör ismerteti.

## <a name="configuration"></a>Konfiguráció

A platform metrikáit és a tevékenység naplóját a rendszer automatikusan gyűjti, de diagnosztikai beállítást kell létrehoznia az erőforrás-naplók összegyűjtéséhez vagy a Azure Monitoron kívüli továbbításához. A diagnosztikai beállításoknak a Azure Portal, a CLI vagy a PowerShell használatával történő létrehozásával kapcsolatos részletes folyamatért lásd: [diagnosztikai beállítás létrehozása a platform-naplók és-metrikák összegyűjtéséhez az Azure-ban](../../azure-monitor/platform/diagnostic-settings.md) .

Diagnosztikai beállítás létrehozásakor ki kell választania azt a tárterületet, amelyhez engedélyezni szeretné a naplókat (blob, üzenetsor, tábla, fájl). Ha a diagnosztikai beállítást a Azure Portal hozza létre, akkor kiválaszthatja az erőforrást egy listából. Ha a PowerShellt vagy az Azure CLI-t használja, akkor a tárolási típus erőforrás-AZONOSÍTÓját kell használnia. A Azure Portal erőforrás-AZONOSÍTÓját a Storage-fiók **tulajdonságlapjának** megnyitásával érheti el.

Azt is meg kell adnia, hogy a rendszer milyen típusú műveletekkel gyűjtsön naplókat. Az Azure Storage-kategóriák az alábbi táblázatban láthatók:

| Kategória | Leírás |
|:---|:---|
| StorageRead | Olvasási műveletek a blobokon.  |
| StorageWrite | Írási műveletek a blobokon. |
| StorageDelete | Törlési műveletek a blobokon. |

## <a name="analyzing-metric-data"></a>Metrikus adatok elemzése

Az Azure Storage mérőszámait más Azure-szolgáltatásoktól származó metrikákkal is elemezheti a metrikák Explorerrel. Nyissa meg a metrikák Explorert a **Azure monitor** menüből válassza a **metrikák** lehetőséget. Az eszköz használatával kapcsolatos részletekért lásd: az [Azure Metrikaböngésző használatának első lépései](../../azure-monitor/platform/metrics-getting-started.md) . 

Az alábbi példa azt szemlélteti, hogyan lehet megtekinteni a **tranzakciókat** a fiók szintjén.

![képernyőfelvétel a metrikák eléréséről a Azure Portal](./media/monitor-storage/access-metrics-portal.png)

A dimenziókat támogató mérőszámok esetében a mérőszámot a kívánt dimenzió értékkel szűrheti. Az alábbi példa azt szemlélteti, hogyan lehet megtekinteni egy adott művelethez tartozó **tranzakciókat** a fiók szintjén az **API-név** dimenzió értékének kiválasztásával.

![képernyőfelvétel a Azure Portal dimenzióval rendelkező metrikák eléréséről](./media/monitor-storage/access-metrics-portal-with-dimension.png)

Az Azure Storage által támogatott dimenziók teljes listájáért lásd: [mérőszámok méretei](monitor-storage-reference.md#metrics-dimensions).

Az Azure Storage összes mérőszáma a következő névterekben található:

- Microsoft. Storage/storageAccounts
- Microsoft. Storage/storageAccounts/blobServices
- Microsoft. Storage/storageAccounts/fileServices
- Microsoft. Storage/storageAccounts/queueServices
- Microsoft. Storage/storageAccounts/tableServices

Az összes Azure Monitor támogatási metrikák (beleértve az Azure Storage-t) listáját lásd: [Azure monitor támogatott metrikák](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported).


### <a name="access-metrics"></a>Hozzáférési metrikák

> [!TIP]
> Az Azure CLI-vagy .NET-példák megtekintéséhez válassza az alábbi megfelelő lapot.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>A metrika definíciójának listázása

Megtekintheti a Storage-fiók metrikájának definícióját, illetve az egyes tárolási szolgáltatásokat, például a blobot, a fájlt, a táblát vagy a várólista-szolgáltatást. Használja a [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition?view=azps-3.3.0) parancsmagot.

Ebben a példában a `<resource-ID>` helyőrzőt cserélje le a teljes Storage-fiók erőforrás-azonosítójával vagy egy egyéni tárolási szolgáltatás erőforrás-azonosítójával, például a blob, fájl, tábla vagy üzenetsor szolgáltatáshoz. Ezeket az erőforrás-azonosítókat a Storage-fiók **tulajdonságlapján** találja a Azure Portalon.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="read-metric-values"></a>Metrika értékeinek olvasása

Elolvashatja a Storage-fiók, illetve az egyéni tárolási szolgáltatás, például a blob, fájl, tábla vagy várólista szolgáltatás fiók szintű metrikájának értékét. Használja a [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric?view=azps-3.3.0) parancsmagot.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-account-level-metric-definition"></a>Fiók szintű metrika definíciójának listázása

Megtekintheti a Storage-fiók metrikájának definícióját, illetve az egyes tárolási szolgáltatásokat, például a blobot, a fájlt, a táblát vagy a várólista-szolgáltatást. Használja az az [monitor Metrics List-fogalommeghatározások](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list-definitions) parancsot.
 
Ebben a példában a `<resource-ID>` helyőrzőt cserélje le a teljes Storage-fiók erőforrás-azonosítójával vagy egy egyéni tárolási szolgáltatás erőforrás-azonosítójával, például a blob, fájl, tábla vagy üzenetsor szolgáltatáshoz. Ezeket az erőforrás-azonosítókat a Storage-fiók **tulajdonságlapján** találja a Azure Portalon.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Fiók szintű metrika értékeinek olvasása

Elolvashatja a Storage-fiók metrikai értékeit, vagy az egyes tárolási szolgáltatásokat, például a blobot, a fájlt, a táblát vagy a várólista-szolgáltatást. Használja az az [monitor metrika List](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) parancsot.

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor [.net SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) -t biztosít a metrika definíciójának és értékeinek olvasásához. A [mintakód](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) azt mutatja be, hogyan használható az SDK különböző paraméterekkel. A tárolási metrikák `0.18.0-preview` használatához vagy újabb verziójának kell lennie.
 
Ezekben a példákban cserélje le `<resource-ID>` a helyőrzőt a teljes Storage-fiók erőforrás-azonosítójával, vagy egy egyéni tárolási szolgáltatás erőforrás-azonosítójával, például a blob, fájl, tábla vagy üzenetsor szolgáltatáshoz. Ezeket az erőforrás-azonosítókat a Storage-fiók **tulajdonságlapján** találja a Azure Portalon.

Cserélje le `<subscription-ID>` a változót az előfizetés azonosítójával.  A `<tenant-ID>`, `<application-ID>`a és `<AccessKey>`a értékének beszerzésével kapcsolatos útmutatásért lásd [: How to: a portál használata az erőforrásokhoz hozzáférő Azure ad-alkalmazás és egyszerű szolgáltatásnév létrehozásához](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/). 

#### <a name="list-account-level-metric-definition"></a>Fiók szintű metrika definíciójának listázása

Az alábbi példák azt mutatják be, hogyan listázható a metrika definíciója a fiók szintjén:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

#### <a name="read-account-level-metric-values"></a>Fiók szintű metrika értékeinek olvasása

Az alábbi példa azt szemlélteti, hogyan `UsedCapacity` lehet az adatolvasást a fiók szintjén:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            // Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

#### <a name="read-multi-dimensional-metric-values"></a>Többdimenziós metrika értékeinek olvasása

Többdimenziós mérőszámok esetén meg kell határoznia a metaadat-szűrőt, ha adott dimenzió értékén szeretné beolvasni a metrikai adatokat.

Az alábbi példa bemutatja, hogyan olvashatja el a metrikus adatokat a mérőszámot támogató többdimenziós:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

---

## <a name="analyzing-log-data"></a>Naplózási adatok elemzése

Az erőforrás-naplókat megadhatja blobként egy Storage-fiókban, az Event-adatként vagy a log analitikus lekérdezésekkel.

Az ezekben a naplókban megjelenő mezők részletes ismertetését lásd az [Azure Storage-figyelési adatreferenciában](monitor-storage-reference.md) .

> [!NOTE]
> Az Azure Storage-naplók Azure Monitor nyilvános előzetes verzióban érhető el, és az előzetes teszteléshez minden nyilvános felhőalapú régióban elérhető. Az előzetes verzióra való regisztráláshoz tekintse meg [ezt a lapot](https://www.microsoft.com).  Ez az előzetes verzió lehetővé teszi a Blobok (többek között a Azure Data Lake Storage Gen2), a fájlok, a várólisták, a táblák és a Premium Storage-fiókok általános célú v1 és általános célú v2 Storage-fiókokban való naplózását. A klasszikus Storage-fiókok nem támogatottak.

### <a name="access-logs-in-a-storage-account"></a>Naplófájlok elérése egy Storage-fiókban

A naplók úgy jelennek meg, mint a cél Storage-fiókban lévő tárolóban tárolt Blobok. Az adatok gyűjtése és tárolása egyetlen blobon belül történik, egy sorba tagolt JSON-adattartalomként. A blob neve a következő elnevezési konvenciót követi:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Például:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="access-logs-in-event-hub"></a>Hozzáférési naplók az Event hub-ban

Az Event hub-nak küldött naplók nem fájlként vannak tárolva, de ellenőrizheti, hogy az Event hub kapott-e naplózási adatokat. A Azure Portal navigáljon az Event hubhoz, majd ellenőrizze, hogy a **Bejövő üzenetek** száma nagyobb-e nullánál. 

![Naplók](media/monitor-storage/event-hub-log.png)

A biztonsági információkkal és az események kezelésével és figyelési eszközeivel elérheti és beolvashatja az Event hub számára elküldhető naplózási adatokat. További információ: Mit tehetek [az Event hub-ba küldött figyelési adatokkal?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub).

### <a name="access-logs-in-log-analytics-workspace"></a>Hozzáférési naplók Log Analytics munkaterületen

A Log Analytics munkaterületre küldött naplók Azure Monitor naplózási lekérdezések használatával érhetők el.

Lásd: a [log Analytics első lépései Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

Az adattárolást a következő táblázatokban tárolja a rendszer.

| Tábla | Leírás |
|:---|:---|
|StorageBlobLogs | A blob Storage-ban tevékenységet leíró naplók. |
|StorageFileLogs | A fájlmegosztás tevékenységeit leíró naplók. |
|StorageQueueLogs | A várólistákban tevékenységet leíró naplók.|
|StorageTableLogs| A táblákban tevékenységet leíró naplók.|

### <a name="azure-storage-log-analytics-queries-in-azure-monitor"></a>Azure Storage Log Analytics-lekérdezések Azure Monitor

Íme néhány lekérdezés, amelyet beírhat a **log Search** keresési sávjába, hogy segítsen az Azure Storage-fiókok figyelésében. Ezek a lekérdezések az [új nyelvvel](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)működnek.

A következő lekérdezések segítségével figyelheti az Azure Storage-fiókokat.

* 10 leggyakoribb hiba listázása az elmúlt 3 napban.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* A legtöbb hibát okozó 10 művelet listázása az elmúlt 3 napban.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* Az elmúlt 3 napban a leghosszabb végpontok közötti késéssel rendelkező 10 művelet listázása.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* A kiszolgálóoldali sávszélesség-szabályozási hibákat okozó összes művelet listázása az elmúlt 3 napban.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* Az elmúlt 3 napban névtelen hozzáféréssel rendelkező kérelmek listázása.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* Az elmúlt 3 napban használt műveletek kördiagramának létrehozása.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>GYIK

**Támogatja az Azure Storage a Managed Disks és a nem felügyelt lemezek metrikáit?**

Nem, az Azure-beli számítási szolgáltatás támogatja a lemezek mérőszámait. További részletekért tekintse meg a [cikket](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) .

## <a name="next-steps"></a>További lépések

- [Azure Storage-figyelési adatok referenciája](monitor-storage-reference.md) az Azure Storage által létrehozott naplók és metrikák hivatkozásához.
- [Azure-erőforrások figyelése Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md) az Azure-erőforrások figyelésével kapcsolatos részletekért.
- [Azure Storage-metrikák migrálása](./storage-metrics-migration.md)

