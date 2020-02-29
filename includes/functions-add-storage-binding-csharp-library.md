---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 5e1a2622df0038141dd5cb05237f93d5e33e0bfb
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190915"
---
Egy C# eszközosztály projektben a kötések kötési attribútumokként vannak definiálva a Function metódusban. A függvények által igényelt *function. JSON* fájl ezután automatikusan létrejön ezen attribútumok alapján.

Nyissa meg a *HttpExample.cs* projektfájlt, és adja hozzá a következő paramétert a `Run` metódus definícióhoz:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

A `msg` paraméter egy `ICollector<T>` típus, amely a függvény befejeződése után kimeneti kötésbe írt üzenetek gyűjteményét jelöli. Ebben az esetben a kimenet egy `outqueue`nevű tárolási várólista. A Storage-fiókhoz tartozó kapcsolatok karakterláncát a `StorageAccountAttribute`állítja be. Ez az attribútum azt a beállítást jelzi, amely a Storage-fiókhoz tartozó kapcsolatok sztringjét tartalmazza, és alkalmazható az osztály, a metódus vagy a paraméter szintjén. Ebben az esetben kihagyhatja a `StorageAccountAttribute`, mert már használja az alapértelmezett Storage-fiókot.

A futtatási metódus definíciójának ekkor a következőhöz hasonlóan kell kinéznie:  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::
