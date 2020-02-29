---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191037"
---
Frissítse a *HttpExample\\\_\_init\_\_.* a következő kódot, és adja hozzá a `msg` paramétert a Function definícióhoz, és `msg.set(name)` a `if name:` utasítás alatt.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

A `msg` paraméter a [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest)egy példánya. A `set` metódus egy karakterlánc-üzenetet ír a várólistába, ebben az esetben az URL-lekérdezési karakterlánc függvénynek átadott nevet.
