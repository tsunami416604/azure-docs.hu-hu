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
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202098"
---
A Blobok írásához a következő típusokat lehet kötni:

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

<sup>1</sup> a *function. JSON* fájlban lévő "in" kötési `direction` szükséges, vagy C# a `FileAccess.Read` egy Class könyvtárban. Azonban használhatja azt a Container objektumot is, amellyel a futtatókörnyezet írási műveleteket hajthat végre, például blobokat tölthet fel a tárolóba.

a <sup>2</sup> "be" `direction` kötést igényel a *function. JSON* fájlban, C# vagy `FileAccess.ReadWrite` egy osztály-függvénytárban.

Ha egy Storage SDK-típushoz próbál kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e a [megfelelő Storage SDK-verzióra](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

Az aszinkron függvények esetében `out` paraméter helyett a visszaadott értéket vagy a `IAsyncCollector` használja.

`string` vagy `Byte[]`hoz való kötés csak akkor ajánlott, ha a blob mérete kicsi, mert a blob teljes tartalma betöltődik a memóriába. Általában érdemes `Stream` vagy `CloudBlockBlob` típust használni. További információ: a jelen cikk [párhuzamosságok és memóriahasználat](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) című szakasza.
