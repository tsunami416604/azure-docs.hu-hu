---
title: fájl belefoglalása
description: fájl belefoglalása
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5f5f682c8c31c17f1a645bcdacdc78f32bd5c001
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77205743"
---
Egy adott Function alkalmazás összes függvényének kódja egy olyan legfelső szintű Project mappában található, amely egy gazdagép konfigurációs fájlját és egy vagy több almappát tartalmaz. Minden almappa egy külön függvény kódját tartalmazza. A mappa szerkezete a következő ábrázolásban látható:

```
FunctionApp
 | - host.json
 | - MyFirstFunction
 | | - function.json
 | | - ...  
 | - MySecondFunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

A functions alkalmazás 2. x és újabb verziójában a Function app összes funkciójának ugyanazzal a nyelvi veremmel kell rendelkeznie.  

A [Host. JSON](../articles/azure-functions/functions-host-json.md) fájl futtatókörnyezet-specifikus konfigurációkat tartalmaz, és a Function alkalmazás gyökérkönyvtárában található. A *bin* mappa csomagokat és más, a Function alkalmazás által igényelt függvénytár-fájlokat tartalmaz. Tekintse meg a Function app-projekt nyelvspecifikus követelményeit:

* [C#osztály könyvtára (. csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C#parancsfájl (. CSX)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F#parancsfájl](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [Python](../articles/azure-functions/functions-reference-python.md#folder-structure)
