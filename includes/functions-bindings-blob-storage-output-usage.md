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
ms.openlocfilehash: 414381d42c52e7489fbd6aaacf0f079cdd978ebe
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642096"
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

<sup>1</sup> a "in" kötést kötelező `direction` megadni a *function. JSON* fájlban vagy `FileAccess.Read` egy C# Class könyvtárban. Azonban használhatja azt a Container objektumot is, amellyel a futtatókörnyezet írási műveleteket hajthat végre, például blobokat tölthet fel a tárolóba.

<sup>2</sup> a "be `direction` " kötést igényli a *function. JSON* fájlban vagy `FileAccess.ReadWrite` egy C# Class könyvtárban.

Ha egy Storage SDK-típushoz próbál kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e a [megfelelő Storage SDK](#azure-storage-sdk-version-in-functions-1x)-verzióra mutató hivatkozással.

Az aszinkron funkciók, a visszaadott értékének használata vagy `IAsyncCollector` helyett egy `out` paraméter.

A vagy `string` `Byte[]` a alkalmazáshoz való kötés csak akkor ajánlott, ha a blob mérete kicsi, mivel a blob teljes tartalma betöltődik a memóriába. Általában a vagy `Stream` `CloudBlockBlob` a típus használata javasolt. További információ: a jelen cikk [párhuzamosságok és memóriahasználat](#trigger---concurrency-and-memory-usage) című szakasza.