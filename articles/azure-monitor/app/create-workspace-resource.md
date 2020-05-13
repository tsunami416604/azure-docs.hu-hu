---
title: Új Azure Monitor Application Insights munkaterület-alapú erőforrás létrehozása | Microsoft Docs
description: Ismerkedjen meg az új Azure Monitor Application Insights munkaterület-alapú erőforrások engedélyezéséhez szükséges lépésekkel.
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 65c2220bd3e9ea93e562b256e84796aed9046d00
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211407"
---
# <a name="workspace-based-application-insights-resources-preview"></a>Munkaterület-alapú Application Insights erőforrások (előzetes verzió)

A munkaterület-alapú erőforrások a Application Insights és Log Analytics közötti teljes integrációt támogatják. Most már elküldheti Application Insights telemetria egy közös Log Analytics munkaterületre, amely teljes hozzáférést biztosít Log Analytics összes szolgáltatásához, miközben az alkalmazások, az infrastruktúra és a platformok naplóit egyetlen konszolidált helyen tartja.

Ez lehetővé teszi a közös szerepköralapú Access Control (RBAC) használatát az erőforrások között, és kiküszöböli az alkalmazások közötti vagy munkaterület-lekérdezések szükségességét.

> [!NOTE]
> A munkaterületen alapuló Application Insights erőforrások adatfeldolgozása és megőrzése a Log Analytics munkaterületen történik, ahol az adatok találhatók. [További]( https://docs.microsoft.com/azure/azure-monitor/app/pricing#workspace-based-application-insights) információ a munkaterület-alapú Application Insights erőforrások számlázásáról.

Az új felület teszteléséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com), és hozzon létre egy Application Insights erőforrást:

![Munkaterület-alapú Application Insights erőforrás](./media/create-workspace-resource/create-workspace-based.png)

Ha még nem rendelkezik meglévő Log Analytics munkaterülettel, [tekintse meg a log Analytics munkaterület-létrehozási dokumentációt](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

A nyilvános előzetes verzió **munkaterület-alapú erőforrásai jelenleg csak az USA 2. nyugati régiójában, az USA keleti** régiójában és az USA déli középső régiójában vannak.

Miután létrehozta az erőforrást, megjelenik a megfelelő munkaterület-információ az **Áttekintés** ablaktáblán:

![Munkaterület neve](./media/create-workspace-resource/workspace-name.png)

A kék hivatkozás szövegére kattintva megtekintheti a társított Log Analytics munkaterületet, ahol kihasználhatja az új, egyesített munkaterület-lekérdezési környezetet.

> [!NOTE]
> Továbbra is visszamenőleges kompatibilitást biztosítunk a Application Insights klasszikus erőforrás-lekérdezésekhez, a munkafüzetekhez és a napló alapú riasztásokhoz a Application Insights felhasználói felületén belül. Az [Új munkaterület-alapú tábla struktúrájának/sémájának](apm-tables.md) lekérdezéséhez vagy megtekintéséhez először navigáljon a log Analytics munkaterületre. Az előzetes verzióban a **naplók** kiválasztása a Application Insights ablaktáblán elérhetővé teszi a klasszikus Application Insights lekérdezési élményt.

## <a name="copy-the-connection-string"></a>A kapcsolati sztring másolása

A [kapcsolódási karakterlánc]() azonosítja azt az erőforrást, amelyhez hozzá szeretné rendelni a telemetria-adatait. Azt is lehetővé teszi, hogy módosítsa az erőforrás által a telemetria célként használt végpontokat. A kapcsolódási karakterláncot át kell másolnia, és hozzá kell adnia az alkalmazás kódjához vagy egy környezeti változóhoz.

![Kattintson és másolja a kialakítási kulcsot](./media/create-new-resource/instrumentation-key.png)

## <a name="monitoring-configuration"></a>Figyelési konfiguráció

A munkaterület-alapú Application Insights erőforrás létrehozása után a figyelés konfigurálása viszonylag egyszerű.

### <a name="code-based-application-monitoring"></a>Kód alapú alkalmazások figyelése

A kód alapú alkalmazások figyeléséhez csak telepítse a megfelelő Application Insights SDK-t, és mutasson rá a kialakítási kulcsra vagy a kapcsolódási sztringre az újonnan létrehozott erőforráshoz.  

A Application Insights SDK kód alapú figyeléshez való beállításával kapcsolatos részletes dokumentációért forduljon a nyelvhez vagy a keretrendszerhez kapcsolódó dokumentációhoz:

- [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)
- [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)
- [Háttér-feladatok & modern konzolon futó alkalmazások (.NET/.NET Core)](https://docs.microsoft.com/azure/azure-monitor/app/worker-service)
- [Klasszikus konzolos alkalmazások (.NET)](https://docs.microsoft.com/azure/azure-monitor/app/console) 
- [Java](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started?tabs=maven)
- [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)
- [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs)
- [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

### <a name="codeless-monitoring-and-visual-studio-resource-creation"></a>Kód-és Visual Studio-erőforrások létrehozása

A szolgáltatások (például a Azure Functions és az Azure App Services) kód nélkül történő figyeléséhez először létre kell hoznia a munkaterület-alapú Application Insights erőforrást, majd az adott erőforrásra kell mutatnia a figyelési konfigurációs fázisban.

Habár ezek a szolgáltatások lehetővé tehetik, hogy új Application Insights erőforrást hozzanak létre a saját erőforrás-létrehozási folyamaton belül, a felhasználói felületi beállításokon keresztül létrehozott erőforrások jelenleg a klasszikus Application Insights élményre korlátozódnak.

Ugyanez vonatkozik a Visual Studióban a ASP.NET és a ASP.NET Core Application Insights erőforrás-létrehozási felületére is. Ki kell választania egy meglévő munkaterület-alapú erőforrást a Visual Studio monitoring engedélyezése felhasználói felületén. Ha kiválasztja az új erőforrás létrehozása elemet a Visual Studióban, a rendszer korlátozza a klasszikus Application Insights-erőforrások létrehozását.

## <a name="creating-a-resource-automatically"></a>Erőforrás automatikus létrehozása

### <a name="azure-cli"></a>Azure CLI

Az előzetes verzió Application Insights Azure CLI-parancsokhoz való hozzáféréshez először futtatnia kell a következőt:

```azurecli
 az extension add -n application-insights
```

Ha nem futtatja a `az extension add` parancsot, a következő hibaüzenet jelenik meg:`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Most futtathatja a következőt a Application Insights erőforrás létrehozásához:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--only-show-errors]
                                         [--query-access {Disabled, Enabled}]
                                         [--tags]
                                         [--workspace]
```

#### <a name="example"></a>Példa

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g my_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

A parancshoz tartozó teljes Azure CLI-dokumentációért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create).

### <a name="azure-powershell"></a>Azure PowerShell

A `New-AzApplicationInsights` PowerShell-parancs jelenleg nem támogatja a munkaterület-alapú Application Insights erőforrás létrehozását. Ha munkaterület-alapú erőforrást szeretne létrehozni a PowerShell-lel, használja az alábbi Azure Resource Manager-sablonokat, és telepítse a PowerShell használatával.

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

#### <a name="template-file"></a>Sablonfájl

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>Parameters fájl

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>A társított munkaterület módosítása

A munkaterület-alapú Application Insights erőforrás létrehozása után módosíthatja a társított Log Analytics munkaterületet.

A Application Insights erőforrás ablaktáblán válassza a **Tulajdonságok**  >  **változás munkaterület**  >  **log Analytics munkaterületek** lehetőséget.

## <a name="next-steps"></a>További lépések

* [Metrikák böngészése](../../azure-monitor/platform/metrics-charts.md)
* [Analytics-lekérdezések](../../azure-monitor/app/analytics.md)

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
