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
ms.openlocfilehash: 9f74365f3fe935be45fa9c45e5b12c45b97b2f8a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068359"
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

A verzió 2.x verzióját a Functions-futtatókörnyezet, a függvényalkalmazás a függvények az azonos nyelvi modult meg kell osztani.  

A [host.json](../articles/azure-functions/functions-host-json.md) fájl, amely tartalmazza az egyes futtatókörnyezet-specifikus konfigurációkat, szerepel a gyökérmappában található azon a függvényalkalmazást. A `bin` mappa csomagokat és a tár által az függvényalkalmazás szükséges egyéb fájlokat a tartalmaz. Tekintse meg a függvényalkalmazás-projekt nyelvspecifikus követelményei:

* [C# osztálytár (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C# script (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F#parancsfájl](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)



