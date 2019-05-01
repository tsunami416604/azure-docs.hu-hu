---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
origin.date: 09/12/2018
ms.date: 10/19/2018
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: 27dc1b1315a8e33b8ac13b34d4a86ad0343388b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731186"
---
A kód egy egyedi függvényalkalmazás a függvények a projekt gyökérmappájában, amely tartalmaz egy gazdagép konfigurációs fájlt, és a egy vagy több almappában található. Minden almappa külön funkció, ahogy az alábbi ábrázolás kódot tartalmazza:

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

A verzió 2.x verzióját a Functions-futtatókörnyezet, a függvényalkalmazás a függvények meg kell osztani a azonos nyelv feldolgozó.  

A [host.json](../articles/azure-functions/functions-host-json.md) fájl, amely tartalmazza az egyes futtatókörnyezet-specifikus konfigurációkat, szerepel a gyökérmappában található azon a függvényalkalmazást. A `bin` mappa csomagokat és a tár által az függvényalkalmazás szükséges egyéb fájlokat a tartalmaz. Tekintse meg a függvényalkalmazás-projekt nyelvspecifikus követelményei:

- [C# osztálytár (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
- [C# script (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
- [F#parancsfájl](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
- [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
- [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)


<!-- ms.date: 10/19/2018 -->

