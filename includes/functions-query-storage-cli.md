---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4fe159660421113e0f0ac0586ae7e4a22d5bcda7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77474232"
---
### <a name="query-the-storage-queue"></a>A tárolóvárólista lekérdezése

A [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) parancs segítségével megtekintheti a storage várólisták a fiókjában, mint a következő példában:

```azurecli-interactive
az storage queue list --output tsv
```

A parancs kimenete tartalmaz `outqueue`egy nevű várólistát , amely a függvény futásakor létrehozott várólista.

Ezután a [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) paranccsal tekintse meg a várólistában lévő üzeneteket, ahogy ebben a példában is látható:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

A visszaadott karakterláncnak meg kell egyeznie a függvény tesztelésére küldött üzenettel.

> [!NOTE]  
> Az előző példa dekódolja a visszaadott karakterláncot a base64-ből. Ennek az az oka, hogy a várólista-tárolókötések [alap64 karakterláncként](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)írnak és olvasnak az Azure Storage-ból.