---
title: Azure-tevékenység naplójának küldése Log Analytics munkaterületre Azure Resource Manager sablon használatával
description: ARM-sablonokkal létrehozhat egy Log Analytics munkaterületet és egy diagnosztikai beállítást, amellyel elküldheti a tevékenység naplóját Azure Monitor naplókba.
ms.subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: b4b8bb991685ce13be89eff26a4442f32cde7206
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85446387"
---
# <a name="send-azure-activity-log-to-log-analytics-workspace-using-azure-resource-manager-template"></a>Azure-tevékenység naplójának küldése Log Analytics munkaterületre Azure Resource Manager sablon használatával
A műveletnapló egy Azure-beli platform-napló, amely betekintést nyújt az előfizetési szintű eseményekre. Ez olyan adatokat tartalmaz, mint amikor egy erőforrás módosul, vagy amikor a virtuális gép elindul. Megtekintheti a tevékenység naplóját a Azure Portal vagy beolvashatja a bejegyzéseket a PowerShell és a parancssori felület használatával. Ez a rövid útmutató bemutatja, hogyan használhatja a ARM-sablonokat egy Log Analytics munkaterület létrehozásához, valamint egy diagnosztikai beállítást, amellyel elküldheti a tevékenység naplóját Azure Monitor naplókba, amelyekkel elemezheti a [napló lekérdezéseit](../log-query/log-query-overview.md) , és más funkciókat is engedélyezhet, például [naplózási riasztásokat](../platform/alerts-log-query.md) és [munkafüzeteket](../platform/workbooks-overview.md). 

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="create-a-log-analytics-workspace"></a>A Log Analytics-munkaterület létrehozása

### <a name="review-the-template"></a>A sablon áttekintése
A következő sablon egy üres Log Analytics munkaterületet hoz létre. Mentse ezt a sablont *CreateWorkspace.jsként*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the workspace."
          }
      },
      "sku": {
          "type": "string",
          "allowedValues": [
            "pergb2018",
            "Free",
            "Standalone",
            "PerNode",
            "Standard",
            "Premium"
            ],
          "defaultValue": "pergb2018",
          "metadata": {
          "description": "Pricing tier: PerGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
          }
        },
        "location": {
          "type": "string",
          "allowedValues": [
          "australiacentral", 
          "australiaeast", 
          "australiasoutheast", 
          "brazilsouth",
          "canadacentral", 
          "centralindia", 
          "centralus", 
          "eastasia", 
          "eastus", 
          "eastus2", 
          "francecentral", 
          "japaneast", 
          "koreacentral", 
          "northcentralus", 
          "northeurope", 
          "southafricanorth", 
          "southcentralus", 
          "southeastasia",
          "switzerlandnorth",
          "switzerlandwest",
          "uksouth", 
          "ukwest", 
          "westcentralus", 
          "westeurope", 
          "westus", 
          "westus2" 
          ],
          "metadata": {
              "description": "Specifies the location for the workspace."
              }
        },
        "retentionInDays": {
          "type": "int",
          "defaultValue": 120,
          "metadata": {
            "description": "Number of days to retain data."
          }
        },
        "resourcePermissions": {
          "type": "bool",
          "defaultValue": true,
          "metadata": {
            "description": "true to use resource or workspace permissions. false to require workspace permissions."
          }
        }
      },
      "resources": [
      {
          "type": "Microsoft.OperationalInsights/workspaces",
          "name": "[parameters('workspaceName')]",
          "apiVersion": "2020-03-01-preview",
          "location": "[parameters('location')]",
          "properties": {
              "sku": {
                  "name": "[parameters('sku')]"
              },
              "retentionInDays": "[parameters('retentionInDays')]",
              "features": {
                  "searchVersion": 1,
                  "legacy": 0,
                  "enableLogAccessUsingOnlyResourcePermissions": "[parameters('resourcePermissions')]"
              }
          }
      }
  ]
}
```

### <a name="deploy-the-template"></a>A sablon üzembe helyezése
A sablont bármely szabványos módszer használatával üzembe helyezheti [egy ARM-sablon üzembe helyezéséhez](/azure-resource-manager/templates/deploy-portal) , például a parancssori felület és a PowerShell használatával. Cserélje le az **erőforráscsoport**, a **workspaceName**és a **hely** minta értékeit a környezetének megfelelő értékekkel. A munkaterület nevének egyedinek kell lennie az összes Azure-előfizetés között.

# <a name="cli"></a>[Parancssori felület](#tab/CLI1)

```azurecli
az login
az deployment group create \
    --name CreateWorkspace \
    --resource-group my-resource-group \
    --template-file CreateWorkspace.json \
    --parameters workspaceName='my-workspace-01' location='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell1)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile CreateWorkspace.json -workspaceName my-workspace-01 -location eastus
```

---

### <a name="verify-the-deployment"></a>A telepítés ellenőrzése
Győződjön meg arról, hogy a munkaterület az alábbi parancsok egyikével lett létrehozva. Cserélje le az **erőforráscsoport** és a **workspaceName** minta értékeit a fent használt értékekre.

# <a name="cli"></a>[Parancssori felület](#tab/CLI2)

```azurecli
az monitor log-analytics workspace show --resource-group my-workspace-01 --workspace-name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell2)

```powershell
Get-AzOperationalInsightsWorkspace -Name my-workspace-01 -ResourceGroupName my-resource-group
```

---

## <a name="create-diagnostic-setting"></a>Diagnosztikai beállítások létrehozása
A következő sablon egy diagnosztikai beállítást hoz létre, amely elküldi a tevékenység naplóját egy Log Analytics munkaterületre. Mentse ezt a sablont *CreateDiagnosticSetting.jsként*.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        }
    },
    "resources": [
        {
          "type": "Microsoft.Insights/diagnosticSettings",
          "apiVersion": "2017-05-01-preview",
          "name": "[parameters('settingName')]",
          "dependsOn": [],
          "properties": {
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "Administrative",
                "enabled": true
              },
              {
                "category": "Alert",
                "enabled": true
              },
              {
                "category": "Autoscale",
                "enabled": true
              },
              {
                "category": "Policy",
                "enabled": true
              },
              {
                "category": "Recommendation",
                "enabled": true
              },
              {
                "category": "ResourceHealth",
                "enabled": true
              },
              {
                "category": "Security",
                "enabled": true
              },
              {
                "category": "ServiceHealth",
                "enabled": true
              }
            ]
          }
        }
    ]
}
```

### <a name="deploy-the-template"></a>A sablon üzembe helyezése
A sablont bármely szabványos módszer használatával üzembe helyezheti [egy ARM-sablon üzembe helyezéséhez](/azure-resource-manager/templates/deploy-portal) , például a parancssori felület és a PowerShell használatával. Cserélje le az **erőforráscsoport**, a **workspaceName**és a **hely** minta értékeit a környezetének megfelelő értékekkel. A munkaterület nevének egyedinek kell lennie az összes Azure-előfizetés között.

# <a name="cli"></a>[Parancssori felület](#tab/CLI3)

```azurecli
az deployment sub create --name CreateDiagnosticSetting --location eastus --template-file CreateDiagnosticSetting.json --parameters settingName='Send Activity log to workspace' workspaceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace-01'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell3)

```powershell
New-AzSubscriptionDeployment -Name CreateDiagnosticSetting -location eastus -TemplateFile CreateDiagnosticSetting.json -settingName="Send Activity log to workspace" -workspaceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace-01"
```
---

### <a name="verify-the-deployment"></a>A telepítés ellenőrzése
Győződjön meg arról, hogy a diagnosztikai beállítás az alábbi parancsok egyikével lett létrehozva. Cserélje le a minta értékeit az előfizetésre, és állítsa a nevet a fent használt értékekre.

> [!NOTE]
> Jelenleg nem lehet beolvasni az előfizetési szint diagnosztikai beállításait a PowerShell használatával.

```azurecli
az monitor diagnostic-settings show --resource '/subscriptions/00000000-0000-0000-0000-000000000000' --name 'Send Activity log to workspace'
```



## <a name="generate-log-data"></a>Naplófájlok előállítása
A rendszer csak az Log Analytics munkaterületre küldi el az új tevékenységeket, ezért olyan műveleteket hajt végre az előfizetésben, amelyek naplózásra kerülnek, például egy virtuális gép elindításához vagy leállításához, illetve egy másik erőforrás létrehozásához vagy módosításához. Előfordulhat, hogy néhány percet várnia kell, hogy a rendszer létrehozza a diagnosztikai beállítást, és hogy először a munkaterületre írja be az adatgyűjtést. A késleltetést követően a rendszer a tevékenység naplójába írt összes eseményt néhány másodpercen belül elküldi a munkaterületre.

## <a name="retrieve-data-with-a-log-query"></a>Az adatlekérdezés naplózási lekérdezéssel
Az adatok munkaterületről való lekéréséhez használja a Azure Portal a Log Analytics használatával. A Azure Portal keresse meg, majd válassza a **figyelő**elemet. 

![Azure Portal](media/quick-collect-activity-log/azure-portal-monitor.png)

A **Azure monitor** menüben válassza a **naplók** lehetőséget. A **példákat tartalmazó lekérdezések** oldalának lezárása. Ha a hatókör nem a létrehozott munkaterületre van beállítva, kattintson a **hatókör kiválasztása** lehetőségre, és keresse meg.

![Log Analytics hatókör](media/quick-collect-activity-log/log-analytics-scope.png)

A lekérdezési ablakban írja be a parancsot, `AzureActivity` majd kattintson a **Futtatás**gombra. Ez egy egyszerű lekérdezés, amely visszaadja a *AzureActivity* tábla összes rekordját, amely a tevékenység naplójából elküldett összes rekordot tartalmazza.

![Egyszerű lekérdezés](media/quick-collect-activity-log/query-01.png)

Bontsa ki az egyik rekordot a részletes tulajdonságainak megtekintéséhez.

![Tulajdonságok kibontása](media/quick-collect-activity-log/expand-properties.png)

Összetettebb lekérdezéseket is kipróbálhat, például `AzureActivity | summarize count() by CategoryValue` a kategória szerint összefoglalt események számát.

![Összetett lekérdezés](media/quick-collect-activity-log/query-02.png)


## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban úgy konfigurálta a tevékenység naplóját, hogy az Log Analytics munkaterületre legyen küldve. Mostantól más adatokat is konfigurálhat a munkaterületre, ahol elemezheti azt a Azure Monitor található [naplók](../log-query/log-query-overview.md) használatával, és olyan funkciókat használhat, mint például a [naplózási riasztások](../platform/alerts-log-query.md) és a [munkafüzetek](../platform/workbooks-overview.md). A következő lépésekkel gyűjtsön [erőforrás-naplókat](../platform/resource-logs.md) az Azure-erőforrásokból, amelyek a tevékenység naplójában lévő adatokat szolgáltatva betekintést nyújtanak az egyes erőforrásokon végrehajtott műveletekre.


> [!div class="nextstepaction"]
> [Erőforrás-naplók összegyűjtése és elemzése Azure Monitor](tutorial-resource-logs.md)