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
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642135"
---
A blob trigger számos metaadat-tulajdonságot biztosít. Ezek a tulajdonságok a kötési kifejezésekben való használata más kötések részeként vagy a kód paramétereiben használható. Ezeknek az értékeknek ugyanazok a szemantikai értékei, mint a [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet) típusa.

|Tulajdonság  |Típus  |Leírás  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Az indító blob elérési útja.|
|`Uri`|`System.Uri`|A blob URI-ja az elsődleges helyen.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|A blob rendszertulajdonságai. |
|`Metadata` |`IDictionary<string,string>`|A blobhoz tartozó felhasználó által definiált metaadatok.|

Az alábbi C# szkriptek és JavaScript-példák például az indító blob elérési útját naplózzák, beleértve a tárolót:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```
