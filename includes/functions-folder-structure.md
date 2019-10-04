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
ms.openlocfilehash: aad66a91f7de8380ac7e87f0ce8e35ed43cac4a6
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594531"
---
A kód egy egyedi függvényalkalmazás a függvények a projekt gyökérmappájában, amely tartalmaz egy gazdagép konfigurációs fájlt, és a egy vagy több almappában található. Minden egyes külön funkció a kódját tartalmazza. A mappastruktúra a következő ábrázolása látható:

```
FunctionApp
 | - host.json
 | - Myfirstfunction
 | | - function.json
 | | - ...  
 | - mysecondfunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

A verzió 2.x verzióját a Functions-futtatókörnyezet, a függvényalkalmazás a függvények az azonos nyelvi modult meg kell osztani.  

A [host.json](../articles/azure-functions/functions-host-json.md) fájl futtatókörnyezet-specifikus konfigurációkat tartalmaz, és a függvényalkalmazás gyökérmappájában található. A *bin* mappa tartalmazza a csomagok és más függvénytárfájlok, amely a függvényalkalmazáshoz szükség van. Tekintse meg a függvényalkalmazás-projekt nyelvspecifikus követelményei:

* [C# osztálytár (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C# script (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F#parancsfájl](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)



