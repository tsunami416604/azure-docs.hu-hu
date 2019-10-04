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
ms.openlocfilehash: 557e34e8a6bddd36a92b0e212bda3609baa14407
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642161"
---
A következő típusú paramétereket használhatja az indító blobhoz:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* A POCO szerializálható JSON-ként
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

az <sup>1</sup> érték "be" `direction` kötést igényel a *function. JSON* fájlban vagy `FileAccess.ReadWrite` egy C# Class könyvtárban.

Ha egy Storage SDK-típushoz próbál kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e a [megfelelő Storage SDK](#azure-storage-sdk-version-in-functions-1x)-verzióra mutató hivatkozással.

`string` A(,)vagyaPocotípusúkötéscsakakkorajánlott,haablobméretekicsi,mertablobteljestartalmabetöltődik`Byte[]`a memóriába. Általában a vagy `Stream` `CloudBlockBlob` a típus használata javasolt. További információ: [párhuzamosság és memóriahasználat](#trigger---concurrency-and-memory-usage) a cikk későbbi részében.