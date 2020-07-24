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
ms.openlocfilehash: 4a879c4041fe317955a07eda9dd8a3ef9f542275
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87056036"
---
A következő típusú paramétereket használhatja az indító blobhoz:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

az <sup>1</sup> érték "be" kötést igényel a `direction` C#-beli *function.jsvagy rendszeren* `FileAccess.ReadWrite` .

Ha egy Storage SDK-típushoz próbál kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e a [megfelelő Storage SDK-verzióra](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

Kötés a következőhöz: `string` vagy `Byte[]` csak akkor ajánlott, ha a blob mérete kicsi, mivel a blob teljes tartalma betöltődik a memóriába. Általában a vagy a típus használata javasolt `Stream` `CloudBlockBlob` . További információ: [párhuzamosság és memóriahasználat](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) a cikk későbbi részében.