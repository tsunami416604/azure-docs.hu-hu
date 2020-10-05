---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: c27f3fea6d2dd8b891220ba06b375b33e6cd950b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "80673474"
---
Egy Java-projektben a kötések kötési megjegyzésekként vannak definiálva a Function metódusban. A fájl *function.js* ezután automatikusan létrejön a jegyzetek alapján.

Keresse meg a függvény kódját a _src/Main/Java_területen, nyissa meg a *function. Java* projektfájlt, és adja hozzá a következő paramétert a `run` metódus-definícióhoz:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

A `msg` paraméter egy [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) típus, amely a függvény befejeződése után üzenetként írt karakterláncok gyűjteményét jelöli. Ebben az esetben a kimenet egy nevű tárolási várólista `outqueue` . A Storage-fiókhoz tartozó kapcsolatok karakterláncát a metódus állítja be `connection` . A felhasználói karakterlánc helyett adja meg a Storage-fiók kapcsolatok sztringjét tartalmazó alkalmazásbeállítás értékét.

A `run` metódus definíciójának ekkor az alábbi példához hasonlóan kell kinéznie:  

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-22":::