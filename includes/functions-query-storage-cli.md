---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: ca576290ea511dc54b89ecebef72ca2a42e9169f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329547"
---
### <a name="query-the-storage-queue"></a>A tárolási várólista lekérdezése

A [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) paranccsal megtekintheti a fiókjában lévő tárolási várólistákat, ahogy az alábbi példában is látható:

```azurecli-interactive
az storage queue list --output tsv
```

A parancs kimenete tartalmaz egy `outqueue` nevű várólistát, amely a függvény futtatásakor létrehozott üzenetsor.

Ezután a [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) parancs használatával tekintse meg az ebben a várólistában lévő üzeneteket, az alábbi példában látható módon:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

A visszaadott karakterláncnak meg kell egyeznie a függvény teszteléséhez küldött üzenettel.

> [!NOTE]  
> Az előző példa dekódolja a visszaadott karakterláncot a Base64-ből. Ennek az az oka, hogy a várólista-tárolási kötések az Azure Storage-ban [Base64-karakterláncként](../articles/azure-functions/functions-bindings-storage-queue.md#encoding)írnak és olvashatók.