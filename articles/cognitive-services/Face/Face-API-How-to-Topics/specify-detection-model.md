---
title: Észlelési modell megadása - Arc
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan választhatja ki, hogy melyik arcfelismerő modellt használja az Azure Face-alkalmazással.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: 40ca1dbf981c5a9025cf5a0bac6b007709d69a77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76934579"
---
# <a name="specify-a-face-detection-model"></a>Arcfelismerési modell megadása

Ez az útmutató bemutatja, hogyan adhat meg egy arcfelismerő modellt az Azure Face szolgáltatáshoz.

A Face szolgáltatás gépi tanulási modellek segítségével hajtja végre a képeken lévő emberi arcokon végzett műveleteket. Modelleink pontosságát továbbra is javítjuk az ügyfelek visszajelzései és a kutatás terén elért eredmények alapján, és ezeket a fejlesztéseket modellfrissítésekként biztosítjuk. A fejlesztők nek lehetőségük van megadni, hogy az arcfelismerő modell melyik verzióját szeretnék használni; kiválaszthatják azt a modellt, amely a legjobban megfelel a használati esetüknek.

Olvasson tovább, hogy megtudja, hogyan adja meg az arcfelismerési modell bizonyos arcműveletek. A Face szolgáltatás arcfelismerést használ, amikor egy arc képét más adatformává alakítja.

Ha nem biztos abban, hogy a legújabb modellt kell-e [használnia,](#evaluate-different-models) ugorjon a Különböző modellek kiértékelése szakaszra az új modell kiértékeléséhez és az eredmények összehasonlításához az aktuális adatkészlet használatával.

## <a name="prerequisites"></a>Előfeltételek

Ismernie kell a a mi arcfelismerés fogalmát. Ha nem, tekintse meg az arcfelismerés fogalmi útmutatóját vagy útmutatóját:

* [Arcfelismerési fogalmak](../concepts/face-detection.md)
* [Arcok észlelése a képen](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Lapok észlelése megadott modellel

Az Arcfelismerés megkeresi az emberi arcok határolódobozának helyét, és azonosítja a vizuális tereptárgyakat. Kivonja az arc jellemzőit, és tárolja őket későbbi [felhasználásra a felismerési](../concepts/face-recognition.md) műveletekben.

A Face [- Detect] API használatakor hozzárendelheti `detectionModel` a modellverziót a paraméterhez. A rendelkezésre álló értékek a következők:

* `detection_01`
* `detection_02`

A Face – A REST API észlelése a következőként fog kinézni a [face - Detect] REST API-hoz:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

Ha az ügyfélkönyvtárat használja, az értéket `detectionModel` a megfelelő karakterlánc átadásával rendelheti hozzá. Ha nem rendeli hozzá, az API az alapértelmezett`detection_01`modellverziót ( ) fogja használni. Tekintse meg a következő kódpéldát a .NET ügyféltárhoz.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Lap hozzáadása a megadott modellel rendelkező személyhez

A Face szolgáltatás kinyerheti az arcadatokat egy lemezképből, és társíthatja azt egy **személyobjektumhoz** a [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API-n keresztül. Ebben az API-hívásban ugyanúgy adhatja meg az észlelési modellt, mint a [Face - Detect].

Tekintse meg a következő kódpéldát a .NET ügyféltárhoz.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Ez a kód létrehoz egy `mypersongroupid` **személycsoportot** azonosítóval, és hozzáad egy **személyt.** Ezután hozzáad egy arcot ehhez `detection_02` a **személyhez** a modell használatával. Ha nem adja meg az *detectionModel* paramétert, az `detection_01`API az alapértelmezett modellt fogja használni.

> [!NOTE]
> Nem kell ugyanazt az észlelési modellt használnia egy **Személy** objektum összes arcához, és nem kell ugyanazt az észlelési modellt használnia, amikor új arcokat észlel egy **személyobjektummal** (például a [Face - Identify] API-ban).

## <a name="add-face-to-facelist-with-specified-model"></a>Laphozzáadása a FaceList listához a megadott modellel

Észlelési modellt akkor is megadhat, ha arcot ad hozzá egy meglévő **FaceList-objektumhoz.** Tekintse meg a következő kódpéldát a .NET ügyféltárhoz.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Ez a kód létrehoz `My face collection` egy **FaceList** nevű, `detection_02` és hozzáteszi, hogy egy arc hozzá a modell. Ha nem adja meg az *detectionModel* paramétert, az `detection_01`API az alapértelmezett modellt fogja használni.

> [!NOTE]
> Nem kell ugyanazt az észlelési modellt használnia a **FaceList-objektum** összes arcához, és nem kell ugyanazt az észlelési modellt használnia, amikor új lapokat észlel egy **FaceList-objektummal** való összehasonlításhoz.

## <a name="evaluate-different-models"></a>Különböző modellek értékelése

A különböző arcfelismerő modellek különböző feladatokra vannak optimalizálva. A különbségek áttekintését az alábbi táblázatban találja.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Alapértelmezett beállítás az összes arcfelismerő művelethez. | 2019 májusában jelenik meg, és opcionálisan elérhető az összes arcfelismerési műveletben.
|Nincs kicsi, oldalnézeti vagy elmosódott lapokra optimalizálva.  | Nagyobb pontosság kicsi, oldalnézetű és elmosódott lapokon. |
|Arcattribútumokat (fejpóz, kor, érzelem és így tovább) ad vissza, ha azok meg vannak adva az észlelési hívásban. |  Nem ad vissza arcattribútumokat.     |
|Az arctájképeket adja vissza, ha azok meg vannak adva az észlelési hívásban.   | Nem tér vissza arc tereptárgyak.  |

A legjobb módja annak, hogy `detection_01` `detection_02` összehasonlítsa a teljesítményét a és a modellek, hogy használja őket egy minta adatkészleten. Javasoljuk, hogy hívja meg a [Face - Detect] API-t a különböző képeken, különösen a sok arc vagy az arcok, amelyek nehezen látható, az egyes észlelési modellek használatával. Ügyeljen arra, hogy az egyes modellek hány arcot adjanak vissza.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan adja meg a különböző Face API-kkal használandó észlelési modellt. Ezután kövesse a rövid útmutatót az arcfelismerés használatának megkezdéséhez.

* [Lap .NET SDK](../Quickstarts/csharp-sdk.md)
* [Arc Python SDK](../Quickstarts/python-sdk.md)
* [Arc Go SDK](../Quickstarts/go-sdk.md)

[Face – Detect]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face – Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
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
