---
title: Nyelvi támogatás - Computer Vision
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Computer Vision funkciói által támogatott természetes nyelvek listáját tartalmazza; OCR, Szöveg felismerése és olvasása.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: a834c68119340d796f87971912a07fc0524a6d21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220137"
---
# <a name="language-support-for-computer-vision"></a>Nyelvi támogatás a Computer Vision-hez

A Computer Vision egyes funkciói több nyelvet is támogatnak; az itt nem említett funkciók csak az angol nyelvet támogatják.

## <a name="text-recognition"></a>Szövegfelismerés

A Computer Vision számos nyelven képes felismerni a szöveget. Pontosabban az [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) API számos nyelvet támogat, míg az [Olvasási](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) API és a [Szöveg felismerése](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) API csak az angol nyelvet támogatja. A funkcióval és az egyes [API-k](concept-recognizing-text.md) előnyeivel kapcsolatos további információkért lásd: Nyomtatott és kézzel írt szöveg felismerése.

Az OCR automatikusan felismeri a bemeneti anyag nyelvét, így nincs szükség nyelvi kód megadására az API-hívásban. A nyelvkódok azonban mindig a JSON-válasz ban lévő `"language"` csomópont értékeként lesznek visszaadva.

|Nyelv| Nyelvkód | OCR API |
|:-----|:----:|:-----:|
|Arab | `ar`|✔ |
|Kínai (egyszerűsített) | `zh-Hans`|✔ |
|Kínai (hagyományos) | `zh-Hant`|✔ |
|Cseh | `cs` |✔ |
|Dán | `da` |✔ |
|Holland | `nl` |✔ |
|Angol | `en` |✔ |
|Finn | `fi` |✔ |
|Francia | `fr` |✔ |
|Német | `de` |✔ |
|Görög | `el` |✔ |
|Magyar | `hu` |✔ |
|Olasz | `it` |✔ |
|Japán | `ja` |✔ |
|Koreai | `ko` |✔ |
|Norvég | `nb` |✔ |
|Lengyel | `pl` |✔ |
|Portugál | `pt` |✔ |
|Román | `ro` |✔ |
|Orosz | `ru` |✔ |
|Szerb (cirill betűs) | `sr-Cyrl` |✔ |
|Szerb (latin betűs) | `sr-Latn` |✔ |
|Szlovák | `sk` |✔ |
|Spanyol | `es` |✔ |
|Svéd | `sw` |✔ |
|Török | `tr` |✔ |

## <a name="image-analysis"></a>Képelemzés

Az Elemzés [– Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API egyes műveletek más nyelveken `language` is visszaadhatnak eredményeket, a lekérdezési paraméterrel megadva. A többi művelet angol nyelvű eredményt ad vissza, függetlenül attól, hogy milyen nyelven van megadva, míg mások kivételt tesznek a nem támogatott nyelvekre. A műveletek a `visualFeatures` és `details` a lekérdezési paraméterekkel vannak megadva; Tekintse meg az [Áttekintés című témakört](home.md) a képelemzéssel kapcsolatban elvégzett összes művelet ről.

|Nyelv | Nyelvkód | Kategóriák | Címkék | Leírás | Felnőtt | Márkák | Color | Arcok | ImageType (Képtípus) | Objektumok | Hírességek | Nevezetességek |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Kínai | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Angol | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japán | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugál | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Spanyol | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>További lépések

Ismerkedés az ebben az útmutatóban említett Computer Vision funkciókkal.

* [Helyi lemezkép elemzése (REST)](./quickstarts/csharp-analyze.md)
* [Nyomtatott szöveg kibontása (REST)](./quickstarts/csharp-print-text.md)