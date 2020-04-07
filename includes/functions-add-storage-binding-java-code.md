---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: f4f109a6da0cb75555f9aadfa2fff31b8aa3107d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673487"
---
Most már használhatja `msg` az új paramétert a kimeneti kötéshez való íráshoz a függvénykódból. Adja hozzá a következő kódsort a sikeres `name` válasz `msg` előtt, hogy hozzáadja a kimeneti kötés értékét.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="33":::

Kimeneti kötés használataesetén nem kell használnia az Azure Storage SDK-kódot a hitelesítéshez, a várólista-referencia beszerzéséhez vagy az adatok írásához. A Functions futásidejű és a várólista kimeneti kötése ezeket a feladatokat elvégezheti.

A `run` módszernek most a következő példához hasonlóan kell kinéznie:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-37":::