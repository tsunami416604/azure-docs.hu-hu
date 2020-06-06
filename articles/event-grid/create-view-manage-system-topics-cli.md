---
title: Azure Event Grid rendszertémakörök létrehozása, megtekintése és kezelése a parancssori felület használatával
description: Ez a cikk bemutatja, hogyan hozhat létre, tekinthet meg és törölhet rendszertémaköröket az Azure CLI használatával.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: spelluru
ms.openlocfilehash: c361e7303f73aee1d2e60bd4dd0b9ed0d0e4746f
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84457462"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>Event Grid rendszertémakörök létrehozása, megtekintése és kezelése az Azure CLI használatával
Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet rendszertémaköröket az Azure CLI használatával. A rendszertémakörök áttekintését itt találja: [rendszertémakörök](system-topics.md).

## <a name="create-a-system-topic"></a>Rendszerbeli témakör létrehozása

- Először hozzon létre egy rendszertémakört egy Azure-forráson, majd hozzon létre egy esemény-előfizetést az adott témakörhöz a következő témakörökben talál:
    - [az eventgrid System-topic Create](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-create)

        ```azurecli
        az eventgrid system-topic create \
            -g myResourceGroup \
            --name systemtopic1 \
            --location westus2 \
            --topic-type microsoft.storage.storageaccounts \
            --source /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/storagegaccountname
        ```
    - [az eventgrid System-topic Event-előfizetés Create](/cli/azure/ext/eventgrid/eventgrid/system-topic/event-subscription?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-event-subscription-create)

        ```azurecli
        az eventgrid system-topic event-subscription create --name es1 \
            -g rg1 --system-topic-name systemtopic1 \
            --endpoint https://contoso.azurewebsites.net/api/f1?code=code         
          ```
- To create a system topic (implicitly) when creating an event subscription for an Azure source, use the [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription?view=azure-cli-latest#ext-eventgrid-az-eventgrid-event-subscription-create) method. Here's an example:
    
    ```azurecli
    storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
    endpoint=https://$sitename.azurewebsites.net/api/updates

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <event_subscription_name> \
      --endpoint $endpoint
    ```
    Részletes útmutatást tartalmazó oktatóanyagért lásd: [előfizetés a Storage-fiókra](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account).

## <a name="view-all-system-topics"></a>Az összes rendszertémakör megtekintése
A következő parancsokkal megtekintheti az összes rendszertémakört és a kiválasztott rendszer részleteit:

- [az eventgrid System-topic List](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-list)

    ```azurecli
    az eventgrid system-topic list   
     ```
- [az eventgrid System-topic show](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-show)

    ```azurecli
    az eventgrid system-topic show -g rg1 -n systemtopic1    
     ```

## <a name="delete-a-system-topic"></a>Rendszer-témakör törlése
Egy rendszertémakör törléséhez használja a következő parancsot: 

- [az eventgrid System-topic delete](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-delete)

    ```azurecli
    az eventgrid system-topic delete -g myResourceGroup --name systemtopic1  
     ```

## <a name="next-steps"></a>Következő lépések
A Azure Event Grid által támogatott rendszertémakörökkel és a témakörökkel kapcsolatos további tudnivalókért tekintse meg [Azure Event Grid szakasz rendszertémaköreit](system-topics.md) . 
