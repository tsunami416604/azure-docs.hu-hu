---
title: Az Azure Storage figyelése | Microsoft Docs
description: Ismerje meg, hogyan figyelheti az Azure Storage teljesítményét és rendelkezésre állását.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring
ms.openlocfilehash: 2f6320bf4606dc9357ac676b3bc5d7421b24a36c
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86182712"
---
# <a name="monitor-azure-storage"></a>Az Azure Storage figyelése

Ha olyan kritikus fontosságú alkalmazásokkal és üzleti folyamatokkal rendelkezik, amelyek az Azure-erőforrásokra támaszkodnak, ezeket az erőforrásokat szeretné figyelni a rendelkezésre állás, a teljesítmény és a művelet szempontjából. Ez a cikk az Azure Storage által generált figyelési információkat ismerteti, valamint azt, hogy miként használhatók a Azure Monitor funkciói az adatriasztások elemzéséhez.

> [!NOTE]
> Az Azure Monitor Azure Storage-naplók nyilvános előzetes verzióban érhetők el, és elérhetők az előzetes teszteléshez az összes nyilvános felhőben. Az előzetes verzióra való regisztráláshoz tekintse meg [ezt a lapot](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Ez az előzetes verzió lehetővé teszi a Blobok (köztük a Azure Data Lake Storage Gen2), a fájlok, a várólisták és a táblák naplófájljainak naplózását. Ez a funkció a Azure Resource Manager üzemi modellel létrehozott összes Storage-fiókhoz elérhető. Lásd: a [Storage-fiók áttekintése](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Figyelő áttekintése

Az egyes tárolási erőforrások Azure Portal **áttekintő** lapja az erőforrás-használat, például a kérelmek és az óradíjak rövid áttekintését tartalmazza. Ezek az információk hasznosak, de csak kis mennyiségű figyelési adat érhető el. Ezeket az adatokat a rendszer automatikusan gyűjti, és elemzésre azonnal elérhetővé válik a tárolási erőforrás létrehozásakor. További típusú adatgyűjtést is engedélyezhet néhány konfigurációval.

## <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?
Az Azure Storage [Azure monitor](../../azure-monitor/overview.md)használatával hoz létre figyelési adatgyűjtési szolgáltatást, amely az Azure-ban teljes verem-figyelési szolgáltatás. A Azure Monitor a funkciók teljes készletét biztosítja az Azure-erőforrások és-erőforrások figyelésére más felhőkben és a helyszínen. 

További információ a Azure Monitorről: az [Azure-erőforrások figyelése a Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md)használatával. A cikk a következő témákat ismerteti:

- Mi az Azure Monitor?
- A figyeléshez kapcsolódó költségek
- Az Azure-ban összegyűjtött adatok figyelése
- Adatgyűjtés konfigurálása
- Szabványos eszközök az Azure-ban a figyelési adatok elemzéséhez és riasztásához

Az alábbi, az Azure Storage-ból gyűjtött adatok leírásával a cikk a következő részekre épül. A példák bemutatják, hogyan konfigurálhatja az adatgyűjtést, és hogyan elemezheti ezeket az Azure-eszközökkel.

## <a name="monitor-data-from-azure-storage"></a>Adatok figyelése az Azure Storage-ból

Az Azure Storage ugyanolyan típusú figyelési adatokat gyűjt, mint az Azure- [erőforrások monitorozásával](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)kapcsolatos egyéb Azure-erőforrások. Az Azure Storage által létrehozott naplókkal és metrikákkal kapcsolatos további információkért lásd az [Azure Storage-figyelési adatok referenciáját](monitor-storage-reference.md).

A Azure Monitor metrikái és naplói csak Azure Resource Manager Storage-fiókokat támogatják. A Azure Monitor nem támogatja a klasszikus Storage-fiókokat. Ha metrikákat vagy naplókat szeretne használni egy klasszikus Storage-fiókon, át kell telepítenie egy Azure Resource Manager Storage-fiókba. Lásd: [áttelepítés Azure Resource Managerra](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

Ha szeretné, továbbra is használhatja a klasszikus metrikákat és naplókat. Valójában a klasszikus metrikák és naplók párhuzamosan érhetők el Azure Monitor metrikákkal és naplókkal. A támogatás addig marad érvényben, amíg az Azure Storage befejezi a szolgáltatást az örökölt metrikák és naplók esetében.

### <a name="logs-in-azure-monitor-preview"></a>Naplók Azure Monitorban (előzetes verzió)

A naplóbejegyzések csak akkor jönnek létre, ha a szolgáltatás-végpontra irányuló kérések történnek. Ha például egy Storage-fiók egy tevékenységgel rendelkezik a blob-végponton, de nem a tábla vagy a várólista végpontján, akkor csak a blob szolgáltatáshoz tartozó naplók jönnek létre. Az Azure Storage-naplók részletes információkat tartalmaznak a tárolási szolgáltatásokkal kapcsolatos sikeres és sikertelen kérelmekről. Ezekkel az információkkal monitorozhatók az egyes kérelmek és diagnosztizálhatók a tárolási szolgáltatások problémái. A kéréseket a rendszer a lehető legjobb módon naplózza.

#### <a name="log-authenticated-requests"></a>Hitelesített kérelmek naplózása

 A hitelesített kérések alábbi típusai vannak rögzítve:

- Sikeres kérelmek
- Sikertelen kérések, köztük az időtúllépések, torlódások, valamint a hálózati, hitelesítési és egyéb hibák
- Megosztott elérési aláírást (SAS) vagy OAuth használó kérelmek, beleértve a sikertelen és sikeres kérelmeket
- Elemzési adatokra irányuló kérelmek (a klasszikus naplózási adatok a **$logs** tárolóban és az osztály metrikájának adatai a **$metric** táblákban)

A tárolási szolgáltatás által kezdeményezett kérelmek (például a naplók létrehozása vagy törlése) nem kerülnek naplózásra. A naplózott adatok teljes listáját a [Storage naplózott műveletek és állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) és a [tárolási napló formátuma](monitor-storage-reference.md)című témakörben tekintheti meg.

#### <a name="log-anonymous-requests"></a>Névtelen kérelmek naplózása

 A rendszer a következő típusú névtelen kérelmeket naplózza:

- Sikeres kérelmek
- Kiszolgálóhibák
- Időtúllépési hibák az ügyfél és a kiszolgáló esetében
- Sikertelen GET kérelmek a 304 hibakódmal (nincs módosítva)

Az összes többi sikertelen névtelen kérelmet nem naplózza a rendszer. A naplózott adatok teljes listáját a [Storage naplózott műveletek és állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) és a [tárolási napló formátuma](monitor-storage-reference.md)című témakörben tekintheti meg.

## <a name="configuration"></a>Konfiguráció

A platform metrikáit és a tevékenység naplóját a rendszer automatikusan gyűjti, de diagnosztikai beállítást kell létrehoznia az erőforrás-naplók összegyűjtéséhez vagy a Azure Monitoron kívüli továbbításához. A diagnosztikai beállításoknak a Azure Portal, az Azure CLI vagy a PowerShell használatával történő létrehozásával kapcsolatban lásd: [diagnosztikai beállítás létrehozása a platform-naplók és-metrikák gyűjtéséhez az Azure-ban](../../azure-monitor/platform/diagnostic-settings.md).

Ha diagnosztikai beállítást hoz létre, válassza ki azt a tárterületet, amelyhez engedélyezni szeretné a naplókat, például blob, üzenetsor, tábla vagy fájl. Data Lake Storage Gen2 nem jelenik meg tárolási típusként. Ennek az az oka, hogy Data Lake Storage Gen2 a blob Storage számára elérhető képességek halmaza. 

Ha a diagnosztikai beállítást a Azure Portal hozza létre, kiválaszthatja az erőforrást egy listából. Ha a PowerShellt vagy az Azure CLI-t használja, akkor a tárolási típus erőforrás-AZONOSÍTÓját kell használnia. A Azure Portal erőforrás-AZONOSÍTÓját a Storage-fiók **tulajdonságlapjának** megnyitásával érheti el.

Meg kell adnia azoknak a műveleteknek a kategóriáit is, amelyekhez naplókat kíván gyűjteni. Az Azure Storage kategóriái az alábbi táblázatban láthatók.

| Kategória | Leírás |
|:---|:---|
| StorageRead | Olvasási műveletek a blobokon. |
| StorageWrite | Írási műveletek a blobokon. |
| StorageDelete | Törlési műveletek a blobokon. |

## <a name="analyze-metric-data"></a>Metrikai adatok elemzése

Az Azure Storage mérőszámait más Azure-szolgáltatásokból származó metrikákkal elemezheti Metrikaböngésző használatával. A **Azure monitor** menüből válassza a **metrikák** lehetőséget a Metrikaböngésző megnyitásához. Az eszköz használatáról további információt az [Azure Metrikaböngésző használatának első lépéseivel](../../azure-monitor/platform/metrics-getting-started.md)foglalkozó témakörben talál. 

Ebből a példából megtudhatja, hogyan tekintheti meg a **tranzakciókat** a fiók szintjén.

![Képernyőfelvétel a metrikák eléréséről a Azure Portal](./media/monitor-storage/access-metrics-portal.png)

A dimenziókat támogató metrikák esetében szűrheti a mérőszámot a kívánt dimenzió értékkel. Ebből a példából megtudhatja, hogyan tekintheti meg a **tranzakciókat** a fiók szintjén egy adott művelethez az **API-név** dimenzió értékeinek kiválasztásával.

![Képernyőfelvétel a Azure Portal dimenzióval rendelkező metrikák eléréséről](./media/monitor-storage/access-metrics-portal-with-dimension.png)

Az Azure Storage által támogatott dimenziók teljes listájáért lásd: [mérőszámok méretei](monitor-storage-reference.md#metrics-dimensions).

Az Azure Storage összes mérőszáma a következő névterekben található:

- Microsoft. Storage/storageAccounts
- Microsoft. Storage/storageAccounts/blobServices
- Microsoft. Storage/storageAccounts/fileServices
- Microsoft. Storage/storageAccounts/queueServices
- Microsoft. Storage/storageAccounts/tableServices

Az Azure Storage-t tartalmazó összes Azure Monitor támogatási mérőszámok listáját lásd: [Azure monitor támogatott metrikák](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported).


### <a name="access-metrics"></a>Hozzáférési metrikák

> [!TIP]
> Az Azure CLI-vagy .NET-példák megtekintéséhez válassza ki az itt felsorolt megfelelő lapokat.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>A metrika definíciójának listázása

Megtekintheti a Storage-fiók vagy az egyéni tárolási szolgáltatás metrika-definícióját, például a blobot, a fájlt, a táblát vagy a várólista-szolgáltatást. Használja a [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition?view=azps-3.3.0) parancsmagot.

Ebben a példában a helyőrzőt cserélje le a `<resource-ID>` teljes Storage-fiók erőforrás-azonosítójával vagy egy egyéni tárolási szolgáltatás erőforrás-azonosítójával, például a blob, fájl, tábla vagy üzenetsor szolgáltatáshoz. Ezek az erőforrás-azonosítók a Storage-fiók **tulajdonságlapján** találhatók a Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="read-metric-values"></a>Metrika értékeinek olvasása

Elolvashatja a Storage-fiók vagy az egyéni tárolási szolgáltatás, például a blob, fájl, tábla vagy üzenetsor szolgáltatás fiók szintű metrikájának értékét. Használja a [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric?view=azps-3.3.0) parancsmagot.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>A fiók szintű metrika definíciójának listázása

Megtekintheti a Storage-fiók vagy az egyéni tárolási szolgáltatás metrika-definícióját, például a blobot, a fájlt, a táblát vagy a várólista-szolgáltatást. Használja az az [monitor Metrics List-fogalommeghatározások](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list-definitions) parancsot.
 
Ebben a példában a helyőrzőt cserélje le a `<resource-ID>` teljes Storage-fiók erőforrás-azonosítójával vagy egy egyéni tárolási szolgáltatás erőforrás-azonosítójával, például a blob, fájl, tábla vagy üzenetsor szolgáltatáshoz. Ezek az erőforrás-azonosítók a Storage-fiók **tulajdonságlapján** találhatók a Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Fiók szintű metrika értékeinek olvasása

Megtekintheti a Storage-fiók vagy az egyéni tárolási szolgáltatás metrikai értékeit, például a blob, fájl, tábla vagy üzenetsor szolgáltatást. Használja az az [monitor metrika List](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) parancsot.

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor biztosítja a [.net SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) -t a metrika definíciójának és értékeinek olvasásához. A [mintakód](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) azt mutatja be, hogyan használható az SDK különböző paraméterekkel. A `0.18.0-preview` tárolási metrikák esetében vagy újabb verziót kell használnia.
 
Ezekben a példákban cserélje le a `<resource-ID>` helyőrzőt a teljes Storage-fiók erőforrás-azonosítójával vagy egy egyéni tárolási szolgáltatás erőforrás-azonosítójával, például a blob, fájl, tábla vagy üzenetsor szolgáltatáshoz. Ezek az erőforrás-azonosítók a Storage-fiók **tulajdonságlapján** találhatók a Azure Portal.

Cserélje le a `<subscription-ID>` változót az előfizetés azonosítójával. A, a és a értékek értékének beszerzésével kapcsolatos útmutatásért `<tenant-ID>` `<application-ID>` `<AccessKey>` lásd: [a portál használata Azure ad-alkalmazás és-szolgáltatásnév létrehozásához, amely hozzáférhet az erőforrásokhoz](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/). 

#### <a name="list-the-account-level-metric-definition"></a>A fiók szintű metrika definíciójának listázása

Az alábbi példa bemutatja, hogyan listázhat mérőszám-definíciót a fiók szintjén:

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

Az alábbi példa azt szemlélteti, hogyan olvashatók be `UsedCapacity` az információk a fiók szintjén:

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

#### <a name="read-multidimensional-metric-values"></a>Többdimenziós metrika értékeinek olvasása

Többdimenziós metrikák esetében meg kell határoznia a metaadat-szűrőket, ha adott dimenzió értékeken szeretné beolvasni a metrikai adatokat.

Az alábbi példa bemutatja, hogyan olvashatja el a metrikus adatokat a többdimenziós támogatást támogató metrika esetében:

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

## <a name="analyze-log-data"></a>Naplóadatok elemzése

Az erőforrás-naplókat megadhatja blobként egy Storage-fiókban, az Event-adatként vagy a log analitikus lekérdezésekkel.

Az ezekben a naplókban megjelenő mezők részletes ismertetését az [Azure Storage figyelési adatreferenciája](monitor-storage-reference.md)című témakörben tekintheti meg.

> [!NOTE]
> Az Azure Monitor Azure Storage-naplók nyilvános előzetes verzióban érhetők el, és elérhetők az előzetes teszteléshez az összes nyilvános felhőben. Az előzetes verzióra való regisztráláshoz tekintse meg [ezt a lapot](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Ez az előzetes verzió lehetővé teszi a Blobok (köztük a Azure Data Lake Storage Gen2), a fájlok, a várólisták, a táblák, a Premium Storage-fiókok általános célú v1-ben és az általános célú v2 Storage-fiókokban való naplózását. A klasszikus Storage-fiókok nem támogatottak.

### <a name="access-logs-in-a-storage-account"></a>Naplófájlok elérése egy Storage-fiókban

A naplók úgy jelennek meg, mint a cél Storage-fiókban lévő tárolóban tárolt Blobok. Az adatok gyűjtése és tárolása egy blobon belül, egy sorba tagolt JSON-adattartalomként történik. A blob neve az alábbi elnevezési konvenciót követi:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Bemutatunk egy példát:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="access-logs-in-an-event-hub"></a>Eseménynaplók elérése az Event hub-ban

Az Event hub-nak küldött naplók nem fájlként vannak tárolva, de ellenőrizheti, hogy az Event hub kapott-e naplózási adatokat. A Azure Portal nyissa meg az Event hub eszközt, és ellenőrizze, hogy a **Bejövő üzenetek** száma nagyobb-e nullánál. 

![Auditnaplók](media/monitor-storage/event-hub-log.png)

A biztonsági információkkal és az események kezelésével és figyelési eszközeivel elérheti és beolvashatja az Event hub-nak elküldett naplózási adatokat. További információ: Mit tehetek [az Event hub-ba küldött figyelési adatokkal?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub).

### <a name="access-logs-in-a-log-analytics-workspace"></a>Naplók elérése Log Analytics munkaterületen

A Log Analytics munkaterületre küldött naplók Azure Monitor naplózási lekérdezések használatával érhetők el.

További információ: [Bevezetés a log Analytics használatába Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

Az adattárolás ezekben a táblázatokban történik.

| Táblázat | Leírás |
|:---|:---|
|StorageBlobLogs | A blob Storage-ban tevékenységet leíró naplók. |
|StorageFileLogs | A fájlmegosztás tevékenységeit leíró naplók. |
|StorageQueueLogs | A várólistákban tevékenységet leíró naplók.|
|StorageTableLogs| A táblákban tevékenységet leíró naplók.|

A Data Lake Storage Gen2 naplói nem jelennek meg egy dedikált táblában. Ennek oka, hogy Data Lake Storage Gen2 nem szolgáltatás. Ez a funkció egy blob Storage-fiókban is engedélyezhető. Ha engedélyezte ezeket a képességeket, a naplók továbbra is megjelennek a StorageBlobLogs táblában. 

### <a name="azure-storage-log-analytics-queries-in-azure-monitor"></a>Azure Storage Log Analytics-lekérdezések Azure Monitor

Íme néhány lekérdezés, amely megadható a **log keresési** sávban az Azure Storage-fiókok figyeléséhez. Ezek a lekérdezések az [új nyelvvel](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)működnek.

Az alábbi lekérdezések segítségével figyelheti az Azure Storage-fiókokat:

* A 10 leggyakoribb hiba listázása az elmúlt három napban.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* A legtöbb hibát okozó tíz művelet listázása az elmúlt három napban.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* Az elmúlt három nap során a leghosszabb késéssel rendelkező 10 művelet listázása.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* Az összes olyan művelet listázása, amely a kiszolgálóoldali sávszélesség-szabályozási hibákat okozta az elmúlt három napban.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* Az összes névtelen hozzáféréssel rendelkező kérelem listázása az elmúlt három napban.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* Az elmúlt három napban használt műveletek kördiagramának létrehozása.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>GYIK

**Támogatja az Azure Storage a Managed Disks és a nem felügyelt lemezek metrikáit?**

Nem. Az Azure-beli számítási szolgáltatás támogatja a lemezek mérőszámait. További információ: [a felügyelt és nem felügyelt lemezek esetében a lemezre vonatkozó mérőszámok](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

## <a name="next-steps"></a>További lépések

- Az Azure Storage által létrehozott naplók és mérőszámok ismertetését az [Azure Storage-figyelési adatok referenciája](monitor-storage-reference.md)című témakörben tekintheti meg.
- Az Azure-erőforrások monitorozásával kapcsolatos további információkért lásd: [Azure-erőforrások figyelése Azure monitorokkal](../../azure-monitor/insights/monitor-azure-resource.md).
- A metrikák áttelepítésével kapcsolatos további információkért lásd: [Azure Storage-metrikák áttelepítése](./storage-metrics-migration.md).
