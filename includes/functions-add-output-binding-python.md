---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78191037"
---
Frissítse *az\\\_init\_. HttpExample-t, hogy az megfeleljen a következő kódnak, adja hozzá a paramétert a függvény definíciójában és az utasításban.\_\_* `msg` `msg.set(name)` `if name:`

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

A `msg` paraméter a (z [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest)) példánya. A `set` metódus egy karakterlánc-üzenetet ír a várólistába, ebben az esetben az URL-lekérdezési karakterláncban szereplő függvénynek átadott nevet.
