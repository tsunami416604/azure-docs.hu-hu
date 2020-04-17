---
title: Új Azure Application Insights-erőforrás létrehozása | Microsoft dokumentumok
description: Manuálisan állítsa be az Application Insights figyelése egy új élő alkalmazás.
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 0c8b9ccaa70a2fd1bf46c6f4537f54d702ecc48f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537576"
---
# <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása

Az Azure Application Insights az alkalmazásadatait egy Microsoft *Azure-forrásban*jeleníti meg. Ezért egy új erőforrás létrehozása része az [Application Insights új alkalmazások figyeléséhez történő beállításának.][start] Miután létrehozta az új erőforrást, lejuthat a műszerezési kulcs, és ezzel konfigurálhatja az Application Insights SDK. A instrumentation kulcs a telemetriai adatokat az erőforráshoz kapcsolja.

## <a name="sign-in-to-microsoft-azure"></a>Bejelentkezés a Microsoft Azure-ba

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása

Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és hozzon létre egy Application Insights-erőforrást:

![Kattintson a "+" jelre a bal felső sarokban. Válassza ki a fejlesztői eszközöket, majd az Application Insights](./media/create-new-resource/new-app-insights.png)

   | Beállítások        |  Érték           | Leírás  |
   | ------------- |:-------------|:-----|
   | **Név**      | Egyedi érték | A figyelt alkalmazást azonosító név. |
   | **Erőforráscsoport**     | myResourceGroup      | Az App Insights-adatok üzemeltetéséhez az új vagy meglévő erőforráscsoport neve. |
   | **Helyen** | USA keleti régiója | Válasszon egy helyet a közelben, vagy az alkalmazás üzemeltetési helye közelében. |

> [!NOTE]
> Bár ugyanazt az erőforrásnevet használhatja a különböző erőforráscsoportok között, hasznos lehet globálisan egyedi nevet használni. Ez akkor lehet hasznos, ha [több erőforrás-lekérdezést](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query#identifying-an-application) kíván végrehajtani, mivel ez leegyszerűsíti a szükséges szintaxist.

Írja be a megfelelő értékeket a szükséges mezőkbe, majd válassza **a Véleményezés + create**lehetőséget.

![Írja be az értékeket a szükséges mezőkbe, majd válassza a "véleményezés + létrehozás" lehetőséget.](./media/create-new-resource/review-create.png)

Az alkalmazás létrehozása után megnyílik egy új ablaktábla. Ez az ablaktábla, ahol a figyelt alkalmazás teljesítmény- és használati adatait láthatja. 

## <a name="copy-the-instrumentation-key"></a>A műszerezési kulcs másolása

A instrumentation kulcs azonosítja az erőforrást, amelyhez a telemetriai adatokat társítani szeretné. A műszerezési kulcsot másolnia kell, és hozzá kell adnia az alkalmazás kódjához.

![Kattintson és másolja a műszerezési kulcsot](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>Az SDK telepítése az alkalmazásba

Telepítse az Application Insights SDK-t az alkalmazásba. Ez a lépés nagymértékben függ az alkalmazás típusától.

A műszerezési kulccsal konfigurálhatja [az alkalmazásba telepített SDK-t.][start]

Az SDK szabványos modulokat tartalmaz, amelyek telemetriai adatokat küldenek anélkül, hogy további kódot kellene írnia. Felhasználói műveletek nyomon követéséhez vagy a problémák részletesebb diagnosztizálásához [használja az API-t][api] saját telemetriai adatok küldéséhez.

## <a name="creating-a-resource-automatically"></a>Erőforrás automatikus létrehozása

### <a name="powershell"></a>PowerShell

Új Application Insights-erőforrás létrehozása

```powershell
New-AzApplicationInsights [-ResourceGroupName] <String> [-Name] <String> [-Location] <String> [-Kind <String>]
 [-Tag <Hashtable>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="example"></a>Példa

```powershell
New-AzApplicationInsights -Kind java -ResourceGroupName testgroup -Name test1027 -location eastus
```
#### <a name="results"></a>Results (Eredmények)

```powershell
Id                 : /subscriptions/{subid}/resourceGroups/testgroup/providers/microsoft.insights/components/test1027
ResourceGroupName  : testgroup
Name               : test1027
Kind               : web
Location           : eastus
Type               : microsoft.insights/components
AppId              : 8323fb13-32aa-46af-b467-8355cf4f8f98
ApplicationType    : web
Tags               : {}
CreationDate       : 10/27/2017 4:56:40 PM
FlowType           :
HockeyAppId        :
HockeyAppToken     :
InstrumentationKey : 00000000-aaaa-bbbb-cccc-dddddddddddd
ProvisioningState  : Succeeded
RequestSource      : AzurePowerShell
SamplingPercentage :
TenantId           : {subid}
```

A parancsmag teljes PowerShell-dokumentációját, és megtudhatja, hogyan lehet lekérni a műszerezési kulcsot az [Azure PowerShell dokumentációjában.](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsights?view=azps-2.5.0)

### <a name="azure-cli-preview"></a>Azure CLI (előzetes verzió)

Az application insights előzetes verziójának eléréséhez először futtatnia kell az Application Insights Azure CLI-parancsait:

```azurecli
 az extension add -n application-insights
```

Ha nem futtatja `az extension add` a parancsot, megjelenik egy hibaüzenet, amely a következőket mondja:`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Most már futtathatja a következőket az Application Insights-erőforrás létrehozásához:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--kind]
                                         [--tags]
```

#### <a name="example"></a>Példa

```azurecli
az monitor app-insights component create --app demoApp --location westus2 --kind web -g demoRg --application-type web
```

#### <a name="results"></a>Results (Eredmények)

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g demoApp  --application-type web
{
  "appId": "87ba512c-e8c9-48d7-b6eb-118d4aee2697",
  "applicationId": "demoApp",
  "applicationType": "web",
  "creationDate": "2019-08-16T18:15:59.740014+00:00",
  "etag": "\"0300edb9-0000-0100-0000-5d56f2e00000\"",
  "flowType": "Bluefield",
  "hockeyAppId": null,
  "hockeyAppToken": null,
  "id": "/subscriptions/{subid}/resourceGroups/demoApp/providers/microsoft.insights/components/demoApp",
  "instrumentationKey": "00000000-aaaa-bbbb-cccc-dddddddddddd",
  "kind": "web",
  "location": "eastus",
  "name": "demoApp",
  "provisioningState": "Succeeded",
  "requestSource": "rest",
  "resourceGroup": "demoApp",
  "samplingPercentage": null,
  "tags": {},
  "tenantId": {tenantID},
  "type": "microsoft.insights/components"
}
```

A parancs teljes Azure CLI dokumentációját, valamint a instrumentation kulcs lekérésének módját az [Azure CLI dokumentációjában](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create)találja.

## <a name="next-steps"></a>További lépések
* [Diagnosztikai keresés](../../azure-monitor/app/diagnostic-search.md)
* [Metrikák böngészése](../../azure-monitor/platform/metrics-charts.md)
* [Analytics-lekérdezések](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
