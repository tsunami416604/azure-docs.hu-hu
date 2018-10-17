---
title: Mi a Face API szolgáltatás?
titleSuffix: Azure Cognitive Services
description: A szószedet megadja azoknak a kifejezéseknek a magyarázatát, amelyekkel a Face API-szolgáltatással dolgozva találkozhat.
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: overview
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 15de899be5ab85e9fe84ba1b6284bc9419fcf8a1
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123470"
---
# <a name="what-is-the-face-api-service"></a>Mi a Face API szolgáltatás?

Üdvözli a Face API szolgáltatás, ami egy felhőalapú szolgáltatás a legfejlettebb arcfelismerési algoritmusokkal. A Face API két fő funkcióval rendelkezik: arc észlelés attribútumokkal és arcfelismerés.

## <a name="face-detection"></a>Arcfelismerés

A Face API nagy pontossággal azonosítja maximum 64 emberi arc helyét a képen. A kép megadása történhet fájlként, bájtokban vagy érvényes URL-lel.

![Arcfelismerés – Áttekintés](./Images/Face.detection.jpg)

Mindegyik felismert archoz visszaadja az arc helyét a képen belül mutató arc négyszöget (bal, felső, szélesség és magasság). Az arcfelismerés igény szerint kiszűr még egy sor archoz kapcsolódó attribútumot, például testtartás, nem, életkor, fejtartás, arcszőrzet és szemüveg. További információkat lásd: [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-recognition"></a>Arcfelismerés

Az arcfelismerés széles körben sokféle helyzetben használatos, ideértve a biztonság, a természetes felhasználói felületek, a képtartalom elemzés és kezelés, mobilalkalmazások és robotika területeit. Négy arcfelismerési funkció áll rendelkezésre: arcellenőrzés, hasonló arcok keresése, arcok csoportosítása és személyazonosítás.

### <a name="face-verification"></a>Arcellenőrzés

A Face API-ellenőrzés hitelesítést végez két észlelt arccal vagy egy észlelt arccal és egy személy objektummal. Bővebben lásd [Face – Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

### <a name="finding-similar-face"></a>Hasonló arcok keresése

Adott a cél felismert arc és a keresendő jelölt arcok halmaza, a szolgáltatás megkeresi a cél archoz leginkább hasonló arcok kis halmazát. Két támogatott üzemmódja van, a `matchFace` és a `matchPerson`. Az `matchPerson` mód a hasonló arcokat azután adja vissza, miután alkalmazta rájuk a [Face – Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) általi azonos személy küszöböt. A `matchFace` mód nem foglalkozik az azonos személy küszöbbel és a leginkább hasonló jelöltek arcait adja vissza. Vegyük a következő példát, a jelölt arcok jelennek meg.
![Áttekintés – Face hasonló keresése](./Images/FaceFindSimilar.Candidates.jpg) és lekérdezett arc ![Áttekintés – Face hasonló keresése](./Images/FaceFindSimilar.QueryFace.jpg)

Négy hasonló arc keresésekor az `matchPerson` mód az (a) és (b) arcokat adja vissza, amelyek ugyanahhoz a személyhez tartoznak, mint a lekérdezett arc. A `matchFace` mód pontosan négy jelöltet ad vissza, (a), (b), (c) és (d), még akkor is, ha a hasonlóság kicsi. További információkat lásd: [Face – Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

### <a name="face-grouping"></a>Arccsoportosítás

Adott az ismeretlen arcok halmaza, az arccsoportosító API hasonlóság alapján több csoportra osztja őket. Mindegyik csoport az eredeti ismeretlen arcok halmazának különálló valódi részhalmaza, amelyek hasonló arcokat tartalmaznak. Ugyanabba a csoportba tartozó valamennyi arcot tekinthetjük úgy, hogy ugyanahhoz a személy objektumhoz tartozik. További információkat lásd: [Face – Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

### <a name="face-identification"></a>Arcok azonosítása

A Face API használható arra, hogy embereket azonosítson a felismert arc és egy emberek (mint LargePersonGroup/PersonGroup definiált) adatbázis alapján. Ez az adatbázis előre létrehozható és az idők során módosítható.

Az alábbi ábra egy „myfriends” nevű LargePersonGroup/PersonGroup példa. Mindegyik csoport legfeljebb 1 000 000/10 000 személy objektumot tartalmazhat. Emellett minden egyes személy objektum rendelkezhet maximum 248 regisztrált arccal.

![Áttekintés – LargePersonGroup/PersonGroup](./Images/person.group.clare.jpg)

A LargePersonGroup/PersonGroup létrehozása és betanítása után, azonosítás végezhető a csoport és az új észlelt arc alapján. Ha az arcot azonosítja mint a csoport egy személy objektumát, a személy objektumot fogja visszaadni.

Személyek azonosításával kapcsolatos további információkért tekintse meg a következő API-útmutatókat:

[Face – Azonosítás](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)
[PersonGroup – Létrehozás](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
[PersonGroup Person – Létrehozás](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
[PersonGroup – Betanítás](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)
[LargePersonGroup – Létrehozás](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)
[LargePersonGroup Person – Létrehozás](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)
[LargePersonGroup – Betanítás](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)

### <a name="face-storage"></a>A Face Storage

A Face Storage a LargePersonGroup/PersonGroup Person objektumok ([PersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)/[LargePersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)) vagy a LargeFaceLists/FaceLists ([FaceList – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)/[LargeFaceList – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)) használatakor standard szintű előfizetést tesz lehetővé a további megőrzött arcok számára a Face API-val történő azonosítás vagy hasonlósági párosítás céljára. A tárolt képek díja 1000 arconként 0,5 dollár és a díjszabás napi lebontású. Az ingyenes szintű előfizetések ingyenesek, de legfeljebb 1000 személyes korlát vonatkozik rájuk.

Az arcképtárolás díjszabása napi lebontású. Például ha a fiókja a hónap első felében napi 10 000 megőrzött képet használ fel, a második felében pedig egyet sem, akkor csak a tárolt napokra kell a 10 000 arcért fizetnie. Tekintsünk meg egy másik példát: ha a hónap minden napján néhány órán keresztül 1000 megőrzött arcot használ fel, majd minden éjjel törli őket, akkor is fizetnie kell minden napra a 1000 megőrzött arcért.

## <a name="getting-started-tutorials"></a>Első lépések oktatóanyag

Az alábbi oktatóanyagok a Face API – alapszintű funkcióit és az előfizetési folyamatokat mutatják be:

- [Első lépések a Face API-val CSharp-oktatóanyag](Tutorials/FaceAPIinCSharpTutorial.md)
- [Első lépések a Face API-val Java for Android oktatóanyag](Tutorials/FaceAPIinJavaForAndroidTutorial.md)
- [Első lépések a Face API-val Python oktatóanyag](Tutorials/FaceAPIinPythonTutorial.md)

## <a name="sample-apps"></a>Mintaalkalmazások

Vessen egy pillantást ezekre a mintaalkalmazásokra, amelyek a Face API-t használják.

- [A Microsoft Face API: Windows ügyfélkódtár és minták](https://github.com/Microsoft/Cognitive-Face-Windows)
  - A WPF mintaalkalmazás számos arcfelismerési forgatókönyvet, elemzést és azonosítást mutat be.
- [FamilyNotes UWP-alkalmazás](https://github.com/Microsoft/Windows-appsample-familynotes)
  - Univerzális Windows Platform (UWP) mintaalkalmazás, amely bemutatja a beszéd, Cortana, kézírás és kamera használatát egy családi jegyzetmegosztó forgatókönyvbe ágyazva.
- [Videó képkocka elemzés minta](https://github.com/microsoft/cognitive-samples-videoframeanalysis)
  - Univerzális Windows Platform (UWP) mintaalkalmazás, amely bemutatja az élő video-adatfolyamok közel valós idejű elemzését a Face, a Computer Vision és az Emotion API-k használatával.

## <a name="related-topics"></a>Kapcsolódó témakörök

- [Face API 1.0-s verzió kibocsátási megjegyzései](ReleaseNotes.md)
- [Hogyan lehet arcokat megtalálni a képen](Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
- [Hogyan lehet arcokat azonosítani a képen](Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)
