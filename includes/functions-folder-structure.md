---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 08/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3cbe634d862682a5f6b06c2cfc77a4d3b03954f9
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42811559"
---
A legfelső szintű mappában található, a kód egy egyedi függvényalkalmazás a függvények (`wwwroot`), amely tartalmaz egy gazdagép konfigurációs fájlban és a egy vagy több almappákat. Minden almappa tartalmazza a kódot egy külön függvény esetében a következő példához hasonlóan:

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
 | - bin
 | | - mycompiledcsharp.dll
```

A host.json fájl tartalmaz néhány futásidejű-specifikus konfigurációkat, és a gyökérmappában található azon a függvényalkalmazás központot. Rendelkezésre álló beállításokkal kapcsolatos további információkért lásd: a [host.json referencia](../articles/azure-functions/functions-host-json.md).

Minden függvény, amely tartalmaz egy vagy több kódfájlokat, a function.json konfiguráció és egyéb függőségek mappát tartalmaz. Az egy C# hordozhatóosztálytár-projektjének, a lefordított osztály függvénytárfájl (.dll) van telepítve a `bin` almappába.

