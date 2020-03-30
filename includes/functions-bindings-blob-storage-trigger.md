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
ms.openlocfilehash: fa3888d28c52625684676036def7e2920b77b5ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77202111"
---
Az eseményindító blobhoz a következő paramétertípusokat használhatja:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* JSON-ként szerializálható POCO
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> A `direction` *function.json* vagy `FileAccess.ReadWrite` a C# osztálykönyvtárban "inout" kötést igényel.

Ha megpróbál kötést kötni a Storage SDK egyik típusához, és hibaüzenetet kap, győződjön meg arról, hogy [rendelkezik a megfelelő Storage SDK-verzióra](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

A `string`, `Byte[]`vagy POCO kötés csak akkor ajánlott, ha a blob mérete kicsi, mivel a teljes blob tartalom betöltődik a memóriába. Általában célszerű használni, `Stream` vagy `CloudBlockBlob` típus. További információt a cikk [későbbi részében, az Egyidejűség és a memóriahasználat](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) című témakörben talál.