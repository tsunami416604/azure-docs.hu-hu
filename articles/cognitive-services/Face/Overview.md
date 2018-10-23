---
title: Mi az a Face API szolgáltatás?
titleSuffix: Azure Cognitive Services
description: Ez a témakör a Face API szolgáltatást és a kapcsolódó kifejezéseket ismerteti.
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: overview
ms.date: 10/11/2018
ms.author: sbowles
ms.openlocfilehash: 6c1e0d0a51bc01c581c05e7ce3215f5501b4be76
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310368"
---
# <a name="what-is-the-face-api-service"></a>Mi az a Face API szolgáltatás?

A Face API szolgáltatás egy felhőalapú szolgáltatás, amely algoritmusokat biztosít a képeken és videókon szereplő emberi arcok elemzéséhez. A Face API két fő funkcióval rendelkezik: attribútumokkal való arcészleléssel és arcfelismeréssel.

## <a name="face-detection"></a>Arcfelismerés

A Face API nagy pontossággal tud akár 64 emberi arcot felismerni egy képen. A kép fájl (bájtstream) vagy érvényes URL-cím alapján adható meg.

![Arcfelismerés – Áttekintés](./Images/Face.detection.jpg)

A rendszer minden észlelt arccal együtt visszaad egy téglalapot (bal oldali és felső koordináta, szélességi és hosszúsági érték), amely az észlelt arc helyét jelzi a képen. Az arcfelismerés igény szerint kiszűr még egy sor archoz kapcsolódó attribútumot, például testtartás, nem, életkor, fejtartás, arcszőrzet és szemüveg. További információkat lásd: [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-recognition"></a>Arcfelismerés

Az emberi arcok felismerésének képessége számos helyzetben lehet fontos, beleértve a biztonsági szolgáltatásokat, a természetes felhasználói felületeket, a képtartalmak elemzését és kezelését, a mobilalkalmazásokat és a robotikát is. A Face API szolgáltatás négy arcfelismerési funkciót nyújt: az arcellenőrzést, a hasonló arcok keresését, az arccsoportosítást és a személyazonosítás.

### <a name="face-verification"></a>Arcellenőrzés

Az arcellenőrzés hitelesítést végez két észlelt arc között vagy egy észlelt arc és egy személyobjektum között. Bővebben lásd [Face – Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

### <a name="finding-similar-faces"></a>Hasonló arcok keresése

Adott a cél felismert arc és a keresendő jelölt arcok halmaza, a szolgáltatás megkeresi a cél archoz leginkább hasonló arcok kis halmazát. Két működési mód támogatott, a **matchFace** és a **matchPerson**. A **matchPerson** mód hasonló arcokat ad vissza a [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) alapján származtatott, azonos személyekre vonatkozó küszöbérték alkalmazása után. A **matchFace** mód nem foglalkozik az azonos személyekre vonatkozó küszöbértékkel, és a leginkább hasonló jelöltek arcát adja vissza. Az alábbi példában a jelöltek arca szerepel.
![Áttekintés – Hasonló arcok keresése](./Images/FaceFindSimilar.Candidates.jpg) Ez a lekérdezett arc.
![Áttekintés – Hasonló arcok keresése](./Images/FaceFindSimilar.QueryFace.jpg)

Négy hasonló arc megkereséséhez a **matchPerson** mód az (a) és a (b) képet adja vissza, amely a lekérdezett arccal egyező személyt ábrázolja. A **matchFace** mód az (a), (b), (c) és (d) képet adja vissza, pontosan négy jelöltet, még akkor is, ha a hasonlóság kicsi. További információkat lásd: [Face – Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

### <a name="face-grouping"></a>Arccsoportosítás

Egy csoportnyi ismeretlen arc esetén az arccsoportosítás API-ja automatikusan több csoportra bontja az arcokat a hasonlóságuk alapján. Mindegyik csoport az eredeti ismeretlen arcok halmazának különálló valódi részhalmaza, amelyek hasonló arcokat tartalmaznak. Az ugyanabba a csoportba tartozó valamennyi arcot tekinthetjük úgy, hogy ugyanahhoz a személyhez tartozik. További információkat lásd: [Face – Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

### <a name="person-identification"></a>Személyazonosítás

A Face API segítségével észlelt arc és a személyek adatbázisa alapján azonosíthatók a személyek. Ez az adatbázis előre létrehozható, és menet közben szerkeszthető.

Az alábbi ábra a „myfriends” nevű példaadatbázist mutatja. Mindegyik csoport legfeljebb 1 000 000/10 000 különböző személyobjektumot tartalmazhat. Mindegyik személyobjektumhoz legfeljebb 248 arc lehet regisztrálva.

![Áttekintés – LargePersonGroup/PersonGroup](./Images/person.group.clare.jpg)

Egy adatbázis létrehozása és betanítása után azonosítás végezhető a csoport és egy új észlelt arc között. Ha a rendszer a csoport egyik tagjaként azonosítja az arcot, visszaadja a személyobjektumot.

Személyek azonosításával kapcsolatos további információkért tekintse meg a következő API-útmutatókat:

[Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)  
[PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)  
[PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)  
[PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)  
[LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)  
[LargePersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)  
[LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)  

#### <a name="face-storage-and-pricing"></a>Arctárolás és díjszabás

A Face Storage a LargePersonGroup/PersonGroup Person objektumok ([PersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)/[LargePersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)) vagy a LargeFaceLists/FaceLists ([FaceList – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)/[LargeFaceList – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)) használatakor standard szintű előfizetést tesz lehetővé a további megőrzött arcok számára a Face API-val történő azonosítás vagy hasonlósági párosítás céljára. A tárolt képek díja 1000 arconként 0,50 dollár, és a díjszabás arányosítása naptári napok alapján történik. Az ingyenes szintű előfizetésekre egy legfeljebb 1000 személyes korlát vonatkozik.

Például ha a fiókja a hónap első felében napi 10 000 megőrzött képet használ fel, a második felében pedig egyet sem, akkor csak a tárolt napokra kell a 10 000 arcért fizetnie. Tekintsünk meg egy másik példát: ha a hónap minden napján néhány órán keresztül 1000 megőrzött arcot használ fel, majd minden éjjel törli őket, akkor is fizetnie kell minden napra a 1000 megőrzött arcért.

## <a name="sample-apps"></a>Mintaalkalmazások

Vessen egy pillantást ezekre a mintaalkalmazásokra, amelyek a Face API-t használják.

- [A Microsoft Face API: Windows ügyfélkódtár és minták](https://github.com/Microsoft/Cognitive-Face-Windows)
  - A WPF mintaalkalmazás számos arcfelismerési forgatókönyvet, elemzést és azonosítást mutat be.
- [FamilyNotes UWP-alkalmazás](https://github.com/Microsoft/Windows-appsample-familynotes)
  - Univerzális Windows Platform (UWP) mintaalkalmazás, amely bemutatja a beszéd, Cortana, kézírás és kamera használatát egy családi jegyzetmegosztó forgatókönyvbe ágyazva.
- [Videó képkocka elemzés minta](https://github.com/microsoft/cognitive-samples-videoframeanalysis)
  - Win32-mintaalkalmazás, amely bemutatja az élő videóstreamek közel valós idejű elemzését a Face, a Computer Vision és az Emotion API használatával.

## <a name="tutorials"></a>Oktatóanyagok
Az alábbi oktatóanyagok a Face API alapszintű funkcióit és az előfizetési folyamatokat mutatják be:
- [Első lépések a Face API-val CSharp-oktatóanyag](Tutorials/FaceAPIinCSharpTutorial.md)
- [Első lépések a Face API-val Java for Android oktatóanyag](Tutorials/FaceAPIinJavaForAndroidTutorial.md)
- [Első lépések a Face API-val Python oktatóanyag](Tutorials/FaceAPIinPythonTutorial.md)

## <a name="next-steps"></a>További lépések

Próbálja ki az egyszerű Face API-forgatókönyvek implementálására szolgáló rövid útmutatót.
- [Rövid útmutató: Arcfelismerés egy képen a C# használatával](quickstarts/csharp.md) (más nyelvekhez is elérhető)
