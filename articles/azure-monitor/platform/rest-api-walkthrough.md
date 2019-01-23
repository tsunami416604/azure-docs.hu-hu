---
title: Az Azure Monitoring REST API-forgatókönyv
description: Hogyan-kérések hitelesítéséhez az Azure Monitor REST API használatával lekérheti az elérhető metrikai meghatározások és metrikaértékek.
author: mcollier
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: mcollier
ms.subservice: ''
ms.openlocfilehash: 91b4d96caf59a8be67381aa6b420a3f759220025
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54472959"
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Az Azure Monitoring REST API-forgatókönyv
Ez a cikk bemutatja, hogyan végezhet hitelesítést, így a kód használatával is a [a Microsoft Azure Monitor REST API-referencia](https://msdn.microsoft.com/library/azure/dn931943.aspx).         

Az Azure Monitor API lehetővé teszi az elérhető alapértelmezett metrikadefinícióinak granularitási és metrikaértékek programozott módon lekéréséhez. Az adatok menthetők egy külön, például Azure SQL Database, Azure Cosmos DB-hez vagy az Azure Data Lake adattárban. Itt további elemzés végezhető igény szerint.

Mellett számos metrika adatpontok dolgozik, a API is lehetővé teszi a listában a riasztási szabályok Tevékenységnaplók megtekintése és még sok más. Használható műveletek teljes listáját lásd: a [a Microsoft Azure Monitor REST API-referencia](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## <a name="authenticating-azure-monitor-requests"></a>Hitelesítő Azure Monitor-kérelmek
Az első lépéseként fel kell hitelesíteni a kérelmet.

Az Azure Monitor API ellen végrehajtott összes feladatot használja az Azure Resource Manager-hitelesítési modellre. Ezért minden kérelmet hitelesíteni kell az Azure Active Directoryval (Azure AD). Az ügyfélalkalmazás hitelesítés egyik módja, hogy az Azure AD egyszerű szolgáltatás létrehozása és a hitelesítéshez (JWT-) token lekérése. Az alábbi parancsprogram azt ismerteti, hogy az Azure AD PowerShell-lel egyszerű szolgáltatásnév létrehozása. A részletes lépésenkénti útmutatóért tekintse meg a dokumentációt a [erőforrások eléréséhez az egyszerű szolgáltatás létrehozása az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps). Emellett lehetőség [hozzon létre egy egyszerű szolgáltatást az Azure Portalon keresztül](../../active-directory/develop/howto-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Connect-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"
$secureStringPassword = ConvertTo-SecureString -String $pwd -AsPlainText -Force

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $secureStringPassword

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Az Azure Monitor API lekérdezéséhez, az ügyfélalkalmazás használ a korábban létrehozott szolgáltatásnevet hitelesítéshez. Az alábbi példa PowerShell-példaszkript bemutatja egy megközelítést használ a [Active Directory Authentication Library](../../active-directory/develop/active-directory-authentication-libraries.md) (ADAL) a JWT-hitelesítési token beszerzése. A JWT jogkivonat egy engedélyezési HTTP paraméter, a kérelmek részeként az Azure Monitor REST API kerülnek.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $secureStringPassword)

$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Miután hitelesítése, lekérdezések majd ellen az Azure Monitor REST API hajthatók végre. Nincsenek két hasznos lekérdezést:

1. A metrikadefiníciók erőforrás listázása
2. A metrikaértékek beolvasása

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>(API-t többdimenziós) Metrikadefinícióinak beolvasása

Használja a [Azure Monitor metrikadefiníciók REST API-val](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) metrikák elérhető szolgáltatás listájának eléréséhez.

**Módszer**: GET

**Request URI**: https://management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Ha például lekérdezheti az Azure Storage-fiókhoz tartozó metrikadefiníciók, a kérelem lenne a következőképpen jelenik meg:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```
> [!NOTE]
> A többdimenziós metrikák az Azure Monitor REST API használatával metrikadefinícióinak lekéréséhez használja "2018-01-01" az API-verziót.
>
>

Az eredményül kapott JSON-válasz törzse a következő példához hasonló lesz: (Vegye figyelembe, hogy a második metrika rendelkezik-e a dimenziók)

```JSON
{
    "value": [
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/UsedCapacity",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Capacity",
            "name": {
                "value": "UsedCapacity",
                "localizedValue": "Used capacity"
            },
            "isDimensionRequired": false,
            "unit": "Bytes",
            "primaryAggregationType": "Average",
            "supportedAggregationTypes": [
                "Total",
                "Average",
                "Minimum",
                "Maximum"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ]
        },
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/Transactions",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Transaction",
            "name": {
                "value": "Transactions",
                "localizedValue": "Transactions"
            },
            "isDimensionRequired": false,
            "unit": "Count",
            "primaryAggregationType": "Total",
            "supportedAggregationTypes": [
                "Total"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT5M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT15M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT30M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ],
            "dimensions": [
                {
                    "value": "ResponseType",
                    "localizedValue": "Response type"
                },
                {
                    "value": "GeoType",
                    "localizedValue": "Geo type"
                },
                {
                    "value": "ApiName",
                    "localizedValue": "API name"
                }
            ]
        },
        ...
    ]
}
```

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Dimenzió értékei (API-t többdimenziós) beolvasása
A rendelkezésre álló metrikadefinícióinak ismertek, miután lehetnek, amelyek rendelkeznek a dimenziók bizonyos metrikák. A metrika, előfordulhat, hogy szeretné felderíteni, hogy milyen értékek tartományán lekérdezése előtt a dimenziónál. Ezeket a dimenzió értékeket ezután választhatja szűrése vagy szegmens a metrikák lekérdezése közben dimenzió értékei alapján a metrikák alapján.  Használja a [Azure Monitor-metrikák REST API](https://docs.microsoft.com/rest/api/monitor/metrics) ennek eléréséhez.

A metrika neve "értéke" (nem a "localizedValue") használja a szűrési kérelmeket. Ha nincs szűrő meg van adva, a rendszer visszaadja az alapértelmezett metrikát. Ez az API használatát csak egy helyettesítő karaktert tartalmazó szűrő egy dimenziót teszi lehetővé.

> [!NOTE]
> Az Azure Monitor REST API használatával dimenzióértékek lekéréséhez használja "2018-01-01", az API-verziót.
>
>

**Módszer**: GET

**Kérés URI-ja**: https://management.azure.com/subscriptions/ *{előfizetés azonosítója}*/resourceGroups/*{erőforrás-csoport-neve}*/szolgáltatók/*{erőforrás-szolgáltató – namespace}* / *{erőforrástípus}*/*{erőforrás neve}*/providers/microsoft.insights/metrics?metricnames=*{metrika}*& időtartam =*{starttime és endtime}*& $filter =*{szűrő}*& resultType = metaadatok & api-version =*{apiVersion}*

Például lettek kibocsátva a "API-név dimenzió" a "Tranzakció" metrika ahol dimenzió értékek listájának beolvasása a GeoType dimenzió = "Elsődleges" a megadott időtartományban a kérés a következő lenne:

```PowerShell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=${filter}&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```
Az eredményül kapott JSON-válasz törzse a következő példához hasonló lesz:

```JSON
{
  "timespan": "2018-03-01T00:00:00Z/2018-03-02T00:00:00Z",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "DeleteBlob"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "SetBlobProperties"
            }
          ]
        },
        ...
      ]    
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Metrikaértékek (API-t többdimenziós) beolvasása
Ha a rendelkezésre álló metrikadefinícióinak beolvasása és a lehetséges dimenzióértékek ismert, ezt követően lehetősége a kapcsolódó metrikaértékek lekéréséhez.  Használja a [Azure Monitor-metrikák REST API](https://docs.microsoft.com/rest/api/monitor/metrics) ennek eléréséhez.

A metrika neve "értéke" (nem a "localizedValue") használja a szűrési kérelmeket. Ha a dimenzió egyetlen szűrő sincs megadva, az összesített összesített metrikája adja vissza. Ha egy metrika lekérdezést több időrendben ad vissza, majd segítségével a "Felső" és "OrderBy" lekérdezési paraméter időrendben korlátozott rendezett listáját adja vissza.

> [!NOTE]
> Az Azure Monitor REST API használatával többdimenziós metrikaértékek lekéréséhez használja az API-verziót "2018-01-01".
>
>

**Módszer**: GET

**Kérés URI-ja**: https://management.azure.com/subscriptions/ *{előfizetés azonosítója}*/resourceGroups/*{erőforrás-csoport-neve}*/szolgáltatók/*{erőforrás-szolgáltató – namespace}* / *{erőforrástípus}*/*{erőforrás neve}*/providers/microsoft.insights/metrics?metricnames=*{metrika}*& időtartam =*{starttime és endtime}*& $filter =*{szűrő}*& időköz =*{timeGrain}*& összesítési =*{} aggreation}*& api-version =*{apiVersion}*

Például beolvasni az első 3 API-k, csökkenő érték szerint a "Tranzakciók" 5 perc széles során, a GeotType lett-e az "elsődleges", a kérelem a következő lenne:

```PowerShell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=${filter}&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```
Az eredményül kapott JSON-válasz törzse a következő példához hasonló lesz:

```JSON
{
  "cost": 0,
  "timespan": "2018-03-01T02:00:00Z/2018-03-01T02:05:00Z",
  "interval": "PT1M",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "GetBlobProperties"
            }
          ],
          "data": [
            {
              "timeStamp": "2017-09-19T02:00:00Z",
              "total": 2
            },
            {
              "timeStamp": "2017-09-19T02:01:00Z",
              "total": 1
            },
            {
              "timeStamp": "2017-09-19T02:02:00Z",
              "total": 3
            },
            {
              "timeStamp": "2017-09-19T02:03:00Z",
              "total": 7
            },
            {
              "timeStamp": "2017-09-19T02:04:00Z",
              "total": 2
            }
          ]
        },
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-definitions"></a>Metrikadefiníciók beolvasása
Használja a [Azure Monitor metrikadefiníciók REST API-val](https://msdn.microsoft.com/library/mt743621.aspx) metrikák elérhető szolgáltatás listájának eléréséhez.

**Módszer**: GET

**Request URI**: https://management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Például egy Azure Logic App metrikadefinícióinak lekéréséhez a kérelem lenne a következőképpen jelenik meg:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosotweets-metricdef-results.json" `
                  -Verbose
```
> [!NOTE]
> Az Azure Monitor REST API használatával metrikadefinícióinak lekéréséhez használja "2016-03-01", az API-verziót.
>
>

Az eredményül kapott JSON-válasz törzse a következő példához hasonló lesz:
```JSON
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions",
  "value": [
    {
      "name": {
        "value": "RunsStarted",
        "localizedValue": "Runs Started"
      },
      "category": "AllMetrics",
      "startTime": "0001-01-01T00:00:00Z",
      "endTime": "0001-01-01T00:00:00Z",
      "unit": "Count",
      "primaryAggregationType": "Total",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        }
      ],
      "properties": null,
      "dimensions": null,
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions/RunsStarted",
      "supportedAggregationTypes": [ "None", "Average", "Minimum", "Maximum", "Total", "Count" ]
    }
  ]
}
```

További információkért lásd: a [listázása az Azure Monitor REST API az erőforráshoz tartozó metrikadefiníciók](https://msdn.microsoft.com/library/azure/mt743621.aspx) dokumentációját.

## <a name="retrieve-metric-values"></a>Metrikaértékek beolvasása
Miután a rendelkezésre álló metrikadefinícióinak ismertek, ezt követően lehetősége a kapcsolódó metrikaértékek lekéréséhez. A metrika neve "értéke" (nem a "localizedValue") használata a szűrési kérések eredményéről (például lekérni a "CpuTime" és "Kérések" metrika adatpontok). Ha nincs szűrő meg van adva, a rendszer visszaadja az alapértelmezett metrikát.

> [!NOTE]
> Az Azure Monitor REST API használatával metrikaértékek lekéréséhez használja "2016-09-01" az API-verziót.
>
>

**Módszer**: GET

**Kérés URI-ja**: https://management.azure.com/subscriptions/ *{előfizetés azonosítója}*/resourceGroups/*{erőforrás-csoport-neve}*/szolgáltatók/*{erőforrás-szolgáltató – namespace}* / *{erőforrástípus}*/*{erőforrás neve}*/providers/microsoft.insights/metrics?$filter=*{szűrő}*& api-version =*{apiVersion}*

Például lekérdezheti az RunsSucceeded metrika adatpontok az adott időtartományt és a egy metrikaindítójának aggregációs időköze 1 óra, a kérelem a következő lesz a következő:

```PowerShell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-results.json" `
    -Verbose
```

Az eredményül kapott JSON-válasz törzse a következő példához hasonló lesz:

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T19:00:00Z",
          "total": 0.0
        },
        {
          "timeStamp": "2017-08-18T20:00:00Z",
          "total": 159.0
        },
        {
          "timeStamp": "2017-08-18T21:00:00Z",
          "total": 174.0
        },
        {
          "timeStamp": "2017-08-18T22:00:00Z",
          "total": 97.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

Több adatokat vagy összesítési pont lekéréséhez adja hozzá a metrikai meghatározásainak nevek és típusú összesítést a szűrő, az alábbi példában látható módon:

```PowerShell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-multiple-results.json" `
    -Verbose
```
Az eredményül kapott JSON-válasz törzse a következő példához hasonló lesz:

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/ActionsCompleted",
      "name": {
        "value": "ActionsCompleted",
        "localizedValue": "Actions Completed "
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

### <a name="use-armclient"></a>ARMClient használata
Egy további módszer az, hogy használjon [ARMClient](https://github.com/projectkudu/armclient) a Windows-gépen. ARMClient automatikusan kezeli az Azure AD-hitelesítés (és az eredményül kapott JWT jogkivonat). A felhasznált ARMClient metrikaadatok beolvasása lépései:

1. Telepítés [Chocolatey](https://chocolatey.org/) és [ARMClient](https://github.com/projectkudu/armclient).
2. Egy terminálablakban, írja be a *armclient.exe bejelentkezési*. Ezt kéri, hogy jelentkezzen be az Azure-bA.
3. Típus *armclient [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01 GET*
4. Típus *armclient [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-09-01 GET*

Annak érdekében, hogy egy adott logikai alkalmazás metrikadefinícióinak beolvasása, például a következő parancs kiadásával:
```
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```


## <a name="retrieve-the-resource-id"></a>Az erőforrás-azonosító lekéréséhez
A REST API-val valóban segítségével értheti meg elérhető metrikai meghatározások, a részletesség és a kapcsolódó értékeket. Hogy információ hasznos, amikor használatával a [Azure Management Library](https://msdn.microsoft.com/library/azure/mt417623.aspx).

A fenti kóddal, a használandó erőforrás-azonosító a kívánt Azure-erőforrások teljes elérési útja. Például a lekérdezéseket az Azure Web Apps, az erőforrás-azonosítója a következő lesz:

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

Az alábbi listában néhány példa az erőforrás azonosítója formátumokat a különböző Azure-erőforrásokat tartalmazza:

* **IoT Hub** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **Elastic SQL Pool** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{pool-db}*/elasticpools/*{sql-pool-name}*
* **SQL Database (v12)** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{server-name}*/databases/*{database-name}*
* **A Service Bus** -/subscriptions/*{előfizetés azonosítója}*/resourceGroups/*{erőforrás-csoport-neve}*/providers/Microsoft.ServiceBus/*{namespace}* / *{servicebus-name}*
* **A Virtual machine scale sets** -/subscriptions/*{előfizetés azonosítója}*/resourceGroups/*{erőforrás-csoport-neve}*/providers/Microsoft.Compute/virtualMachineScaleSets/ *{virtuális gép neve}*
* **Virtuális gépek** -/subscriptions/*{előfizetés azonosítója}*/resourceGroups/*{erőforrás-csoport-neve}*/providers/Microsoft.Compute/virtualMachines/*{virtuális gép neve}*
* **Az Event Hubs** -/subscriptions/*{előfizetés azonosítója}*/resourceGroups/*{erőforrás-csoport-neve}*/providers/Microsoft.EventHub/namespaces/*{} az eventhub-namespace}*

Számos megközelítés lehetséges a beolvasása az erőforrás-azonosító, beleértve az Azure erőforrás-kezelő, a kívánt erőforrást megtekintése az Azure Portalon, és a PowerShell vagy az Azure CLI használatával.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer
Az erőforrás-azonosítója egy kívánt erőforrás megkereséséhez az egy hasznos megközelítés, hogy használja a [Azure erőforrás-kezelő](https://resources.azure.com) eszköz. Keresse meg a kívánt erőforrást, és tekintse meg az azonosító is látható, ahogy az alábbi képernyőfelvételen is látható:

!["Az Azure Resource Explorer" ALT](./media/rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure Portal
Az erőforrás-azonosító az Azure Portalon is szerezhető be. Ehhez keresse meg a kívánt erőforrást, és válassza ki a tulajdonságokat. Az erőforrás-azonosítója a Tulajdonságok szakaszának jelenik meg az alábbi képernyőképen látható módon:

![ALT "erőforrás-azonosító az Azure Portalon a Tulajdonságok panelen megjelenő"](./media/rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell
Az erőforrás-azonosítója, valamint az Azure PowerShell-parancsmagok használatával lehet beolvasni. Az erőforrás-azonosító beszerzése az Azure Logic Apps, például futtassa a Get-AzureLogicApp parancsmag az alábbi példában látható módon:

```PowerShell
Get-AzureRmLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

Az eredmény a következő példához hasonló lesz:
```
Id             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets
Name           : ContosoTweets
Type           : Microsoft.Logic/workflows
Location       : centralus
ChangedTime    : 8/21/2017 6:58:57 PM
CreatedTime    : 8/18/2017 7:54:21 PM
AccessEndpoint : https://prod-08.centralus.logic.azure.com:443/workflows/f3a91b352fcc47e6bff989b85446c5db
State          : Enabled
Definition     : {$schema, contentVersion, parameters, triggers...}
Parameters     : {[$connections, Microsoft.Azure.Management.Logic.Models.WorkflowParameter]}
SkuName        :
AppServicePlan :
PlanType       :
PlanId         :
Version        : 08586982649483762729
```


### <a name="azure-cli"></a>Azure CLI
Az erőforrás-azonosító az Azure Storage-fiókot az Azure CLI használatával lekérdezheti, hajtsa végre a "az storage account show" parancsot az alábbi példában látható módon:

```
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

Az eredmény a következő példához hasonló lesz:
```JSON
{
  "accessTier": null,
  "creationTime": "2017-08-18T19:58:41.840552+00:00",
  "customDomain": null,
  "enableHttpsTrafficOnly": false,
  "encryption": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/contosotweets2017",
  "identity": null,
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "centralus",
  "name": "contosotweets2017",
  "networkAcls": null,
  "primaryEndpoints": {
    "blob": "https://contosotweets2017.blob.core.windows.net/",
    "file": "https://contosotweets2017.file.core.windows.net/",
    "queue": "https://contosotweets2017.queue.core.windows.net/",
    "table": "https://contosotweets2017.table.core.windows.net/"
  },
  "primaryLocation": "centralus",
  "provisioningState": "Succeeded",
  "resourceGroup": "azmon-rest-api-walkthrough",
  "secondaryEndpoints": null,
  "secondaryLocation": "eastus2",
  "sku": {
    "name": "Standard_GRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "available",
  "statusOfSecondary": "available",
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

> [!NOTE]
> Az Azure Logic Apps még nem érhető el az Azure CLI-n keresztül, így az Azure Storage-fiók jelenik meg az előző példában.
>
>

## <a name="retrieve-activity-log-data"></a>Tevékenységnapló adatainak beolvasása
Metrikadefiníciók és a kapcsolódó értékeket kívül is is lehet az Azure Monitor REST API használatával lekérheti az Azure-erőforrások kapcsolatos további érdekes elemzést. Tegyük fel, akkor lehet lekérdezés [tevékenységnapló](https://msdn.microsoft.com/library/azure/dn931934.aspx) adatokat. A következő minta azt mutatja be, egy Azure-előfizetés lekérdezési tevékenységnapló adatainak adott időtartományon belül, az Azure Monitor REST API használatával:

```PowerShell
$apiVersion = "2015-04-01"
$filter = "eventTimestamp ge '2017-08-18' and eventTimestamp le '2017-08-19'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -Verbose
```

## <a name="next-steps"></a>További lépések
* Tekintse át a [figyelőeszközök](../../azure-monitor/overview.md).
* Nézet a [az Azure monitorban támogatott mérőszámok](metrics-supported.md).
* Tekintse át a [Microsoft Azure REST API-referencia figyelése](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Tekintse át a [Azure Management Library](https://msdn.microsoft.com/library/azure/mt417623.aspx).

