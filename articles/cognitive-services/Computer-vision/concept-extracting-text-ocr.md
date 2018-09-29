---
title: SMS-t OCR - Computer Vision kibontása
titleSuffix: Azure Cognitive Services
description: SMS-t használ a Computer Vision API optikai karakterfelismerés (OCR) kibontása kapcsolatos fogalmakat.
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 4cbc8ebcdd19aa65a37ffe0f82019a7681f1c6c5
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434874"
---
# <a name="extracting-text-with-ocr"></a>Szöveg kinyerése OCR-rel

Optikai karakterfelismerés (OCR) technológia a Computer Vision szöveges tartalom észleli a képet, és az azonosított szöveg kibontása karaktersorozattá. Az eredmény a Keresés és számos más orvosi, biztonságot és banki is használhatja. A szöveg nyelvét automatikusan felismeri. Az optikai Karakterfelismeréssel időt takaríthat meg, és kényelmes: a felhasználók által, ezáltal a szöveg átírás helyett fényképet készítsenek.

Optikai Karakterfelismerés 25 nyelveket támogatja. Ezek a nyelvek a következők: arab, egyszerűsített kínai, hagyományos kínai, Cseh, dán, holland, angol nyelven, finn, francia, német, görög, magyar, olasz, japán, koreai, norvég, lengyel, portugál, román, spanyol, szerb (cirill betűs és a latin betűs) Szlovák, spanyol, svéd és török.

Ha szükséges, optikai Karakterfelismerés javítja a felismert szöveget fokban kép vízszintes tengely körüli elforgatásának. Optikai Karakterfelismerés biztosít a keret koordináták minden szó, az alábbi ábrán látható módon.

![Optikai Karakterfelismerés – áttekintés](./Images/vision-overview-ocr.png)

## <a name="ocr-requirements"></a>Optikai Karakterfelismerés követelmények

Számítógépes Látástechnológia OCR használatával a következő követelményeknek megfelelő rendszerképekből szöveget vonhat ki:

* A képnek JPEG, PNG, GIF vagy BMP formátumúnak kell lennie
* A bemeneti kép mérete 50 x 50 és 4200 x 4200 képpontban kell esnie
* A lemezkép nem lehet nagyobb, mint 10 Megapixel

A bemeneti kép forgatható szerint bármilyen több 90 fok és a egy kis szöge legfeljebb 40 fok.

## <a name="improving-ocr-accuracy"></a>Optikai Karakterfelismerés pontosságának javítása

Szövegek felismerése pontossága attól függ, hogy a kép minőségét. Egy pontatlan olvasása a következő helyzetekben okozhatja:

* Határozatlan képek.
* Kézzel írt vagy kurzív szöveg.
* Művészi betűstílusok.
* A kis betűméret.
* Összetett hátterek, árnyékok változása, vagy tükröződést szöveg vagy perspektíva torzulást keresztül.
* Hiányzik vagy túl nagy méretű nagybetűk szavak alapjait:
* Dolní index je, apróságok vagy áthúzott szöveg.

### <a name="ocr-limitations"></a>Optikai Karakterfelismerés korlátozások

A fényképek, ahol szövege a következő meghatározó téves részlegesen felismert származhatnak. Az egyes fényképek, különösen nélkül szöveg, fényképeket pontosság sokkal függően változhat a kép típusát.

## <a name="next-steps"></a>További lépések

Tudnivalók a fogalmak [nyomtatott és kézzel írt szöveg felismerése](concept-recognizing-text.md).
