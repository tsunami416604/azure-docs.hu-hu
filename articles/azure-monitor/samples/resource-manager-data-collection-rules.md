---
title: Resource Manager-sablonok – minták az adatgyűjtés szabályaihoz
description: Példa Azure Resource Manager sablonok létrehozására az adatgyűjtési szabályok és a Azure Monitor virtuális gépek közötti társítások létrehozásához.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: 12e6aa85f7d8c2c4aebe80efc92f771917e6a1d5
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746012"
---
# <a name="resource-manager-template-samples-for-data-collection-rules-in-azure-monitor"></a>Resource Manager-sablonok minták a Azure Monitor adatgyűjtési szabályaihoz
Ez a cikk minta [Azure Resource Manager sablonokat](../../azure-resource-manager/templates/template-syntax.md) tartalmaz a [log Analytics-ügynök](../platform/log-analytics-agent.md) és a [diagnosztikai bővítmény](../platform/diagnostics-extension-overview.md) üzembe helyezéséhez és konfigurálásához a Azure monitor-ben. Mindegyik minta tartalmaz egy sablonfájlt és egy, a sablonhoz adni kívánt minta értékeket tartalmazó paramétereket tartalmazó fájlt.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-association-with-azure-vm"></a>Társítás létrehozása az Azure-beli virtuális géppel

A következő minta telepíti a Azure Monitor ügynököt egy Windows Azure-beli virtuális gépre. Az Azure-beli virtuális gépek és az adatgyűjtési szabályok között társítás jön létre.

### <a name="template-file"></a>Sablonfájl

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('vmName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this virtual machine.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```

## <a name="create-association-ith-azure-arc"></a>Egyesület létrehozása – Azure arc

A következő minta telepíti a Azure Monitor ügynököt egy Windows Azure-beli virtuális gépre. A társítás egy Azure arc-kompatibilis kiszolgáló-számítógép és egy adatgyűjtési szabály között jön létre.

### <a name="template-file"></a>Sablonfájl

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.HybridCompute/machines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('machineName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this Arc server.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```


## <a name="next-steps"></a>További lépések

* [További Azure monitor-sablonok beszerzése](resource-manager-samples.md).
* [További információ a log Analytics-ügynökről](../platform/log-analytics-agent.md).
* [További információ a diagnosztikai bővítményről](../platform/diagnostics-extension-overview.md).
