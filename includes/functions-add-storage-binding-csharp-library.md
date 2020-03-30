---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 5e1a2622df0038141dd5cb05237f93d5e33e0bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190915"
---
C# osztálykönyvtár-projektben a kötések a függvénymetódus kötési attribútumaiként vannak definiálva. A Functions által megkövetelt *function.json* fájl ezután automatikusan létrejön ezen attribútumok alapján.

Nyissa *meg* a HttpExample.cs projektfájlt, `Run` és adja hozzá a következő paramétert a metódusdefinícióhoz:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

A `msg` paraméter `ICollector<T>` egy olyan típus, amely a függvény befejezésekor egy kimeneti kötésbe írt üzenetek gyűjteményét jelöli. Ebben az esetben a kimenet egy `outqueue`tárolási várólista nevű. A Storage fiók kapcsolati karakterláncát `StorageAccountAttribute`a. Ez az attribútum azt a beállítást jelzi, amely a Storage-fiók kapcsolati karakterláncát tartalmazza, és az osztály, a metódus vagy a paraméter szintjén alkalmazható. Ebben az esetben kihagyhatja, `StorageAccountAttribute` mert már használja az alapértelmezett tárfiókot.

A Futtatás metódus definíciójának most a következőkre kell hasonlítania:  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::
