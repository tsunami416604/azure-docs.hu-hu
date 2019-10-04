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
ms.openlocfilehash: 060a84c06e65441381a0cf74f20fb4a9d7b0f042
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642148"
---
A blob bemeneti kötéséhez a következő paramétereket használhatja:

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

az <sup>1</sup> érték "be" `direction` kötést igényel a *function. JSON* fájlban vagy `FileAccess.ReadWrite` egy C# Class könyvtárban.

Ha egy Storage SDK-típushoz próbál kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e a [megfelelő Storage SDK](#azure-storage-sdk-version-in-functions-1x)-verzióra mutató hivatkozással.

A vagy `string` `Byte[]` a alkalmazáshoz való kötés csak akkor ajánlott, ha a blob mérete kicsi, mivel a blob teljes tartalma betöltődik a memóriába. Általában a vagy `Stream` `CloudBlockBlob` a típus használata javasolt. További információ: a jelen cikk [párhuzamosságok és memóriahasználat](#trigger---concurrency-and-memory-usage) című szakasza.
