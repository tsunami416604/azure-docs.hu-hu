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
ms.openlocfilehash: 512d05b245f1279a977ba61d5b4c4904fc0f6bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77202124"
---
A blob bemeneti kötéshez a következő paramétertípusokat használhatja:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> A `direction` *function.json* vagy `FileAccess.ReadWrite` a C# osztálykönyvtárban "inout" kötést igényel.

Ha megpróbál kötést kötni a Storage SDK egyik típusához, és hibaüzenetet kap, győződjön meg arról, hogy [rendelkezik a megfelelő Storage SDK-verzióra](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

Kötés, `string` `Byte[]` vagy csak akkor ajánlott, ha a blob mérete kicsi, mivel a teljes blob tartalmát betöltődnek a memóriába. Általában célszerű használni, `Stream` vagy `CloudBlockBlob` típus. További információt a cikk [korábbi, Egyidejűségi és memóriahasználat](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) című részében talál.
