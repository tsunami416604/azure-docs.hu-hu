---
title: SMS-t OCR - Computer Vision kibontása
titleSuffix: Azure Cognitive Services
description: SMS-t használ a Computer Vision API optikai karakterfelismerés (OCR) kibontása kapcsolatos fogalmakat.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 07d3f5d365fa5c552ccb61c97532a9931b7e282e
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53583680"
---
# <a name="extracting-text-with-optical-character-recognition"></a>Az optikai karakterfelismerés szöveg kibontása

Optikai karakterfelismerés (OCR) technológia a Computer Vision szöveges tartalom észleli a képet, és az azonosított szöveg kibontása karaktersorozattá. Az eredmény kereséshez és sok más célra, például egészségügyi, biztonsági vagy banki adatként hasznosítható. A szöveg nyelvét automatikusan felismeri. Az optikai karakterfelismeréssel időt takaríthat meg, és használata kényelmes: a szövegek átírása helyett lefényképezheti azt.

Az optikai karakterfelismerés 25 nyelvet támogat. Ezeken a nyelveken a következők: Arab, kínai (egyszerűsített), kínai (hagyományos), Cseh, dán, holland, angol nyelven, finn, francia, német, görög, magyar, olasz, japán, koreai, norvég, lengyel, portugál, román, spanyol, szerb (cirill betűs és Latin), szlovák, spanyol, Svéd és török.

Szükség esetén az OCR a felismert szöveg dőlésszögét a kép vízszintes tengelyéhez igazítja. Optikai Karakterfelismerés biztosít a keret koordináták minden szó, az alábbi ábrán látható módon.

![Folyamatban van-e forgatni kép ábrázoló diagram és a szöveg, olvassa el és a előrebocsátott](./Images/vision-overview-ocr.png)

## <a name="ocr-requirements"></a>Optikai Karakterfelismerés követelmények

Számítógépes Látástechnológia OCR használatával a következő követelményeknek megfelelő rendszerképekből szöveget vonhat ki:

* A képnek JPEG, PNG, GIF vagy BMP formátumúnak kell lennie
* A bemeneti kép mérete 50 x 50 és 4200 x 4200 képpontban kell esnie


A bemeneti kép forgatható szerint bármilyen több 90 fok és a egy kis szöge legfeljebb 40 fok.

## <a name="improving-ocr-accuracy"></a>Optikai Karakterfelismerés pontosságának javítása

A szövegfelismerés pontossága függ a kép minőségétől. Felismerési hibákat okozhatnak az alábbi körülmények:

* Elmosódott képek.
* Kézzel írt, folyóírásos szöveg.
* Művészi betűstílusok.
* Kis méretű szöveg.
* Bonyolult háttér, árnyékok, a szöveget átfedő fényhatás vagy perspektivikus torzítás.
* Túl nagy méretű vagy hiányzó nagybetűk a szavak elején.
* Felső vagy alsó indexben lévő, vagy áthúzott szöveg.

### <a name="ocr-limitations"></a>Optikai Karakterfelismerés korlátozások

A fényképek, ahol szövege a következő meghatározó téves részlegesen felismert származhatnak. Az egyes fényképek, különösen nélkül szöveg, fényképeket pontosság sokkal függően változhat a kép típusát.

## <a name="next-steps"></a>További lépések

Tudnivalók a fogalmak [nyomtatott és kézzel írt szöveg felismerése](concept-recognizing-text.md).
