---
title: Nyelvi támogatás – Computer Vision
titleSuffix: Azure Cognitive Services
description: Számítógépes Látástechnológia funkciók által támogatott természetes nyelvek listáját.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 1a70d1b2ea504d0ccfba925810a2d19d0c7583cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60759605"
---
# <a name="language-support-for-computer-vision"></a>Számítógépes Látástechnológia nyelvi támogatás

Computer Vision az egyes funkciói támogatja több nyelv; bármely itt nem említett szolgáltatások csak az angol nyelvű támogatják.

## <a name="text-recognition"></a>Szövegfelismerés

Számítógépes Látástechnológia felismerje a különböző nyelvű szöveget. Pontosabban a [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) API támogatja a különböző nyelveken, mivel a [olvasási](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) API és [szöveg felismerése](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) API csak angol nyelven támogatja. Lásd: [nyomtatott és kézzel írt szöveg felismerése](concept-recognizing-text.md) ezt a funkciót, és a magánfelhőmodell előnyeit minden API további tájékoztatást.

Optikai Karakterfelismerés automatikusan észleli a nyelvet a bemeneti anyag, így nem kell, ha meg szeretné adni az API-hívás egy nyelvi kódot. Azonban nyelvkódokról mindig visszaadott értéket a `"language"` csomópont a JSON-válaszban.

|Nyelv| Nyelvkód | OPTIKAI KARAKTERFELISMERÉS API |
|:-----|:----:|:-----:|
|arab | `ar`|✔ |
|kínai (egyszerűsített) | `zh-Hans`|✔ |
|kínai (hagyományos) | `zh-Hant`|✔ |
|cseh | `cs` |✔ |
|dán | `da` |✔ |
|holland | `nl` |✔ |
|Angol | `en` |✔ |
|finn | `fi` |✔ |
|francia | `fr` |✔ |
|német | `de` |✔ |
|görög | `el` |✔ |
|magyar | `hu` |✔ |
|olasz | `it` |✔ |
|japán | `ja` |✔ |
|koreai | `ko` |✔ |
|norvég | `nb` |✔ |
|lengyel | `pl` |✔ |
|portugál | `pt` |✔ |
|román | `ro` |✔ |
|orosz | `ru` |✔ |
|szerb (cirill betűs) | `sr-Cyrl` |✔ |
|szerb (latin betűs) | `sr-Latn` |✔ |
|szlovák | `sk` |✔ |
|spanyol | `es` |✔ |
|svéd | `sw` |✔ |
|török | `tr` |✔ |

## <a name="image-analysis"></a>Képelemzés

Az egyes műveletek a [elemzése – kép](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API más nyelven, a megadott adhat vissza eredményt a `language` lekérdezési paraméter. Egyéb műveletek adja vissza az eredményeket angol függetlenül attól, milyen nyelven van megadva, és mások kivételt nem támogatott nyelvekhez. Műveletek vannak megadva, a `visualFeatures` és `details` lekérdezési paramétereket; lásd: a [áttekintése](home.md) képelemzés elvégezhető összes műveletet listáját.

|Nyelv | Nyelvkód | Categories | Címkék | Leírás | Felnőtt tartalom | Márkák | Szín | Arcok | Képtípus | Objektumok | Hírességek | Arcrész |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|kínai | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Angol | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|japán | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|portugál | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|spanyol | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>További lépések

Ismerkedjen meg a jelen útmutatóban említett számítógépes Látástechnológiai funkciót.

* [Elemezheti a helyi rendszerképet (REST)](./quickstarts/csharp-analyze.md)
* [(REST) nyomtatott szöveg kinyerése](./quickstarts/csharp-print-text.md)