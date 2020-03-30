---
title: Az Azure Storage-metrikák az Azure Monitorban | Microsoft dokumentumok
description: Ismerje meg az Azure Monitor által kínált új metrikákat.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 73181222bf3f15bbbac24fc253eddfea1c57bc6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247095"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Azure Storage-metrikák az Azure Monitorban

Az Azure Storage metrikákkal elemezheti a használati trendeket, nyomon követheti a kérelmeket, és diagnosztizálhatja a tárfiókkal kapcsolatos problémákat.

Az Azure Monitor egységes felhasználói felületeket biztosít a különböző Azure-szolgáltatások figyeléséhez. További információ: [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md). Az Azure Storage integrálja az Azure Monitort azáltal, hogy metrikaadatokat küld az Azure Monitor platformra.

## <a name="access-metrics"></a>Hozzáférési mutatók

Az Azure Monitor többféle módon érheti el a metrikákat. Elérheti őket az [Azure Portalon,](https://portal.azure.com)az Azure Monitor API-k (REST és .NET) és elemzési megoldások, például az Event Hubs. További információ: [Azure Monitor Metrics](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

A mérőszámok alapértelmezés szerint engedélyezve vannak, és az elmúlt 93 nap adataihoz férhet hozzá. Ha hosszabb ideig kell megőriznie az adatokat, archiválhatja a metrikák adatait egy Azure Storage-fiókba. Ez az Azure Monitor [diagnosztikai beállításaiban](../../azure-monitor/platform/platform-logs-overview.md) van konfigurálva.

### <a name="access-metrics-in-the-azure-portal"></a>Metrikák elérése az Azure Portalon

Az Azure Portalon idővel figyelheti a metrikákat. A következő példa bemutatja, hogyan tekintheti meg a **tranzakciókat** a számla szintjén.

![képernyőkép a metrikák eléréséről az Azure Portalon](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

A dimenziókat támogató mutatók esetében szűrheti a metrikát a kívánt dimenzióértékkel. A következő példa bemutatja, hogyan tekintheti meg **a tranzakciókat** egy adott művelet ben egy adott művelet értékeinek kiválasztásával **az API-név** dimenzióhoz.

![képernyőkép a dimenziós metrikák eléréséről az Azure Portalon](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Metrikák elérése a REST API-val

Az Azure Monitor [REST API-kat](/rest/api/monitor/) biztosít a metrikadefinícióés értékek olvasásához. Ez a szakasz bemutatja, hogyan olvassa el a tárolási metrikák. Az erőforrás-azonosító minden REST API-ban használatos. További információkért olvassa el a Szolgáltatás a Storage szolgáltatásai erőforrás-azonosítójának ismertetése című témakört.

A következő példa bemutatja, hogyan használhatja az [ArmClient](https://github.com/projectkudu/ARMClient) a parancssorban, hogy egyszerűsítse a tesztelést a REST API-val.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Fiókszintű metrikadefiníció listázása a REST API-val

A következő példa bemutatja, hogyan sorolhatod fel a metrikadefiníciót a fiók szintjén:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Ha szeretné felsorolni a blob, tábla, fájl vagy várólista metrikadefinícióit, meg kell adnia az API-val rendelkező minden szolgáltatáshoz különböző erőforrás-azonosítókat.

A válasz a metrikadefiníciót tartalmazza JSON formátumban:

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

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Fiókszintű metrikaértékek olvasása a REST API-val

A következő példa bemutatja, hogyan lehet olvasni a metrikaadatokat a fiók szintjén:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

A fenti példában, ha azt szeretné olvasni metrika értékek blob, tábla, fájl vagy várólista, meg kell adnia a különböző erőforrás-azonosítók minden szolgáltatás az API-val.

A következő válasz JSON formátumú metrikus értékeket tartalmaz:

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

### <a name="access-metrics-with-the-net-sdk"></a>A .NET SDK-val rendelkező mérőszámok elérése

Az Azure Monitor [.NET SDK-t](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) biztosít a metrikadefiníció és -értékek olvasásához. A [mintakód bemutatja,](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) hogyan kell használni az SDK-t különböző paraméterekkel. A tárolási `0.18.0-preview` metrikákhoz vagy újabb verziót kell használnia. Az erőforrás-azonosító a .NET SDK-ban használatos. További információkért olvassa el a Szolgáltatás a Storage szolgáltatásai erőforrás-azonosítójának ismertetése című témakört.

A következő példa bemutatja, hogyan használhatja az Azure Monitor .NET SDK tárolási metrikák olvasásához.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Listaszámlaszintű metrikadefiníció a .NET SDK-val

A következő példa bemutatja, hogyan sorolhatod fel a metrikadefiníciót a fiók szintjén:

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

Ha szeretné felsorolni a blob, tábla, fájl vagy várólista metrikadefinícióit, meg kell adnia az API-val rendelkező minden szolgáltatáshoz különböző erőforrás-azonosítókat.

#### <a name="read-metric-values-with-the-net-sdk"></a>Metrikus értékek olvasása a .NET SDK-val

A következő példa bemutatja, hogyan lehet adatokat olvasni `UsedCapacity` a fiók szintjén:

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

A fenti példában, ha azt szeretné olvasni metrika értékek blob, tábla, fájl vagy várólista, meg kell adnia a különböző erőforrás-azonosítók minden szolgáltatás az API-val.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Többdimenziós metrikaértékek olvasása a .NET SDK-val

Többdimenziós metrikák esetén meg kell határoznia a metaadat-szűrőt, ha adott dimenzióértékre vonatkozó metrikaadatokat szeretne olvasni.

A következő példa bemutatja, hogyan olvashatja a metrikaadatokat a többdimenziós metrika alapján:

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

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Az Azure Storage szolgáltatásainak erőforrás-azonosítójának ismertetése

Az erőforrás-azonosító az Azure-ban lévő erőforrások egyedi azonosítója. Ha az Azure Monitor REST API-t használja a metrikák definícióinak vagy értékeinek olvasásához, erőforrás-azonosítót kell használnia ahhoz az erőforráshoz, amelyen működni kíván. Az erőforrás-azonosító sablon a következő formátumot követi:

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

A Storage az Azure Monitor segítségével a tárfiók és a szolgáltatás szintjén is biztosít metrikákat. Például lekérheti metrikák csak Blob storage. Minden szint saját erőforrás-azonosítóval rendelkezik, amely csak ezen a szinten a metrikák lekéréséhez szolgál.

### <a name="resource-id-for-a-storage-account"></a>Tárfiók erőforrásazonosítója

A következőkben látható a tárfiók erőforrás-azonosítójának megadásának formátuma.

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
```

### <a name="resource-id-for-the-storage-services"></a>A tárolási szolgáltatások erőforrásazonosítója

Az alábbiakban látható az egyes tárolószolgáltatások erőforrás-azonosítójának megadásának formátuma.

* Blob-szolgáltatás erőforrásazonosítója
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
```
* Táblaszolgáltatás-erőforrás azonosítója
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
```
* Várólista-szolgáltatás erőforrásazonosítója
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
```
* Fájlszolgáltatás-erőforrás azonosítója
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
```

### <a name="resource-id-in-azure-monitor-rest-api"></a>Erőforrás-azonosító az Azure Monitor REST API-jában

Az alábbiakban az Azure Monitor REST API-t hívásakor használt minta látható.

```
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
```

## <a name="capacity-metrics"></a>Kapacitásmetrikák
A kapacitásmetrikák értékeit óránként küldi el az Azure Monitor. Az értékek naponta frissülnek. Az idősécséma határozza meg azt az időintervallumot, amelyhez a metrikák értékei megjelennek. A támogatott időgabona az összes kapacitás metrikák egy óra (PT1H).

Az Azure Storage a következő kapacitásmetrikákat biztosítja az Azure Monitorban.

### <a name="account-level"></a>Fiók szintje

| Metrikus neve | Leírás |
| ------------------- | ----------------- |
| Használtkapacitás | A tárfiók által használt tárterület mennyisége. Standard szintű tárfiókok esetében ez a blob, a tábla, a fájl és a várólista által használt kapacitás összege. Prémium szintű és Blob Storage-fiókok esetében a BlobCapacity értékével azonos. <br/><br/> Egység: Bájt <br/> Összesítés típusa: Átlag <br/> Példa értékre: 1024 |

### <a name="blob-storage"></a>Blob Storage

| Metrikus neve | Leírás |
| ------------------- | ----------------- |
| BlobCapacity kapacitás | A storage-fiókban használt Blob-tárterület összesen. <br/><br/> Egység: Bájt <br/> Összesítés típusa: Átlag <br/> Példa értékre: 1024 <br/> Méretek: **BlobType**és **BlobTier** ([Definíció](#metrics-dimensions)) |
| BlobCount (BlobCount)    | A tárfiókban tárolt blobobjektumok száma. <br/><br/> Egység: Darabszám <br/> Összesítés típusa: Átlag <br/> Példa értékre: 1024 <br/> Méretek: **BlobType**és **BlobTier** ([Definíció](#metrics-dimensions)) |
| Konténerszám    | A tárolók száma a tárfiókban. <br/><br/> Egység: Darabszám <br/> Összesítés típusa: Átlag <br/> Példa értékre: 1024 |
| Indexkapacitás     | Az ADLS Gen2 hierarchikus index által használt tárterület mennyisége <br/><br/> Egység: Bájt <br/> Összesítés típusa: Átlag <br/> Példa értékre: 1024 |

### <a name="table-storage"></a>Table Storage

| Metrikus neve | Leírás |
| ------------------- | ----------------- |
| Táblakapacitás | A tárfiók által használt table storage-tárterület mennyisége. <br/><br/> Egység: Bájt <br/> Összesítés típusa: Átlag <br/> Példa értékre: 1024 |
| Tábladarabszám   | A tárfiókban lévő táblák száma. <br/><br/> Egység: Darabszám <br/> Összesítés típusa: Átlag <br/> Példa értékre: 1024 |
| Táblaentitásszáma | A tárfiókban lévő táblaentitások száma. <br/><br/> Egység: Darabszám <br/> Összesítés típusa: Átlag <br/> Példa értékre: 1024 |

### <a name="queue-storage"></a>Queue Storage

| Metrikus neve | Leírás |
| ------------------- | ----------------- |
| Várólistakapacitás | A tárfiók által használt várólista-tárterület mennyisége. <br/><br/> Egység: Bájt <br/> Összesítés típusa: Átlag <br/> Példa értékre: 1024 |
| Várólistaszáma   | A tárfiókban lévő várólisták száma. <br/><br/> Egység: Darabszám <br/> Összesítés típusa: Átlag <br/> Példa értékre: 1024 |
| QueueMessageCount (Üzenetsor) | A tárfiókban le nem járt várólistaüzenetek száma. <br/><br/>Egység: Darabszám <br/> Összesítés típusa: Átlag <br/> Példa értékre: 1024 |

### <a name="file-storage"></a>File Storage

| Metrikus neve | Leírás |
| ------------------- | ----------------- |
| Fájlkapacitás | A tárfiók által használt fájltár mennyisége. <br/><br/> Egység: Bájt <br/> Összesítés típusa: Átlag <br/> Példa értékre: 1024 |
| Fájlszám   | A tárfiókban lévő fájlok száma. <br/><br/> Egység: Darabszám <br/> Összesítés típusa: Átlag <br/> Példa értékre: 1024 |
| FileShareCount | A tárfiók fájlmegosztásainak száma. <br/><br/> Egység: Darabszám <br/> Összesítés típusa: Átlag <br/> Példa értékre: 1024 |

## <a name="transaction-metrics"></a>Tranzakciómetrikák

A tranzakciós metrikák at minden kérés egy tárfiók az Azure Storage az Azure Monitor. Abban az esetben, ha nincs tevékenység a tárfiókban, nem lesznek adatok a tranzakciós metrikák az időszakban. Az összes tranzakciós metrika fiók- és szolgáltatásszinten is elérhető (Blob storage, Table storage, Azure Files és Queue storage). Az idősécséma határozza meg a metrikaértékek bemutatásának időintervallumát. A támogatott időszemek az összes tranzakciós metrikához PT1H és PT1M.

Az Azure Storage a következő tranzakciós metrikákat biztosítja az Azure Monitorban.

| Metrikus neve | Leírás |
| ------------------- | ----------------- |
| Tranzakciók | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és sikertelen kérések, valamint a hibára futott kérések számát tartalmazza. <br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Alkalmazható dimenziók: ResponseType, GeoType, ApiName és Authentication ([Definíció](#metrics-dimensions))<br/> Példa értékre: 1024 |
| Bejövő forgalom | A bejövő adatok mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja. <br/><br/> Egység: Bájt <br/> Összesítés típusa: Összes <br/> Alkalmazható dimenziók: GeoType, ApiName és Authentication ([Definíció](#metrics-dimensions)) <br/> Példa értékre: 1024 |
| Kimenő forgalom | A kimenő adatok mennyisége. Ez a szám magában foglalja a kimenő egy külső ügyfél az Azure Storage-ból, valamint az Azure-on belüli kimenő. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat. <br/><br/> Egység: Bájt <br/> Összesítés típusa: Összes <br/> Alkalmazható dimenziók: GeoType, ApiName és Authentication ([Definíció](#metrics-dimensions)) <br/> Példa értékre: 1024 |
| SuccessServerLatency (Sikereskiszolgálók átkának) | Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést. <br/><br/> Egység: Ezredmásodperc <br/> Összesítés típusa: Átlag <br/> Alkalmazható dimenziók: GeoType, ApiName és Authentication ([Definíció](#metrics-dimensions)) <br/> Példa értékre: 1024 |
| SuccessE2ELatency (SuccessE2ELatency) | A tárolási szolgáltatás vagy a megadott API-művelet számára elküldött sikeres kérések végpontok közötti késésének átlaga. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. <br/><br/> Egység: Ezredmásodperc <br/> Összesítés típusa: Átlag <br/> Alkalmazható dimenziók: GeoType, ApiName és Authentication ([Definíció](#metrics-dimensions)) <br/> Példa értékre: 1024 |
| Rendelkezésre állás | A rendelkezésre állás i a storage szolgáltatás vagy a megadott API-művelet rendelkezésre állásának százalékos aránya. A Rendelkezésre állás az összes számlázható kérelem értékének és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik. <br/><br/> Egység: Százalék <br/> Összesítés típusa: Átlag <br/> Alkalmazható dimenziók: GeoType, ApiName és Authentication ([Definíció](#metrics-dimensions)) <br/> Példa értékre: 99.99 |

## <a name="metrics-dimensions"></a>Metrikadimenziók

Az Azure Storage támogatja a következő dimenziók metrikák az Azure Monitorban.

| Dimenzió neve | Leírás |
| ------------------- | ----------------- |
| **BlobType típus** | A blob típusa csak a Blob metrikák. A támogatott értékek a **BlockBlob**, **A PageBlob**és az **Azure Data Lake Storage.** A Blob hozzáfűző része a BlockBlob része. |
| **BlobTier között** | Az Azure Storage különböző hozzáférési szinteket kínál, amelyek lehetővé teszik a blobobjektum-adatok legköltséghatékonyabb módon történő tárolását. További információk az [Azure Storage blobcsomagjában.](../blobs/storage-blob-storage-tiers.md) A támogatott értékek a következők: <br/> <li>**Forró**: Forró szint</li> <li>**Cool**: Cool tier</li> <li>**Archívum**: Archív szint</li> <li>**Prémium**: Prémium szint blokkblobhoz</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: Tier típusok a prémium szintű lapblobhoz</li> <li>**Standard**: A normál lapblob okának rétegtípusa</li> <li>**Nem többszintű**: Általános célú v1 tárfiók hozadéktípusa</li> |
| **GeoTípus** | Tranzakció elsődleges vagy másodlagos fürtről. A rendelkezésre álló értékek közé tartozik **az Elsődleges** és **a Másodlagos**. Az olvasási szintű georedundáns tárolásra (RA-GRS) vonatkozik a másodlagos bérlőobjektum-olvasási adatokra. |
| **Választípusa** | Tranzakcióválasz típusa. Az elérhető értékek közé a következők tartoznak: <br/><br/> <li>**ServerOtherError**: Minden más kiszolgálóoldali hiba, kivéve a leírt hibákat </li> <li>**ServerBusyError**: Hitelesített kérelem, amely HTTP 503 állapotkódot adott vissza. </li> <li>**ServerTimeoutError**: HTTP 500 állapotkódot visszaadott, időbeli kimenő hitelesített kérelem. Az időtúllépés egy kiszolgálóhiba miatt lépett fel. </li> <li>**AuthorizationError**: Hitelesített kérelem, amely jogosulatlan hozzáférés vagy engedélyezési hiba miatt sikertelen volt. </li> <li>**NetworkError**: Hálózati hiba miatt sikertelen hitelesített kérelem. Leggyakrabban akkor fordul elő, ha egy ügyfél idő előtt, az időkorlát letelte előtt zár be egy kapcsolatot. </li>  <li>**ClientAccountBandwidthThrottlingError**: A kérelem a [tárfiók méretezhetőségi korlátainak](scalability-targets-standard-account.md)túllépéséhez szükséges sávszélességre van szabályozva .</li><li>**ClientAccountRequestThrottlingError**: A rendszer kérésre szabályozta a rendszer a [tárfiók méretezhetőségi határértékeinek](scalability-targets-standard-account.md)túllépéséhez.<li>**ClientThrottlingError**: Egyéb ügyféloldali szabályozási hiba. A ClientAccountBandwidthThrottlingError és a ClientAccountRequestThrottlingError ki van zárva.</li> <li>**ClientTimeoutError**: HTTP 500 állapotkódot visszaadott, időbeli kimenő hitelesített kérelem. Ha az ügyfél hálózati időkorlátja vagy a kérés időkorlátja a tárolási szolgáltatás által várt értéknél alacsonyabbra van állítva, akkor ez egy várt időtúllépés. Máskülönben a rendszer ServerTimeoutError hibát jelent.</li> </li> <li>**ClientOtherError**: Minden más ügyféloldali hiba, kivéve a leírt hibákat. </li> <li>**Siker**: Sikeres kérés</li> <li> **SuccessWithThrottling**: Sikeres kérelem, ha egy SMB-ügyfél az első kísérlet(ek)ben szabályozást kap, de az újrapróbálkozások után sikeres.</li> |
| **API-név** | A művelet neve. Példa: <br/> <li>**Tároló létrehozása**</li> <li>**Törlés**</li> <li>**GetBlob (GetBlob)**</li> Az összes műveletnévről a [dokumentumban](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)látható. |
| **Hitelesítés** | A tranzakciókban használt hitelesítési típus. Az elérhető értékek közé a következők tartoznak: <br/> <li>**AccountKey**: A tranzakció hitelesítése tárfiók kulcs.</li> <li>**SAS**: A tranzakció hitelesítése megosztott hozzáférésű aláírásokkal történik.</li> <li>**OAuth**: A tranzakció oauth hozzáférési jogkivonatokkal van hitelesítve.</li> <li>**Névtelen**: A tranzakció kérése névtelenül történik. Nem tartalmazza az elővizsgálati kérelmeket.</li> <li>**AnonymousPreflight**: A tranzakció elővizsgálati kérelem.</li> |

A dimenziókat támogató mutatókhoz meg kell adnia a dimenzióértéket a megfelelő metrikák értékeinek megtekintéséhez. Ha például a **sikeres** válaszok tranzakciók értékét keresi meg, a **ResponseType** dimenziót a **Sikeres**értékkel kell szűrnie. Vagy ha megnézi a **BlobCount** értékét a Blokkblobhoz, szűrnie kell a **BlobType** dimenziót a **BlockBlob**segítségével.

## <a name="service-continuity-of-legacy-metrics"></a>Az örökölt metrikák szolgáltatásfolytonossága

Az örökölt metrikák párhuzamosan érhetők el az Azure Monitor által felügyelt metrikákkal. A támogatás ugyanaz marad, amíg az Azure Storage befejezi a szolgáltatást a régebbi metrikákon.

## <a name="faq"></a>GYIK

**Az új metrikák támogatják a Klasszikus tárfiókot?**

Nem, az Azure Monitor új metrikák csak az Azure Resource Manager storage-fiókok támogatása. Ha metrikákat szeretne használni a Storage-fiókokon, át kell telepítenie az Azure Resource Manager Storage-fiókba. Lásd: [Áttelepítés az Azure Resource Managerbe](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

**Támogatja az Azure Storage a felügyelt lemezek vagy a nem felügyelt lemezek metrikákat?**

Nem, az Azure Compute támogatja a lemezekmetrikákat. További részleteket a [cikkben](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) talál.

**Hogyan lehet leképezni és áttelepíteni a klasszikus metrikákat új mérőszámokkal?**

A klasszikus metrikák és az új metrikák közötti részletes leképezést az [Azure Storage-metrikák áttelepítésében](./storage-metrics-migration.md)találhatja meg.

## <a name="next-steps"></a>További lépések

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
