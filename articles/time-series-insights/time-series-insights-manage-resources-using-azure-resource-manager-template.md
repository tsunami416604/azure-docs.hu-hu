---
title: A Azure idő adatsorozat Insights környezet használata Azure Resource Manager-sablonok kezelése |} Microsoft Docs
description: Ez a cikk ismerteti a programozott módon, Azure Resource Manager Azure idő adatsorozat Insights környezete kezeléséhez.
ms.service: time-series-insights
services: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
ms.reviewer: anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 99aabc01132da60a1b09fcf65f439b8e084bbffa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34651964"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Idő adatsorozat Insights-erőforrások használata Azure Resource Manager-sablonok létrehozása

Ez a cikk ismerteti, hogyan hozhat létre és telepíthet idő adatsorozat Insights-erőforrások Azure Resource Manager sablonok, PowerShell és az idő adatsorozat Insights erőforrás-szolgáltató használatával.

Idő adatsorozat Insights támogatja a következőket:
   | Erőforrás | Leírás |
   | --- | --- |
   | Környezet | Egy alkalommal adatsorozat Insights környezete események, amelyek esemény brókerek olvasni, tárolt és elérhetővé a lekérdezés logikai csoportosítása. További információ: [a Azure idő adatsorozat Insights környezet megtervezése](time-series-insights-environment-planning.md) |
   | Eseményforrás | Egy esemény forrása kapcsolatot létesíthet egy esemény broker, amelyből idő adatsorozat Insights beolvassa és események ingests környezetbe. Az IoT-központ és az Event Hubs jelenleg támogatott esemény forrásai. |
   | Referencia-adatkészlet | Hivatkozási adatkészletek adja meg a környezetben eseményekkel kapcsolatos metaadatokat. A hivatkozási adatkészletek metaadatok tartományhoz fog csatlakozni az események érkező során. Az esemény kulcstulajdonságok erőforrásként hivatkozási adatkészletek vannak definiálva. A tényleges metaadatok, amely a referencia-adatkészlet feltöltött, illetve a módosított adatok vezérlősík API-k használatával. |
   | Hozzáférési szabályzat | Hozzáférési házirendek adja ki a lekérdezések, kezelheti a referenciaadatok a környezetben, lekérdezések és a környezet perspektívák engedélyeket. További információ: [adatokat az Azure-portál használatával idő adatsorozat Insights környezetben való hozzáférést](time-series-insights-data-access.md) |

A Resource Manager-sablon egy JSON-fájl, amely meghatározza az infrastruktúra és az erőforrás egy erőforráscsoportban. További információkért lásd a következő dokumentumokat:

- [Azure Resource Manager áttekintése – sablon telepítése](../azure-resource-manager/resource-group-overview.md#template-deployment)
- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../azure-resource-manager/resource-group-template-deploy.md)

A [201-timeseriesinsights-környezet-az-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) gyorsindítási sablonon közzé van téve a Githubon. Ez a sablon környezetet hoz létre idő adatsorozat Insights, a gyermek esemény forrását az Eseményközpontban lévő események felhasználásához, valamint a hozzáférési házirendek, amelyek hozzáférést biztosíthat a környezeti adatok konfigurálva. Ha egy meglévő Eseményközpont nincs megadva, a telepítés jön létre.

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Központi telepítése a következő gyorsindítási sablonon helyileg a PowerShell használatával

Az alábbi eljárás bemutatja a PowerShell használatával történő központi telepítése egy Azure Resource Manager-sablon által létrehozott egy idő adatsorozat Insights környezetben, a gyermek esemény forrását az Eseményközpontban lévő események felhasználásához, valamint a hozzáférési házirendek, amelyek hozzáférést konfigurálva a a környezetnek adatokat. Ha egy meglévő Eseményközpont nincs megadva, a telepítés jön létre.

A hozzávetőleges munkafolyamata a következőképpen történik:

1. Telepítse a PowerShell.
1. Hozzon létre a sablont és egy paramétert.
1. A PowerShellben jelentkezzen be az Azure-fiókjával.
1. Ha még nem létezik, hozzon létre egy új erőforráscsoportot.
1. A telepítés tesztelésére.
1. A sablon telepítéséhez.

### <a name="install-powershell"></a>A PowerShell telepítése

Azure PowerShell telepítése utasításait követve [Ismerkedés az Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Sablon létrehozása

Klónozott vagy másolása a [201-timeseriesinsights-környezet-az-eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) sablont a Githubból.

### <a name="create-a-parameters-file"></a>Paraméterek fájl létrehozása

Hozzon létre egy paraméterek fájlt, másolja át a [201-timeseriesinsights-környezet-az-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) fájlt.

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

#### <a name="required-parameters"></a>Kötelező paraméter

   | Paraméter | Leírás |
   | --- | --- |
   | eventHubNamespaceName | A forrás eseményközpont névtere. |
   | eventHubName | A forrás eseményközpont neve. |
   | consumerGroupName | A fogyasztói csoportot, amely az idő adatsorozat Insights szolgáltatás használatával fogja beolvasni az adatokat az event hubs neve. **Megjegyzés:** Erőforrásverseny elkerülése érdekében a fogyasztói csoportot kell az az idő adatsorozat Insights szolgáltatásban dedikált és nincsenek megosztva, más olvasók. |
   | EnvironmentName | A környezet neve. A név nem tartalmazhat: "<", ">", "%", "&", ": ','\\','?', '/' és bármely vezérlőkarakterek. Minden egyéb karakterek használhatók.|
   | eventSourceName | Az esemény forrás gyermek-erőforrás neve. A név nem tartalmazhat: "<", ">", "%", "&", ": ','\\','?', '/' és bármely vezérlőkarakterek. Minden egyéb karakterek használhatók. |

#### <a name="optional-parameters"></a>Választható paraméterek:

   | Paraméter | Leírás |
   | --- | --- |
   | existingEventHubResourceId | Egy nem kötelező erőforrás-azonosítója egy meglévő Eseményközpontot, az idő adatsorozat Insights környezetbe az eseményforrás keresztül csatlakozó. **Megjegyzés:** a felhasználó a sablon telepítéséhez hajtsa végre a listkeys műveletet az Event Hubs jogosultsággal kell rendelkeznie. Ha nem ad meg értéket fogad el, a sablon egy új eseményközpont fogja létrehozni. |
   | environmentDisplayName | A környezet neve helyett tooling vagy felhasználói felület megjelenítése nem kötelező rövid neve. |
   | environmentSkuName | A termékváltozat neve. További információkért lásd: a [idő adatsorozat Insights árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
   | environmentSkuCapacity | A termékváltozat egység kapacitását. További információkért lásd: a [idő adatsorozat Insights árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/time-series-insights/).|
   | environmentDataRetentionTime | A minimális időtartam a környezet események lesznek elérhetők a lekérdezéshez. Az értéket kell adni az ISO 8601 formátumban, például "P30D" adatmegőrzési a 30 napos. |
   | eventSourceDisplayName | Megjeleníti az adatforrás neve helyett tooling vagy felhasználói felületek nem kötelező rövid neve. |
   | eventSourceTimestampPropertyName | Az esemény tulajdonság, amely az eseményforrás Timestamp típusú lesz. Ha az érték nincs megadva timestampPropertyName, vagy null értékű vagy üres-karakterlánc van megadva, az esemény létrehozásának idejét használható. |
   | eventSourceKeyName | A megosztott elérési kulcsot, amely az idő adatsorozat Insights szolgáltatás az eseményközpontba való kapcsolódáshoz használandó neve. |
   | accessPolicyReaderObjectIds | A felhasználók vagy alkalmazások az Azure ad-ben, hogy a környezet olvasási hozzáféréssel kell rendelkeznie az objektumazonosítók listáját. A szolgáltatás egyszerű objectId meghívásával érhető el a **Get-AzureRMADUser** vagy a **Get-AzureRMADServicePrincipal** parancsmagok. A hozzáférési házirendek az Azure AD-csoport létrehozása jelenleg nem támogatott. |
   | accessPolicyContributorObjectIds | Objektumazonosítók a felhasználók vagy alkalmazások az Azure AD, a környezet közreműködői hozzáférési listáját. A szolgáltatás egyszerű objectId meghívásával érhető el a **Get-AzureRMADUser** vagy a **Get-AzureRMADServicePrincipal** parancsmagok. A hozzáférési házirendek az Azure AD-csoport létrehozása jelenleg nem támogatott. |

Tegyük fel a következő paraméterfájl hozzon létre egy olyan környezetben, és olvassa be az eseményeket egy meglévő eseményközpont eseményforrást szeretne használni. A környezet közreműködői hozzáférést két hozzáférési házirendeket is létrehoz.

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

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Jelentkezzen be az Azure és az Azure-előfizetés beállítása

Egy PowerShell-parancssorba futtassa a következő parancsot:

```powershell
Connect-AzureRmAccount
```

Jelentkezzen be az Azure-fiókjával kéri. A bejelentkezés után futtassa a következő parancsot az elérhető előfizetések megtekintéséhez:

```powershell
Get-AzureRMSubscription
```

Ez a parancs elérhető Azure-előfizetések listáját adja vissza. Válasszon egy előfizetést, az aktuális munkamenet a következő parancs futtatásával. Cserélje le `<YourSubscriptionId>` használni kívánt Azure-előfizetés GUID:

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Az erőforráscsoport beállítása

Ha a csoport, hozzon létre egy új erőforráscsoportot a meglévő erőforrás még a **New-AzureRmResourceGroup** parancsot. Adja meg az erőforráscsoportot és helyet használni kívánt nevét. Példa:

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Ha sikeres, az új erőforráscsoport összegzését jelenik meg.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Az üzemelő példány tesztelése

A központi telepítés futtatásával ellenőrizze a `Test-AzureRmResourceGroupDeployment` parancsmag. Ha a központi telepítés tesztelése, adja meg a paramétereket pontosan ugyanúgy, a központi telepítés végrehajtása közben.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

### <a name="create-the-deployment"></a>A központi telepítés létrehozásához

Az új központi telepítés létrehozásához futtassa a `New-AzureRmResourceGroupDeployment` parancsmagot, és adja meg a szükséges paramétereket, amikor a rendszer kéri. A paraméternek számít a központi telepítésre, az erőforráscsoport és az elérési út vagy URL neve a sablon fájl nevét. Ha a **mód** paraméter nincs megadva, az alapértelmezett érték **növekményes** szolgál. További információkért lásd: [növekményes és teljes telepítések](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments).

A következő parancs bekéri a öt szükséges paramétereket a PowerShell-ablakban:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

Ehelyett adja meg egy paraméterfájl, használja a következő parancsot:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Beágyazott paramétereket használhatja a központi telepítési parancsmag futtatása esetén is. A parancs a következőképpen történik:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Futtatásához egy [teljes](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) központi telepítését, állítsa be a **mód** paramétert **Complete**:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

## <a name="verify-the-deployment"></a>A telepítés ellenőrzése

Ha az erőforrások telepítése sikeres volt, a központi telepítés összegzését a PowerShell-ablakban jelenik meg:

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

## <a name="deploy-the-quickstart-template-through-the-azure-portal"></a>Az Azure portálon keresztül a gyors üzembe helyezés sablon üzembe helyezése

A gyorsindítási sablonon kezdőlapja a Githubból is magában foglalja a **az Azure telepítéséhez** gombra. Az Azure portálon kattintson rá egyéni telepítési oldal megnyitása. Ezen a lapon adja meg vagy válassza ki az értékeket az egyes származó paraméterek a [kötelező paraméterek](time-series-insights-manage-resources-using-azure-resource-manager-template.md#required-parameters) vagy [választható paraméterek:](time-series-insights-manage-resources-using-azure-resource-manager-template.md#optional-parameters) táblákat. Kattintson a beállítások megadása után a **beszerzési** gomb indít el a sablon-üzembehelyezés.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>További lépések

- Idő adatsorozat Insights-erőforrások REST API-val programozott módon kezeléséről további információért lásd: [adatsorozat Insights Időkezelést](https://docs.microsoft.com/rest/api/time-series-insights-management/).
