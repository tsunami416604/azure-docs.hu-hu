---
title: A Arcfelismerési API-szolgáltatás kibocsátási megjegyzései |} Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Kibocsátási megjegyzések a Arcfelismerési API-szolgáltatás egy korábbi verziók kiadás változások közé tartozik.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 918b3ea5dbaaa44e4eee1a679354c7becffd4686
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349338"
---
# <a name="face-api-release-notes"></a>Arcfelismerési API kibocsátási megjegyzések

Ez a cikk a Microsoft Arcfelismerési API szolgáltatás 1.0-s verziója vonatkozik.

### <a name="release-changes-in-may-2018"></a>Előfordulhat, hogy 2018 kiadás változásai

* Továbbfejlesztett `gender` attribútum is és jelentősen javult `age`, `glasses`, `facialHair`, `hair`, `makeup` attribútumok. Használhatja őket keresztül [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` paraméter. 

* Nagyobb bemeneti kép méretkorlátot 4 MB-6 MB a [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - hozzáadása Arcfelismerési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Arcfelismerési hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup személy - hozzáadása Arcfelismerési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) és [LargePersonGroup személy - hozzáadása Arcfelismerési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2018"></a>Kiadás március 2018 változásai

* A hozzáadott millió szintű tároló: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) és [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). További részletek a [a nagy méretű szolgáltatásának használatáról](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Nagyobb [szembesülhetnek – azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` paramétert [1, 5] számára [1, 100] és az alapértelmezett 10-re.

### <a name="release-changes-in-may-2017"></a>Előfordulhat, hogy 2017 kiadás változásai

* Hozzáadott `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure`, és `noise` az attribútumok [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` paraméter.

* 10e személyek támogatott egy PersonGroup és [szembesülhetnek – azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* A tördelési támogatott [PersonGroup személy - listában](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) a választható paraméterek: `start` és `top`.

* Támogatott egyidejűségi a különböző FaceLists és más személyeknek PersonGroup lapok hozzáadása és törlése.

### <a name="release-changes-in-march-2017"></a>2017. március kiadás változásai
* Hozzáadott `emotion` attribútumnak [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` paraméter.

* Rögzített maga nem lehet észlel, a visszakapott téglalap [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) , `targetFace` a [FaceList - hozzáadása Arcfelismerési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) és [PersonGroup személy - hozzáadása Arcfelismerési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Szigorúan 36 x 36 a 4096 x 4096 képpont között van a észlelhető arcfelismerési mérete rögzített.

### <a name="release-changes-in-november-2016"></a>November 2016 kibocsátási változásai
* Tárolásához további megőrzött lapok használata oldallal Storage – Standard előfizetés hozzáadott [PersonGroup személy - hozzáadása Arcfelismerési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) vagy [FaceList - hozzáadása Arcfelismerési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) azonosító vagy hasonlóság megfelelő. A tárolt képek van szó, 0,5 $ 1000 lapok száma, és ez a számláló értéke arányosítva naponta. Ingyenes szint előfizetések továbbra is legfeljebb 1000 teljes személyek lehet.

### <a name="release-changes-in-october-2016"></a>2016. októberi kiadási változásai
* A hibaüzenet, a "Nincsenek lapot a lemezkép" a "Nincs lapot a lemezkép" targetFace a lapot a módosított [FaceList - hozzáadása Arcfelismerési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) és [PersonGroup személy -Arcfelismerésihozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

### <a name="release-changes-in-july-2016"></a>2016. július kiadás változásai
* Arcfelismerési személy objektum hitelesítés a támogatott [szembesülhetnek - ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Választható hozzáadott `mode` paraméter két működő mód kijelölés engedélyezése: `matchPerson` és `matchFace` a [Arcfelismerési - hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) és az alapértelmezett érték a `matchPerson`.

* Választható hozzáadott `confidenceThreshold` beállítani, hogy lapot tartozik egy személy objektum küszöbértékét felhasználó paraméter [szembesülhetnek – azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Választható hozzáadott `start` és `top` paraméterek a [PersonGroup - lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) engedélyezéséhez adja meg a kezdési pont és az összes PersonGroups listájához.

### <a name="v10-changes-from-v0"></a>V0-ről változik 1.0-s verzió
* A szolgáltatás legfelső szintű végpont frissítése ```https://westus.api.cognitive.microsoft.com/face/v0/``` való ```https://westus.api.cognitive.microsoft.com/face/v1.0/```. Alkalmazott módosítások: [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [szembesülhetnek – azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Arcfelismerési - hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) és [szembesülhetnek - csoport](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Módosítsa a minimális észlelhető arcfelismerési méretét és 36 x 36 képpont. Lapok 36 x 36 képpontnál kisebb nem fogja észlelni.

* Elavult Arcfelismerési V0 PersonGroup és személy adatait. Ezek az adatok nem érhető el a tapasztalt 1.0-s verziójú szolgáltatással.

* Elavult V0 végpontja Arcfelismerési API: 2016. június 30.
