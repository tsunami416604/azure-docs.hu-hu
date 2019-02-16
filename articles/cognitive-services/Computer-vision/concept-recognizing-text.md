---
title: Nyomtatott, kézzel írt szöveg – a Computer Vision felismerése
titleSuffix: Azure Cognitive Services
description: A Computer Vision API használatával képeken nyomtatott és kézzel írt szöveg felismerése kapcsolatos fogalmakat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 48ce15a11c3e3282535420f3e1bb1915276d70f5
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313177"
---
# <a name="recognizing-printed-and-handwritten-text"></a>Nyomtatott és kézzel írt szöveg felismerése

Számítógépes Látástechnológia képesek észlelni és nyomtatott vagy kézzel írt szöveg kinyerése a különféle objektumokat a különféle felületekkel és hátterek, például a visszaigazolások, poszterek, névjegyek, betűket és hátterekkel képét.

A szöveg szolgáltatás nagyon hasonlít [optikai karakterfelismerés (OCR)](concept-extracting-text-ocr.md), de eltérően optikai Karakterfelismerés, aszinkron, és használja a frissített modelleket.

> [!NOTE]
> Ez a technológia jelenleg előzetes verzióban és csak angol nyelvre érhető el.

## <a name="text-recognition-requirements"></a>Szöveg követelményei

Számítógépes Látástechnológia képesek felismerni a képeken az alábbi követelményeknek eleget tevő nyomtatott és kézzel írt szöveg:

- A kép be kell mutatni, JPEG, PNG vagy BMP formátumban
- A fájlméret nem érheti el a 4 megabájtot (MB)
- A kép mérete 50 x 50 és 4200 x 4200 képpontban kell esnie

## <a name="next-steps"></a>További lépések

Tekintse meg a [ismeri fel a szöveg dokumentációja](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) további.