---
title: Az Azure Storage mérőszámainak Azure figyelőben |} Microsoft Docs
description: Ismerje meg az új mérőszámok Azure figyelő kínál.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 09/05/2017
ms.author: fryu
ms.openlocfilehash: 5316013631670ab3612e441e64e2f330f01941b7
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="azure-storage-metrics-in-azure-monitor-preview"></a>Az Azure Storage mérőszámainak Azure figyelőben (előzetes verzió)

Az Azure Storage a metrikákat elemezheti a használati trendeket, tárolni kívánt nyomkövetési kérelmek és eseményadatokat a storage-fiók.

Az Azure biztosít, egységes felhasználói felületek keresztüli különböző Azure-szolgáltatásokhoz. További információkért lásd: [Azure figyelő](../../monitoring-and-diagnostics/monitoring-overview.md). Az Azure Storage metrika adatokat küld az Azure-figyelő platform integrálja az Azure-figyelő.

## <a name="access-metrics"></a>Hozzáférés metrikák

Az Azure hozzáférési metrikák több lehetőség is biztosít. Végezheti el azokat a [Azure-portálon](https://portal.azure.com), az Azure figyelő API-k (REST és .net) és elemzési megoldások, például a művelet felügyeleti csomag és az Event Hubs. További információkért lásd: [Azure figyelő metrikák](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Alapértelmezés szerint engedélyezve vannak a metrikákat, és érheti el az adatok az elmúlt 30 nap. Ha szeretné megőrizni az adatokat egy hosszabb ideig, úgy archiválhatók metrikai adatok az Azure Storage-fiók. Ez úgy van konfigurálva a [diagnosztikai beállítások](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) Azure-figyelőben.

### <a name="access-metrics-in-the-azure-portal"></a>Az Azure portálon hozzáférés metrikák

Figyelheti a metrikák adott idő alatt az Azure portálon. A következő példa bemutatja, hogyan megtekintéséhez **UsedCapacity** fiók szintjén.

![Képernyőkép a metrikát a az Azure-portál elérése](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

A dimenziók támogató metrika szűrheti kell a kívánt dimenzió értékű. A következő példa bemutatja, hogyan megtekintéséhez **tranzakciók** a fiók szintjén **sikeres** válaszának típusa.

![képernyőfelvétel az Azure portálon dimenzió metrikák elérése](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>A REST API Access metrikák

Az Azure biztosít, [REST API-k](/rest/api/monitor/) metrika definíció- és értékek olvasni. Ez a szakasz bemutatja, hogyan olvasni a tárolási. Erőforrás-azonosító szerepel az összes REST API-k. További információkért olvassa el [Storage szolgáltatásaihoz erőforrás-azonosító ismertetése](#understanding-resource-id-for-services-in-storage).

A következő példa bemutatja, hogyan használható [ArmClient](https://github.com/projectkudu/ARMClient) egyszerűbbé teheti a REST API-val tesztelése a parancssorból.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Fiók szintű metrika definíciója REST API-val

A következő példa bemutatja, hogyan listázhat fiók szintjén metrika definíciója:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2017-05-01-preview

```

Ha ki szeretné listázni a blob, table, fájl vagy várólista metrikai meghatározásainak, az API-t adjon meg a különböző erőforrás-azonosítók az egyes szolgáltatásokhoz.

A válasz JSON formátumban metrika definícióját tartalmazza:

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

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Olvassa el a fiók szintű metrika értékek REST API-val

A következő példa bemutatja, hogyan lehet olvasni a fiók szintjén metrika adatokat:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metric=Availability&api-version=2017-05-01-preview&aggregation=Average&interval=PT1H"

```

Újabb példa, ha szeretné olvasni metrika értékeinek blob, table, fájl vagy várólista, meg kell adnia különböző erőforrás-azonosítók minden egyes szolgáltatás API-val.

A következő válasz JSON formátumban metrika értéket tartalmaz:

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

### <a name="access-metrics-with-the-net-sdk"></a>A .net SDK hozzáférés metrikák

Az Azure biztosít, [.Net SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) metrika definíció- és értékek olvasni. A [példakód](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) bemutatja, hogyan használhatja az SDK-más paraméterekkel együtt. Kell használnia `0.18.0-preview` vagy a storage mérőszámainak újabb verziója. Erőforrás-Azonosítót használja a .net SDK. További információkért olvassa el [Storage szolgáltatásaihoz erőforrás-azonosító ismertetése](#understanding-resource-id-for-services-in-storage).

A következő példa bemutatja, hogyan olvasni tárolási Azure Monitor .net SDK használatával.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Lista fiók szintű metrika-definíció a .net SDK

A következő példa bemutatja, hogyan listázhat fiók szintjén metrika definíciója:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        //How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        Using metrics in Azure Monitor is currently free. However, if you use additional solutions ingesting metrics data, you may be billed by these solutions. For example, you are billed by Azure Storage if you archive metrics data to an Azure Storage account. Or you are billed by Operation Management Suite (OMS) if you stream metrics data to OMS for advanced analysis.
        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
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

Ha ki szeretné listázni a blob, table, fájl vagy várólista metrikai meghatározásainak, az API-t adjon meg a különböző erőforrás-azonosítók az egyes szolgáltatásokhoz.

#### <a name="read-metric-values-with-the-net-sdk"></a>Olvassa el a metrika értékek a .net SDK

A következő példa bemutatja, hogyan olvashatja `UsedCapacity` fiók szintjén adatokat:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        //How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToString("o");
        string endDate = DateTime.Now.ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metric: "UsedCapacity",

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

Újabb példa, ha szeretné olvasni metrika értékeinek blob, table, fájl vagy várólista, meg kell adnia különböző erőforrás-azonosítók minden egyes szolgáltatás API-val.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Olvassa el a .net SDK többdimenziós metrika értékek

A többdimenziós metrikáihoz kell meta data szűrő határozza meg, ha azt szeretné, hogy az adott dimenzió értékekre metrika adatokat olvasni.

A következő példa bemutatja, hogyan olvassa el a metrika-több dimenzió támogató a metrikaadatokat:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "{SubscriptionID}";
        //How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToString("o");
        string endDate = DateTime.Now.ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metric: "BlobCapacity",
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

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Erőforrás-azonosító az Azure Storage szolgáltatások ismertetése

Erőforrás-azonosító egy erőforrást az Azure-ban egy egyedi azonosítót. A Azure REST API használatával olvassa el a metrikák definíciók vagy értékek, a erőforrás-azonosító az erőforrás üzemeltetni, kívánó kell használnia. Az erőforrás-azonosító sablon ezt a formátumot követi:

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
`

Tárolási metrikákat, a tárolási fiók szinttel és a szolgáltatási szint Azure megfigyelővel biztosít. Például csak a Blob Storage metrikák kérheti le. Minden szinttel rendelkezik, saját erőforrás-azonosító csak adott szinten a metrikák beolvasásához használt.

### <a name="resource-id-for-a-storage-account"></a>A storage-fiókok erőforrás-azonosító

Az alábbiakban látható megadásával az erőforrás-azonosítója egy tárfiók formátumát.

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
`

### <a name="resource-id-for-the-storage-services"></a>A tárolási szolgáltatások erőforrás-azonosító

Az alábbiakban látható ad meg az erőforrás-azonosítója a tárolási szolgáltatások formátumát.

* BLOB szolgáltatás erőforrás-azonosító `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
`
* TABLE szolgáltatás erőforrás-azonosító `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
`
* Várólista szolgáltatás erőforrás-azonosító `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
`
* Szolgáltatás erőforrás-azonosító `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
`

### <a name="resource-id-in-azure-monitor-rest-api"></a>Erőforrás-azonosító az Azure figyelő REST API-n

Az alábbiakban látható a Azure REST API meghívásakor használt mintát.

`
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
`

## <a name="capacity-metrics"></a>A kapacitás metrikák
A kapacitás értékeihez Azure figyelő óránként küldi. Az értékek a naponta frissülnek. Az idő felbontása határozza meg az időtartam, amelynek metrikák értékek jelenjenek meg. A támogatott időfelbontási a teljesítmény-mérőszámait egy óra (PT1H).

Az Azure Storage Azure a figyelő a következő kapacitás mérőszámait jeleníti meg.

### <a name="account-level"></a>Fiók szint

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| UsedCapacity | A tárfiók által használt tárhely mennyiségét. Standard szintű storage-fiókok esetén a blob, table, fájlt és várólista által használt kapacitás összege. Prémium szintű storage-fiókok és a Blob storage-fiókok megegyezik a BlobCapacity. <br/><br/> Egység: bájtok <br/> Összesítési típusát: átlagos <br/> Példa érték: 1024 |

### <a name="blob-storage"></a>Blob Storage

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| BlobCapacity | A teljes száma a tárfiókban lévő használt Blob-tároló. <br/><br/> Egység: bájtok <br/> Összesítési típusát: átlagos <br/> Példa érték: 1024 <br/> Dimenzió: BlobType ([Definition](#metrics-dimensions)) |
| BlobCount    | A storage-fiókban tárolt blob objektumok száma. <br/><br/> Egység: száma <br/> Összesítési típusát: átlagos <br/> Példa érték: 1024 <br/> Dimenzió: BlobType ([Definition](#metrics-dimensions)) |
| ContainerCount    | A tárfiók a tárolók száma. <br/><br/> Egység: száma <br/> Összesítési típusát: átlagos <br/> Példa érték: 1024 |

### <a name="table-storage"></a>Table Storage

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| TableCapacity | A Table storage a tárfiók által használt mennyisége. <br/><br/> Egység: bájtok <br/> Összesítési típusát: átlagos <br/> Példa érték: 1024 |
| TableCount   | A tárfiókban lévő táblák számát. <br/><br/> Egység: száma <br/> Összesítési típusát: átlagos <br/> Példa érték: 1024 |
| TableEntityCount | A tárfiókban lévő táblaentitásokat száma. <br/><br/> Egység: száma <br/> Összesítési típusát: átlagos <br/> Példa érték: 1024 |

### <a name="queue-storage"></a>Queue Storage

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| QueueCapacity | A storage-fiókot használják a Queue storage mennyisége. <br/><br/> Egység: bájtok <br/> Összesítési típusát: átlagos <br/> Példa érték: 1024 |
| QueueCount   | A tárfiókban lévő várólisták száma. <br/><br/> Egység: száma <br/> Összesítési típusát: átlagos <br/> Példa érték: 1024 |
| QueueMessageCount | A tárfiókban lévő leküldéshez üzenetek száma. <br/><br/>Egység: száma <br/> Összesítési típusát: átlagos <br/> Példa érték: 1024 |

### <a name="file-storage"></a>File Storage

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| FileCapacity | A tárfiók által használt fájltároló mennyisége. <br/><br/> Egység: bájtok <br/> Összesítési típusát: átlagos <br/> Példa érték: 1024 |
| FileCount   | A tárfiókban lévő fájlok száma. <br/><br/> Egység: száma <br/> Összesítési típusát: átlagos <br/> Példa érték: 1024 |
| FileShareCount | Hány fájl osztja meg a tárfiók. <br/><br/> Egység: száma <br/> Összesítési típusát: átlagos <br/> Példa érték: 1024 |

## <a name="transaction-metrics"></a>Tranzakció metrikák

Tranzakció metrikák kell küldeni az Azure Storage Azure figyelő percenként. Minden tranzakció metrikák érhetők el a forráshelyfiók és a szolgáltatási szint (Blob-tároló, a Table storage, Azure-fájlok és a Queue storage). Az idő felbontása határozza meg a alatt az időtartam alatt, amelyek mérték értékek jelenjenek meg. A támogatott idő szemek összes tranzakció metrikáihoz PT1H és PT1M.

Az Azure Storage Azure a figyelő a következő tranzakció mérőszámait jeleníti meg.

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| Tranzakciók | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és sikertelen kérések, valamint a hibára futott kérések számát tartalmazza. <br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Alkalmazható dimenziók: ResponseType, GeoType, APINÉV ([Definition](#metrics-dimensions))<br/> Példa érték: 1024 |
| Belépő | A bejövő adatok mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja. <br/><br/> Egység: bájtok <br/> Összesítési típusát: teljes <br/> Alkalmazható dimenziók: GeoType, APINÉV ([Definition](#metrics-dimensions)) <br/> Példa érték: 1024 |
| Kimenő forgalom | A kimenő adatok mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat. <br/><br/> Egység: bájtok <br/> Összesítési típusát: teljes <br/> Alkalmazható dimenziók: GeoType, APINÉV ([Definition](#metrics-dimensions)) <br/> Példa érték: 1024 |
| SuccessServerLatency | Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést. <br/><br/> Egység: ideje ezredmásodpercben <br/> Összesítési típusát: átlagos <br/> Alkalmazható dimenziók: GeoType, APINÉV ([Definition](#metrics-dimensions)) <br/> Példa érték: 1024 |
| SuccessE2ELatency | A tárolási szolgáltatás vagy a megadott API-művelet számára elküldött sikeres kérések végpontok közötti késésének átlaga. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. <br/><br/> Egység: ideje ezredmásodpercben <br/> Összesítési típusát: átlagos <br/> Alkalmazható dimenziók: GeoType, APINÉV ([Definition](#metrics-dimensions)) <br/> Példa érték: 1024 |
| Rendelkezésre állás | A százalékos aránya a társzolgáltatás vagy a megadott API-művelet rendelkezésre állása. Rendelkezésre állási kiszámítása a számlázható kérelmek teljes száma érték és elosztjuk, beleértve az ezeket a kérelmeket, amely a váratlan hibák előállított vonatkozó kérelmek száma. Váratlan hibákat eredményez romlik a rendelkezésre állás a társzolgáltatás vagy a megadott API-művelet. <br/><br/> Egység: százaléka <br/> Összesítési típusát: átlagos <br/> Alkalmazható dimenziók: GeoType, APINÉV ([Definition](#metrics-dimensions)) <br/> Érték példa: 99,99 |

## <a name="metrics-dimensions"></a>Metrikák dimenziók

Az Azure Storage támogatja az Azure-figyelő a metrikák méretei követően.

| Dimenzió neve | Leírás |
| ------------------- | ----------------- |
| BlobType | Csak a Blob metrikáihoz blob típusa. A támogatott értékek a következők **BlockBlob** és **PageBlob**. Hozzáfűző Blob BlockBlob tartalmazza. |
| ResponseType | Tranzakció választípus. A lehetséges értékek a következők: <br/><br/> <li>ServerOtherError: Minden más kiszolgálóoldali hiba kivéve ismertetett állók közül. </li> <li> ServerBusyError: Hitelesítése a kérelemhez, amely egy 503-as HTTP-állapotkódot küldte. </li> <li> ServerTimeoutError: Időtúllépés hitelesített kérelmet, amely az 500-as HTTP-állapotkódot küldte. Időtúllépés történt a kiszolgáló hibája miatt. </li> <li> AuthorizationError: Hitelesített kérelmet, amely a jogosulatlan hozzáférés adat-vagy engedélyezési hiba miatt sikertelen volt. </li> <li> NetworkError: Hitelesített kérelmet, amely hálózati hibák miatt nem sikerült. Általában akkor fordul elő, egy ügyfél túl korán bezárása után a kapcsolat időtúllépés lejárati idejük előtt. </li> <li>    ClientThrottlingError: Ügyféloldali szabályozási hiba. </li> <li> ClientTimeoutError: Időtúllépés hitelesített kérelmet, amely az 500-as HTTP-állapotkódot küldte. Ha az ügyfél hálózati időtúllépés vagy a kérelmi időkorlátot. a tároló szolgáltatás által vártnál alacsonyabb értékre van beállítva, akkor egy várt időtúllépés. Ellenkező esetben azt az elvártnak megfelelően egy ServerTimeoutError. </li> <li> ClientOtherError: Más ügyféloldali hibák kivételével ismertetett néhányat a meglévők közül. </li> <li> Sikerült: A kérelem sikeres|
| GeoType | Tranzakció elsődleges vagy másodlagos fürtből. A rendelkezésre álló értékeket tartalmazza az elsődleges és másodlagos. Érvényes írásvédett Georedundáns redundáns Storage(RA-GRS) másodlagos bérlőhöz objektumok olvasásakor. |
| APINÉV | A művelet neve. Példa: <br/> <li>CreateContainer</li> <li>DeleteBlob</li> <li>GetBlob</li> Összes művelet nevét, lásd: [dokumentum](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages#logged-operations.md). |

A metrikák támogató dimenziók esetében meg kell adnia a megfelelő mérőszámok értékeit a dimenzió értéket. Például, ha megnézi **tranzakciók** érték sikeres válaszok szűrése szüksége a **ResponseType** dimenzió **sikeres**. Vagy ha megnézi **BlobCount** érték Blokkblob, telepítenie kell szűrheti a **BlobType** dimenzió **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>A szolgáltatás folytonosságának örökölt mérőszámokat

A hagyományos metrikák felügyelt Azure-figyelő metrikák párhuzamosan érhetők el. A támogatási tartja azonos mindaddig, amíg az Azure Storage karakterlánccal végződik-e a szolgáltatás örökölt mérőszámokat.

## <a name="next-steps"></a>További lépések

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
