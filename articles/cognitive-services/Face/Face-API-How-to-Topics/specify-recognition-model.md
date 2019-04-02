---
title: A beszédfelismerést modell – Face API megadása
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, mely modellünk használata az Azure Face API-alkalmazás kiválasztása.
services: cognitive-services
author: longl
manager: nitinme
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: longl
ms.openlocfilehash: 33348e637143b923719425b9674f99a475d848d9
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58806660"
---
# <a name="specify-a-face-recognition-model"></a>Adja meg az arcok felismerése modell

Ez az útmutató bemutatja, hogyan adjon meg egy arcok felismerése modellt arcfelismerés, azonosítása és hasonlóság keresést az Azure Face API használatával.

A Face API használja a machine learning-modellek műveletek végrehajtása az emberi arcok a képeken. A felhasználói visszajelzések és a kutatási fejlődésére alapuló modellek pontossága fejlesztésével, és ezek a fejlesztések adatmodell-frissítéseket, tudunk. A fejlesztők adja meg, a face modellünk melyik verzióját szeretnék használni, lehetősége van. a modell, amely legjobban megfelel a kérjünk a használat kiválaszthatnak.

Ha Ön új felhasználó, azt javasoljuk, használja a legújabb modellre. Látogasson el lapunkra további modell ütközések elkerülésével különböző Face műveletek megadása. Ha Ön tapasztalt felhasználó, és nem biztos e kell váltani a legújabb modellre, ugorjon a [különböző modell kiértékelése](#evaluate-different-models) szakasz az új modell értékelése és az aktuális adatok használatával eredmények összehasonlítása.

## <a name="prerequisites"></a>Előfeltételek

Meg kell ismernie az arcfelismerés is használható mesterséges Intelligencia és a azonosító. Ha nem, először tekintse meg ezeket az útmutatókat:

* [A kép arcok észlelése](HowtoDetectFacesinImage.md)
* [A kép arcok azonosítása](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>A megadott modell arcok észlelése

Arcfelismerés, emberi arcok a vizuális arcrész azonosítja, és megkeresi a határolókeret helyüket. Kinyeri az arcfelismerési funkciókat és a használt azonosító tárolja azokat. Az összes ezt az információt képezi a egy oldallal ábrázolása.

A modell használt ki kell olvasni az arcfelismerési funkciókat, így megadhatja egy modellverziója észlelése művelet végrehajtásakor.

Használatakor a [A Face – észlelése] API-t, rendelje hozzá a modell a következőt a `recognitionModel` paraméter. Az elérhető értékek a következők:

* `recognition_01`
* `recognition_02`

Másik lehetőségként megadhatja a _returnRecognitionModel_ paraméter (alapértelmezett **false (hamis)**) jelzi-e _recognitionModel_ vissza kell adni az adott válaszként. Tehát a kérelem URL-CÍMÉT a [A Face – észlelése] REST API-t fog kinézni:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]
&subscription-key=<Subscription key>`

Az ügyféloldali kódtárat használja, ha az érték rendelhet `recognitionModel` verzió képviselő karakterláncot adja át.
Ha meghagyja a hozzá nem rendelt, az alapértelmezett modellverziója (_recognition_01_) fogja használni. Tekintse meg a következő mintakód a .NET ügyféloldali kódtár.

```csharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Arcazonosítás megadott modellel

A Face API face adatok kinyerése egy képet, és társítsa azt egy **személy** objektum (keresztül a [adja hozzá a face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API-hívás, például), és több **személy** objektumok lehetnek. tárolja a **is lehet PersonGroup**. Ezután egy új arc lehet összehasonlítani a egy **is lehet PersonGroup** (az a [Face - Identify] hívja), és az adott csoport egyező személy azonosítható.

A **is lehet PersonGroup** rendelkeznie kell egy egyedi modellünk összes a **személy**s, és megadhatja a használatával a `recognitionModel` paraméter a csoport létrehozásakor ([PersonGroup - Create] vagy [LargePersonGroup - Create]). Ha nincs megadva ez a paraméter az eredeti `recognition_01` modellt használja. Egy csoport mindig használja a modell több létrehozták, és új arcok válnak ebben a modellben társított; való felvételekor Ez a csoport létrehozása után nem módosítható. Milyen modell megtekintéséhez egy **is lehet PersonGroup** konfigurálva van, használja a [is lehet PersonGroup - Get] API-t a _returnRecognitionModel_ állítja be a paraméter **Igaz**.

Tekintse meg a következő mintakód a .NET ügyféloldali kódtár.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceServiceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

Ebben a kódban egy **is lehet PersonGroup** azonosítójú `mypersongroupid` jön létre, és használatára van beállítva a _recognition_02_ modell arcfelismerési funkciókat kibontásához.

Ennek megfelelően meg kell adnia a melyik modellt használja, ha az arcok észlelése ez porovnání **is lehet PersonGroup** (keresztül a [A Face – észlelése] API-t). A modell használata mindig összhangban kell lennie a **is lehet PersonGroup**a konfigurációs; ellenkező esetben sikertelen lesz a művelet miatt nem kompatibilis modellek.

Nem változik a a [Face - Identify] API; csak adja meg a modell verziójának észlelési kell.

## <a name="find-similar-faces-with-specified-model"></a>A megadott modell hasonló arcok keresése

Adja meg a felismerés modell hasonlóság keresés is. Rendelhet a modell verziót `recognitionModel` a face lista létrehozásakor [FaceList - létrehozása] API vagy [LargeFaceList – létrehozása]. Ha nincs megadva ez a paraméter az eredeti `recognition_01` modellt használja. Face listáját mindig használja a modell több létrehozták, és új arcok válnak ebben a modellben társított; való felvételekor a létrehozás után nem módosítható. Milyen face listáját úgy van konfigurálva, a modell megtekintéséhez használja a [FaceList - Get] API-t a _returnRecognitionModel_ állítja be a paraméter **igaz**.

Tekintse meg a következő mintakód a .NET ügyféloldali kódtár.

```csharp
await faceServiceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Ez a kód létrehozza a face listát `My face collection`révén a _recognition_02_ modell a a kivonási funkció. Ha a face listáját egy új felismert arc hasonló arcokat keres, a face kell az észlelt ([A Face – észlelése]) használatával a _recognition_02_ modell. A modell az előző szakaszban látható módon kell lennie a konzisztens.

Nem változik a a [Arcfelismerés – hasonló keresése] API; csak adja meg a modell verziójának észlelési.

## <a name="verify-faces-with-specified-model"></a>Ellenőrizheti az arcokat a megadott modell

A [A Face – ellenőrzése] API ellenőrzi, hogy két arc ugyanazt az embert. Nem változik a ellenőrizze API modelleket kapcsolatban, de csak összehasonlíthatja észlelt ugyanannak a modellnek az arcokat. Ezért a két arc is kell használatával észlelt `recognition_01` vagy `recognition_02`.

## <a name="evaluate-different-models"></a>Különböző modell értékelése

Ha szeretné a teljesítményt, hasonlítsa össze a _recognition_01_ és _recognition_02_ modellek az adatokon, szüksége lesz:

1. Hozzon létre két **is lehet PersonGroup**-s _recognition_01_ és _recognition_02_ jelölik.
1. Arcfelismerés, és regisztrálja őket, hogy a rendszerkép-adatok használatával **személy**esetében ez a két s **is lehet PersonGroup**s, és a trigger a betanítási folyamat az [PersonGroup - Train] API-t.
1. Tesztelés [Face - Identify] mindkét **is lehet PersonGroup**s és összehasonlíthatja az eredményeket.

Általában egy megbízhatósági küszöbértéket (érték nulla és a egy másikkal, amelyik meghatározza, hogy hogyan abban a modellt kell egy ARC azonosításához között) adja meg, ha szüksége lehet, hogy különböző küszöbértékek különböző modellek esetén. Egy küszöbérték egy modell nem oszthatók meg a másikra hivatott, és nem feltétlenül hoznak létre a ugyanazokat az eredményeket.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan, adja meg a modellünk több különböző Face szolgáltatás API-k használatához. Ezután kövesse a gyors üzembe helyezés arcfelismerés használatának megkezdéséhez.

* [A kép arcok észlelése](../quickstarts/csharp-detect-sdk.md)

[A Face – észlelése]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Arcfelismerés – hasonló keresése]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face - Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[A Face – ellenőrzése]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[Is lehet PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - létrehozása]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList – létrehozása]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
