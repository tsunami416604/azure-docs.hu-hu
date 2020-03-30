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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205743"
---
Egy adott függvényalkalmazás összes függvényének kódja egy gyökérprojekt mappában található, amely egy gazdagép konfigurációs fájlját és egy vagy több almappát tartalmaz. Minden almappa külön függvény kódját tartalmazza. A mappastruktúra a következő ábrán látható:

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

A Functions futásidejű 2.x-es és újabb verziójában a függvényalkalmazás minden függvényének ugyanazt a nyelvi veremben kell megosztania.  

A [host.json](../articles/azure-functions/functions-host-json.md) fájl futásidejű konfigurációkat tartalmaz, és a függvényalkalmazás gyökérmappájában található. A *tárolómappa* olyan csomagokat és egyéb könyvtárfájlokat tartalmaz, amelyeket a függvényalkalmazás igényel. Tekintse meg a függvényalkalmazás-projekt nyelvspecifikus követelményeit:

* [C# osztálykönyvtár (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C#-szkript (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F# parancsfájl](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [Javascript](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [Python](../articles/azure-functions/functions-reference-python.md#folder-structure)
