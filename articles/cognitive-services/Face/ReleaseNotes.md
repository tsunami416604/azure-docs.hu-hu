---
title: Kibocsátási megjegyzések – Face Service
titleSuffix: Azure Cognitive Services
description: A Face szolgáltatás kibocsátási megjegyzései közé tartozik a különböző verziók kiadási változásainak előzményei.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: yluiu
ms.openlocfilehash: 767c9dec373a2bda806d75d602b194edde98c6b5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "76165869"
---
# <a name="face-release-notes"></a>Face kibocsátási megjegyzések

Ez a cikk a Face Service 1,0-es verziójára vonatkozik.

### <a name="release-changes-in-june-2019"></a>A kiadás módosításai 2019 júniusában

* Új Arcfelismerés-modell lett hozzáadva, amely nagyobb pontossággal rendelkezik a kis-és az oldalsó nézetekben, a bezárt és a homályos arcokon. Használja a [Face-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), a [FaceList-Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [a LargeFaceList-Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), a [PersonGroup person – Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) és a [LargePersonGroup person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) hozzáadására, és adja meg az új arc észlelési modell nevét `detection_02` a `detectionModel` paraméterben. További információ az [észlelési modell megadásáról](Face-API-How-to-Topics/specify-detection-model.md).

### <a name="release-changes-in-april-2019"></a>A kiadás változásai április 2019-ától

* A `age` és `headPose` attribútumok jobb általános pontossága. Az `headPose` attribútum a most engedélyezett `pitch` értékkel is frissül. Ezeket az attribútumokat az `returnFaceAttributes` [arc-észlelési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` paraméter paraméterében adhatja meg. 

* Továbbfejlesztett [Arcfelismerés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)Face, [LargeFaceList – Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup person – Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) és [LargePersonGroup személy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)hozzáadása

### <a name="release-changes-in-march-2019"></a>Kiadási változások a 2019 márciusában

* Nagyobb pontossággal bővült egy új Arcfelismerés modell. Használja a [Face-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), a [FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), a [LargeFaceList-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), a [PersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) és a [LargePersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) paranccsal az új Arcfelismerés modell nevének `recognition_02` megadásával `recognitionModel` a paraméterben. További információ a [felismerési modell megadásáról](Face-API-How-to-Topics/specify-recognition-model.md).

### <a name="release-changes-in-january-2019"></a>Kiadási változások a januári 2019

* Pillanatkép-szolgáltatás hozzáadva az adatáttelepítés támogatásához az előfizetések között: [Pillanatkép](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). További információ a [Face-adatok áttelepítéséről egy másik Face-előfizetésre](Face-API-How-to-Topics/how-to-migrate-face-data.md).

### <a name="release-changes-in-october-2018"></a>Kiadási változások a 2018 októberében

* A `status` `lastActionDateTime` `lastSuccessfulTrainingDateTime` [PersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) [LargeFaceList - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf) [LargePersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5), a, a és a PersonGroup kifinomult leírása – betanítási állapot, LargePersonGroup-betanítási állapot és LargeFaceList-betanítási állapot. `createdDateTime`

### <a name="release-changes-in-may-2018"></a>Kiadási változások 2018 májusában

* A `gender` továbbfejlesztett attribútum jelentős és továbbfejlesztett `age`, `glasses` `facialHair` `hair`,,, `makeup` attribútumok. Használja őket a [Face-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` paraméterrel. 

* Megnövelt képfájlok maximális mérete 4 MB-ról 6 MB-ra a [Face-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), a [FaceList – Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), a [LargeFaceList-Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), a [PersonGroup person hozzáadásával – Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) és [LargePersonGroup személy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)hozzáadása.

### <a name="release-changes-in-march-2018"></a>Kiadási változások a 2018 márciusában

* Hozzáadott millió léptékű tároló: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) és [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). További részletek a [nagyméretű szolgáltatások használatáról](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Megnövelt [arc-azonosító](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` paraméter [1, 5] és [1, 100] között, és az alapértelmezett érték 10.

### <a name="release-changes-in-may-2017"></a>Kiadási változások 2017 májusában

* `accessory` `returnFaceAttributes` `noise` `hair` `makeup` [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) A ( `exposure`z),,,,, és attribútumok a Face-Detect paraméterben lettek hozzáadva. `occlusion` `blur`

* Támogatott 10K-PersonGroup és [-Azonosítás](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Támogatott tördelés a [PersonGroup személyek számára –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) választható paraméterekkel: `start` és. `top`

* Támogatott Egyidejűség az arcok hozzáadásával/törlésével szemben a különböző FaceLists és a PersonGroup lévő különböző személyekkel.

### <a name="release-changes-in-march-2017"></a>Kiadási változások a 2017 márciusában
* Attribútum `emotion` hozzáadva a [Face-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` paraméterben.

* A rendszer nem tudja felderíteni az arc észlelését a Face [-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `targetFace` helyett a [FaceList-Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) és a [PersonGroup személy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)hozzáadásával.

* Javítva kell a észlelhető méretet, hogy a rendszer szigorúan 36x36 és 4096x4096 képpontok között legyen.

### <a name="release-changes-in-november-2016"></a>Kiadási változások a 2016 novemberében
* Arcképtárolás standard előfizetés hozzáadva a további megőrzött arcok tárolásához, ha [PersonGroup-személyt használ – arc](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) -vagy [FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) hozzáadása az azonosításhoz vagy a hasonlóság egyeztetéséhez. A tárolt képek díja 1000 arconként 0,5 dollár és a díjszabás napi lebontású. Az ingyenes szintű előfizetések továbbra is a 1 000 összes személyre korlátozódnak.

### <a name="release-changes-in-october-2016"></a>Kiadási változások a 2016 októberében
* A targetFace egynél több arc hibaüzenete módosult: "a képen egynél több arc szerepel" a (z) "to" képhez több arc található a [FaceList – Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) és [PersonGroup személy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)hozzáadása – Face hozzáadása.

### <a name="release-changes-in-july-2016"></a>Kiadási változások a 2016 júliusában
* A személyre kiterjedő objektum hitelesítése támogatott a [Face-ellenőrzés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)során.

* `mode` Opcionális paraméter hozzáadva, amely lehetővé teszi két működési `matchPerson` mód `matchFace` kijelölését: és a [Face-Find hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) és alapértelmezett értéke `matchPerson`.

* Nem kötelező `confidenceThreshold` paraméter a felhasználó számára, amely azt határozza meg, hogy az adott arc egy személy objektumhoz tartozik-e a [Face-IDENTIFY](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)elemben.

* Hozzáadva `start` opcionális `top` és paraméterek a [PersonGroup-listához](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) annak engedélyezéséhez, hogy a felhasználó megadja a kezdőpontot és a teljes PersonGroups-számot.

### <a name="v10-changes-from-v0"></a>V 1.0 változások a v0-ból
* A ```https://westus.api.cognitive.microsoft.com/face/v0/``` szolgáltatás gyökérszintű végpontjának frissítése ```https://westus.api.cognitive.microsoft.com/face/v1.0/```a verzióról a verzióra. Alkalmazott módosítások: [szembenézés észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Arcfelismerés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), arc-azonosítás, [Face-Find hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) és [Face-Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* A képpontok minimálisan észlelhető mérete a 36x36 képpont értékre módosult. A 36x36 képpontnál kisebb arcok nem észlelhetők.

* A PersonGroup és a személyes adatértékek elavultak a Face v0-ben. Ezek az adatműveletek nem érhetők el a Face V 1.0 szolgáltatással.

* A rendszer a Face API v0-végpontját (2016. június 30-án) elavult.
