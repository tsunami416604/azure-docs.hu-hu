---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 6a359cdd44cc0c0cfbd93bd23b69a67a641c7fbb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "80673295"
---
## <a name="update-the-tests"></a>A tesztek frissítése

Mivel az archetípus is létrehoz egy tesztet, frissítenie kell ezeket a teszteket az új paraméter a `msg` `run` metódus aláírásában való kezeléséhez.  

Keresse meg a kód helyét az _src/test/Java_területen, nyissa meg a *function. Java* projektfájlt, és cserélje le a kód sorát a `//Invoke` következő kóddal.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::