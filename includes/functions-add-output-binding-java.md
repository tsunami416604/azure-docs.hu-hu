---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: c27f3fea6d2dd8b891220ba06b375b33e6cd950b
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673474"
---
Java projektben a kötések a függvénymetódus kötési jegyzeteiként vannak definiálva. A *function.json* fájl ezután automatikusan generálódik ezen jegyzetek alapján.

Tallózással keresse meg a függvénykód helyét _az src/main/java_alatt, nyissa meg `run` a *Function.java* projektfájlt, és adja hozzá a következő paramétert a metódus definíciójához:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

A `msg` paraméter [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) egy típus, amely a függvény befejezésekor a kimeneti kötéshez üzenetekként írt karakterláncok gyűjteményét jelöli. Ebben az esetben a kimenet egy `outqueue`tárolási várólista nevű. A storage-fiók kapcsolati karakterláncát `connection` a metódus állítja be. Ahelyett, hogy maga a kapcsolati karakterlánc, adja át az alkalmazás beállítást, amely tartalmazza a storage-fiók kapcsolati karakterlánc.

A `run` metódus definíciójának most a következő példához hasonlóan kell kinéznie:  

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-22":::