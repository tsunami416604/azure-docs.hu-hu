---
title: Kibocsátási megjegyzések - Arcszolgáltatás
titleSuffix: Azure Cognitive Services
description: A Face szolgáltatás kiadási feljegyzései tartalmazzák a különböző verziók kiadási módosításainak előzményeit.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: yluiu
ms.openlocfilehash: 767c9dec373a2bda806d75d602b194edde98c6b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76165869"
---
# <a name="face-release-notes"></a>Arckiadási megjegyzések

Ez a cikk a Face service 1.0-s verziójára vonatkozik.

### <a name="release-changes-in-june-2019"></a>A kiadások módosításai 2019 júniusában

* Hozzáadott egy új arcfelismerő modell továbbfejlesztett pontossággal a kis, oldalsó nézet, eldugult és elmosódott arcokon. Használja [a Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) és [LargePersonGroup Person - Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) az új arcfelismerő modell nevének `detection_02` megadásával a paraméterben. `detectionModel` További részletek: [Észlelési modell megadása.](Face-API-How-to-Topics/specify-detection-model.md)

### <a name="release-changes-in-april-2019"></a>A kiadások módosításai 2019 áprilisában

* Javítottáltalános pontossága és `age` `headPose` attribútumok. Az `headPose` attribútum is frissül `pitch` az érték engedélyezve van most. Használja ezeket az `returnFaceAttributes` attribútumokat a Face - [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` paraméter paraméterében. 

* Az Arc jobb sebessége [- Arcfelismerés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Face hozzáadása,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) [LargeFaceList - Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) és [LargePersonGroup Person hozzáadása - Arc hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2019"></a>A kiadás változásai 2019 márciusában

* Hozzáadott egy új arcfelismerő modellt, nagyobb pontossággal. Használja [a Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) és [LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) segítségével adja meg az új arcfelismerő modell nevét `recognition_02` a paraméterben. `recognitionModel` További részletek A [felismerési modell megadása.](Face-API-How-to-Topics/specify-recognition-model.md)

### <a name="release-changes-in-january-2019"></a>A kiadás változásai 2019 januárjában

* Snapshot funkcióval támogatja az előfizetések közötti adatáttelepítést: [Pillanatkép.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get) További részletek: [Az arcadatok áttelepítése egy másik Face-előfizetésbe.](Face-API-How-to-Topics/how-to-migrate-face-data.md)

### <a name="release-changes-in-october-2018"></a>A kiadások módosításai 2018 októberében

* A `status`, `createdDateTime`, `lastActionDateTime`és `lastSuccessfulTrainingDateTime` a [PersonGroup – Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5)és A [LargeFaceList – Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf)( Képzési állapot ) kifinomult leírása .

### <a name="release-changes-in-may-2018"></a>A kiadások módosításai 2018 májusában

* A `gender` továbbfejlesztett attribútum jelentősen és `age` `glasses`továbbfejlesztett `hair` `makeup` , , `facialHair`, attribútumok. Használja őket [a Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` paraméteren keresztül. 

* A bemeneti képfájl méretének növelése 4 MB-ról 6 MB-ra [korlátozható az arcon - Arcfelismerés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Face hozzáadása,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) [LargeFaceList - Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) és [LargePersonGroup Person hozzáadása - Arc hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2018"></a>A 2018 márciusi kiadási változások

* Milliós méretű tároló hozzáadása: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) és [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). További részletek a [Nagyméretű funkció használata című részben.](Face-API-How-to-Topics/how-to-use-large-scale.md)

* Növelt [arc - Azonosítsa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` a paramétert [1, 5] és [1, 100] és alapértelmezett 10-re.

### <a name="release-changes-in-may-2017"></a>2017 májusában megjelent a kiadás módosítása

* `hair`Hozzáadva `makeup` `accessory`, `occlusion` `blur`, `exposure`, `noise` , és attribútumok a [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` paraméterben.

* Támogatott 10K személyek egy PersonGroup és [face - Azonosítás](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Támogatott tördelés a [PersonGroup Person - List-ben](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) választható paraméterekkel: `start` és `top`.

* Támogatott egyidejűség az arcok hozzáadásában/törlésében különböző facelisták és különböző személyek ellen a PersonGroup csoportban.

### <a name="release-changes-in-march-2017"></a>2017 márciusában megjelent a kiadás módosítása
* Hozzáadott `emotion` attribútum a [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` paraméterben.

* Javítva, hogy az arcot nem lehetett újra észlelni a [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) mint `targetFace` a [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) és [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Javítva a kimutatható arcméret, hogy megbizonyosodjon arról, hogy szigorúan 36x36 és 4096x4096 pixel között van.

### <a name="release-changes-in-november-2016"></a>A kiadások módosításai 2016 novemberében
* Face Storage Standard előfizetés hozzáadása további megőrzött arcok tárolásához [a PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) vagy [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) azonosításhoz vagy hasonlóságegyeztetéshez. A tárolt képek díja 1000 arconként 0,5 dollár és a díjszabás napi lebontású. Az ingyenes szintű előfizetések száma továbbra is 1000 főre korlátozódik.

### <a name="release-changes-in-october-2016"></a>2016 októberében a kiadás módosításai
* A FaceList -As [FaceList - Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) és [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

### <a name="release-changes-in-july-2016"></a>2016 júliusában a kiadás módosításai
* Támogatott személyes objektumhitelesítés az [Arcon – Ellenőrzés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Hozzáadott `mode` választható paraméter, amely lehetővé `matchPerson` teszi `matchFace` a kiválasztás két munkamód: és [a Face - Find Hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) és alapértelmezett . `matchPerson`

* Hozzáadott `confidenceThreshold` választható paraméter a felhasználó számára, hogy beállítsa a küszöbértéket, hogy egy arc tartozik-e egy személy [objektumface - Azonosítás](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Kötelező `start` és `top` paraméterek hozzáadva a [PersonGroup - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) listában, hogy a felhasználó megadhassa a kezdőpontot és a listához megadott teljes PersonGroups számot.

### <a name="v10-changes-from-v0"></a>V1.0 változások V0
* Frissített szolgáltatásgyökér-végpont ```https://westus.api.cognitive.microsoft.com/face/v0/``` ```https://westus.api.cognitive.microsoft.com/face/v1.0/```a rendszerből a-ba A következő re alkalmazott módosítások: [Arc - Észlelés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Arc - Azonosítás](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), Arc - [Hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) keresése és Arc [- Csoport](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* A minimálisan észlelhető arcméret 36x36 képpontra frissült. A rendszer nem észleli a 36x36 képpontnál kisebb arcokat.

* A PersonGroup és a Person data elavulta a Face V0-ben. Ezek az adatok nem érhetők el a Face V1.0 szolgáltatással.

* 2016. június 30-án elavultak a Face API V0 végpontja.
