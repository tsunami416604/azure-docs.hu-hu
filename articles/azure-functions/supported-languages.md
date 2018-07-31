---
title: Az Azure Functions támogatott nyelvek
description: Ismerje meg, mely nyelveket támogatja (elérhetővé tétel GA), és amelyeket a kísérleti vagy előzetes verzióban érhető el.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: glenga
ms.openlocfilehash: 00f291e903948bf43bc997816b6072186cf1f889
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343083"
---
# <a name="supported-languages-in-azure-functions"></a>Az Azure Functions támogatott nyelvek

Ez a cikk bemutatja a támogatási szintet kínált nyelvet használhatja az Azure Functions használatával.

## <a name="levels-of-support"></a>Támogatási szintek

Támogatási három szintre sorolhatók:

* **Általánosan elérhető (GA)** – teljes körűen támogatott, és éles használatra jóváhagyott.
* **Előzetes verzió** – még nem támogatott, de a várt a későbbiekben elérni a végleges verzió állapota.
* **Kísérleti** – nem támogatott, és előfordulhat, hogy lesz hagyva a jövőben; nem tudunk garanciát végleges Preview-ban és a végleges verzió állapota.

## <a name="languages-in-runtime-1x-and-2x"></a>Nyelveket a futtatókörnyezet 1.x és a 2.x

[Az Azure Functions runtime két verziója](functions-versions.md) érhetők el. Az 1.x modul az általánosan elérhető Az egyetlen modul, amely jogosult az éles környezetben. A 2.x verziójú futtatókörnyezet jelenleg előzetes verzióban, így az általa támogatott nyelvek elérhetők előzetes verzióban. Az alábbi táblázat az egyes futtatókörnyezet-verzió támogatott nyelvek.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Kísérleti nyelvek

A kísérleti nyelvek 1.x nem jól méretezhető, és nem támogatják az összes kötését. Ha például Python azért lassú a Functions-futtatókörnyezetben fut *python.exe* az egyes függvény meghívási. És a Python HTTP-kötések támogatja, míg a támogatásikérelem-objektum nem férhet hozzá.

Kísérleti támogatása a PowerShell 4.0-s verziója korlátozva, mivel az a függvényalkalmazásokat a futó virtuális gépeken telepített. Ha szeretné futtatni a PowerShell-parancsfájlok, érdemes lehet [Azure Automation](https://azure.microsoft.com/services/automation/).

A 2.x verziójú futtatókörnyezet kísérleti nyelvek nem támogatja. A 2.x-es hozzáadjuk csak akkor, ha jól skálázható nyelvet, és támogatja a speciális eseményindítók.

Ha szeretné, amelyek csak 1.x elérhető nyelvek egyikével, maradjon 1.x modulban. Azonban ne használjon kísérleti nyelvek, amelyeket Ön támaszkodnak, mivel azok nem hivatalos támogatott. Kérheti, hogy úgy segítenek [GitHub-problémák létrehozásához](https://github.com/Azure/azure-webjobs-sdk-script/issues), de támogatási esetek kísérleti nyelvek problémák nem nyitható meg. 

### <a name="language-extensibility"></a>Nyelvi bővíthetőség

A 2.x verziójú futtatókörnyezet arra tervezték, hogy [nyelvi bővíthetőség](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). A bővíthetőség kell alapulnia első nyelvek között a Java, a 2.x-es előzetes verzióban egyik modell.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure Functions használata a végleges verzió vagy előzetes nyelvek valamelyikével, a következő forrásanyagokban talál:

> [!div class="nextstepaction"]
> [C#](functions-reference-csharp.md)

> [!div class="nextstepaction"]
> [F#](functions-reference-fsharp.md)

> [!div class="nextstepaction"]
> [JavaScript](functions-reference-node.md)

> [!div class="nextstepaction"]
> [Java](functions-reference-java.md)