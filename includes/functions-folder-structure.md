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
ms.openlocfilehash: 2808264b4641bda49a53677ebe216a3b53b7d0d9
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293618"
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

* [C# osztálytár (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C# script (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F #-szkript](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)



