---
title: Támogatott nyelvek az Azure Functionsben
description: Ismerje meg, hogy mely nyelvek támogatottak (GA), és melyek a kísérleti vagy előzetes verzióban.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 029ea753439dca3093bf214a5adfb6d58a1fe567
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74942256"
---
# <a name="supported-languages-in-azure-functions"></a>Támogatott nyelvek az Azure Functionsben

Ez a cikk ismerteti az Azure Functions szolgáltatással használható nyelvekhez nyújtott támogatási szinteket.

## <a name="levels-of-support"></a>A támogatás szintjei

A támogatásnak három szintje van:

* **Általánosan elérhető (GA)** – Teljes mértékben támogatott és termelési használatra jóváhagyott.
* **Előzetes** verzió – még nem támogatott, de várhatóan a jövőben eléri a GA-állapotot.
* **Kísérleti** - Nem támogatott, és lehet, hogy elhagyott a jövőben; nincs garancia az esetleges előzetes verzióra vagy a GA állapotra.

## <a name="languages-by-runtime-version"></a>Nyelvek futásidejű verzió szerint 

[Az Azure Functions futásidejű](functions-versions.md) három verziója érhető el. Az alábbi táblázat bemutatja, hogy mely nyelvek támogatottak az egyes futásidejű verziókban.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Kísérleti nyelvek

Az 1.x verzió kísérleti nyelvei nem méretezhetők jól, és nem támogatják az összes kötést.

Ne használjon kísérleti funkciókat semmire, amire támaszkodik, mivel nincs hivatalos támogatás számukra. A kísérleti nyelvekkel kapcsolatos problémák esetén nem szabad támogatási eseteket megnyitni. 

A későbbi futásidejű verziók nem támogatják a kísérleti nyelveket. Az új nyelvek támogatása csak akkor kerül hozzáadásra, ha a nyelv támogatott a termelésben. 

### <a name="language-extensibility"></a>Nyelvi bővíthetőség

A 2.x-es verziótól kezdve a futásidejű [nyelvbővíthetőséget](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility)kínál. A 2.x futásidejű JavaScript és Java nyelvek ezzel a bővíthetőséggel készülnek.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni arról, hogyan fejleszthet funkciókat a támogatott nyelveken, olvassa el az alábbi forrásokat:

+ [C# osztálykönyvtár fejlesztői útmutatója](functions-dotnet-class-library.md)
+ [C# parancsfájl fejlesztői hivatkozása](functions-reference-csharp.md)
+ [Java fejlesztői útmutató](functions-reference-java.md)
+ [JavaScript fejlesztői útmutató](functions-reference-node.md)
+ [A PowerShell fejlesztői útmutatója](functions-reference-powershell.md)
+ [Python fejlesztői útmutató](functions-reference-python.md)
+ [TypeScript fejlesztői útmutató](functions-reference-node.md#typescript)
