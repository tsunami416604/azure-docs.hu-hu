---
title: Kibocsátási megjegyzések – Face API-szolgáltatás
titleSuffix: Azure Cognitive Services
description: Kibocsátási megjegyzések a Face API szolgáltatás különböző verzióinak kibocsátási változások előzményeit tartalmazza.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 40ad849525ab0afb36754012f735a52f6b5d520d
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55297361"
---
# <a name="face-api-release-notes"></a>Face API kibocsátási megjegyzései

Ez a cikk a Face API szolgáltatás 1.0-s verziója vonatkozik.

### <a name="release-changes-in-january-2019"></a>Január 2019 kiadás változásai

* Pillanatkép funkció hozzáadott előfizetések közötti adatok áttelepítésének támogatásához: [Pillanatkép](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/face/snapshot).

### <a name="release-changes-in-october-2018"></a>2018. október kiadás változásai

* Leírását alakítva `status`, `createdDateTime`, `lastActionDateTime`, és `lastSuccessfulTrainingDateTime` a [is lehet PersonGroup - képzési állapotának beolvasása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup – képzési állapotának beolvasása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5), és [ LargeFaceList – Get képzési állapot](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

### <a name="release-changes-in-may-2018"></a>2018. május kiadás változásai

* Továbbfejlesztett `gender` attribútum is és jelentős mértékben továbbfejlesztett `age`, `glasses`, `facialHair`, `hair`, `makeup` attribútumok. Használatukhoz keresztül [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` paraméter. 

* Nagyobb bemeneti kép fájl mérete legfeljebb 4 MB, a 6 MB-ra [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [személy is lehet PersonGroup - hozzáadása Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) és [személy LargePersonGroup – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2018"></a>2018. márciusi kiadás változásai

* A hozzáadott millió méretű tároló: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) és [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). További információk: [a nagy méretű szolgáltatás használata](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Nagyobb [arc - azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` paramétert [1, 5], [1, 100] és az alapértelmezett 10.

### <a name="release-changes-in-may-2017"></a>Kiadás változásai 2017. május

* Hozzáadott `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure`, és `noise` az attribútumok [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` paraméter.

* Támogatja a 10 ezer személyek egy is lehet PersonGroup és [arc - azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Támogatott a tördelés [személy is lehet PersonGroup - listában](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) a választható paraméterek: `start` és `top`.

* Támogatott egyidejűségi az arcok különböző Facelist és az is lehet PersonGroup más személyek hozzáadásával és eltávolításával.

### <a name="release-changes-in-march-2017"></a>2017. márciusi kiadás változásai
* Hozzáadott `emotion` attribútum [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` paraméter.

* Kijavítva a face nem lehet észlel, a téglalap által visszaadott [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) , `targetFace` a [FaceList – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) és [is lehet PersonGroup személy – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Kijavítva a észlelhető oldallal méretét róla, hogy szigorúan 36 x 36, 4096 x 4096 képpont között.

### <a name="release-changes-in-november-2016"></a>2016. November kiadás változásai
* Face Storage Standard előfizetésre megőrzött képeket tárolhat további használatakor hozzáadott [is lehet PersonGroup személy – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) vagy [FaceList – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) objectet vagy hasonlóság megfelelő. A tárolt képek díja 1000 arconként 0,5 dollár és a díjszabás napi lebontású. Ingyenes szint előfizetések legfeljebb 1000 teljes személyek továbbra is.

### <a name="release-changes-in-october-2016"></a>2016. október kiadás változásai
* A hibaüzenet több felületen a "Tartoznak-e egynél több arcok a képen" "Van egynél több arcok a képen" a targetFace a módosított [FaceList – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) és [is lehet PersonGroup személy -Facehozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

### <a name="release-changes-in-july-2016"></a>2016. júliusi kiadás változásai
* Face személy objektum hitelesítés a támogatott [arc - ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Hozzáadott választható `mode` kétféle működő kiválasztásának engedélyezése a paraméter: `matchPerson` és `matchFace` a [arc - hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) és az alapértelmezett érték a `matchPerson`.

* Hozzáadott választható `confidenceThreshold` paraméterrel megadhatja azt a küszöbértéket, hogy egy ARC kihez tartozik egy személy objektumot a felhasználó [arc - azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Hozzáadott választható `start` és `top` paramétereket lévő [is lehet PersonGroup - lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) engedélyezéséhez adja meg a kezdő pont és az összes Persongroup listájához.

### <a name="v10-changes-from-v0"></a>A V0 változik 1.0-s verzió
* Frissítve a legfelső szintű szolgáltatásvégpont ```https://westus.api.cognitive.microsoft.com/face/v0/``` való ```https://westus.api.cognitive.microsoft.com/face/v1.0/```. Változások a alkalmazni: [Face – észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [arc - azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [arc – keresés hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) és [arc - csoport](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* A minimális észlelhető oldallal mérete frissítve 36 x 36 képpont. 36 x 36 képpontnál kisebb arcok észlelése nem történik meg.

* Elavult Face V0 is lehet PersonGroup és személy adatait. Ezek az adatok nem érhető el, a Face 1.0-s verziójú szolgáltatással.

* Elavult V0 végpontja Face API: 2016. június 30.
