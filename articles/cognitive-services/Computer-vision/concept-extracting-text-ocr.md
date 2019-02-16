---
title: Az optikai karakterfelismerés (OCR) – Computer Vision szöveg kibontása
titleSuffix: Azure Cognitive Services
description: SMS-t használ a Computer Vision API optikai karakterfelismerés (OCR) kibontása kapcsolatos fogalmakat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: deb73eb9fdd6879a5fbe1fed820bf92b2d627b65
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310440"
---
# <a name="extract-text-with-optical-character-recognition"></a>Az optikai karakterfelismerés szöveg kinyerése

Számítógépes Látástechnológia optikai karakterfelismerés (OCR) funkció szöveges tartalom észleli a képet, és karaktersorozattá alakítja át a meghatározott szöveget. Az eredmény például keresés, orvosi, biztonsági és banki számos célra használhatja. 

Optikai Karakterfelismerés 25 nyelveket támogatja: Arab, kínai (egyszerűsített), kínai (hagyományos), Cseh, dán, holland, angol nyelven, finn, francia, német, görög, magyar, olasz, japán, koreai, norvég, lengyel, portugál, román, spanyol, szerb (cirill betűs és Latin), szlovák, spanyol, Svéd és török. Optikai Karakterfelismerés automatikusan észleli az észlelt szöveg nyelvét.

Ha szükséges, optikai Karakterfelismerés visszaadó forgó eltolás a kép vízszintes tengely kapcsolatos fokban kijavítja a elforgatási szögét a felismert szöveget. Optikai Karakterfelismerés is biztosít a keret koordináták minden szó, az alábbi ábrán látható módon.

![Folyamatban van-e forgatni kép ábrázoló diagram és a szöveg, olvassa el és a előrebocsátott](./Images/vision-overview-ocr.png)

## <a name="image-requirements"></a>A rendszerképre vonatkozó követelmények

Számítógépes Látástechnológia OCR használatával a következő követelményeknek megfelelő rendszerképekből szöveget vonhat ki:

* A képnek JPEG, PNG, GIF vagy BMP formátumúnak kell lennie
* A bemeneti kép mérete 50 x 50 és 4200 x 4200 képpontban kell esnie
* A kép szövegének forgatható szerint bármilyen több 90 fok és a egy kis szöge legfeljebb 40 fok.

## <a name="improving-ocr-accuracy"></a>Optikai Karakterfelismerés pontosságának javítása

A szövegfelismerés pontossága függ a kép minőségétől. Egy pontatlan olvasási oka lehet a a következőket:

* Elmosódott képek.
* Kézzel írt, folyóírásos szöveg.
* Művészi betűstílusok.
* Kis méretű szöveg.
* Bonyolult háttér, árnyékok, a szöveget átfedő fényhatás vagy perspektivikus torzítás.
* Túl nagy méretű vagy hiányzó nagybetűk a szavak elején.
* Felső vagy alsó indexben lévő, vagy áthúzott szöveg.

### <a name="ocr-limitations"></a>Optikai Karakterfelismerés korlátozások

A képek, ahol szövege a következő meghatározó téves részlegesen felismert származhatnak. Bizonyos képek, különösen a fényképek nélkül szöveg, a pontosság sokkal függően változhat kép típusa.

## <a name="next-steps"></a>További lépések

Tekintse meg a [OCR dokumentációja](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) további.
