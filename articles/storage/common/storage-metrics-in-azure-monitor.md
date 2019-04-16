---
title: Az Azure Storage-mérőszámok az Azure Monitor |} A Microsoft Docs
description: Ismerje meg az új metrikák az Azure Monitor érhető el.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 09/05/2017
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: 426dd265f4d608b8dd3c9ab746479ea103419562
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579342"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Azure Storage-metrikák az Azure Monitorban

Metrikák az Azure Storage is elemezhetők a használati trendek, nyomkövetési kéréseket, és diagnosztizálhatja a problémákat a tárfiókkal.

Az Azure Monitor egységes felhasználói felületet biztosít a különböző Azure-szolgáltatások figyelésére. További információkért lásd: [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md). Az Azure Storage által metrikaadatok küldése az Azure Monitor platform az Azure Monitor integrálható.

## <a name="access-metrics"></a>Access-metrikák

Az Azure Monitor hozzáférés metrikák több módot is biztosít. Is elérhesse azokat a [az Azure portal](https://portal.azure.com), az Azure Monitor API-k (REST és .NET) és elemzési megoldásokkal, például az Event Hubs. További információkért lásd: [Azure Monitor-metrikák](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Alapértelmezés szerint engedélyezve vannak a metrikákat, és elérheti az elmúlt 93 nap adatait. Ha szeretne egy hosszabb ideig megőrizni az adatokat, úgy archiválhatók metrikák adatai egy Azure Storage-fiókhoz. Ennek a konfigurációja a [diagnosztikai beállítások](../../azure-monitor/platform/diagnostic-logs-overview.md) az Azure monitorban.

### <a name="access-metrics-in-the-azure-portal"></a>Az Azure Portalon a hozzáférési metrikák

Metrikák időbeli az Azure Portalon követheti nyomon. Az alábbi példa bemutatja, hogyan kell megtekinteni **tranzakciók** fiók szintjén.

![Képernyőkép a metrikák az Azure Portal eléréséhez](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Dimenziók támogató metrikákhoz szűrheti a metrika a kívánt dimenzió értékkel. Az alábbi példa bemutatja, hogyan kell megtekinteni **tranzakciók** egy adott művelethez tartozó értékek kijelölésével a fiók szintjén **API neve** dimenzió.

![Képernyőkép a metrikák az Azure Portalon léptékű elérése](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Hozzáférés metrikák REST API-val

Az Azure Monitor biztosítja [REST API-k](/rest/api/monitor/) metrikai meghatározásainak és értékek olvasását. Ez a szakasz bemutatja, hogyan olvashatja be a storage-mérőszámok. Erőforrás-azonosító minden REST API-k használatban van. További információkért olvassa el erőforrás-azonosító, a tárolási szolgáltatások ismertetése.

Az alábbi példa bemutatja, hogyan használható [ArmClient](https://github.com/projectkudu/ARMClient) egyszerűsítése a REST API-val tesztelése a parancssorból.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Fiók szintű metrika definíciója a REST API-val

Az alábbi példa bemutatja, hogyan listázhatja a fiók szintjén metrika definíciója:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Ha szeretné a metrikadefinícióinak beolvasása a blob, table, fájl vagy üzenetsor listázása, meg kell adnia különböző erőforrás-azonosítók az egyes szolgáltatások az API-val.

A válasz tartalmazza a metrikai definíció JSON formátumban:

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Olvassa el a fiókszintű metrikaértékek REST API-val

Az alábbi példa bemutatja, hogyan olvashatja el a fiók szintjén metrikaadatok:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

A fenti példa, ha metrikaértékek blob, table, fájl vagy üzenetsor, az olvasni kívánt meg kell adnia különböző erőforrás-azonosítók az egyes szolgáltatások az API-val.

A következő választ metrikaértékek JSON formátumban tartalmazza:

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

### <a name="access-metrics-with-the-net-sdk"></a>A .NET SDK használatával hozzáférés-metrikák

Az Azure Monitor biztosítja [.NET SDK-val](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) metrikai meghatározásainak és értékek olvasását. A [mintakód](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) bemutatja, hogyan használhatja az SDK-t eltérő paraméterekkel rendelkező. Kell használnia `0.18.0-preview` vagy újabb verzióját, a storage-mérőszámok. Erőforrás-azonosító szerepel a .NET SDK-t. További információkért olvassa el erőforrás-azonosító, a tárolási szolgáltatások ismertetése.

Az alábbi példa bemutatja, hogyan olvassa el a storage-mérőszámok az Azure Monitor .NET SDK használatával.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Fiók szintű metrika definíciója a .NET SDK használatával

Az alábbi példa bemutatja, hogyan listázhatja a fiók szintjén metrika definíciója:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        // Using metrics in Azure Monitor is currently free. However, if you use additional solutions ingesting metrics data, you may be billed by these solutions. For example, you are billed by Azure Storage if you archive metrics data to an Azure Storage account. Or you are billed by Operation Management Suite (OMS) if you stream metrics data to OMS for advanced analysis.
        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            //Enumrate metric definition:
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

Ha szeretné a metrikadefinícióinak beolvasása a blob, table, fájl vagy üzenetsor listázása, meg kell adnia különböző erőforrás-azonosítók az egyes szolgáltatások az API-val.

#### <a name="read-metric-values-with-the-net-sdk"></a>Olvassa el a metrikaértékek a .NET SDK használatával

Az alábbi példa bemutatja, hogyan olvashatja be `UsedCapacity` fiók szintjén adatokat:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

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

A fenti példa, ha metrikaértékek blob, table, fájl vagy üzenetsor, az olvasni kívánt meg kell adnia különböző erőforrás-azonosítók az egyes szolgáltatások az API-val.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>A .NET SDK-val többdimenziós metrikaértékek olvasása

Többdimenziós metrikák esetén szüksége meta data szűrő meghatározásához, ha azt szeretné, az adott dimenzióérték metrikaadatok olvasni.

Az alábbi példa bemutatja, hogyan olvashatja el a mérőszámparancshoz támogató metrikát a metrikaadatok:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

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

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Erőforrás-azonosító az Azure Storage-szolgáltatások ismertetése

Erőforrás-azonosító az Azure-beli erőforrás egyedi azonosító. Metrikai meghatározások vagy értékeket beolvasni az Azure Monitor REST API használatakor, amelyen a művelethez használandó tervezi az erőforrás erőforrás-azonosító kell használnia. Az erőforrás-azonosítója sablon ezt a formátumot követi:

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Storage metrikákat, a storage-fiók szintjén, és a szolgáltatási szint és az Azure Monitor is biztosít. Letöltheti például csak Blob storage mérőszámait. Minden szinten vannak a saját erőforrás-azonosító, amelyre az imént adott szint esetében a metrikák beolvasásához használt.

### <a name="resource-id-for-a-storage-account"></a>A storage-fiók erőforrás-azonosító

Az alábbiakban látható egy storage-fiók erőforrás ID megadása a formátumát.

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
```

### <a name="resource-id-for-the-storage-services"></a>A tárolási szolgáltatások erőforrás-azonosító

Az alábbiakban látható az erőforrás-azonosító megadása a tároló minden formátumát.

* BLOB service erőforrás-azonosító
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
```
* TABLE service erőforrás-azonosító
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
```
* Queue szolgáltatás erőforrás-azonosító
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
```
* Service erőforrás-azonosító
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
```

### <a name="resource-id-in-azure-monitor-rest-api"></a>Az Azure Monitor REST API-t az erőforrás-azonosító

Az alábbiakban látható a minta az Azure Monitor REST API hívása során használt.

```
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
```

## <a name="capacity-metrics"></a>A kapacitás-metrikák
A kapacitás értékeihez kapnak az Azure Monitor óránként. Az értékeket a naponta frissülnek. Az aggregációs időköz határozza meg az időintervallum, amelynek metrikák jelennek meg. A támogatott időfelbontási szint összes kapacitási mérőszámot az érték egy óra (PT1H).

Az Azure Storage a következő kapacitási mérőszámot az Azure monitorban biztosít.

### <a name="account-level"></a>Fiók szintjén

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| UsedCapacity | A tárfiók által felhasznált tárterület mennyisége. Standard szintű tárfiókok esetén, az összeg, a blob, table, fájl és várólista által használt kapacitás. A premium storage-fiókok és a Blob storage-fiókok hogy megegyezik a BlobCapacity. <br/><br/> Szervezeti egység: Bájt <br/> Aggregation Type: Átlag <br/> Érték. példa: 1024 |

### <a name="blob-storage"></a>Blob Storage

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| BlobCapacity | A teljes használható a storage-fiókban lévő blobtárolóba. <br/><br/> Szervezeti egység: Bájt <br/> Aggregation Type: Átlag <br/> Érték. példa: 1024 <br/> Dimenzió: BlobType ([definíció](#metrics-dimensions)) |
| BlobCount    | A storage-fiókban tárolt blob-objektumok száma. <br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Átlag <br/> Érték. példa: 1024 <br/> Dimenzió: BlobType ([definíció](#metrics-dimensions)) |
| ContainerCount    | A tárfiókban lévő tárolók száma. <br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Átlag <br/> Érték. példa: 1024 |
| IndexCapacity     | Az ADLS Gen2 hierarchikus Index által felhasznált tárterület mérete <br/><br/> Szervezeti egység: Bájt <br/> Aggregation Type: Átlag <br/> Érték. példa: 1024 |

### <a name="table-storage"></a>Table Storage

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| TableCapacity | Használja a tárfiók Table storage mennyisége. <br/><br/> Szervezeti egység: Bájt <br/> Aggregation Type: Átlag <br/> Érték. példa: 1024 |
| TableCount   | A tárfiókban lévő táblák száma. <br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Átlag <br/> Érték. példa: 1024 |
| TableEntityCount | A storage-fiókban található táblaentitások száma. <br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Átlag <br/> Érték. példa: 1024 |

### <a name="queue-storage"></a>Queue Storage

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| QueueCapacity | Használja a tárfiók Queue storage mennyisége. <br/><br/> Szervezeti egység: Bájt <br/> Aggregation Type: Átlag <br/> Érték. példa: 1024 |
| QueueCount   | A tárfiókban lévő üzenetsorok számát. <br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Átlag <br/> Érték. példa: 1024 |
| QueueMessageCount | A storage-fiók lejárt üzenetsorbeli üzenetek száma. <br/><br/>Szervezeti egység: Darabszám <br/> Aggregation Type: Átlag <br/> Érték. példa: 1024 |

### <a name="file-storage"></a>File Storage

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| FileCapacity | Használja a tárfiók File storage mennyisége. <br/><br/> Szervezeti egység: Bájt <br/> Aggregation Type: Átlag <br/> Érték. példa: 1024 |
| FileCount   | A storage-fiókban lévő fájlok száma. <br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Átlag <br/> Érték. példa: 1024 |
| FileShareCount | A tárfiókban lévő fájlmegosztások a száma. <br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Átlag <br/> Érték. példa: 1024 |

## <a name="transaction-metrics"></a>Tranzakció-mérőszámot

Tranzakció-mérőszámot is rendelkezésre halasztása minden kérelemnél storage-fiókba az Azure Monitor az Azure Storage-ból. Esetén nem volt tevékenység a tárfiókban lesz adatok nem a tranzakció-mérőszámot az időszakban. Összes tranzakció-mérőszámot szintjén fiók és a szolgáltatás (Blob storage, Table storage, Azure Files és Queue storage) érhetők el. Az aggregációs időköz határozza meg az időintervallum, amely metrikaértékek jelennek meg. A támogatott idő magok tartozó összes tranzakció-mérőszámot PT1H és PT1M.

Az Azure Storage a következő tranzakció-mérőszámot az Azure monitorban biztosít.

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| Tranzakciók | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és sikertelen kérések, valamint a hibára futott kérések számát tartalmazza. <br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Alkalmazható dimenziók: ResponseType, GeoType, ApiName és hitelesítés ([definíció](#metrics-dimensions))<br/> Érték. példa: 1024 |
| Bejövő forgalom | A bejövő adatok mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja. <br/><br/> Szervezeti egység: Bájt <br/> Aggregation Type: Összes <br/> Alkalmazható dimenziók: GeoType ApiName és hitelesítés ([definíció](#metrics-dimensions)) <br/> Érték. példa: 1024 |
| Kimenő forgalom | A kimenő adatok mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat. <br/><br/> Szervezeti egység: Bájt <br/> Aggregation Type: Összes <br/> Alkalmazható dimenziók: GeoType ApiName és hitelesítés ([definíció](#metrics-dimensions)) <br/> Érték. példa: 1024 |
| SuccessServerLatency | Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést. <br/><br/> Szervezeti egység: Ezredmásodperc <br/> Aggregation Type: Átlag <br/> Alkalmazható dimenziók: GeoType ApiName és hitelesítés ([definíció](#metrics-dimensions)) <br/> Érték. példa: 1024 |
| SuccessE2ELatency | A tárolási szolgáltatás vagy a megadott API-művelet számára elküldött sikeres kérések végpontok közötti késésének átlaga. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. <br/><br/> Szervezeti egység: Ezredmásodperc <br/> Aggregation Type: Átlag <br/> Alkalmazható dimenziók: GeoType ApiName és hitelesítés ([definíció](#metrics-dimensions)) <br/> Érték. példa: 1024 |
| Rendelkezésre állás | A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Rendelkezésre állási a számlázható kérések teljes értékét és elosztjuk, többek között ezeket a kérelmeket, váratlan hibára, vonatkozó kérelmek száma alapján számítjuk. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik. <br/><br/> Szervezeti egység: Százalék <br/> Aggregation Type: Átlag <br/> Alkalmazható dimenziók: GeoType ApiName és hitelesítés ([definíció](#metrics-dimensions)) <br/> Érték. példa: 99.99 |

## <a name="metrics-dimensions"></a>Metrikák dimenziók

Az Azure Storage támogatja a következő dimenziókat a metrikák az Azure monitorban.

| Dimenzió neve | Leírás |
| ------------------- | ----------------- |
| BlobType | A Blob metrikák csak a blob típusa. A támogatott értékek a következők **BlockBlob** és **PageBlob**. Hozzáfűző Blob egy BlockBlob szerepel. |
| ResponseType | Tranzakció válasz típusa. Az elérhető értékek közé a következők tartoznak: <br/><br/> <li>ServerOtherError: Minden egyéb kiszolgálóoldali hiba, kivéve az ismertetett hibákat </li> <li> ServerBusyError: Hitelesített kérés, amely HTTP 503-as állapotkódot adott vissza. </li> <li> ServerTimeoutError: Hitelesített kérés, amely túllépte az időkorlátot, és HTTP 500-as állapotkódot adott vissza. Az időtúllépés egy kiszolgálóhiba miatt lépett fel. </li> <li> AuthorizationError: Hitelesített kérés, amely jogosulatlan adathozzáférés vagy egy engedélyezési hiba miatt hiúsult meg. </li> <li> NetworkError: Hitelesített kérés, amely hálózati hibák miatt hiúsult meg. Leggyakrabban akkor fordul elő, ha egy ügyfél idő előtt, az időkorlát letelte előtt zár be egy kapcsolatot. </li> <li>    ClientThrottlingError: Ügyféloldali szabályozási hiba. </li> <li> ClientTimeoutError: Hitelesített kérés, amely túllépte az időkorlátot, és HTTP 500-as állapotkódot adott vissza. Ha az ügyfél hálózati időkorlátja vagy a kérés időkorlátja a tárolási szolgáltatás által várt értéknél alacsonyabbra van állítva, akkor ez egy várt időtúllépés. Máskülönben a rendszer ServerTimeoutError hibát jelent. </li> <li> ClientOtherError: Minden egyéb ügyféloldali hiba, kivéve az ismertetett hibákat. </li> <li> Sikeres művelet: Sikeres kérelem.|
| GeoType | A tranzakció elsődleges vagy másodlagos fürtből. Az elérhető értékek a következők: elsődleges és másodlagos. Érvényes írásvédett Georedundáns redundáns Storage(RA-GRS) másodlagos bérlőtől objektumok olvasásakor. |
| ApiName | Művelet neve. Példa: <br/> <li>CreateContainer</li> <li>DeleteBlob</li> <li>GetBlob</li> Az összes művelet nevekkel kapcsolatban lásd: [dokumentum](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| Authentication | A tranzakciók használt hitelesítés típusa. Az elérhető értékek közé a következők tartoznak: <br/> <li>AccountKey: A tranzakció tárfiókkulcs adatokkal van hitelesítve.</li> <li>SAS: A tranzakció közös hozzáférésű jogosultságkódok adatokkal van hitelesítve.</li> <li>OAuth: A tranzakció OAuth hozzáférési tokenek adatokkal van hitelesítve.</li> <li>Névtelen: A tranzakció névtelenül van szükség. Ellenőrzési kérelem nem tartalmazza.</li> <li>AnonymousPreflight: A tranzakció ellenőrzési kérést.</li> |

A metrikák támogató dimenziók esetében adja meg a megfelelő mérőszámok értékeit a dimenzió értéket kell. Például, ha megtekinti **tranzakciók** értéket a sikeres válaszok szűrése kell a **ResponseType** dimenzió **sikeres**. Vagy ha megtekinti **BlobCount** érték Blokkblob, meg kell szűrni a **BlobType** dimenzió **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>A szolgáltatás folytonosságának örökölt metrikák

Az Azure Monitor felügyelt metrikákkal párhuzamos örökölt metrikák érhetők el. A támogatási azonos tárolja, amíg Azure Storage-be a szolgáltatást a régi metrikák is.

## <a name="faq"></a>GYIK

**Új mérőszámok támogatja a klasszikus tárfiókot?**

Nem, az új mérőszámok az Azure Monitor csak támogatása az Azure Resource Manager tárfiókok. Ha azt szeretné, a metrikák használata a Storage-fiókok, szüksége migrálása az Azure Resource Manager-tárfiókba. Lásd: [áttelepítése az Azure Resource Manager-](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

**Azure Storage támogatja a mérőszámok a Managed Disks vagy nem felügyelt lemezek?**

Nem, az Azure Compute a támogatja a mérőszámok használatát a lemezeket. Lásd: [cikk](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) további részletekért.

**Hogyan leképezése és áttelepíteni az új mérőszámok klasszikus metrikák?**

Részletes leképezés klasszikus mérőszámok és az új mérőszámok között található [Azure Storage-mérőszámok áttelepítési](./storage-metrics-migration.md).

## <a name="next-steps"></a>További lépések

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
