---
title: Felismerési modell meghatározása – Face API
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan választhatja ki, hogy melyik felismerési modellt szeretné használni az Azure Face API alkalmazással.
services: cognitive-services
author: longl
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: longl
ms.openlocfilehash: fd60923351970dfe5aa5705a0508dbd39941ef58
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68254343"
---
# <a name="specify-a-face-recognition-model"></a>Arcfelismerési modell megadása

Ez az útmutató bemutatja, hogyan határozhatja meg az Arcfelismerés, az azonosítás és a hasonlóságok észlelését az Azure Face API használatával.

A Face API gépi tanulási modelleket használ a képeken található emberi arcokon végzett műveletek végrehajtásához. Folyamatosan fejlesztjük modelljeink pontosságát az ügyfelek visszajelzései és a kutatás előrehaladása alapján, és ezeket a fejlesztéseket a modell frissítéseiként tesszük elérhetővé. A fejlesztők megadhatják, hogy az Arcfelismerés modell melyik verzióját szeretné használni; kiválaszthatják azt a modellt, amelyik a legjobban megfelel a használati esetnek.

Ha Ön új felhasználó, javasoljuk, hogy használja a legújabb modellt. Olvassa el, hogy megtudja, hogyan határozhatja meg különböző arc-műveletekben, miközben elkerülheti a modellek ütközéseit. Ha Ön fejlett felhasználó, és nem biztos abban, hogy a legújabb modellre kell váltania, ugorjon a [különböző modellek](#evaluate-different-models) kiértékelése szakaszra az új modell kiértékeléséhez és az eredmények összehasonlításához a jelenlegi adatkészlettel.

## <a name="prerequisites"></a>Előfeltételek

Ismernie kell az AI arcfelismerés és-azonosítás fogalmait. Ha nem, tekintse meg a következő útmutatók első lépéseit:

* [Arcok észlelése egy képben](HowtoDetectFacesinImage.md)
* [Arcok azonosítása egy képben](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>A megadott modellel rendelkező arcok észlelése

Az Arcfelismerés azonosítja az emberi arcok vizuális tereptárgyait, és megkeresi a hozzájuk tartozó mezők helyét. Emellett kinyeri az arc funkcióit és tárolja azokat az azonosításban való használatra. Az összes információ egy arc ábrázolását képezi.

Az észlelési modellt a rendszer a Face funkció kibontásakor használja, így megadhatja a modell verzióját az észlelési művelet végrehajtásakor.

Az [Szembenézés észlelése] API használatakor rendelje hozzá a modell verzióját `recognitionModel` a (z) paraméterrel. Az elérhető értékek a következők:

* `recognition_01`
* `recognition_02`

Opcionálisan megadhatja a _returnRecognitionModel_ paramétert (az alapértelmezett **Hamis értéket**) annak jelzésére, hogy a _recognitionModel_ válaszként kell-e visszaadni. Ezért a REST API [Szembenézés észlelése] címe a következőképpen fog kinézni:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Ha az ügyféloldali kódtárat használja, a értéket a következőhöz `recognitionModel` rendelheti hozzá:.
Ha nem törli a hozzárendelést, a rendszer az alapértelmezett modell-verziót (_recognition_01_) fogja használni. Tekintse meg a .NET-ügyfél függvénytárának következő kódrészletét.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Arcok azonosítása a megadott modellel

A Face API képes kinyerni az arc adatait egy rendszerképből, és társíthatja azt egy **személy** objektummal (például a [Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API-hívás hozzáadásával), és több **személy** objektum is tárolható egy **PersonGroup**. Ezt követően egy új arc összehasonlítható egy **PersonGroup** (az [Face - Identify] hívással), és azonosítható a csoporton belüli megfelelő személy is.

A **PersonGroup** egyetlen egyedi felismerési modellel kell rendelkezniük az **összes s számára**, és ezt a `recognitionModel` paramétert használva lehet megadni a csoport létrehozásakor ([PersonGroup - Create] vagy [LargePersonGroup - Create]). Ha nem megadja ezt a paramétert, a rendszer `recognition_01` az eredeti modellt használja. Egy csoport mindig a-val létrehozott felismerési modellt fogja használni, és az új arcok társítva lesznek ehhez a modellhez, amikor hozzáadja őket a rendszerhez. Ez a csoport létrehozása után nem módosítható. Ha szeretné megtekinteni, hogy a **PersonGroup** melyik modellre van konfigurálva, használja a [PersonGroup – Get] t a _returnRecognitionModel_ paraméter **true**értékre állításával.

Tekintse meg a .NET-ügyfél függvénytárának következő kódrészletét.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

Ebben a kódban létrejön egy **PersonGroup** , amely `mypersongroupid` létrehoz egy azonosítót, és úgy van beállítva, hogy a _recognition_02_ modellt használja az arc funkcióinak kinyeréséhez.

Ennek megfelelően meg kell adnia, hogy melyik modellt kell használni a **PersonGroup** összehasonlítására szolgáló arcok észleléséhez (az [Szembenézés észlelése] API használatával). Az Ön által használt modellnek mindig konzisztensnek kell lennie a **PersonGroup**konfigurációjával. Ellenkező esetben a művelet nem kompatibilis modellek miatt sikertelen lesz.

Nincs változás a [Face - Identify] ban; az észleléshez csak a modell verzióját kell megadnia.

## <a name="find-similar-faces-with-specified-model"></a>Hasonló arcok keresése a megadott modellel

A hasonlósági kereséshez is megadhat egy felismerési modellt. A modell verzióját hozzárendelheti a FaceList `recognitionModel` API [FaceList – létrehozás] val vagy a [LargeFaceList – létrehozás]létrehozásához használt Arcfelismerés létrehozásához. Ha nem megadja ezt a paramétert, a rendszer `recognition_01` az eredeti modellt használja. Az Arcfelismerés mindig a szolgáltatással létrehozott felismerési modellt fogja használni, és az új arcok társítva lesznek ehhez a modellhez, amikor hozzáadja őket a rendszerhez. Ez a létrehozás után nem módosítható. Ha szeretné megtekinteni, hogy milyen modellel van konfigurálva az Arcfelismerés, használja a [FaceList – Get] t a _returnRecognitionModel_ paraméter **true**értékkel való beállításával.

Tekintse meg a .NET-ügyfél függvénytárának következő kódrészletét.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Ez a kód egy nevű `My face collection`arc listát hoz létre, amely a _recognition_02_ modellt használja a szolgáltatások kinyeréséhez. Ha egy új észlelt arc fölé keres hasonló arcokat, az arc észlelését ([Szembenézés észlelése]) a _recognition_02_ -modell használatával kell észlelni. Az előző szakaszban leírtaknak megfelelően a modellnek konzisztensnek kell lennie.

Az [Szembenézés – hasonlók] ban nincs változás. az észleléshez csak a modell verzióját kell megadnia.

## <a name="verify-faces-with-specified-model"></a>Arcok ellenőrzése a megadott modellel

Az [Szembenézés – ellenőrzés] API ellenőrzi, hogy két arc ugyanahhoz a személyhez tartozik-e. Nem változik az API ellenőrzése a felismerési modellekkel kapcsolatban, de csak az azonos modellel észlelt arcok hasonlíthatók össze. Így a két arcot is a vagy `recognition_01` `recognition_02`a használatával kell észlelni.

## <a name="evaluate-different-models"></a>Különböző modellek kiértékelése

Ha össze szeretné hasonlítani a _recognition_01_ és a _recognition_02_ modelljeinek teljesítményét az adataihoz, a következőket kell tennie:

1. Hozzon létre két **PersonGroup**-t a _recognition_01_ és a _recognition_02_ .
1. A képadatokkal felderítheti az arcokat, és regisztrálja őket a Persons szolgáltatásban a két **PersonGroup**s esetében, és kiválthatja a betanítási folyamatot a [PersonGroup - Train] API használatával.
1. Tesztelje az [Face - Identify] mindkét **PersonGroup**, és hasonlítsa össze az eredményeket.

Ha általában a megbízhatósági küszöbértéket adja meg (a nulla és az egyik közötti érték, amely meghatározza, hogy a modellnek milyen mértékben kell azonosítania egy arcot), előfordulhat, hogy különböző küszöbértékeket kell használnia a különböző modellekhez. Az egyik modell küszöbértéke nem osztható meg egy másikhoz, és nem feltétlenül ugyanazt az eredményt fogja eredményezni.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan határozhatja meg a különböző Face Service API-kkal használandó felismerési modellt. Ezután kövessen egy rövid útmutatót a Arcfelismerés használatának megkezdéséhez.

* [Képeken lévő arcok észlelése](../quickstarts/csharp-detect-sdk.md)

[Szembenézés észlelése]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Szembenézés – hasonlók]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face - Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Szembenézés – ellenőrzés]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup – Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList – létrehozás]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList – Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList – létrehozás]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
