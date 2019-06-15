---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: craigshoemaker
ms.service: functions
ms.topic: include
ms.date: 09/25/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: fc5b43dcdee394fea023124171fb42c1a18224dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66131458"
---
Bővítmény csomagjaiból győződjön meg arról, a beállítás keresztül elérhető Azure Functions csapata által közzétett összes kötése a *host.json* fájlt. A helyi fejlesztési, gondoskodjon arról, hogy a legújabb verziójának [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#install-the-azure-functions-core-tools).

Bővítmény csomagok használatához frissítse a *host.json* -fájl a következő bejegyzés `extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

- A `id` tulajdonság hivatkozik, a Microsoft Azure Functions bővítmény csomagjaiból névterét.
- A `version` hivatkozik a csomag verzióját.

Verziók növekmény csomagot a csomag módosításokat csomagként. Főverzió változások történhetnek, csak ha a csomag-csomagok áthelyezése egy főverzió. A `version` tulajdonságot használ a [időköz jelölése verzió tartományok megadása](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards). A Functions futtatókörnyezete mindig választja, a verziótartományon vagy időköz által meghatározott maximális megengedhető verziójára.

Után a bővítmény csomagjaiból hivatkozik a projektben, majd az összes alapértelmezett kötéseket érhetők el az funkciók. A kötések érhető el a [kiterjesztésű csomag](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) vannak:

|Csomag  |Version  |
|---------|---------|
|Microsoft.Azure.WebJobs.Extensions.CosmosDB|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.DurableTask|1.8.0-as|
|Microsoft.Azure.WebJobs.Extensions.EventGrid|2.0.0|
|Microsoft.Azure.WebJobs.Extensions.EventHubs|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SendGrid|3.0.0|
|Microsoft.Azure.WebJobs.Extensions.ServiceBus|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SignalRService|1.0.0|
|Microsoft.Azure.WebJobs.Extensions.Storage|3.0.4|
|Microsoft.Azure.WebJobs.Extensions.Twilio|3.0.0|