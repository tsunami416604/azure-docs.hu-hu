---
title: Nyelvi támogatás – Computer Vision
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Computer Vision szolgáltatások által támogatott természetes nyelvek listáját tartalmazza; OCR, szövegfelismerés és olvasás.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: a207118af0b07be79c934a9665f47e73c5eecde0
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589193"
---
# <a name="language-support-for-computer-vision"></a>A Computer Vision nyelvi támogatása

A Computer Vision egyes funkciói több nyelvet támogatnak; az itt nem említett szolgáltatások csak az angol nyelvet támogatják.

## <a name="text-recognition"></a>Szövegfelismerés

A Computer Vision számos nyelven képes felismerni a szöveget. Az OCR és az olvasási API-k nem igénylik a nyelvi kód megadását. További információ a funkcióról és az egyes API-k előnyeiről: a [nyomtatott és a kézírásos szöveg felismerése](concept-recognizing-text.md) .

|Nyelv| Nyelvkód | OCR API | API olvasása |
|:-----|:----:|:-----:|:---:|
|Arab | `ar`|✔ | |
|Kínai (egyszerűsített) | `zh-Hans`|✔ | |
|Kínai (hagyományos) | `zh-Hant`|✔ | |
|Cseh | `cs` |✔ | |
|Dán | `da` |✔ | |
|Holland | `nl` |✔ |✔ |
|Angol | `en` |✔ |✔ |
|Finn | `fi` |✔ | |
|Francia | `fr` |✔ |✔ |
|Német | `de` |✔ |✔ |
|Görög | `el` |✔ | |
|Magyar | `hu` |✔ | |
|Olasz | `it` |✔ |✔ |
|Japán | `ja` |✔ | |
|Koreai | `ko` |✔ | |
|Norvég | `nb` |✔ | |
|Lengyel | `pl` |✔ | |
|Portugál | `pt` |✔ |✔ |
|Román | `ro` |✔ | |
|Orosz | `ru` |✔ | |
|Szerb (cirill betűs) | `sr-Cyrl` |✔ | |
|Szerb (latin betűs) | `sr-Latn` |✔ | |
|Szlovák | `sk` |✔ | |
|Spanyol | `es` |✔ |✔ |
|Svéd | `sw` |✔ | |
|Török | `tr` |✔ | |

## <a name="image-analysis"></a>Rendszerkép elemzése

Az [elemzés-rendszerkép](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API egyes műveletei más nyelveket is visszaadhatnak a `language` lekérdezési paraméterrel megadottak szerint. A többi művelet angol nyelven adja vissza az eredményeket, függetlenül attól, hogy milyen nyelven van megadva, mások pedig kivételt képeznek a nem támogatott nyelvek esetében. A műveletek a és a `visualFeatures` `details` lekérdezési paraméterekkel vannak megadva. az [áttekintést](home.md) a képelemzéssel elvégezhető műveletek listájáról tekintheti meg.

|Nyelv | Nyelvkód | Kategóriák | Címkék | Leírás | Felnőtt | Márkák | Szín | Arcok | ImageType | Objektumok | Hírességek | Nevezetességek |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Kínai | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Angol | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japán | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugál | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Spanyol | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>További lépések

Ismerkedjen meg az útmutatóban említett Computer Vision-funkciókkal.

* [Helyi rendszerkép (REST) elemzése](./quickstarts/csharp-analyze.md)
* [Nyomtatott szöveg kinyerése (REST)](./quickstarts/csharp-print-text.md)
