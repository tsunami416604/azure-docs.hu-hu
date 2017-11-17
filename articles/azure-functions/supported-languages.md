---
title: "Az Azure Functions támogatott nyelvek"
description: "Ismerje meg a nyelveket támogatja (GA), és amelyeket a kísérleti vagy a kép."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: tdykstra
ms.openlocfilehash: 5786a206b258cfe7c48f52ead9b5a4cceb64cd5f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="supported-languages-in-azure-functions"></a>Az Azure Functions támogatott nyelvek

Ez a cikk ismerteti a szintű érhető el a nyelv használható az Azure Functions.

## <a name="levels-of-support"></a>Szintű támogatást

Támogatás három szintje van:

* **Általánosan elérhető (GA)** – teljes mértékben támogatott, és az üzemi használatra jóváhagyott.
* **Előzetes** – még nem támogatott, de a jövőben GA állapot elérésére várt.
* **Kísérleti** – nem támogatott, és előfordulhat, hogy elhagyásra kerül a jövőben; végleges preview vagy GA állapot garanciát.

## <a name="languages-in-runtime-1x-and-2x"></a>Futásidejű nyelvek 1.x és a 2.x

[Az Azure Functions futtatókörnyezettel két verziója](functions-versions.md) érhetők el. A 1.x futásidejű rendszere GA A csak futásidejű engedélyezik az éles környezetben is. A 2.x futásidejű jelenleg előzetes, a támogatott nyelvek még csak előzetes verziójúak. Az alábbi táblázat az egyes futásidejű verzióját támogatott nyelvek.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Kísérleti nyelvek

A kísérleti nyelvek 1.x nem jól méretezhető, és nem támogatja az összes kötését. Például Python lassú, mert a Functions futtatókörnyezete fut *python.exe* minden egyes függvény elindításával. És Python támogatja a HTTP-kötések, amíg nem férhet hozzá a request objektumon.

PowerShell kísérleti támogatása korlátozódik a 4.0-s verziója, mert a virtuális gépeken futó alkalmazások függvény telepített. Ha meg szeretné futtatni a PowerShell-parancsfájlok, fontolja meg a [Azure Automation](https://azure.microsoft.com/services/automation/).

A 2.x-futtatókörnyezet nem támogatja a kísérleti nyelveket. A 2.x csak akkor, ha jól méretezés egy nyelv támogatását, és támogatja a speciális eseményindítók felveszi azt.

Ha szeretné a csak a 1.x elérhető nyelvek egyikével, a 1.x Runtime maradnak. De ne használjon kísérleti nyelveket, amelyeket akkor támaszkodjon, amíg nincs hivatalos támogatása. Által segítséget kérhet [GitHub problémák létrehozása](https://github.com/Azure/azure-webjobs-sdk-script/issues), de támogatási eseteket kísérleti nyelvek problémákat nem nyitható meg. 

### <a name="language-extensibility"></a>Nyelvi bővíthetőség

A 2.x futásidejű úgy lett kialakítva [nyelvi bővítési](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). A bővítési alapján első nyelvek között modell Java, amely 2.x megtekintés.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure Functions GA vagy előzetes nyelveinek használatával, lásd a következőket:

> [!div class="nextstepaction"]
> [C#](functions-reference-csharp.md)

> [!div class="nextstepaction"]
> [F#](functions-reference-fsharp.md)

> [!div class="nextstepaction"]
> [JavaScript](functions-reference-node.md)

> [!div class="nextstepaction"]
> [Java](functions-reference-java.md)