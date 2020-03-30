---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 3788fdb954917f28f64a0dfe035bed4ded0932d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "69642135"
---
A blob eseményindító számos metaadat-tulajdonságot biztosít. Ezek a tulajdonságok más kötésekben lévő kötési kifejezések részeként vagy a kódban paraméterekként használhatók. Ezek az értékek ugyanazt a szemantikát, mint a [CloudBlob-típus.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet)

|Tulajdonság  |Típus  |Leírás  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Az aktiváló blob elérési útja.|
|`Uri`|`System.Uri`|A blob URI az elsődleges hely.|
|`Properties` |[BlobTulajdonságai](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|A blob rendszertulajdonságai. |
|`Metadata` |`IDictionary<string,string>`|A blob felhasználó által definiált metaadatai.|

Például a következő C# parancsfájl és javaScript példák naplóaz elérési utat az aktiváló blob, beleértve a tárolót:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```
