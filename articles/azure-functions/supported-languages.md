---
title: Támogatott nyelvek a Azure Functions
description: Megtudhatja, hogy mely nyelvek támogatottak (GA), és melyek előzetes verzióban érhetők el, és hogyan bővíthetők a függvények fejlesztése más nyelvekre.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 91a6ea886c3828678771b24d69bb7987af1fb105
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83994901"
---
# <a name="supported-languages-in-azure-functions"></a>Támogatott nyelvek a Azure Functions

Ez a cikk a Azure Functions használatával használható nyelvekhez kínált támogatási szinteket ismerteti. Emellett ismerteti a függvények a natív módon nem támogatott nyelvekkel való létrehozására vonatkozó stratégiákat is.

## <a name="levels-of-support"></a>Támogatási szintek

Két támogatási szint létezik:

* **Általánosan elérhető (GA)** – teljes mértékben támogatott és jóváhagyott éles használatra.
* **Előzetes** verzió – még nem támogatott, de a jövőben várhatóan elérhető lesz a ga állapota.

## <a name="languages-by-runtime-version"></a>Nyelvek futásidejű verzió szerint 

[A Azure functions futtatókörnyezet három verziója](functions-versions.md) érhető el. Az alábbi táblázat azt mutatja, hogy az egyes futásidejű verziók Milyen nyelveket támogatnak.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

## <a name="custom-handlers-preview"></a>Egyéni kezelők (előzetes verzió) 

Az egyéni kezelők olyan egyszerű webkiszolgálók, amelyek a Azure Functions gazdagépről kapnak eseményeket. Minden olyan nyelv, amely támogatja a HTTP primitíveket, egyéni kezelőt valósíthat meg. Ez azt jelenti, hogy az egyéni kezelők a nem hivatalosan támogatott nyelveken is létrehozhatnak függvényeket. További információ: [Azure functions egyéni kezelők (előzetes verzió)](functions-custom-handlers.md).

## <a name="language-extensibility"></a>Nyelvi bővíthetőség

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
