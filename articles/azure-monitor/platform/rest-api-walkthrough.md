---
title: Azure Monitoring REST API forgatókönyve
description: A kérelmek hitelesítése és az Azure Monitor REST API használata a rendelkezésre álló metrika-definíciók és metrikaértékek lekéréséhez.
ms.subservice: metrics
ms.topic: conceptual
ms.date: 03/19/2018
ms.openlocfilehash: f52776fc6b5f5f530cc368a2f148a2ff63fb5b40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294620"
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Azure Monitoring REST API forgatókönyve

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a cikk bemutatja, hogyan hajthatja végre a hitelesítést, hogy a kód használhassa a [Microsoft Azure Monitor REST API-referencia .](https://docs.microsoft.com/rest/api/monitor/)

Az Azure Monitor API lehetővé teszi a rendelkezésre álló alapértelmezett metrika-definíciók, részletesség és metrikaértékek programozott lekérését. Az adatok menthetők egy külön adattárban, például az Azure SQL Database, az Azure Cosmos DB vagy az Azure Data Lake. Innen szükség szerint további elemzést lehet végezni.

A különböző metrikaadat-pontok on-val végzett munka mellett a Figyelő API lehetővé teszi a riasztási szabályok listázását, a tevékenységnaplók megtekintését és még sok más. Az elérhető műveletek teljes listáját a [Microsoft Azure Monitor REST API-kézikönyvében található.](https://docs.microsoft.com/rest/api/monitor/)

## <a name="authenticating-azure-monitor-requests"></a>Az Azure Monitor-kérelmek hitelesítése

Az első lépés a kérelem hitelesítése.

Az Azure Monitor API-val végrehajtott összes feladat az Azure Resource Manager hitelesítési modelljét használja. Ezért minden kérelmet hitelesíteni kell az Azure Active Directory (Azure AD) segítségével. Az ügyfélalkalmazás hitelesítésének egyik módszere egy Azure AD egyszerű szolgáltatás létrehozása és a hitelesítési (JWT) jogkivonat lekérése. A következő mintaparancsfájl bemutatja egy Azure AD egyszerű szolgáltatás létrehozása a PowerShellen keresztül. Részletesebb útmutatásért tekintse meg az Azure [PowerShell használatával az erőforrások eléréséhez egyszerű szolgáltatás létrehozásáról szóló dokumentációt.](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps) Lehetőség van [egy egyszerű szolgáltatás létrehozása az Azure Portalon keresztül.](../../active-directory/develop/howto-create-service-principal-portal.md)

```powershell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Connect-AzAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"
$secureStringPassword = ConvertTo-SecureString -String $pwd -AsPlainText -Force

# Create a new Azure AD application
$azureAdApplication = New-AzADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $secureStringPassword

# Create a new service principal associated with the designated application
New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Az Azure Monitor API lekérdezéséhez az ügyfélalkalmazásnak a korábban létrehozott egyszerű szolgáltatáshitelesítést kell használnia. A következő példa PowerShell-parancsfájl egy megközelítést mutat be, az [Active Directory hitelesítési tár](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) használatával a JWT hitelesítési jogkivonat beszerzéséhez. A JWT-jogkivonat egy HTTP-engedélyezési paraméter részeként kerül átadásra az Azure Monitor REST API-ba irányuló kérelmekben.

```powershell
$azureAdApplication = Get-AzADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireTokenAsync("https://management.core.windows.net/", $cred).GetAwaiter().GetResult()

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

A hitelesítés után a lekérdezések ezután végrehajthatók az Azure Monitor REST API-val. Két hasznos lekérdezés van:

1. Egy erőforrás metrikadefinícióinak felsorolása
2. A metrikaértékek lekérése

> [!NOTE]
> Az Azure REST API-val való hitelesítésről az [Azure REST API-val kapcsolatos referencia](https://docs.microsoft.com/rest/api/azure/)című további tudnivalókat további tudnivalókat talál.
>
>

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Metrikadefiníciók lekérése (többdimenziós API)

Az [Azure Monitor metrikadefiníciók REST API-t](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) a szolgáltatás hoz rendelkezésre álló metrikák listájának eléréséhez.

**Módszer**: GET

**Kérelem URI**:\/\/https: management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Például egy Azure Storage-fiók metrikadefinícióinak lekéréséhez a kérés a következőképpen jelenik meg:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```

> [!NOTE]
> Metrikadefiníciók beolvasásához a többdimenziós Azure Monitor metrikák REST API használatával, használja a "2018-01-01" API-verzió.
>
>

Az eredményül kapott JSON-választörzs hasonló lenne a következő példához: (Vegye figyelembe, hogy a második metrika dimenziókkal rendelkezik)

```json
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

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Dimenzióértékek beolvasása (többdimenziós API)

Miután a rendelkezésre álló metrikadefiníciók ismertek, előfordulhat, hogy bizonyos metrikák, amelyek dimenziók. A metrika lekérdezése előtt érdemes lehet felderíteni, hogy egy dimenzió milyen értéktartományt tartalmaz. Ezek alapján a dimenzióértékek, majd választhat, hogy a szűrés vagy szegmentálás a metrikák alapján dimenzióértékek lekérdezése közben a metrikák.  Ennek eléréséhez használja az [Azure Monitor Metrics REST API-t.](https://docs.microsoft.com/rest/api/monitor/metrics)

A mérőszám "value" (nem a 'localizedvalue' ) nevét használja a szűrési kérelmekhez. Ha nincs megadva szűrő, a rendszer az alapértelmezett metrikát adja vissza. Az API használata csak lehetővé teszi, hogy egy dimenzió helyettesítő karakterrel rendelkezik.

> [!NOTE]
> Dimenzióértékek beolvasásához az Azure Monitor REST API használatával használja a "2018-01-01" API-verzió.
>
>

**Módszer**: GET

**URI kérése**\:: https //management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*$filter /providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name} /providers/microsoft.insights/metrics?metricnames=**{metric}*&timespan=*{starttime/endtime}*&=*{filter}*&resultType=metadata&api-version=*{apiVersion }*

Ha például a "Transactions" metrikához az "API-név dimenzió" számára kibocsátott dimenzióértékek listáját szeretné beolvasni, ahol a GeoType dimenzió = "Elsődleges" a megadott időtartományban, a kérés a következő:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=${filter}&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```

Az eredményül kapott JSON választörzs hasonló lenne a következő példához:

```json
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

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Metrikus értékek beolvasása (többdimenziós API)

Miután a rendelkezésre álló metrikadefiníciók és a lehetséges dimenzióértékek ismertek, akkor lehetséges a kapcsolódó metrikaértékek beolvasása.  Ennek eléréséhez használja az [Azure Monitor Metrics REST API-t.](https://docs.microsoft.com/rest/api/monitor/metrics)

Használja a metrika "érték" (nem a "localizedValue") bármilyen szűrési kérelmek. Ha nincs megadva dimenziószűrő, a rendszer a ggregált metrikát adja vissza. Ha egy metrikalekérdezés több idősorozatot ad vissza, akkor a "Top" és az "OrderBy" lekérdezési paraméterek használatával korlátozott rendezett idősorok listáját adhat vissza.

> [!NOTE]
> Többdimenziós metrikaértékek beolvasása az Azure Monitor REST API használatával használja a "2018-01-01" API-verzió.
>
>

**Módszer**: GET

**URI**kérése https://management.azure.com/subscriptions/: *{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name} {resource-name} {resource-name}*/providers/microsoft.insights/metrics?metricnames=*{metric}*&timespan=*{starttime/endtime}*&$filter=*{filter}*&interval=*{timeGrain}*&aggregation=*{aggreation=&* api-version=*{ { apiVersion}*

Ha például a 3 első API-t csökkenő értékben szeretné lekérni egy 5 méteres tartományban a "Tranzakciók" számával, ahol a GeotType "Elsődleges" volt, a kérés a következő lesz:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=${filter}&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```

Az eredményül kapott JSON választörzs hasonló lenne a következő példához:

```json
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

## <a name="retrieve-metric-definitions"></a>Metrikadefiníciók lekérése

Az [Azure Monitor metrikadefiníciók REST API-t](https://msdn.microsoft.com/library/mt743621.aspx) a szolgáltatás hoz rendelkezésre álló metrikák listájának eléréséhez.

**Módszer**: GET

**Kérelem URI**:\/\/https: management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Például egy Azure Logic-alkalmazás metrikadefinícióinak lekéréséhez a kérés a következőképpen jelenik meg:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosotweets-metricdef-results.json" `
                  -Verbose
```

> [!NOTE]
> Metrikadefiníciók lekérése az Azure Monitor REST API használatával, használja a "2016-03-01" API-verzió.
>
>

Az eredményül kapott JSON választörzs hasonló lenne a következő példához:

```json
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

További információkért tekintse [meg a tekintse meg a metrika definíciók listájában egy erőforrás az Azure Monitor REST API dokumentációjában.](https://msdn.microsoft.com/library/azure/mt743621.aspx)

## <a name="retrieve-metric-values"></a>Metrikus értékek lekérése

Miután a rendelkezésre álló metrikadefiníciók ismertek, akkor lehetséges a kapcsolódó metrikaértékek beolvasása. Használja a metrika "érték" (nem a "localizedValue") minden szűrési kérelmek (például a "CpuTime" és a "Kérések" metrikus adatpontok). Ha nincs megadva szűrő, a rendszer az alapértelmezett metrikát adja vissza.

> [!NOTE]
> Metrikaértékek beolvasása az Azure Monitor REST API használatával, használja a "2016-09-01" API-verzió.
>
>

**Módszer**: GET

**Uri kérése**:`https://management.azure.com/subscriptions/\*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?$filter=*{filter}*&api-version=*{apiVersion}*`

Például a Futtatott metrika adatpontjainak beolvasásához az adott időtartományra és egy 1 órás idősécerre vonatkozóan a kérelem a következő:

```powershell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-results.json" `
    -Verbose
```

Az eredményül kapott JSON választörzs hasonló lenne a következő példához:

```json
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

Több adat vagy összesítési pont lekéréséhez adja hozzá a metrikadefiníció-neveket és az aggregációs típusokat a szűrőhöz, ahogy az a következő példában látható:

```powershell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-multiple-results.json" `
    -Verbose
```

Az eredményül kapott JSON választörzs hasonló lenne a következő példához:

```json
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

### <a name="use-armclient"></a>ArmClient használata

További megközelítés az [ARMClient](https://github.com/projectkudu/armclient) használata a Windows-számítógépen. ArmClient kezeli az Azure AD-hitelesítés (és az eredményül kapott JWT-token) automatikusan kezeli. A következő lépések az ARMClient használatát ismertetik a metrikaadatok beolvasásához:

1. Telepítse [a Chocolatey](https://chocolatey.org/) és [az ARMClient alkalmazást.](https://github.com/projectkudu/armclient)
2. A terminálablakba írja be az *armclient.exe bejelentkezést.* Ezzel kéri, hogy jelentkezzen be az Azure-ba.
3. Típus *armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. Típus *armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-09-01*

Egy adott logikai alkalmazás metrikadefinícióinak beolvasásához például adja ki a következő parancsot:

```console
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```

## <a name="retrieve-the-resource-id"></a>Az erőforrás-azonosító lekérése

A REST API használata valóban segíthet megérteni a rendelkezésre álló metrika-definíciókat, a részletességet és a kapcsolódó értékeket. Ez az információ az [Azure Management Library](https://msdn.microsoft.com/library/azure/mt417623.aspx)használatakor hasznos.

Az előző kód esetében a használni kívánt Azure-erőforrás teljes elérési útja a használandó erőforrás azonosítója. Például egy Azure Web App lekérdezéséhez az erőforrás-azonosító a következő:

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

Az alábbi lista néhány példát tartalmaz a különböző Azure-erőforrások erőforrás-azonosító formátumaira:

* **IoT Hub** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **Rugalmas SQL-készlet** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{pool-db}*/elasticpools/*{sql-pool-name}*
* **SQL Database (v12)** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers}*{server-name}*/databases}*{database-name}*
* **Service Bus** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.ServiceBus/*{namespace}*/*{servicebus-name}*
* **Virtuálisgép-méretezési készletek** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachineScaleSets/*{vm-name}*
* **Virtuális gépek** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachines/*{vm-name}*
* **Event Hubs** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.EventHub/namespaces/*{eventhub-namespace}*

Az erőforrás-azonosító lekéréséhez alternatív módszerek is léteznek, például az Azure Resource Explorer használata, a kívánt erőforrás megtekintése az Azure Portalon, valamint a PowerShellen vagy az Azure CLI-n keresztül.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer

A kívánt erőforrás erőforrás-azonosító megkereséséhez egy hasznos megközelítés az [Azure Resource Explorer](https://resources.azure.com) eszköz használata. Keresse meg a kívánt erőforrást, majd nézze meg a megjelenített azonosítót, ahogy az alábbi képernyőképen látható:

![Alt "Azure Resource Explorer"](./media/rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure portál

Az erőforrás-azonosító az Azure Portalon is beszerezhető. Ehhez keresse meg a kívánt erőforrást, és válassza a Tulajdonságok lehetőséget. Az erőforrás-azonosító a Tulajdonságok szakaszban jelenik meg, ahogy az a következő képernyőképen látható:

![Alt "Erőforrás-azonosító jelenik meg a Tulajdonságok panelen az Azure Portalon"](./media/rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell

Az erőforrás-azonosító azure PowerShell-parancsmagokkal is lehívható. Például egy Azure Logic App erőforrás-azonosítójának beszerzéséhez hajtsa végre a Get-AzureLogicApp-parancsmag, mint a következő példában:

```powershell
Get-AzLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

Az eredménynek hasonlónak kell lennie a következő példához:

```output
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

Az Azure Storage-fiók erőforrás-azonosítójának lekéréséhez az `az storage account show` Azure CLI használatával hajtsa végre a parancsot, ahogy az a következő példában látható:

```azurecli
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

Az eredménynek hasonlónak kell lennie a következő példához:

```json
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
> Az Azure Logic Apps még nem érhető el az Azure CLI-n keresztül, így az előző példában megjelenik egy Azure Storage-fiók.
>
>

## <a name="retrieve-activity-log-data"></a>Tevékenységnapló adatainak beolvasása

A metrikadefiníciók és a kapcsolódó értékek mellett az Azure Monitor REST API-val is lekérheti az Azure-erőforrásokhoz kapcsolódó további érdekes elemzéseket. Például lehetőség van a [tevékenységnapló](https://msdn.microsoft.com/library/azure/dn931934.aspx) adatainak lekérdezésére. A következő minta bemutatja az Azure Monitor REST API használatával a tevékenységnapló-adatok lekérdezéséhez egy Azure-előfizetés adott dátumtartományán belül:

```powershell
$apiVersion = "2015-04-01"
$filter = "eventTimestamp ge '2017-08-18' and eventTimestamp le '2017-08-19'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -Verbose
```

## <a name="next-steps"></a>További lépések

* Tekintse át [a monitoring áttekintését.](../../azure-monitor/overview.md)
* Tekintse meg a [támogatott metrikákat az Azure Monitorsegítségével.](metrics-supported.md)
* Tekintse át a [Microsoft Azure Monitor REST API-referencia.](https://msdn.microsoft.com/library/azure/dn931943.aspx)
* Tekintse át az [Azure Management Library](https://msdn.microsoft.com/library/azure/mt417623.aspx).
