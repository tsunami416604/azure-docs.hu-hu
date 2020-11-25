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
ms.openlocfilehash: f4e6d5fb41769544b7be0f689447364988d0380d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95998835"
---
A blob trigger számos metaadat-tulajdonságot biztosít. Ezek a tulajdonságok a más kötésekben lévő kötési kifejezések vagy a kódban szereplő paraméterek részeként is használhatók. Ezeknek az értékeknek ugyanazok a szemantikai értékei, mint a [CloudBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet) típusa.

|Tulajdonság  |Típus  |Leírás  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Az indító blob elérési útja.|
|`Uri`|`System.Uri`|A blob URI-ja az elsődleges helyen.|
|`Properties` |[BlobProperties](/dotnet/api/microsoft.azure.storage.blob.blobproperties)|A blob rendszertulajdonságai. |
|`Metadata` |`IDictionary<string,string>`|A blobhoz tartozó felhasználó által definiált metaadatok.|

A következő C# parancsfájl és JavaScript-példák például az indító blob elérési útját naplózzák, beleértve a tárolót:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```