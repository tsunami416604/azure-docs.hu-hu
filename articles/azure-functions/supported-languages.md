---
title: Támogatott nyelvek a Azure Functions
description: Megtudhatja, hogy mely nyelvek támogatottak (GA), és melyek kísérleti vagy előzetes verziójúak.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 029ea753439dca3093bf214a5adfb6d58a1fe567
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74942256"
---
# <a name="supported-languages-in-azure-functions"></a>Támogatott nyelvek a Azure Functions

Ez a cikk a Azure Functions használatával használható nyelvekhez kínált támogatási szinteket ismerteti.

## <a name="levels-of-support"></a>Támogatási szintek

Három támogatási szint létezik:

* **Általánosan elérhető (GA)** – teljes mértékben támogatott és jóváhagyott éles használatra.
* **Előzetes** verzió – még nem támogatott, de a jövőben várhatóan elérhető lesz a ga állapota.
* **Kísérleti** – nem támogatott, és a jövőben is elhagyható; az előzetes verzió vagy a GA-állapot jótállása nem lehetséges.

## <a name="languages-by-runtime-version"></a>Nyelvek futásidejű verzió szerint 

[A Azure functions futtatókörnyezet három verziója](functions-versions.md) érhető el. Az alábbi táblázat azt mutatja, hogy az egyes futásidejű verziók Milyen nyelveket támogatnak.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Kísérleti nyelvek

Az 1. x verzióban a kísérleti nyelvek nem méretezhetők jól, és nem támogatják az összes kötést.

Ne használjon olyan kísérleti funkciókat, amelyekre támaszkodik, mivel nem rendelkezik hivatalos támogatással a számukra. A kísérleti nyelvekkel kapcsolatos problémák esetén nem szabad megnyitni a támogatási eseteket. 

A későbbi futásidejű verziók nem támogatják a kísérleti nyelveket. Az új nyelvek támogatását csak akkor adja hozzá a rendszer, ha a nyelv az éles környezetben is támogatott. 

### <a name="language-extensibility"></a>Nyelvi bővíthetőség

A 2. x verziótól kezdődően a futtatókörnyezet [nyelvi bővíthetőséget](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility)kínál. A 2. x futtatókörnyezetben található JavaScript-és Java-nyelvek ezzel a bővíthetőséggel vannak felépítve.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a függvények a támogatott nyelveken való fejlesztéséről, tekintse meg a következő forrásokat:

+ [C# Class Library – fejlesztői segédlet](functions-dotnet-class-library.md)
+ [C# parancsfájl-fejlesztői dokumentáció](functions-reference-csharp.md)
+ [Java fejlesztői segédlet](functions-reference-java.md)
+ [JavaScript fejlesztői segédlet](functions-reference-node.md)
+ [A PowerShell fejlesztői referenciája](functions-reference-powershell.md)
+ [Python fejlesztői segédlet](functions-reference-python.md)
+ [Írógéppel – fejlesztői segédlet](functions-reference-node.md#typescript)
