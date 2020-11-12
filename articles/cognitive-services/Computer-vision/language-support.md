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
ms.openlocfilehash: b065b36103b69f0601daa1388b45865856543d2b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540518"
---
# <a name="language-support-for-computer-vision"></a>A Computer Vision nyelvi támogatása

A Computer Vision egyes funkciói több nyelvet támogatnak; az itt nem említett szolgáltatások csak az angol nyelvet támogatják.

## <a name="optical-character-recognition-ocr"></a>Optikai karakterfelismerés (OCR)

Computer Vision OCR API-jai több nyelvet támogatnak. Nem igénylik a nyelvi kód megadását. További információ: [optikai karakterfelismerés (OCR)](concept-recognizing-text.md) .

|Nyelv| Nyelvkód | OCR API | Olvasási 3,0 és 3,1 | Olvasás: v 3.2 – előzetes verzió. 1 |
|:-----|:----:|:-----:|:---:|:---:|
|Arab | `ar`|✔ | | |
|Kínai (egyszerűsített) | `zh-Hans`|✔ | |✔ |
|Kínai (hagyományos) | `zh-Hant`|✔ | | |
|cseh | `cs` |✔ | | |
|dán | `da` |✔ | | |
|Holland | `nl` |✔ |✔ |✔ |
|Angol | `en` |✔ |✔ |✔ |
|finn | `fi` |✔ | | |
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
|svéd | `sw` |✔ | | |
|Török | `tr` |✔ | | |

## <a name="image-analysis"></a>Képelemzés

Az [elemzés-rendszerkép](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API egyes műveletei más nyelveket is visszaadhatnak a `language` lekérdezési paraméterrel megadottak szerint. A többi művelet angol nyelven adja vissza az eredményeket, függetlenül attól, hogy milyen nyelven van megadva, mások pedig kivételt képeznek a nem támogatott nyelvek esetében. A műveletek a és a `visualFeatures` `details` lekérdezési paraméterekkel vannak megadva. az [áttekintést](overview.md) a képelemzéssel elvégezhető műveletek listájáról tekintheti meg.

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
