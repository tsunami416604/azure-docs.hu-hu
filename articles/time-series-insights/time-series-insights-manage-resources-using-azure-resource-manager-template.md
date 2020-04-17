---
title: Környezet kezelése az Azure Resource Manager-sablonokkal – Azure Time Series Insights | Microsoft dokumentumok
description: Ismerje meg, hogyan kezelheti az Azure Time Series Insights-környezetet programozott módon az Azure Resource Manager használatával.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: seodec18
ms.openlocfilehash: a670e32058794daeaa233464ba7d054f45ef25e3
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536318"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Time Series Insights-erőforrások létrehozása Az Azure Resource Manager-sablonokkal

Ez a cikk ismerteti, hogyan hozhat létre és helyezhet üzembe Time Series Insights-erőforrásokat [az Azure Resource Manager-sablonok](https://docs.microsoft.com/azure/azure-resource-manager/), a PowerShell és a Time Series Insights erőforrás-szolgáltató használatával.

A Time Series Insights a következő forrásokat támogatja:

   | Erőforrás | Leírás |
   | --- | --- |
   | Környezet | A Time Series Insights-környezet az eseményközvetítőktől beolvasott, tárolt és lekérdezésre elérhetővé tett események logikai csoportosítása. További információkért olvassa el [az Azure Time Series Insights-környezet megtervezése című részt.](time-series-insights-environment-planning.md) |
   | Esemény forrása | Az eseményforrás egy eseményközvetítővel való kapcsolat, amelyből a Time Series Insights felolvassa és beolvassa az eseményeket a környezetbe. Jelenleg támogatott eseményforrások az IoT Hub és az Event Hub. |
   | Referencia-adatkészlet | A referencia-adatkészletek metaadatokat szolgáltatnak a környezetben lévő eseményekről. A referencia-adatkészletek metaadatait a bejövő forgalom során eseményekkel fogja egyesíteni. A referencia-adatkészletek az eseménykulcs tulajdonságai alapján erőforrásokként vannak definiálva. A referencia-adatkészletet összeállító tényleges metaadatokat a rendszer adatsík API-kon keresztül tölti fel vagy módosítja. |
   | Hozzáférési szabályzat | A hozzáférési házirendek engedélyeket adnak az adatlekérdezések kérdéséhez, a környezetben lévő hivatkozási adatok kezeléséhez, valamint a környezethez társított mentett lekérdezések és perspektívák megosztásához. További információkért olvassa el [az Adatok elérésének megadása egy Time Series Insights-környezethez való hozzáférés engedélyezéséhez az Azure Portal használatával](time-series-insights-data-access.md) |

Az Erőforrás-kezelő sablon egy JSON-fájl, amely meghatározza az erőforráscsoport erőforrásainak infrastruktúráját és konfigurációját. A következő dokumentumok részletesebben ismertetik a sablonfájlokat:

- [Az Azure Resource Manager-sablon telepítése](../azure-resource-manager/templates/overview.md)
- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../azure-resource-manager/templates/deploy-powershell.md)
- [Microsoft.TimeSeriesInsights erőforrástípusok](/azure/templates/microsoft.timeseriesinsights/allversions)

A [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) gyorsindítási sablon a GitHubon van közzétéve. Ez a sablon létrehoz egy Time Series Insights-környezetet, egy gyermekeseményforrást, amely úgy van konfigurálva, hogy eseményeket használjon fel egy Eseményközpontból, és olyan hozzáférési szabályzatokat, amelyek hozzáférést biztosítanak a környezet adataihoz. Ha nincs megadva egy meglévő Event Hub, a központi telepítéssel létrejön egy.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>Telepítési sablon és paraméterek megadása

Az alábbi eljárás bemutatja, hogyan használhatja a PowerShellt egy Azure Resource Manager-sablon üzembe helyezéséhez, amely time series insights-környezetet, egy eseményközpontból származó események használatára konfigurált gyermekeseményforrást hoz létre, és a környezet adataihoz hozzáférést biztosító hozzáférési szabályzatokat. Ha nincs megadva egy meglévő Event Hub, a központi telepítéssel létrejön egy.

1. Telepítse az Azure PowerShellt az [Azure PowerShell használatával való ismerkedés](https://docs.microsoft.com/powershell/azure/get-started-azureps)című témakör utasításainak követésével.

1. Klónozza vagy másolja a [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) sablont a GitHubról.

   * Paraméterfájl létrehozása

     Paraméterek fájl létrehozásához másolja a [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) fájlt.

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   * Kötelező paraméterek

     | Paraméter | Leírás |
     | --- | --- |
     | eventHubNamespaceName | A forrásesemény-központ névtere. |
     | eventHubName | A forrásesemény-központ neve. |
     | consumerGroupName | A Time Series Insights szolgáltatás által az eseményközpontból az adatok olvasásához használt fogyasztói csoport neve. **MEGJEGYZÉS:** Az erőforrások kalkulálásának elkerülése érdekében ezt a fogyasztói csoportot a Time Series Insights szolgáltatásnak kell felosztani, és nem szabad megosztani más olvasókkal. |
     | környezetNév | A környezet neve. A név nem `<` `>`tartalmazhat: , `\\` `?`, `/` `%`, `&`, `:`, , , , , , és a vezérlőkaraktereket. Minden egyéb karakter engedélyezett.|
     | eventSourceName | Az eseményforrás gyermekerőforrásának neve. A név nem `<` `>`tartalmazhat: , `\\` `?`, `/` `%`, `&`, `:`, , , , , , és a vezérlőkaraktereket. Minden egyéb karakter engedélyezett. |

    <div id="optional-parameters"></div>

   * Opcionális paraméterek

     | Paraméter | Leírás |
     | --- | --- |
     | existingEventHubResourceId | Egy meglévő Event Hub opcionális erőforrás-azonosítója, amely az eseményforráson keresztül csatlakozik a Time Series Insights-környezethez. **MEGJEGYZÉS:** A sablont üzembe helyező felhasználónak jogosultsággal kell rendelkeznie a listkeys-művelet eseményközponton történő végrehajtásához. Ha nem ad át értéket, a sablon új eseményközpontot hoz létre. |
     | környezetDisplayName | Választható rövid név, amely a környezet neve helyett az eszköz- vagy felhasználói felületeken jelenik meg. |
     | környezetSkuName | A termékváltozat neve. További információért olvassa el a [Time Series Insights árképzési oldalát.](https://azure.microsoft.com/pricing/details/time-series-insights/)  |
     | környezetSkuCapacity | A Sku egységkapacitása. További információért olvassa el a [Time Series Insights árképzési oldalát.](https://azure.microsoft.com/pricing/details/time-series-insights/)|
     | környezetDataRetentionTime | A minimális időfeneketa a környezet eseményei lesz elérhető a lekérdezéshez. Az értéket ISO 8601 formátumban kell megadni, például `P30D` 30 napos adatmegőrzési szabály esetén. |
     | eventSourceDisplayName | Választható rövid név, amely az eseményforrás neve helyett eszköz- vagy felhasználói felületeken jeleníthető meg. |
     | eventSourceTimestampPropertyName | Az eseménytulajdonság, amelyet az eseményforrás időbélyegeként fog használni. Ha a timestampPropertyName érték nincs megadva, vagy ha null vagy üres karakterlánc van megadva, a program az esemény létrehozási idejét fogja használni. |
     | eventSourceKeyName | A Megosztott hozzáférésű kulcs neve, amelyet a Time Series Insights szolgáltatás az eseményközponthoz való csatlakozáshoz használ. |
     | accessPolicyReaderObjectIds | Az Azure AD-ben a felhasználók vagy alkalmazások objektumazonosítóinak listája, amelyeknek reader-hozzáféréssel kell rendelkezniük a környezethez. A szolgáltatásnév objectId lehet beszerezni a **Get-AzADUser** vagy a **Get-AzADServicePrincipal** parancsmagok. Az Azure AD-csoportok hozzáférési szabályzatának létrehozása még nem támogatott. |
     | accessPolicyContributorObjectIds | Az Azure AD-ben a felhasználók vagy alkalmazások objektumazonosítóinak listája, amelyeknek közreműködői hozzáféréssel kell rendelkezniük a környezethez. A szolgáltatásnév objectId lehet beszerezni a **Get-AzADUser** vagy a **Get-AzADServicePrincipal** parancsmagok. Az Azure AD-csoportok hozzáférési szabályzatának létrehozása még nem támogatott. |

   * Például a következő paraméterek fájl lenne használva egy környezet és egy eseményforrás, amely beolvassa az eseményeket egy meglévő eseményközpontból. Emellett két hozzáférési szabályzatot is létrehoz, amelyek hozzáférést biztosítanak a közreműködői környezethez.

     ```JSON
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

    * További információért olvassa el a [Paraméterek](../azure-resource-manager/templates/parameter-files.md) cikket.

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>A gyorsindítási sablon helyi telepítése a PowerShell használatával

> [!IMPORTANT]
> Az alábbi parancssori műveletek az [Az PowerShell modult](https://docs.microsoft.com/powershell/azure/overview)írják le.

1. A PowerShellben jelentkezzen be az Azure-fiókjába.

    * Egy PowerShell-parancssorból futtassa a következő parancsot:

      ```powershell
      Connect-AzAccount
      ```

    * A rendszer kéri, hogy jelentkezzen be az Azure-fiókjába. A bejelentkezés után futtassa a következő parancsot az elérhető előfizetések megtekintéséhez:

      ```powershell
      Get-AzSubscription
      ```

    * Ez a parancs az elérhető Azure-előfizetések listáját adja vissza. Válasszon előfizetést az aktuális munkamenethez a következő parancs futtatásával. Cserélje `<YourSubscriptionId>` le a használni kívánt Azure-előfizetés GUID azonosítójára:

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. Hozzon létre egy új erőforráscsoportot, ha nem létezik.

   * Ha nem rendelkezik meglévő erőforráscsoporttal, hozzon létre egy új erőforráscsoportot a **New-AzResourceGroup** paranccsal. Adja meg a használni kívánt erőforráscsoport és hely nevét. Például:

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   * Ha sikeres, megjelenik az új erőforráscsoport összegzése.

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. Tesztelje az üzembe helyezést.

   * Ellenőrizze a központi `Test-AzResourceGroupDeployment` telepítést a parancsmag futtatásával. A központi telepítés tesztelése során adja meg a paramétereket pontosan úgy, ahogy a központi telepítés végrehajtásakor.

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. A központi telepítés létrehozása

    * Az új központi telepítés `New-AzResourceGroupDeployment` létrehozásához futtassa a parancsmast, és adja meg a szükséges paramétereket, amikor a rendszer kéri. A paraméterek tartalmazzák a központi telepítés nevét, az erőforráscsoport nevét, valamint a sablonfájl elérési útját vagy URL-címét. Ha a **Mode** paraméter nincs megadva, a **növekményes** alapértelmezett értéket használja a program. További információért olvassa el [a Növekményes és teljes központi telepítések című.](../azure-resource-manager/templates/deployment-modes.md)

    * A következő parancs a PowerShell-ablakban az öt szükséges paramétert kéri:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

    * A paraméterfájl megadásához használja a következő parancsot:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    * A központi telepítési parancsmag futtatásakor szövegközi paramétereket is használhat. A parancs formátuma a következő:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    * [A teljes](../azure-resource-manager/templates/deployment-modes.md) telepítés futtatásához állítsa a **Mód** paramétert **Befejezés**beállításra:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. A telepítés ellenőrzése

    * Ha az erőforrások telepítése sikeresen megtörtént, a központi telepítés összegzése jelenik meg a PowerShell ablakban:

      ```powershell
       DeploymentName          : MyDemoDeployment
       ResourceGroupName       : MyDemoRG
       ProvisioningState       : Succeeded
       Timestamp               : 10/11/2019 3:20:37 AM
       Mode                    : Incremental
       TemplateLink            :
       Parameters              :
                                 Name                                Type                       Value
                                 ==================================  =========================  ==========
                                 eventHubNewOrExisting               String                     new
                                 eventHubResourceGroup               String                     MyDemoRG
                                 eventHubNamespaceName               String                     tsiquickstartns
                                 eventHubName                        String                     tsiquickstarteh
                                 consumerGroupName                   String                     tsiquickstart
                                 environmentName                     String                     tsiquickstart
                                 environmentDisplayName              String                     tsiquickstart
                                 environmentSkuName                  String                     S1
                                 environmentSkuCapacity              Int                        1
                                 environmentDataRetentionTime        String                     P30D
                                 eventSourceName                     String                     tsiquickstart
                                 eventSourceDisplayName              String                     tsiquickstart
                                 eventSourceTimestampPropertyName    String
                                 eventSourceKeyName                  String                     manage
                                 accessPolicyReaderObjectIds         Array                      []
                                 accessPolicyContributorObjectIds    Array                      []
                                 location                            String                     westus

       Outputs                 :
                                  Name              Type                       Value
                                  ================  =========================  ==========
                                  dataAccessFQDN    String
                                  11aa1aa1-a1aa-1a1a-a11a-aa111a111a11.env.timeseries.azure.com

       DeploymentDebugLogLevel :
      ```

1. A gyorsútmutató sablon üzembe helyezése az Azure Portalon keresztül

   * A rövid útmutató sablon kezdőlapja a GitHubon is tartalmaz egy **Üzembe helyezés az Azure-ba** gombot. Kattintson rá megnyit egy egyéni üzembe helyezési lapot az Azure Portalon. Ezen a lapon a [szükséges paraméterek](#required-parameters) vagy [választható paraméterek](#optional-parameters) tábláinak értékeit adhatja meg vagy választhatja ki. A beállítások kitöltése után a **Vásárlás** gombra kattintva elindítja a sablon központi telepítését.
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png"/>
    </a>

## <a name="next-steps"></a>További lépések

- A Time Series Insights-erőforrások REST API-k használatával történő programozott kezeléséről a [Time Series Insights Management című olvasnivalót olvashat.](https://docs.microsoft.com/rest/api/time-series-insights-management/)
