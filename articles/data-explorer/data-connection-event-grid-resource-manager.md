---
title: Eseményrácsos adatkapcsolat létrehozása az Azure Data Explorer számára az Azure Resource Manager-sablon használatával
description: Ebben a cikkben megtudhatja, hogyan hozhat létre eventgrid-adatkapcsolatot az Azure Data Explorer az Azure Resource Manager-sablon használatával.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/28/2019
ms.openlocfilehash: a2e29b9811ab163642e7f65ded584c4889c199db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74669258"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-azure-resource-manager-template"></a>Eseményrácsos adatkapcsolat létrehozása az Azure Data Explorer számára az Azure Resource Manager-sablon használatával

> [!div class="op_single_selector"]
> * [Portál](ingest-data-event-grid.md)
> * [C #](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager-sablon](data-connection-event-grid-resource-manager.md)


Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Data Explorer az Event Hubs, az IoT Hubs és a blobtárolókba írt blobok betöltését (adatbetöltését) kínálja. Ebben a cikkben hozzon létre egy Event Grid adatkapcsolatot az Azure Data Explorer az Azure Resource Manager sablon használatával.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Fürt és adatbázis](create-cluster-database-portal.md) létrehozása
* [Táblázat és oszlopleképezés](ingest-data-event-grid.md#create-a-target-table-in-azure-data-explorer) létrehozása
* [Eseményközpont létrehozása](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)
* Hozzon létre [egy tárfiókot egy Event Grid-előfizetéssel.](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account)

## <a name="azure-resource-manager-template-for-adding-an-event-grid-data-connection"></a>Az Azure Resource Manager-sablon eseményrács-adatkapcsolat hozzáadásához

A következő példa egy Azure Resource Manager-sablont mutat be az Event Grid-adatkapcsolat hozzáadásához.  A [sablon t szerkesztheti és üzembe helyezheti az Azure Portalon](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) az űrlap használatával.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "namespaces_eventhubns_name": {
            "type": "string",
            "defaultValue": "eventhubns",
            "metadata": {
                "description": "Specifies the Event Hub Namespace name."
            }
        },
        "EventHubs_eventhubdemo_name": {
            "type": "string",
            "defaultValue": "eventhubdemo",
            "metadata": {
                "description": "Specifies the Event Hub name."
            }
        },
        "consumergroup_default_name": {
            "type": "string",
            "defaultValue": "$Default",
            "metadata": {
                "description": "Specifies the consumer group of the Event Hub."
            }
        },
        "StorageAccounts_storagedemo_name": {
            "type": "string",
            "defaultValue": "storagedemo",
            "metadata": {
                "description": "Specifies the storage account name"
            }
        },
        "Clusters_kustocluster_name": {
            "type": "string",
            "defaultValue": "kustocluster",
            "metadata": {
                "description": "Specifies the name of the cluster"
            }
        },
        "databases_kustodb_name": {
            "type": "string",
            "defaultValue": "kustodb",
            "metadata": {
                "description": "Specifies the name of the database"
            }
        },
        "tables_kustotable_name": {
            "type": "string",
            "defaultValue": "kustotable",
            "metadata": {
                "description": "Specifies the name of the table"
            }
        },
        "mapping_kustomapping_name": {
            "type": "string",
            "defaultValue": "kustomapping",
            "metadata": {
                "description": "Specifies the name of the mapping rule"
            }
        },
        "dataformat_type": {
            "type": "string",
            "defaultValue": "csv",
            "metadata": {
                "description": "Specifies the data format"
            }
        },
        "dataconnections_kustodc_name": {
            "type": "string",
            "defaultValue": "kustodc",
            "metadata": {
                "description": "Name of the data connection to create"
            }
        },
        "subscriptionId": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "Specifies the subscriptionId of the resources"
            }
        },
        "resourceGroup": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "metadata": {
                "description": "Specifies the resourceGroup of the resources"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {
    },
    "resources": [{
            "type": "Microsoft.Kusto/Clusters/Databases/DataConnections",
            "apiVersion": "2019-09-07",
            "name": "[concat(parameters('Clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'), '/', parameters('dataconnections_kustodc_name'))]",
            "location": "[parameters('location')]",
            "kind": "EventGrid",
            "properties": {
                "storageAccountResourceId": "[resourceId(parameters('subscriptionId'), parameters('resourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('StorageAccounts_storagedemo_name'))]",
                "eventHubResourceId": "[resourceId(parameters('subscriptionId'), parameters('resourceGroup'), 'Microsoft.EventHub/namespaces/eventhubs', parameters('namespaces_eventhubns_name'), parameters('EventHubs_eventhubdemo_name'))]",
                "consumerGroup": "[parameters('consumergroup_default_name')]",
                "tableName": "[parameters('tables_kustotable_name')]",
                "mappingRuleName": "[parameters('mapping_kustomapping_name')]",
                "dataFormat": "[parameters('dataformat_type')]"
            }
        }
    ]
}
```

[!INCLUDE [data-explorer-clean-resources](../../includes/data-explorer-clean-resources.md)]
