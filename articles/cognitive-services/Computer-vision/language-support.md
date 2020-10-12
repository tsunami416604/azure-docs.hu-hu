---
title: Nyelvi támogatás – Computer Vision
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Computer Vision szolgáltatások által támogatott természetes nyelvek listáját tartalmazza; OCR, képelemzés.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 844ef8c09f10e1df888ef4990db22be8705d5c01
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530199"
---
# <a name="language-support-for-computer-vision"></a>A Computer Vision nyelvi támogatása

A Computer Vision egyes funkciói több nyelvet támogatnak; az itt nem említett szolgáltatások csak az angol nyelvet támogatják.

## <a name="optical-character-recognition-ocr"></a>Optikai karakterfelismerés (OCR)

Computer Vision OCR API-jai több nyelvet támogatnak. Nem igénylik a nyelvi kód megadását. További információ: [optikai karakterfelismerés (OCR)](concept-recognizing-text.md) .

|Nyelv| Nyelvkód | OCR API | A v 3.0 olvasása | A v 3.1 nyilvános előzetes verziójának olvasása |
|:-----|:----:|:-----:|:---:|:---:|
|Arab | `ar`|✔ | | |
|Kínai (egyszerűsített) | `zh-Hans`|✔ | |✔ |
|Kínai (hagyományos) | `zh-Hant`|✔ | | |
|Cseh | `cs` |✔ | | |
|Dán | `da` |✔ | | |
|Holland | `nl` |✔ |✔ |✔ |
|Angol | `en` |✔ |✔ |✔ |
|Finn | `fi` |✔ | | |
|Francia | `fr` |✔ |✔ |✔ |
|Német | `de` |✔ |✔ |✔ |
|Görög | `el` |✔ | | |
|Magyar | `hu` |✔ | | |
|Olasz | `it` |✔ |✔ |✔ |
|Japán | `ja` |✔ | |✔ |
|Koreai | `ko` |✔ | | |
|Norvég | `nb` |✔ | | |
|Lengyel | `pl` |✔ | | |
|Portugál | `pt` |✔ |✔ |✔ |
|Román | `ro` |✔ | | |
|Orosz | `ru` |✔ | | |
|Szerb (cirill betűs) | `sr-Cyrl` |✔ | | |
|Szerb (latin betűs) | `sr-Latn` |✔ | | |
|Szlovák | `sk` |✔ | | |
|Spanyol | `es` |✔ |✔ |✔ |
|Svéd | `sw` |✔ | | |
|Török | `tr` |✔ | | |

## <a name="image-analysis"></a>Képelemzés

Az [elemzés-rendszerkép](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API egyes műveletei más nyelveket is visszaadhatnak a `language` lekérdezési paraméterrel megadottak szerint. A többi művelet angol nyelven adja vissza az eredményeket, függetlenül attól, hogy milyen nyelven van megadva, mások pedig kivételt képeznek a nem támogatott nyelvek esetében. A műveletek a és a `visualFeatures` `details` lekérdezési paraméterekkel vannak megadva. az [áttekintést](overview.md) a képelemzéssel elvégezhető műveletek listájáról tekintheti meg.

|Nyelv | Nyelvkód | Kategóriák | Címkék | Leírás | Felnőtt | Márkák | Szín | Arcok | ImageType | Objektumokat | Hírességek | Arcrészek |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Kínai | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Angol | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japán | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugál | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Spanyol | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg az útmutatóban említett Computer Vision-funkciókkal.

* [Helyi rendszerkép (REST) elemzése](./quickstarts/csharp-analyze.md)
* [Nyomtatott szöveg kinyerése (REST)](./quickstarts/csharp-print-text.md)
