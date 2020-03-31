---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191037"
---
Frissítse *a\\\_\_HttpExample\_\_init .py* függvényt, hogy megfeleljen a `if name:` következő kódnak, hozzáadva a `msg` paramétert a függvénydefinícióhoz és `msg.set(name)` az utasítás alá.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

A `msg` paraméter a. [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest) A `set` metódus egy karakterlánc-üzenetet ír a várólistába, ebben az esetben az URL-lekérdezési karakterlánc függvényének átadott nevet.
