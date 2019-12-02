---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4460d19de1859a8a3c51d91d418b948b5d3532a6
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666712"
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

A functions futtatókörnyezet 2. x verziójában a Function alkalmazás összes funkciójának ugyanazzal a nyelvi veremmel kell rendelkeznie.  

A [Host. JSON](../articles/azure-functions/functions-host-json.md) fájl futtatókörnyezet-specifikus konfigurációkat tartalmaz, és a Function alkalmazás gyökérkönyvtárában található. A *bin* mappa csomagokat és más, a Function alkalmazás által igényelt függvénytár-fájlokat tartalmaz. Tekintse meg a Function app-projekt nyelvspecifikus követelményeit:

* [C#osztály könyvtára (. csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C#parancsfájl (. CSX)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F#parancsfájl](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
