---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: eb54439f89cc2443eeed2d3b63dfbe7fedb4bf17
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "80673447"
---
## <a name="update-the-tests"></a>A tesztek frissítése

Mivel az archetípus is létrehoz egy tesztet, frissítenie kell ezeket a teszteket az új paraméter a `msg` `run` metódus aláírásában való kezeléséhez.  

Keresse meg a kód helyét az _src/test/Java_területen, nyissa meg a *function. Java* projektfájlt, és cserélje le a kód sorát a `//Invoke` következő kóddal.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::
