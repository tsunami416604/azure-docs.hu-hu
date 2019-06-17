---
title: Az Azure Functions támogatott nyelvek
description: Ismerje meg, mely nyelveket támogatja (elérhetővé tétel GA), és amelyeket a kísérleti vagy előzetes verzióban érhető el.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/02/2018
ms.author: glenga
ms.openlocfilehash: 8839fc0fb9f19a1cfa95a4191213503dba7602c6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148825"
---
# <a name="supported-languages-in-azure-functions"></a>Az Azure Functions támogatott nyelvek

Ez a cikk bemutatja a támogatási szintet kínált nyelvet használhatja az Azure Functions használatával.

## <a name="levels-of-support"></a>Támogatási szintek

Támogatási három szintre sorolhatók:

* **Általánosan elérhető (GA)** – teljes körűen támogatott, és éles használatra jóváhagyott.
* **Előzetes verzió** – még nem támogatott, de a várt a későbbiekben elérni a végleges verzió állapota.
* **Kísérleti** – nem támogatott, és előfordulhat, hogy lesz hagyva a jövőben; nem tudunk garanciát végleges Preview-ban és a végleges verzió állapota.

## <a name="languages-in-runtime-1x-and-2x"></a>Nyelveket a futtatókörnyezet 1.x és a 2.x

[Az Azure Functions runtime két verziója](functions-versions.md) érhetők el. Az alábbi táblázat az egyes futtatókörnyezet-verzió támogatott nyelvek.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Kísérleti nyelvek

A kísérleti nyelvek verzióban 1.x nem jól méretezhető, és nem támogatják az összes kötését.

Kísérleti jellegű funkciókhoz, amelyeket Ön támaszkodnak, ne használjon, mivel azok nem hivatalos támogatott. Támogatási esetek kísérleti nyelvek kapcsolatos problémák nem lehet megnyitni. 

A verzió 2.x verziójú futtatókörnyezet kísérleti nyelvek nem támogatja. Újabb támogatott nyelvek csak akkor, amikor a nyelv is támogatott, éles környezetben megjelenik. 

### <a name="language-extensibility"></a>Nyelvi bővíthetőség

A 2.x verziójú futtatókörnyezet arra tervezték, hogy [nyelvi bővíthetőség](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). A JavaScript és Java nyelven a 2.x verziójú futtatókörnyezet a beépített a bővíthetőséget.

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

> [!div class="nextstepaction"]
> [Python](functions-reference-python.md)
