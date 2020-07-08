---
title: fájlbefoglalás
description: fájlbefoglalás
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 3788fdb954917f28f64a0dfe035bed4ded0932d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "69642135"
---
A blob trigger számos metaadat-tulajdonságot biztosít. Ezek a tulajdonságok a más kötésekben lévő kötési kifejezések vagy a kódban szereplő paraméterek részeként is használhatók. Ezeknek az értékeknek ugyanazok a szemantikai értékei, mint a [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet) típusa.

|Tulajdonság  |Típus  |Description  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Az indító blob elérési útja.|
|`Uri`|`System.Uri`|A blob URI-ja az elsődleges helyen.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|A blob rendszertulajdonságai. |
|`Metadata` |`IDictionary<string,string>`|A blobhoz tartozó felhasználó által definiált metaadatok.|

A következő C# parancsfájl és JavaScript-példák például az indító blob elérési útját naplózzák, beleértve a tárolót:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```
