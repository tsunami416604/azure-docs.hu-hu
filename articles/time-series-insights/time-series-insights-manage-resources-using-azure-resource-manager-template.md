---
title: Az Azure Time Series Insights sablon management – az Azure Resource Manager-sablonok az Azure Time Series Insights-környezet kezelése |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan kezeli az Azure Time Series Insights-környezetet, programozott módon az Azure Resource Manager használatával.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/08/2017
ms.custom: seodec18
ms.openlocfilehash: 70f82c19bced7618027379fcf9451348ac2591eb
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270620"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager-sablonok használatával a Time Series Insights-erőforrások létrehozása

Ez a cikk bemutatja, hogyan hozhat létre és telepíthet a Time Series Insights-erőforrások Azure Resource Manager-sablonok, a PowerShell és a Time Series Insights erőforrás-szolgáltató.

A Time Series Insights támogatja az alábbi forrásanyagokat:
   | Erőforrás | Leírás |
   | --- | --- |
   | Környezet | Egy Time Series Insights-környezet logikus csoportosításai események, amelyek eseményszervezőként olvasni, tárolja, és elérhetővé vált a lekérdezést. További információ: [az Azure Time Series Insights-környezet megtervezése](time-series-insights-environment-planning.md) |
   | Eseményforrás | Eseményforrás egy eseményközvetítőből, amelyről a Time Series Insights olvassa be, és jól alkalmazkodik a környezetbe való kapcsolat. Az IoT Hub és az Eseményközpont jelenleg támogatott esemény forrásai. |
   | Referencia-adatkészlet | Referencia-adatkészletekhez adja meg, hogy a környezetben eseményekkel kapcsolatos metaadatokat. A referencia-adatkészletekhez metaadatok tartományhoz lesz csatlakoztatva eseményekkel rendelkező bejövő során. Referencia-adatkészletekhez által az esemény kulcstulajdonságok erőforrások vannak meghatározva. A tényleges metaadatok, hogy a referencia-adatkészlet feltöltött vagy módosítani az adatsík API-k használatával. |
   | Hozzáférési szabályzat | Hozzáférési szabályzatok az adatlekérdezések kiadása, referenciaadatok a környezetben, módosíthatók és jelenthetők megosztása a mentett lekérdezéseket és perspektívákat a környezet társított engedélyeket. További információ: [adathozzáférés biztosítása egy Time Series Insights-környezet az Azure portal használatával](time-series-insights-data-access.md) |

A Resource Manager-sablon egy JSON-fájlt, amely meghatározza az infrastruktúra és az erőforrások egy erőforráscsoportban. További információ a következő dokumentumokban talál:

- [Az Azure Resource Manager áttekintése – sablon telepítése](../azure-resource-manager/resource-group-overview.md#template-deployment)
- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../azure-resource-manager/resource-group-template-deploy.md)

A [201-timeseriesinsights-környezet-a-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) gyorssablont a Githubon közzétett. Ez a sablon létrehoz egy Time Series Insights környezetet, a gyermek eseményforrás úgy konfigurálva, hogy az Eseményközpontban lévő események felhasználásához és hozzáférési házirendek, amelyek hozzáférést biztosítani a környezeti adatok. Ha egy meglévő Eseményközponton nincs megadva, az egyik való üzembe helyezéséhez létrejön.

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>A helyi PowerShell-lel gyorsindítási sablon üzembe helyezése

Az alábbi eljárás ismerteti a PowerShell használatával történő üzembe helyezése Azure Resource Manager-sablon, amely létrehoz egy Time Series Insights környezetet, a gyermek eseményforrás úgy konfigurálva, hogy az Eseményközpontban lévő események felhasználásához és hozzáférési házirendek, amelyek hozzáférést biztosítanak a a környezet adatait. Ha egy meglévő Eseményközponton nincs megadva, az egyik való üzembe helyezéséhez létrejön.

A hozzávetőleges munkafolyamat a következőképpen történik:

1. Telepítse a PowerShell.
1. A sablon és a egy paraméterfájl létrehozása.
1. A PowerShellben jelentkezzen be az Azure-fiókjával.
1. Hozzon létre egy új erőforráscsoportot, ha még nem létezik.
1. A telepítés tesztelésére.
1. A sablon üzembe helyezéséhez.

### <a name="install-powershell"></a>A PowerShell telepítése

Azure PowerShell telepítéséhez a következő témakör utasításait követve [Ismerkedés az Azure PowerShell-lel](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Sablon létrehozása

Klónozás vagy másolja ki a [201-timeseriesinsights-környezet-a-eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) sablont a Githubból.

### <a name="create-a-parameters-file"></a>A paraméterfájl létrehozása

Hozzon létre egy paramétereket tartalmazó fájlt, másolja át a [201-timeseriesinsights-környezet-a-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) fájlt.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "eventHubNamespaceName": {
          "value": "GEN-UNIQUE"
      },
      "eventHubName": {
          "value": "GEN-UNIQUE"
      },
      "consumerGroupName": {
          "value": "GEN-UNIQUE"
      },
      "environmentName": {
        "value": "GEN-UNIQUE"
      },
      "eventSourceName": {
        "value": "GEN-UNIQUE"
      }
  }
}
```

#### <a name="required-parameters"></a>Szükséges paraméterek

   | Paraméter | Leírás |
   | --- | --- |
   | eventHubNamespaceName | Az adatforrás event hubs-névtér. |
   | eventHubName | A forrás eseményközpont neve. |
   | consumerGroupName | A fogyasztói csoportot, amelynek használatával a Time Series Insights szolgáltatás az adatok olvasásához az event hubs neve. **MEGJEGYZÉS:** Az Erőforrásverseny elkerülése érdekében, a fogyasztói csoportot lehet a Time Series Insights szolgáltatás számára dedikált, és más olvasók sincs megosztva. |
   | EnvironmentName | A környezeti változó nevére. A neve nem tartalmazhat: "<", ">", '%', 'és', ": ','\\','?", "/" karakterek vezérlőkaraktereket és. Minden egyéb karakter engedélyezett.|
   | eventSourceName | Az esemény forrás gyermek-erőforrás neve. A neve nem tartalmazhat: "<", ">", '%', 'és', ": ','\\','?", "/" karakterek vezérlőkaraktereket és. Minden egyéb karakter engedélyezett. |

#### <a name="optional-parameters"></a>Választható paraméterek:

   | Paraméter | Leírás |
   | --- | --- |
   | existingEventHubResourceId | Egy nem kötelező erőforrás-azonosítója egy meglévő Eseményközponttal, amelyek a Time Series Insights-környezet az eseményforrás keresztül fog csatlakozni. **MEGJEGYZÉS:** A felhasználó helyezi üzembe a sablont az Event Hubs listkeys műveletének művelethez jogosultsággal kell rendelkeznie. Ha nem ad meg értéket fogad el, egy új eseményközpont létrejön a sablon által. |
   | environmentDisplayName | A környezet neve helyett azokat az eszközöket, vagy a felhasználói felületen megjelenítendő nem kötelező rövid neve. |
   | environmentSkuName | A termékváltozat neve. További információkért lásd: a [Time Series Insights díjszabása oldalon](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
   | environmentSkuCapacity | A termékváltozat egység kapacitását. További információkért lásd: a [Time Series Insights díjszabása oldalon](https://azure.microsoft.com/pricing/details/time-series-insights/).|
   | environmentDataRetentionTime | A minimális időtartam a környezet események lesznek elérhetők a lekérdezéshez. Az értéket meg kell adni az ISO 8601 formátumú, például "P30D" egy megőrzési szabályzat 30 napig. |
   | eventSourceDisplayName | Egy nem kötelező rövid név azokat az eszközöket, vagy a felhasználói felületen helyett az eseményforrás nevének megjelenítéséhez. |
   | eventSourceTimestampPropertyName | Az esemény tulajdonság, amely az eseményforrás időbélyegzőként használható. Ha az érték nincs megadva a timestampPropertyName, vagy az null értékű vagy üres karakterlánc van megadva, az esemény létrehozásának időpontját használható. |
   | eventSourceKeyName | A közös hozzáférési kulcs, amely a Time Series Insights szolgáltatás használni fog csatlakozni az eseményközpont neve. |
   | accessPolicyReaderObjectIds | A felhasználók vagy alkalmazások az Azure ad-ben, amely elvben olvasó hozzáférést a környezethez azonosítói objektum listáját. A szolgáltatás egyszerű objectId meghívásával is beszerezhetők a **Get-AzureRMADUser** vagy a **Get-AzureRMADServicePrincipal** parancsmagok. Az Azure AD-csoportok a hozzáférési szabályzat létrehozása még nem támogatott. |
   | accessPolicyContributorObjectIds | A felhasználók vagy alkalmazások, amelyek a környezetet közreműködői hozzáféréssel kell rendelkeznie az Azure AD-ben objektumazonosítók listáját. A szolgáltatás egyszerű objectId meghívásával is beszerezhetők a **Get-AzureRMADUser** vagy a **Get-AzureRMADServicePrincipal** parancsmagok. Az Azure AD-csoportok a hozzáférési szabályzat létrehozása még nem támogatott. |

Tegyük fel a következő paramétereket tartalmazó fájlt szeretne használható egy környezetet, és a egy eseményforrás, amely eseményeket olvas be egy meglévő eseményközponton. A környezet közreműködői hozzáférést két hozzáférési házirendeket is létrehoz.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventHubNamespaceName": {
            "value": "tsiTemplateTestNamespace"
        },
        "eventHubName": {
            "value": "tsiTemplateTestEventHub"
        },
        "consumerGroupName": {
            "value": "tsiTemplateTestConsumerGroup"
        },
        "environmentName": {
          "value": "tsiTemplateTestEnvironment"
        },
        "eventSourceName": {
          "value": "tsiTemplateTestEventSource"
        },
        "existingEventHubResourceId": {
          "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
        },
        "accessPolicyContributorObjectIds": {
            "value": [
                "AGUID001-0000-0000-0000-000000000000",
                "AGUID002-0000-0000-0000-000000000000"
            ]
        }
    }
  }
```

További információkért lásd: a [paraméterek](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) cikk.

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Jelentkezzen be az Azure-ba, és állítsa be az Azure-előfizetés

Egy PowerShell-parancssorban futtassa a következő parancsot:

```powershell
Connect-AzureRmAccount
```

Jelentkezzen be az Azure-fiókja kéri. A bejelentkezés után futtassa a következő parancsot a rendelkezésre álló előfizetések megtekintéséhez:

```powershell
Get-AzureRMSubscription
```

Ez a parancs elérhető Azure-előfizetések listáját adja vissza. Válasszon egy előfizetést, az aktuális munkamenet a következő parancs futtatásával. Cserélje le `<YourSubscriptionId>` használni kívánt Azure-előfizetéshez tartozó GUID Azonosítóval rendelkező:

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Állítsa be az erőforráscsoport

Ha nem rendelkezik egy meglévő erőforrást, csoport, hozzon létre egy új erőforráscsoportot a **New-AzureRmResourceGroup** parancsot. Adja meg az erőforráscsoportot és helyet használni kívánt nevét. Példa:

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Ha ez sikeres, az új erőforráscsoport összegzését jelenik meg.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Az üzemelő példány tesztelése

A telepítés ellenőrzése futtatásával a `Test-AzureRmResourceGroupDeployment` parancsmagot. A központi telepítés tesztelésekor paramétereket megadnia, pontosan, mint a központi telepítés végrehajtása közben.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

### <a name="create-the-deployment"></a>Az üzemelő példány létrehozása

Az új központi telepítés létrehozásához futtassa a `New-AzureRmResourceGroupDeployment` parancsmagot, és adja meg a szükséges paramétereket, amikor a rendszer kéri. A paraméterek tartalmaznia kell egy nevet az üzembe helyezés a sablonfájl az erőforráscsoport és az elérési útja vagy URL-cím nevére. Ha a **mód** paraméter nincs megadva, az alapértelmezett érték **növekményes** szolgál. További információkért lásd: [növekményes és teljes körű központi telepítések](../azure-resource-manager/deployment-modes.md).

A következő parancs kéri a öt szükséges paraméterek a PowerShell-ablakban:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

Ehelyett adja meg a paramétereket tartalmazó fájlt, használja a következő parancsot:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Emellett használhatja a beágyazott paraméterek, a központi telepítési parancsmag futtatásakor. A parancs a következőképpen történik:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Futtatásához egy [teljes](../azure-resource-manager/deployment-modes.md) központi telepítését, állítsa be a **mód** paramétert **Complete**:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

## <a name="verify-the-deployment"></a>Az üzemelő példány ellenőrzése

Ha az erőforrások telepítése sikeresen megtörtént, a központi telepítés összegzését a PowerShell-ablakban jelenik meg:

```powershell
DeploymentName          : azuredeploy
ResourceGroupName       : MyDemoRG
ProvisioningState       : Succeeded
Timestamp               : 12/9/2017 01:06:54
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          eventHubNamespaceName  String               <eventHubNamespaceName>
                          eventHubName     String                     <eventHubName>
                          consumerGroupName  String                   <consumerGroupName>
                          existingEventHubResourceId  String
                          environmentName  String                     <environmentName>
                          environmentDisplayName  String
                          environmentSkuName  String                  S1
                          environmentSkuCapacity  Int                 1
                          environmentDataRetentionTime  String        P30D
                          eventSourceName  String                     <eventSourceName>
                          eventSourceDisplayName  String
                          eventSourceTimestampPropertyName  String
                          eventSourceKeyName  String                  manage
                          accessPolicyReaderObjectIds  Array          []
                          accessPolicyContributorObjectIds  Array     [
                            "AGUID001-0000-0000-0000-000000000000",
                            "AGUID002-0000-0000-0000-000000000000"
                          ]

Outputs                 :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          dataAccessFQDN   String
                          <guid>.env.timeseries.azure.com
```

## <a name="deploy-the-quickstart-template-through-the-azure-portal"></a>Az Azure Portalon a rövid útmutató sablon üzembe helyezése

A gyorsindítási sablon kezdőlapja a GitHub is tartalmaz egy **üzembe helyezés az Azure** gombra. Egy egyéni üzembe helyezés lap kattint, megnyílik az Azure Portalon. Ezen az oldalon adja meg vagy válassza ki az értékeket minden, a paraméterek a [szükséges paraméterek](time-series-insights-manage-resources-using-azure-resource-manager-template.md#required-parameters) vagy [választható paraméterek](time-series-insights-manage-resources-using-azure-resource-manager-template.md#optional-parameters) táblákat. A beállítások gombra kattintva mezőinek kitöltése után a **beszerzési** gombra a sablon telepítése megkezdődik.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>További lépések

- Információk a szoftveres kezelése a REST API-k a Time Series Insights-erőforrások: [Time Series Insights-felügyelet](https://docs.microsoft.com/rest/api/time-series-insights-management/).
