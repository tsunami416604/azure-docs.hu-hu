---
title: A modell – Face API megadása
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, mely face modell használata az Azure Face API-alkalmazás kiválasztása.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: 26ab3cb247309aa21791ca5a984f39ef40ce9a78
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249633"
---
# <a name="specify-a-face-detection-model"></a>Arcfelismerési modell megadása

Ez az útmutató bemutatja, hogyan kell megadnia a face modell az Azure Face API-t.

A Face API használja a machine learning-modellek műveletek végrehajtása az emberi arcok a képeken. A felhasználói visszajelzések és a kutatási fejlődésére alapuló modellek pontossága fejlesztésével, és ezek a fejlesztések adatmodell-frissítéseket, tudunk. A fejlesztők adja meg, a face modell melyik verzióját szeretnék használni, lehetősége van. a modell, amely legjobban megfelel a kérjünk a használat kiválaszthatnak.

Látogasson el lapunkra megtudhatja, hogyan adja meg a face modell bizonyos face műveletek. A Face API használja az arcfelismerés, amikor egy ARC képe alakítja más valamilyen, az adatok.

Ha nem tudja, hogy a legújabb modellre kell használni, ugorjon a [különböző modell kiértékelése](#evaluate-different-models) szakasz az új modell értékelése és az aktuális adatok használatával eredmények összehasonlítása.

## <a name="prerequisites"></a>Előfeltételek

Mesterséges Intelligencia arcfelismerés jelentésével ismernie kell. Ha nem, tekintse meg az arcok észlelése fogalmi útmutató vagy útmutatója:

* [Arcok észlelése fogalmak](../concepts/face-detection.md)
* [A kép arcok észlelése](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>A megadott modell arcok észlelése

Arcfelismerés megkeresi az emberi arcok határolókeret helyét, és azonosítja a vizuális arcrész. Kinyeri az arcfelismerési funkciókat, és a tárolja őket a későbbi használatra [felismerés](../concepts/face-recognition.md) műveleteket.

Használatakor a [A Face – észlelése] API-hoz, hozzárendelhet a modell a következőt a `detectionModel` paraméter. Az elérhető értékek a következők:

* `detection_01`
* `detection_02`

A kérelem URL-CÍMÉT a [A Face – észlelése] REST API-t fog kinézni:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

Az ügyféloldali kódtárat használja, ha az érték rendelhet `detectionModel` ad egy megfelelő karakterláncot. Ha meghagyja a hozzá nem rendelt, az API-t fogja használni az alapértelmezett modellverziója (`detection_01`). Tekintse meg a következő mintakód a .NET ügyféloldali kódtár.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Face személy megadott modell hozzáadása

A Face API face adatok kinyerése egy képet, és társítsa azt egy **személy** keresztül objektum a [is lehet PersonGroup személy – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API-t. Az API-hívás, megadhatja a modell ugyanúgy, mint a [A Face – észlelése].

Tekintse meg a következő mintakód a .NET ügyféloldali kódtár.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Ez a kód létrehoz egy **is lehet PersonGroup** azonosítójú `mypersongroupid` , és hozzáadja egy **személy** rá. Majd hozzáadja a egy oldallal a **személy** használatával a `detection_02` modell. Ha nem adja meg a *detectionModel* paramétert, az API-t fogja használni az alapértelmezett modellt `detection_01`.

> [!NOTE]
> Nem kell az azonos modell használata az összes arcok egy **személy** objektum, és nem kell használnia az ugyanazon modell új arcok észlelése összehasonlítandó egy **személy** objektum (az a [Face - Identify] API-t, a példában).

## <a name="add-face-to-facelist-with-specified-model"></a>A megadott modell FaceList face hozzáadása

Azt is beállíthatja a modell egy ARC hozzáadásakor egy meglévő **FaceList** objektum. Tekintse meg a következő mintakód a .NET ügyféloldali kódtár.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Ez a kód létrehoz egy **FaceList** nevű `My face collection` , és hozzáadja azt egy ARC a `detection_02` modell. Ha nem adja meg a *detectionModel* paramétert, az API-t fogja használni az alapértelmezett modellt `detection_01`.

> [!NOTE]
> Nem kell az azonos modell használata az összes arcok egy **FaceList** objektum, és nem kell használnia az ugyanazon modell új arcok észlelése összehasonlítandó egy **FaceList** objektum.

## <a name="evaluate-different-models"></a>Különböző modell értékelése

A különböző arcok észlelése modellek különböző feladatok vannak optimalizálva. Tekintse meg az alábbi táblázat a különbségeket áttekintését.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Alapértelmezett választás az összes arcok észlelése művelet. | Kiadás dátuma: 2019. május, és szükség esetén minden arcok észlelése művelet érhető el.
|Nem optimalizált kicsi, oldalnézet vagy határozatlan arcokat.  | A kisméretű, oldalnézet és határvonalú arcok pontosság jobb legyen. |
|Értéket ad eredményül, ha azok van megadva, a hibakeresés hívás az a attribútumok (fő testtartás, kor, érzelem és így tovább) face. |  Olyan tulajdonságok nem ad vissza.     |
|Értéket ad eredményül, ha azok van megadva, a hibakeresés hívás az a arcrész face.   | Arcrészek nem ad vissza.  |

A legjobb módszer annak teljesítményét összehasonlítása a `detection_01` és `detection_02` modelleket, hogy a minta adatkészlet használni őket. Azt javasoljuk, hogy hívása a [A Face – észlelése] API a lemezképek különböző különösen lemezképek rendezésére vagy az arcokat, nehezen látható, minden egyes modell használatával. Minden modell visszaadó arcok száma figyelmet fordítania.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan, adja meg a modell különböző Arcfelismerési API-k használata. Ezután kövesse a gyors üzembe helyezés arcfelismerés használatának megkezdéséhez.

* [Arcfelismerés képen egy (.NET SDK-val)](../quickstarts/csharp-detect-sdk.md)

[A Face – észlelése]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face - Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[FaceList - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
