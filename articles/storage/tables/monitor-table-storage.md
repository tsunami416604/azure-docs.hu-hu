---
title: Az Azure Table Storage figyelése | Microsoft Docs
description: Ismerje meg, hogyan figyelheti az Azure Table Storage teljesítményét és rendelkezésre állását. Az Azure Table Storage adatainak figyelése, a konfiguráció megismerése és a metrikák és a naplók adatainak elemzése.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 8104d1d1f8864f8b7c5a6add6c602007f2d04822
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711316"
---
# <a name="monitoring-azure-table-storage"></a>Az Azure Table Storage figyelése

Ha olyan kritikus fontosságú alkalmazásokkal és üzleti folyamatokkal rendelkezik, amelyek az Azure-erőforrásokra támaszkodnak, ezeket az erőforrásokat szeretné figyelni a rendelkezésre állás, a teljesítmény és a művelet szempontjából. Ez a cikk az Azure Table Storage által generált figyelési információkat ismerteti, valamint azt, hogy miként használhatók a Azure Monitor funkciói az adatriasztások elemzéséhez.

> [!NOTE]
> Az Azure Monitor Azure Storage-naplók nyilvános előzetes verzióban érhetők el, és elérhetők az előzetes teszteléshez az összes nyilvános felhőben. Az előzetes verzióra való regisztráláshoz tekintse meg [ezt a lapot](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Ez az előzetes verzió lehetővé teszi a Blobok (köztük a Azure Data Lake Storage Gen2), a fájlok, a várólisták és a táblák naplófájljainak naplózását. Ez a funkció a Azure Resource Manager üzemi modellel létrehozott összes Storage-fiókhoz elérhető. Lásd: a [Storage-fiók áttekintése](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Figyelő áttekintése

Az egyes Table Storage-erőforrások Azure Portal **áttekintő** lapja az erőforrás-használat, például a kérelmek és az óradíjak rövid áttekintését tartalmazza. Ezek az információk hasznosak, de csak kis mennyiségű figyelési adat érhető el. Az adatok némelyikét automatikusan gyűjti a rendszer, és az erőforrás létrehozásakor az elemzéshez azonnal elérhetővé válik. További típusú adatgyűjtést is engedélyezhet néhány konfigurációval.

## <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?
Az Azure Table Storage [Azure monitor](../../azure-monitor/overview.md)használatával hoz létre figyelési adatgyűjtési szolgáltatást, amely az Azure-ban teljes verem-figyelési szolgáltatás. A Azure Monitor a funkciók teljes készletét biztosítja az Azure-erőforrások és-erőforrások figyelésére más felhőkben és a helyszínen. 

A cikkből megtudhatja [, hogyan figyelheti az Azure-erőforrásokat a Azure monitor,](../../azure-monitor/insights/monitor-azure-resource.md) amely leírja a következőket:

- Mi az Azure Monitor?
- A figyeléshez kapcsolódó költségek
- Az Azure-ban összegyűjtött adatok figyelése
- Adatgyűjtés konfigurálása
- Szabványos eszközök az Azure-ban a figyelési adatok elemzéséhez és riasztásához

Az alábbi, az Azure Storage-ból gyűjtött adatok leírásával a cikk a következő részekre épül. A példák bemutatják, hogyan konfigurálhatja az adatgyűjtést, és hogyan elemezheti ezeket az Azure-eszközökkel.

## <a name="monitoring-data"></a>Adatok monitorozása

Az Azure Table Storage ugyanolyan típusú megfigyelési adatokat gyűjt, mint a többi Azure-erőforrást, amelyeket az [Azure-erőforrások adatainak monitorozása](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)című témakörben talál. 

A metrikákkal és az Azure Table Storage által létrehozott metrikákkal kapcsolatos részletes információkért tekintse meg az [Azure Table Storage-figyelési adatok referenciáját](monitor-table-storage-reference.md) .

A Azure Monitor metrikái és naplói csak Azure Resource Manager Storage-fiókokat támogatják. A Azure Monitor nem támogatja a klasszikus Storage-fiókokat. Ha metrikákat vagy naplókat szeretne használni egy klasszikus Storage-fiókon, át kell telepítenie egy Azure Resource Manager Storage-fiókba. Lásd: [áttelepítés Azure Resource Managerra](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

Ha szeretné, továbbra is használhatja a klasszikus metrikákat és naplókat. Valójában a klasszikus metrikák és naplók párhuzamosan érhetők el Azure Monitor metrikákkal és naplókkal. A támogatás addig marad érvényben, amíg az Azure Storage befejezi a szolgáltatást az örökölt metrikák és naplók esetében.

## <a name="collection-and-routing"></a>Gyűjtés és Útválasztás

A platform metrikáit és a tevékenység naplóját a rendszer automatikusan gyűjti, de diagnosztikai beállítás használatával más helyszínekre is átirányíthatja őket. Az erőforrás-naplók összegyűjtéséhez diagnosztikai beállítást kell létrehoznia. 

Diagnosztikai beállítás létrehozásához a Azure Portal, az Azure CLI vagy a PowerShell használatával tekintse meg a [diagnosztikai beállítás létrehozása a platform-naplók és-metrikák gyűjtéséhez az Azure-ban](../../azure-monitor/platform/diagnostic-settings.md)című témakört. 

Diagnosztikai beállítást létrehozó Azure Resource Manager sablon megjelenítéséhez tekintse meg az [Azure Storage diagnosztikai beállításai](https://docs.microsoft.com/azure/azure-monitor/samples/resource-manager-diagnostic-settings#diagnostic-setting-for-azure-storage)című témakört.

Ha diagnosztikai beállítást hoz létre, válassza ki azt a tárterületet, amelyhez engedélyezni szeretné a naplókat, például blob, üzenetsor, tábla vagy fájl. A Table Storage esetében válassza a **táblázat**lehetőséget. 

Ha a diagnosztikai beállítást a Azure Portal hozza létre, kiválaszthatja az erőforrást egy listából. Ha a PowerShellt vagy az Azure CLI-t használja, akkor a Table Storage-végpont erőforrás-AZONOSÍTÓját kell használnia. A Azure Portal erőforrás-AZONOSÍTÓját a Storage-fiók **tulajdonságlapjának** megnyitásával érheti el.

Meg kell adnia a következő típusú műveletek egyikét is, amelyekhez naplókat kíván gyűjteni. 

| Kategória | Leírás |
|:---|:---|
| StorageRead | Objektumok olvasási műveletei. |
| StorageWrite | Írási műveletek az objektumokon. |
| StorageDelete | Objektumok törlési műveletei. |

## <a name="analyzing-metrics"></a>Mérőszámok elemzése

Az Azure Storage mérőszámait más Azure-szolgáltatásokból származó metrikákkal elemezheti Metrikaböngésző használatával. A **Azure monitor** menüből válassza a **metrikák** lehetőséget a Metrikaböngésző megnyitásához. Az eszköz használatáról további információt az [Azure Metrikaböngésző használatának első lépéseivel](../../azure-monitor/platform/metrics-getting-started.md)foglalkozó témakörben talál. 

Ebből a példából megtudhatja, hogyan tekintheti meg a **tranzakciókat** a fiók szintjén.

![Képernyőfelvétel a metrikák eléréséről a Azure Portal](./media/monitor-table-storage/access-metrics-portal.png)

A dimenziókat támogató metrikák esetében szűrheti a mérőszámot a kívánt dimenzió értékkel. Ebből a példából megtudhatja, hogyan tekintheti meg a **tranzakciókat** a fiók szintjén egy adott művelethez az **API-név** dimenzió értékeinek kiválasztásával.

![Képernyőfelvétel a Azure Portal dimenzióval rendelkező metrikák eléréséről](./media/monitor-table-storage/access-metrics-portal-with-dimension.png)

Az Azure Storage által támogatott dimenziók teljes listájáért lásd: [mérőszámok méretei](monitor-table-storage-reference.md#metrics-dimensions).

Az Azure Table Storage metrikái a következő névterekben találhatók: 

- Microsoft. Storage/storageAccounts
- Microsoft. Storage/storageAccounts/tableServices

Az Azure Table Storage-t tartalmazó összes Azure Monitor támogatási mérőszámok listáját lásd: [Azure monitor támogatott metrikák](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported).


### <a name="accessing-metrics"></a>Metrikák elérése

> [!TIP]
> Az Azure CLI-vagy .NET-példák megtekintéséhez válassza ki az itt felsorolt megfelelő lapokat.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>A metrika definíciójának listázása

A Storage-fiók vagy a Table Storage szolgáltatás metrika-definícióját listázhatja. Használja a [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition) parancsmagot.

Ebben a példában a helyőrzőt cserélje le a `<resource-ID>` teljes Storage-fiók erőforrás-azonosítójával vagy a Table Storage szolgáltatás erőforrás-azonosítójával.  Ezek az erőforrás-azonosítók a Storage-fiók **tulajdonságlapján** találhatók a Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Metrika értékeinek olvasása

Elolvashatja a Storage-fiók vagy a Table Storage szolgáltatás fiók szintű metrikai értékeit is. Használja a [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric) parancsmagot.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>A fiók szintű metrika definíciójának listázása

A Storage-fiók vagy a Table Storage szolgáltatás metrika-definícióját listázhatja. Használja az az [monitor Metrics List-fogalommeghatározások](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions) parancsot.
 
Ebben a példában a helyőrzőt cserélje le a `<resource-ID>` teljes Storage-fiók erőforrás-azonosítójával vagy a Table Storage szolgáltatás erőforrás-azonosítójával. Ezek az erőforrás-azonosítók a Storage-fiók **tulajdonságlapján** találhatók a Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Fiók szintű metrika értékeinek olvasása

Megtekintheti a Storage-fiók vagy a Table Storage szolgáltatás metrikai értékeit. Használja az az [monitor metrika List](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list) parancsot.

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor biztosítja a [.net SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) -t a metrika definíciójának és értékeinek olvasásához. A [mintakód](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) azt mutatja be, hogyan használható az SDK különböző paraméterekkel. A `0.18.0-preview` tárolási metrikák esetében vagy újabb verziót kell használnia.
 
Ezekben a példákban cserélje le a `<resource-ID>` helyőrzőt a teljes Storage-fiók vagy a Table Storage szolgáltatás erőforrás-azonosítójával. Ezek az erőforrás-azonosítók a Storage-fiók **tulajdonságlapján** találhatók a Azure Portal.

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

#### <a name="reading-account-level-metric-values"></a>Fiók szintű metrika értékeinek olvasása

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

#### <a name="reading-multidimensional-metric-values"></a>Többdimenziós metrika értékeinek olvasása

Többdimenziós metrikák esetében meg kell határoznia a metaadat-szűrőket, ha adott dimenzió értékeken szeretné beolvasni a metrikai adatokat.

Az alábbi példa bemutatja, hogyan olvashatja el a metrikus adatokat a többdimenziós támogatást támogató metrika esetében:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for table storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default";
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

## <a name="analyzing-logs"></a>Naplók elemzése

Az erőforrás-naplókat megadhatja blobként egy Storage-fiókban, az Event-adatként vagy a log analitikus lekérdezésekkel.

A naplókban megjelenő mezők részletes ismertetését az [Azure Table Storage figyelési adatreferenciája](monitor-table-storage-reference.md)című témakörben tekintheti meg.

> [!NOTE]
> Az Azure Monitor Azure Storage-naplók nyilvános előzetes verzióban érhetők el, és elérhetők az előzetes teszteléshez az összes nyilvános felhőben. Az előzetes verzióra való regisztráláshoz tekintse meg [ezt a lapot](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Ez az előzetes verzió lehetővé teszi a Blobok (köztük a Azure Data Lake Storage Gen2), a fájlok, a várólisták, a táblák, a Premium Storage-fiókok általános célú v1-ben és az általános célú v2 Storage-fiókokban való naplózását. A klasszikus Storage-fiókok nem támogatottak.

A naplóbejegyzések csak akkor jönnek létre, ha a szolgáltatás-végpontra irányuló kérések történnek. Ha például egy Storage-fiók egy tevékenységgel rendelkezik a tábla végpontján, de nem a blob-vagy várólista-végpontokban, akkor csak a Table szolgáltatáshoz tartozó naplók jönnek létre. Az Azure Storage-naplók részletes információkat tartalmaznak a tárolási szolgáltatásokkal kapcsolatos sikeres és sikertelen kérelmekről. Ezekkel az információkkal monitorozhatók az egyes kérelmek és diagnosztizálhatók a tárolási szolgáltatások problémái. A kéréseket a rendszer a lehető legjobb módon naplózza.

### <a name="log-authenticated-requests"></a>Hitelesített kérelmek naplózása

 A hitelesített kérések alábbi típusai vannak rögzítve:

- Sikeres kérések
- Sikertelen kérések, köztük az időtúllépések, torlódások, valamint a hálózati, hitelesítési és egyéb hibák
- Megosztott elérési aláírást (SAS) vagy OAuth használó kérelmek, beleértve a sikertelen és sikeres kérelmeket
- Elemzési adatokra irányuló kérelmek (a klasszikus naplózási adatok a **$logs** tárolóban és az osztály metrikájának adatai a **$metric** táblákban)

A Table Storage szolgáltatás (például a napló létrehozása vagy törlése) által kezdeményezett kérelmek nem kerülnek naplózásra. A naplózott adatok teljes listáját a [Storage naplózott műveletek és állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) és a [tárolási napló formátuma](monitor-table-storage-reference.md)című témakörben tekintheti meg.

### <a name="log-anonymous-requests"></a>Névtelen kérelmek naplózása

 A rendszer a következő típusú névtelen kérelmeket naplózza:

- Sikeres kérések
- Kiszolgálóhibák
- Időtúllépési hibák az ügyfél és a kiszolgáló esetében
- Sikertelen GET kérelmek a 304 hibakódmal (nincs módosítva)

Az összes többi sikertelen névtelen kérelmet nem naplózza a rendszer. A naplózott adatok teljes listáját a [Storage naplózott műveletek és állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) és a [tárolási napló formátuma](monitor-table-storage-reference.md)című témakörben tekintheti meg.

### <a name="accessing-logs-in-a-storage-account"></a>Naplófájlok elérése a Storage-fiókban

A naplók úgy jelennek meg, mint a cél Storage-fiókban lévő tárolóban tárolt Blobok. Az adatok gyűjtése és tárolása egy blobon belül, egy sorba tagolt JSON-adattartalomként történik. A blob neve az alábbi elnevezési konvenciót követi:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/tableServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Bemutatunk egy példát:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/tableServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Eseménynaplók elérése az Event hub-ban

Az Event hub-nak küldött naplók nem fájlként vannak tárolva, de ellenőrizheti, hogy az Event hub kapott-e naplózási adatokat. A Azure Portal nyissa meg az Event hub eszközt, és ellenőrizze, hogy a **Bejövő üzenetek** száma nagyobb-e nullánál. 

![Naplók](media/monitor-table-storage/event-hub-log.png)

A biztonsági információkkal és az események kezelésével és figyelési eszközeivel elérheti és beolvashatja az Event hub-nak elküldett naplózási adatokat. További információ: Mit tehetek [az Event hub-ba küldött figyelési adatokkal?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Naplók elérése Log Analytics munkaterületen

A Log Analytics munkaterületre küldött naplók Azure Monitor naplózási lekérdezések használatával érhetők el.

További információ: [Bevezetés a log Analytics használatába Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

Az adattárolást a **StorageTableLogs** táblában tárolja a rendszer. 

#### <a name="sample-kusto-queries"></a>Példa Kusto-lekérdezésekre

Íme néhány lekérdezés, amely megadható a **log keresési** sávban a tábla tárterületének figyeléséhez. Ezek a lekérdezések az [új nyelvvel](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)működnek.

> [!IMPORTANT]
> Amikor kiválasztja a **naplók** elemet a Storage-fiók erőforráscsoport menüjében, log Analytics megnyílik a lekérdezés hatóköre beállítással az aktuális erőforráscsoporthoz. Ez azt jelenti, hogy a naplók lekérdezése csak az adott erőforráscsoport adatait fogja tartalmazni. Ha olyan lekérdezést szeretne futtatni, amely más erőforrásokból vagy más Azure-szolgáltatásoktól származó adatokból származó adatokkal is rendelkezik, válassza a **naplók** lehetőséget a **Azure monitor** menüből. Részletekért lásd: [a naplózási lekérdezés hatóköre és időbeli tartománya Azure Monitor log Analytics](/azure/azure-monitor/log-query/scope/) .

Az alábbi lekérdezések segítségével figyelheti az Azure Storage-fiókokat:

* A 10 leggyakoribb hiba listázása az elmúlt három napban.

    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* A legtöbb hibát okozó tíz művelet listázása az elmúlt három napban.

    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* Az elmúlt három nap során a leghosszabb késéssel rendelkező 10 művelet listázása.

    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* Az összes olyan művelet listázása, amely a kiszolgálóoldali sávszélesség-szabályozási hibákat okozta az elmúlt három napban.

    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* Az összes névtelen hozzáféréssel rendelkező kérelem listázása az elmúlt három napban.

    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* Az elmúlt három napban használt műveletek kördiagramának létrehozása.
    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>GYIK

**Támogatja az Azure Storage a Managed Disks és a nem felügyelt lemezek metrikáit?**

Nem. Az Azure-beli számítási szolgáltatás támogatja a lemezek mérőszámait. További információ: [a felügyelt és nem felügyelt lemezek esetében a lemezre vonatkozó mérőszámok](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

## <a name="next-steps"></a>Következő lépések

- Az Azure Table Storage által létrehozott naplók és mérőszámok ismertetését lásd: az [Azure Table Storage figyelési adatai referenciája](monitor-table-storage-reference.md).
- Az Azure-erőforrások monitorozásával kapcsolatos további információkért lásd: [Azure-erőforrások figyelése Azure monitorokkal](../../azure-monitor/insights/monitor-azure-resource.md).
- A metrikák áttelepítésével kapcsolatos további információkért lásd: [Azure Storage-metrikák áttelepítése](../common/storage-metrics-migration.md).
