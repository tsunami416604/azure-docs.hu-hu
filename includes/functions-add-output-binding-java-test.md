---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: eb54439f89cc2443eeed2d3b63dfbe7fedb4bf17
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673447"
---
## <a name="update-the-tests"></a>A tesztek frissítése

Mivel az archetípus teszteket is létrehoz, frissítenie kell ezeket a teszteket, hogy kezelni tudja az új `msg` paramétert a `run` metódus aláírásában.  

Tallózással keresse meg a tesztkód helyét _az src/test/java_alatt, nyissa meg `//Invoke` a *Function.java* projektfájlt, és cserélje le a kódsort a következő kódra.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::
