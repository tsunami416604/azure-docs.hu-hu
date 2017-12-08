---
title: "Azure figyelési REST API-forgatókönyv |} Microsoft Docs"
description: "Hogyan kérelmek hitelesítéséhez szükséges és elérhető metrikai meghatározásainak és metrika értékek beolvasása a Azure REST API használatával."
author: mcollier
manager: 
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 565e6a88-3131-4a48-8b82-3effc9a3d5c6
ms.service: monitoring-and-diagnostics
ms.workload: 
ms.tgt_pltfrm: 
ms.devlang: 
ms.search.region: 
ms.search.scope: 
ms.search.validFrom: 
ms.dyn365.ops.version: 
ms.topic: article
ms.date: 09/18/2017
ms.author: mcollier
ms.openlocfilehash: ab522b444c234e1159acfea1780bae1801c4d047
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Az Azure figyelési REST API-forgatókönyv
Ez a cikk bemutatja, hogyan végezhet hitelesítést, a kód használhassa a [Microsoft Azure figyelő REST API-referencia](https://msdn.microsoft.com/library/azure/dn931943.aspx).         

A Azure API lehetővé teszi az elérhető alapértelmezett metrikai meghatározásainak, granularitási és metrika értékek programozott módon beolvasása. Az adatok például az Azure SQL Database, az Azure Cosmos DB vagy az Azure Data Lake külön adattár is menthető. Innen további elemzés végrehajtható igény szerint.

Mellett különböző metrika adatpontok dolgozik, a API is lehetővé teszi a riasztási szabályok tevékenység naplók megtekintése és még sok más listázásához. Elérhető műveletek teljes listáját lásd: a [Microsoft Azure figyelő REST API-referencia](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## <a name="authenticating-azure-monitor-requests"></a>Hitelesítő Azure figyelő kérelmek
Az első lépés hitelesíteni a kérelmet.

A Azure API képest végrehajtott összes feladat az Azure Resource Manager hitelesítési modellt használja. Ezért összes kérelmet hitelesíteni kell az Azure Active Directoryval (Azure AD). Az ügyfélalkalmazás hitelesíteni egy megoldás, a szolgáltatásnevet létrehozni az Azure AD és a hitelesítési (JWT)-jogkivonatot lekérdezni. Az alábbi mintaparancsfájl mutatja be, létrehozása az Azure AD szolgáltatás egyszerű PowerShell segítségével. Részletes útmutató, tekintse meg a dokumentációt a [erőforrásokhoz való hozzáféréshez egyszerű szolgáltatás létrehozása az Azure PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-password). Akkor is lehet [hozzon létre egy egyszerű szolgáltatást az Azure-portálon](../azure-resource-manager/resource-group-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $pwd

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

A Azure API lekérdezni az ügyfélalkalmazás által használandó a korábban létrehozott egyszerű hitelesítést. Az alábbi példa PowerShell-parancsfájl bemutatja egy közelítse, használja a [Active Directory Authentication Library](../active-directory/active-directory-authentication-libraries.md) (ADAL) a JWT hitelesítési jogkivonat beszerzése. A JWT jogkivonat a Azure REST API kerülnek egy HTTP-engedélyezési paraméter, a kérelmek részeként.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Hitelesítés után lekérdezések majd szemben a Azure REST API hajtható végre. Számos hasznos két lekérdezést.

1. A metrikai meghatározásainak erőforrás felsorolása
2. A metrika értékek beolvasása

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>(A többdimenziós API) metrikai meghatározásainak beolvasása

Használja a [Azure figyelő metrika definíciók REST API](https://docs.microsoft.com/en-us/rest/api/monitor/metricdefinitions) egy szolgáltatás számára elérhetők metrikák listájának eléréséhez.

**Módszer**: beolvasása

**A kérelmi URI**: https://management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{ resourceProviderNamespace}*/*{resourceType}*/*{resourceName*/providers/microsoft.insights/metricDefinitions? az API-version =*{apiVersion}*

Például az Azure Storage-fiók metrikai meghatározásainak beolvasása, a kérelem jelent az alábbiak szerint:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2017-05-01-preview"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```
> [!NOTE]
> A többdimenziós Azure figyelő metrikák REST API használatával metrikai meghatározásainak beolvasása, az API-verzió "2017-05-01. dátumú előnézeti" használják.
>
>

Az eredményül kapott JSON adott válasz törzsének az alábbi példához hasonló lenne: (Megjegyzés: a második metrika dimenziója van)

```JSON
{
    "value": [
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/UsedCapacity",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage",
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
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/Transactions",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage",
            "category": "Transaction",
            "name": {
                "value": "Transactions",
                "localizedValue": "Transactions"
            },
            "isDimensionRequired": false,
            "unit": "Count",
            "primaryAggregationType": "Total",
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P30D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P30D"
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

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>(A többdimenziós API) dimenzióértékek beolvasása
Ha az elérhető metrikai meghatározásainak ismert, néhány metrikákat, amelyek rendelkeznek a dimenziók lehet. A metrika-érdemes lehet milyen értékek tartományán felderítése küldése előtt a dimenziónak van. Ezen dimenzió értékek szűréséhez dönthet vagy szegmens metrikáihoz lekérdezése során dimenzió értékek alapján a metrikák alapján. A mérték neve "érték" (nem a "localizedValue") használja a szűrési kéréseit (például lekéri a "CpuTime" és "Kérelmek" metrika adatpontok). Ha a szűrők vannak megadva, az alapértelmezett metrika adja vissza.

> [!NOTE]
> A Azure REST API használatával dimenzióértékek lekéréséhez használja a "2017-05-01. dátumú előnézeti" API-verzióval.
>
>

**Módszer**: beolvasása

**A kérelmi URI**: https://management.azure.com/subscriptions/*{előfizetés-azonosító}*/resourceGroups/*{csoport-erőforrásnév}*/providers/*{ erőforrás-szolgáltató-namespace}*/*{erőforrástípus-}*/*{erőforrásnév}*/providers/microsoft.insights/metrics?metric= *{metrika}*& timespan =*{starttime/endtime}*& $filter =*{szűrő}*& resultType = metaadatok & api-version =*{ apiVersion}*

Például lehetséges értékek a megadott API-név dimenzió listájának beolvasása "Tranzakció" metrika egy adott időtartományban, a kérelemnek így kell az alábbiak szerint:

```PowerShell
$filter = "APIName eq '*'"
$request = "https://management.azure.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metrics?metric=Transactions&timespan=2017-09-01T00:00:00Z/2017-09-10T00:00:00Z&resultType=metadata&$filter=${filter}&api-version=2017-05-01-preview"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```
Az eredményül kapott JSON adott válasz törzsének az alábbi példához hasonló lenne:

```JSON
{
  "timespan": "2017-09-01T00:00:00Z/2017-09-10T00:00:00Z",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
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
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "PutPage"
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
              "value": "Unknown"
            }
          ]
        },
        ...
      ]    
    }
  ]
}
```

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Metrika értékek (többdimenziós API) beolvasása
Ha az elérhető metrikai meghatározásainak és a lehetséges dimenzióértékek ismert, majd lehetőség a kapcsolódó metrika értékek beolvasása. A mérték neve "érték" (nem a "localizedValue") használata a szűrési kéréseit. Ha a dimenzió szűrők vannak megadva, a összesített összesített metrika adja vissza.

> [!NOTE]
> A Azure REST API használatával többdimenziós metrika értékek lekéréséhez használja a "2017-05-01. dátumú előnézeti" API-verzióval.
>
>

**Módszer**: beolvasása

**A kérelmi URI**: https://management.azure.com/subscriptions/*{előfizetés-azonosító}*/resourceGroups/*{csoport-erőforrásnév}*/providers/*{ erőforrás-szolgáltató-namespace}*/*{erőforrástípus-}*/*{erőforrásnév}*/providers/microsoft.insights/metrics?metric= *{metrika}*& timespan =*{starttime/endtime}*& $filter =*{szűrő}*& időköz =*{időkeretben vannak}* & összesítési =*{aggreation}*& api-version =*{apiVersion}*

Például a tárolási "Tranzakció" metrika metrika értékeinek beolvasásához 5 perc széles, az API neve "GetBlobProperties", az összes tranzakciók során a kérelemnek így kell az alábbiak szerint:

```PowerShell
$filter = "APIName eq 'GetBlobProperties'"
$request = "https://management.azure.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metrics?metric=Transactions&timespan=2017-09-19T02:00:00Z/2017-09-19T02:05:00Z&$filter=${filter}&interval=PT1M&aggregation=Count&api-version=2017-05-01-preview"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```
Az eredményül kapott JSON adott válasz törzsének az alábbi példához hasonló lenne:

```JSON
{
  "cost": 0,
  "timespan": "2017-09-19T02:00:00Z/2017-09-19T02:05:00Z",
  "interval": "PT1M",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
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
              "count": 2.0
            },
            {
              "timeStamp": "2017-09-19T02:01:00Z",
              "count": 1.0
            },
            {
              "timeStamp": "2017-09-19T02:02:00Z",
              "count": 3.0
            },
            {
              "timeStamp": "2017-09-19T02:03:00Z",
              "count": 7.0
            },
            {
              "timeStamp": "2017-09-19T02:04:00Z",
              "count": 2.0
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="retrieve-metric-definitions"></a>Metrikai meghatározásainak beolvasása
Használja a [Azure figyelő metrika definíciók REST API](https://msdn.microsoft.com/library/mt743621.aspx) egy szolgáltatás számára elérhetők metrikák listájának eléréséhez.

**Módszer**: beolvasása

**A kérelmi URI**: https://management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{ resourceProviderNamespace}*/*{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions? az API-version =*{apiVersion}*

Például az Azure Logic Apps metrikai meghatározásainak beolvasása, a kérelem jelent az alábbiak szerint:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contostweets-metricdef-results.json" `
                  -Verbose
```
> [!NOTE]
> A Azure REST API használatával metrikai meghatározásainak beolvasása, az API-verzió "2016-03-01" használják.
>
>

Az eredményül kapott JSON adott válasz törzsének az alábbi példához hasonló lenne:
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

További információkért lásd: a [Azure figyelő REST API-t az erőforráshoz tartozó metrikai meghatározásainak listában](https://msdn.microsoft.com/library/azure/mt743621.aspx) dokumentációját.

## <a name="retrieve-metric-values"></a>Metrika értékek beolvasása
Ha az elérhető metrikai meghatározásainak ismert, majd lehetőség a kapcsolódó metrika értékek beolvasása. A mérték neve "érték" (nem a "localizedValue") használja a szűrési kéréseit (például lekéri a "CpuTime" és "Kérelmek" metrika adatpontok). Ha a szűrők vannak megadva, az alapértelmezett metrika adja vissza.

> [!NOTE]
> A Azure REST API használatával metrika értékek lekéréséhez használja a "2016-09-01" API-verzióval.
>
>

**Módszer**: beolvasása

**A kérelmi URI**: https://management.azure.com/subscriptions/*{előfizetés-azonosító}*/resourceGroups/*{csoport-erőforrásnév}*/providers/*{erőforrás-szolgáltató-namespace}*/*{erőforrástípus-}*/*{erőforrásnév}*/providers/microsoft.insights/metrics?$filter=*{szűrő}*& api-version =*{apiVersion}*

Például a RunsSucceeded metrika adatpontok az adott időtartományt pedig egy metrikaindítójának aggregációs időköze 1 órás lekéréséhez a kérelemnek így kell az alábbiak szerint:

```PowerShell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contostweets-metrics-results.json" `
    -Verbose
```

Az eredményül kapott JSON adott válasz törzsének az alábbi példához hasonló lenne:

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

Több adat- vagy Összesítés pontok lekérdezése, vegye fel a metrika definition nevét és az összesítési típusát a szűrőt, az alábbi példában látható módon:

```PowerShell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contostweets-metrics-multiple-results.json" `
    -Verbose
```
Az eredményül kapott JSON adott válasz törzsének az alábbi példához hasonló lenne:

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
Egy további módszert is, hogy használja [ARMClient](https://github.com/projectkudu/armclient) a Windows-számítógépre. ARMClient automatikusan kezeli az Azure AD-alapú hitelesítés (és az eredményül kapott JWT jogkivonat). Metrika adatok beolvasása ARMClient használatának lépései:

1. Telepítés [Chocolatey](https://chocolatey.org/) és [ARMClient](https://github.com/projectkudu/armclient).
2. Írja be egy terminálablakot, *armclient.exe bejelentkezési*. Így kéri, hogy jelentkezzen be az Azure-bA.
3. Típus *armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. Típus *armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-09-01*

Ahhoz, hogy az adott logikai alkalmazás metrikai meghatározásainak beolvasása, például ki a következő parancsot:
```
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```


## <a name="retrieve-the-resource-id"></a>Az erőforrás-azonosító lekérése
A REST API használatával valóban segítenek megérteni az elérhető metrikai meghatározásainak, a részletesség és a kapcsolódó értékek. Hogy információ hasznos használata esetén a [Azure könyvtár](https://msdn.microsoft.com/library/azure/mt417623.aspx).

Az előzőekben látható kód az erőforrás-azonosító használata esetén a kívánt Azure-erőforrás teljes elérési útja. Például lekérdezése alapján az Azure Web Apps, az erőforrás-azonosítója a következő lesz:

*/Subscriptions/{Subscription-ID}/resourceGroups/{Resource-group-name}/Providers/Microsoft.Web/Sites/{Site-Name}/*

Az alábbi listában néhány példa a különböző Azure-erőforrások erőforrás azonosítója formátumai tartalmazza:

* **Az IoT-központ** -következő*{előfizetés-azonosító}*/resourceGroups/*{csoport-erőforrásnév}*/providers/Microsoft.Devices/IotHubs/*{iot-központ-neve}*
* **A rugalmas SQL-készlet** -következő*{előfizetés-azonosító}*/resourceGroups/*{csoport-erőforrásnév}*/providers/Microsoft.Sql/servers/*{alkalmazáskészlet-db}*/elasticpools/*{sql-alkalmazáskészlet-neve}*
* **SQL-adatbázis (v12)** -következő*{előfizetés-azonosító}*/resourceGroups/*{csoport-erőforrásnév}*/providers/Microsoft.Sql/servers/*{kiszolgálónév}*/databases/*{adatbázisnév}*
* **A Service Bus** -következő*{előfizetés-azonosító}*/resourceGroups/*{csoport-erőforrásnév}*/providers/Microsoft.ServiceBus/*{névtér}*/*{szolgáltatásbusz-neve}*
* **Virtuálisgép-méretezési csoportok** -következő*{előfizetés-azonosító}*/resourceGroups/*{csoport-erőforrásnév}*/providers/Microsoft.Compute/virtualMachineScaleSets/ *{Virtuálisgép-név}*
* **Virtuális gépek** -következő*{előfizetés-azonosító}*/resourceGroups/*{csoport-erőforrásnév}*/providers/Microsoft.Compute/virtualMachines/*{virtuálisgép-név}*
* **Az Event Hubs** -következő*{előfizetés-azonosító}*/resourceGroups/*{csoport-erőforrásnév}*/providers/Microsoft.EventHub/namespaces/*{eventhub-namespace}*

Nincsenek alternatív módszer beolvasása az erőforrás-azonosító, beleértve az Azure erőforrás-kezelő, a kívánt erőforrás megtekintése az Azure portálon, és a PowerShell vagy az Azure parancssori felület használatával.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer
Az erőforrás-azonosító található a kívánt erőforráshoz tartozó, egy hasznos megoldás használatára, a [Azure erőforrás-kezelő](https://resources.azure.com) eszköz. Keresse meg a kívánt erőforrás, és tekintse meg az azonosítója, ahogy az alábbi képernyőfelvételen látható:

!["Az Azure erőforrás-kezelő" ALT](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure Portal
Az erőforrás-azonosító is lehet lekérni az Azure-portálon. Ehhez nyissa meg a kívánt erőforrás, és válassza a tulajdonságok. Az erőforrás-azonosítója a Tulajdonságok szakaszának jelenik meg az alábbi képernyőfelvételen látható módon:

![ALT "erőforrás-azonosító az Azure-portálon a Tulajdonságok panelen megjelenő"](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell
Az erőforrás-azonosító, valamint az Azure PowerShell-parancsmagok használatával lehet beolvasni. Az erőforrás-azonosító beszerzése az Azure Logic Apps, például a Get-AzureLogicApp parancsmag, az alábbi példában látható módon kell végrehajtani:

```PowerShell
Get-AzureRmLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

Az eredmény az alábbi példához hasonló legyen:
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
Egy Azure Storage-fiókot az Azure parancssori felület használatával az erőforrás-azonosító lekéréséhez a "az tárolási fiók megjelenítése" parancs végrehajtása a következő példában látható módon:

```
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

Az eredmény az alábbi példához hasonló legyen:
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
> Az Azure Logic Apps még nem érhető el az Azure parancssori felületen keresztül, így egy Azure Storage-fiók látható a fenti példában.
>
>

## <a name="retrieve-activity-log-data"></a>Tevékenység napló adatainak beolvasása
Metrikai meghatározásainak és a kapcsolódó értékekről kívül is is lehet beolvasni a kapcsolódó Azure-erőforrások további érdekes elemzések a Azure REST API használatával. Tegyük fel, akkor lehet lekérdezés [tevékenységnapló](https://msdn.microsoft.com/library/azure/dn931934.aspx) adatokat. A következő példa bemutatja, Azure-előfizetések lekérdezni tevékenység naplózási adatokat egy adott dátumtartományban Azure figyelő REST API használatával:

```PowerShell
$apiVersion = "2015-04-01"
$filter = "eventTimestamp ge '2017-08-18' and eventTimestamp le '2017-08-19'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&$filter=${filter}"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -Verbose
```

## <a name="next-steps"></a>Következő lépések
* Tekintse át a [figyelőeszközök](monitoring-overview.md).
* Nézet a [támogatott Azure-figyelő metrikák](monitoring-supported-metrics.md).
* Tekintse át a [Microsoft Azure figyelése REST API-referencia](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Tekintse át a [Azure könyvtár](https://msdn.microsoft.com/library/azure/mt417623.aspx).
