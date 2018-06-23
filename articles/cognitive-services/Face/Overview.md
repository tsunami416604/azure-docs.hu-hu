---
title: Szembesülhetnek API szolgáltatás áttekintése |} Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: A szószedet kifejezések magyarázatát a Arcfelismerési API-szolgáltatás használatakor esetleg előforduló.
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: bb3ee9431f24a51dfd3877f1c4d621f7ba633b96
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347599"
---
# <a name="what-is-face-api"></a>Mi az a Arcfelismerési API-t?

Üdvözli a Microsoft Arcfelismerési API-val, a legtöbb haladó arcfelismerési algoritmusok biztosító felhőalapú szolgáltatás. Arcfelismerési API két fő funkciókkal rendelkezik: attribútumokkal rendelkező észlelési szembesülhetnek, és a felismerési szembesülhetnek.

## <a name="face-detection"></a>Arcfelismerés

Arcfelismerési API legfeljebb 64 emberi lapok magas pontosság arcfelismerési helyen lévő lemezkép észleli. És fájl bájtok vagy érvényes URL-cím is megadható a kép.

![Áttekintés – Arcfelismerési észlelése](./Images/Face.detection.jpg)

Arcfelismerési téglalap (bal, felső, szélességét és magasságát) jelzi, hogy a kép arcfelismerési helyét adja vissza együtt minden észlelt arcfelismerési. Szükség esetén arcfelismerési észlelési arcfelismerési kapcsolódó attribútumok jelentő, nemét, kor, központi jelentő, arcfelismerést haj és üveg például több bontja ki. További információkért lásd: [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-recognition"></a>Arcfelismerési felismerés

Arcfelismerési felismerés széles körben használt biztonsági, természetes felhasználói felület, kép tartalomelemzés és felügyeleti, mobilalkalmazások és robotics helyzetekben. Négy arcfelismerési felismerés funkciók találhatók: szembesülhetnek ellenőrzési, hasonló lapok megállapítás szokott lenni, arc csoportosítási és személy azonosító.

### <a name="face-verification"></a>Arcellenőrzés

Arcfelismerési API-ellenőrzést hajt végre egy két észlelt oldalát hitelesítést vagy hitelesítési észlelt lapot a egy személy objektumhoz. További részletekért lásd: [szembesülhetnek - ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

### <a name="finding-similar-face"></a>Hasonló oldallal keresése

Megadott egy cél észlelt arcfelismerés és keresést segítségével jelölt lapok készlete, a szolgáltatás megkeresi a, amely a leginkább hasonló, így a cél arcfelismerési egy kis készletét. Két működő mód `matchFace` és `matchPerson` támogatottak. `matchPerson` mód hasonló lapok adja vissza, miután ugyanaz a személy küszöbértéket alkalmazó származó [szembesülhetnek - ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). `matchFace` mód figyelmen kívül hagyja a ugyanaz a személy küszöbértéket, és a felső hasonló jelölt lapok adja vissza. Az alábbi példa igénybe, jelölt lapok vannak felsorolva.
![Áttekintés – Arcfelismerési hasonló](./Images/FaceFindSimilar.Candidates.jpg) és a lekérdezés lap ![áttekintés – Arcfelismerési hasonló keresése](./Images/FaceFindSimilar.QueryFace.jpg)

Négy hasonló arc, található `matchPerson` módot adja vissza (a) és (b), amely ugyanaz a lekérdezés arcfelismerési rendelkező személy tartozik. `matchFace` módot adja vissza (a), (b), (c) és (d), pontosan négy jelöltek akkor is, ha alacsony hasonlóság. További információkért lásd: [Arcfelismerési - hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

### <a name="face-grouping"></a>Arccsoportosítás

A megadott ismeretlen lapok egy készletét, arcfelismerési API automatikusan csoportosítási csoportokba osztja a őket több alapuló. Minden csoport, az eredeti ismeretlen arc különálló megfelelő részhalmazát beállítva, és hasonló lapokat tartalmaz. És ugyanabban a csoportban található összes oldalakat tekinthető azonos személy objektumhoz tartozik. További információkért lásd: [szembesülhetnek - csoport](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

### <a name="face-identification"></a>Arcfelismerés

Arcfelismerési API-t egy észlelt arc alapján és (LargePersonGroup/PersonGroup definiálva) személyek adatbázis azonosítására használható. Ez az adatbázis előzetes létrehozása, adott idő alatt is módosíthatók.

Az alábbi ábra egy példát egy LargePersonGroup/PersonGroup "myfriends" nevű. Minden egyes csoport legfeljebb 1 000 000/10 000 személy objektumokat tartalmazhat. Minden személy objektum eközben regisztrált legfeljebb 248 felületei is rendelkezik.

![Áttekintés – LargePersonGroup/PersonGroup](./Images/person.group.clare.jpg)

Miután LargePersonGroup/PersonGroup létrehozták, és a betanítása, azonosító is hajt végre a csoport és egy új észlelt arc. Ha maga a csoport személy objektumként, akkor a személy objektumot adott vissza.

Személy azonosító kapcsolatos további információkért tekintse meg a következő API-útmutatók:

[Szembesülhetnek – azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)
[PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
[PersonGroup személy - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
[PersonGroup - vonat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) 
 [LargePersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)
[LargePersonGroup személy - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)
[LargePersonGroup - vonat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)

### <a name="face-storage"></a>Face Storage

Arcfelismerési tároló lehetővé teszi, hogy egy szabványos előfizetés tárolásához további megőrzött lapok LargePersonGroup/PersonGroup személy objektumok használata esetén ([PersonGroup személy - hozzáadása Arcfelismerési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)/[LargePersonGroup személy - Hozzáadása Arcfelismerési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)) vagy LargeFaceLists/FaceLists ([FaceList - hozzáadása Arcfelismerési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)/[LargeFaceList - Arcfelismerési hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)) azonosító vagy Arcfelismerési API-val egyező hasonlóság. A tárolt képek van szó, 0,5 $ 1000 lapok száma, és ez a számláló értéke arányosítva naponta. Ingyenes szint előfizetések szabad, de legfeljebb 1000 teljes személyek.

Arcfelismerési tárolás díjszabása arányosítva naponta. Például, ha a fiókot használja 10 000 megőrzött lapok minden nap a hónap első felében, és nincs a második fele meg akkor is felszámítjuk a díjat csak a 10 000 lapok napra vonatkozó tárolja. Azt is megteheti minden nap a hónap során, akkor néhány órán keresztül 1000 lapok fennállnak, és törölje őket éjszakánként, ha meg szeretné továbbra is számlázni 1000 megőrzött lapok minden nap.

## <a name="getting-started-tutorials"></a>Bevezető oktatóanyagok

Az alábbi oktatóanyagok bemutatják az alapvető funkciók Arcfelismerési API-t és az előfizetések folyamatok:

- [A csharp nyelvű oktatóprogram Ismerkedés a Arcfelismerési API](Tutorials/FaceAPIinCSharpTutorial.md)
- [Ismerkedés az Arcfelismerési API-nak Java Android oktatóanyag](Tutorials/FaceAPIinJavaForAndroidTutorial.md)
- [Az oktatóanyag a Python Ismerkedés a Arcfelismerési API](Tutorials/FaceAPIinPythonTutorial.md)

## <a name="sample-apps"></a>Mintaalkalmazások

Vessen egy pillantást a Arcfelismerési API-t használó alkalmazásokat.

- [FamilyNotes UWP-alkalmazást](https://github.com/Microsoft/Windows-appsample-familynotes)
  - Univerzális Windows Platform (UWP) mintaalkalmazás, amely beszéd átalakítás, Cortana, szabadkézi és forgatókönyv megosztását család Megjegyzés a fényképezőgép használatát mutatja.
- [Videó keret elemzési minta](https://github.com/microsoft/cognitive-samples-videoframeanalysis)
  - Univerzális Windows Platform (UWP) mintaalkalmazás, amely élő video-adatfolyamot a oldallal, számítógép stratégiai és Érzelemfelismerési API-k használatával közel valós idejű elemzése.

## <a name="related-topics"></a>Kapcsolódó témakörök

- [Arcfelismerési API-verzió 1.0-ás kibocsátási megjegyzései](ReleaseNotes.md)
- [A kép néz történő Adatmásolást](Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
- [A kép néz azonosítása](Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)
