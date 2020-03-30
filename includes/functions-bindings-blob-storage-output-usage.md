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
ms.openlocfilehash: 0c0ab0e62a5d951f0bc0e237f44cf55c5b8e16cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77202098"
---
A blobok írásához a következő típusokhoz köthető:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> "in" `direction` kötést igényel a `FileAccess.Read` *function.json* vagy a C# osztálykönyvtárban. Azonban használhatja a tárolóobjektumot, amely a futásidejű biztosít írási műveletek, például a blobok feltöltése a tárolóba.

<sup>2</sup> "inout" `direction` kötést igényel a `FileAccess.ReadWrite` *function.json* vagy a C# osztálykönyvtárban.

Ha megpróbál kötést kötni a Storage SDK egyik típusához, és hibaüzenetet kap, győződjön meg arról, hogy [rendelkezik a megfelelő Storage SDK-verzióra](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

Az aszinkron függvényekben használja `IAsyncCollector` a visszatérési értéket vagy egy `out` paraméter helyett.

Kötés, `string` `Byte[]` vagy csak akkor ajánlott, ha a blob mérete kicsi, mivel a teljes blob tartalmát betöltődnek a memóriába. Általában célszerű használni, `Stream` vagy `CloudBlockBlob` típus. További információt a cikk [korábbi, Egyidejűségi és memóriahasználat](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) című részében talál.
