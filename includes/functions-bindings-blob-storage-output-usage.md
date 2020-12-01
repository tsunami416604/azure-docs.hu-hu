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
ms.openlocfilehash: 09e56395122f3be897d8bfb34b1d51cee034b505
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356227"
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

<sup>1</sup> a "in" kötést igényel a `direction` C#-beli *function.json* vagy a `FileAccess.Read` -ben. Azonban használhatja azt a Container objektumot is, amellyel a futtatókörnyezet írási műveleteket hajthat végre, például blobokat tölthet fel a tárolóba.

<sup>2</sup> a "be" kötést igényel a `direction` C#-beli *function.json* vagy a `FileAccess.ReadWrite` -ben.

Ha egy Storage SDK-típushoz próbál kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e a [megfelelő Storage SDK-verzióra](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

A vagy a alkalmazáshoz való kötés `string` `Byte[]` csak akkor ajánlott, ha a blob mérete kicsi, mivel a blob teljes tartalma betöltődik a memóriába. Általában a vagy a típus használata javasolt `Stream` `CloudBlockBlob` . További információ: a jelen cikk [párhuzamosságok és memóriahasználat](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) című szakasza.
