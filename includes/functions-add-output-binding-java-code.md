---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 3705f58a37c109ebe0b774603c60e246fc174f25
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673251"
---
Most már használhatja `msg` az új paramétert a kimeneti kötéshez való íráshoz a függvénykódból. Adja hozzá a következő kódsort a sikeres `name` válasz `msg` előtt, hogy hozzáadja a kimeneti kötés értékét.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="34":::

Kimeneti kötés használataesetén nem kell használnia az Azure Storage SDK-kódot a hitelesítéshez, a várólista-referencia beszerzéséhez vagy az adatok írásához. A Functions futásidejű és a várólista kimeneti kötése ezeket a feladatokat elvégezheti.

A `run` módszernek most a következő példához hasonlóan kell kinéznie:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="17-38":::