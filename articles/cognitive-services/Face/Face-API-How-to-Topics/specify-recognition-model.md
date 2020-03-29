---
title: Felismerési modell megadása - Arc
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan választhatja ki, hogy melyik felismerési modellt használja az Azure Face-alkalmazással.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: longl
ms.openlocfilehash: da9ad5576d146c007e45124668875e9681860ce6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76938823"
---
# <a name="specify-a-face-recognition-model"></a>Arcfelismerési modell megadása

Ez az útmutató bemutatja, hogyan adhat meg egy arcfelismerési modellt az Arcfelismerés, az azonosítás és a hasonlóság kereséséhez az Azure Face szolgáltatás használatával.

A Face szolgáltatás gépi tanulási modellek segítségével hajtja végre a képeken lévő emberi arcokon végzett műveleteket. Modelleink pontosságát továbbra is javítjuk az ügyfelek visszajelzései és a kutatás terén elért eredmények alapján, és ezeket a fejlesztéseket modellfrissítésekként biztosítjuk. A fejlesztők nek lehetőségük van megadni, hogy az arcfelismerő modell melyik verzióját szeretnék használni; kiválaszthatják azt a modellt, amely a legjobban megfelel a használati esetüknek.

Ha Ön új felhasználó, javasoljuk, hogy a legújabb modellt használja. Olvasson tovább, hogy megtudja, hogyan adhatja meg azt a különböző Face műveletek, míg a modell ütközések elkerülése érdekében. Ha ön egy haladó felhasználó, és nem biztos abban, hogy át kell-e váltania a legújabb modellre, ugorjon a [Különböző modellek kiértékelése](#evaluate-different-models) szakaszra az új modell kiértékeléséhez és az eredmények összehasonlításához az aktuális adatkészlet használatával.

## <a name="prerequisites"></a>Előfeltételek

Ismernie kell a a mi arcfelismerés és -azonosítás fogalmát. Ha nem, először tekintse meg az alábbi útmutatóútmutatókat:

* [Arcok észlelése a képen](HowtoDetectFacesinImage.md)
* [A képen lévő arcok azonosítása](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Lapok észlelése megadott modellel

Az arcfelismerés azonosítja az emberi arcok vizuális tájékozódási pontjait, és megtalálja a határolókeret helyeit. Azt is kibontja az arc jellemzőit, és tárolja őket az azonosításhoz. Mindezek az információk egy arc ábrázolását képezik.

A felismerési modell a lapjellemzők kibontásakor használatos, így a Hibakeresés művelet végrehajtásakor megadhat egy modellverziót.

A [Face - Detect] API használatakor rendelje `recognitionModel` hozzá a modellverziót a paraméterrel. A rendelkezésre álló értékek a következők:

* `recognition_01`
* `recognition_02`

Szükség esetén megadhatja a _returnRecognitionModel_ paramétert (alapértelmezett **hamis**) annak jelzésére, hogy _a dajka_ értéket válaszként adja-e vissza. Tehát a [Face - Detect] REST API kérelem URL-címe így fog kinézni:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Ha az ügyfélkönyvtárat használja, az értéket `recognitionModel` a verziót képviselő karakterlánc átadásával rendelheti hozzá.
Ha nem rendeli hozzá, a rendszer az alapértelmezett modellverziót (_recognition_01)_ fogja használni. Tekintse meg a következő kódpéldát a .NET ügyféltárhoz.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Lapok azonosítása megadott modellel

A Face szolgáltatás kinyerheti az arcadatokat egy képből, és társíthatja azt egy **Személy** objektumhoz (például az [Arc hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API-híváson keresztül), és több **személyobjektum** együtt tárolható egy **PersonGroupban.** Ezután egy új arc összehasonlítható egy **PersonGroup** csoporttal (a [Face - Identify] hívással), és a csoporton belüli egyező személy azonosítható.

A **PersonGroup csoportnak** egy egyedi felismerési modellel kell rendelkeznie az összes **személyhez,** és ezt a csoport[(PersonGroup - Create] vagy [LargePersonGroup - Create]) létrehozásakor a `recognitionModel` paraméter használatával adhatja meg. Ha nem adja meg ezt `recognition_01` a paramétert, a rendszer az eredeti modellt használja. A csoport mindig azt a felismerési modellt fogja használni, amivel létrehozták, és az új lapok a modellhez lesznek társítva, amikor hozzáadódnak; ez nem módosítható egy csoport létrehozása után. Ha meg szeretné tekinteni, hogy egy **PersonGroup** milyen modellel van konfigurálva, használja a [PersonGroup - Get] API-t úgy, hogy a _returnRecognitionModel_ paraméter **értéke igaz**legyen.

Tekintse meg a következő kódpéldát a .NET ügyféltárhoz.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

Ebben a kódban létrejön `mypersongroupid` egy persongroup azonosítóval, és úgy van beállítva, hogy a recognition_02 modell használatával kibontsa a face features.In this code, a **PersonGroup** with ID is created, and it is set up to use the _recognition_02_ model to extract face features.

Ennek megfelelően meg kell adnia, hogy melyik modellt kell használnia az arcok összehasonlításához a **PersonGroup** -hoz (a [Face - Detect] API-n keresztül). A használt modellnek mindig konzisztensnek kell lennie a **PersonGroup**konfigurációjával; ellenkező esetben a művelet nem kompatibilis modellek miatt sikertelen lesz.

Nincs változás a [Face - Api azonosítása;] csak meg kell adnia a modell verzióját az észlelésben.

## <a name="find-similar-faces-with-specified-model"></a>Hasonló lapok keresése megadott modellel

A hasonlóságkereséshez felismerési modellt is megadhat. A modellverziót hozzárendelheti, `recognitionModel` amikor az arclistát [facelist - Create] API vagy [LargeFaceList - Create]. Ha nem adja meg ezt `recognition_01` a paramétert, a rendszer az eredeti modellt használja. Az arclista mindig azt a felismerési modellt fogja használni, amellyel létrehozták, és az új arcok a modellhez lesznek társítva, amikor hozzáadódnak; ez nem módosítható a létrehozás után. Ha meg szeretné tekinteni, hogy milyen modellel van konfigurálva egy arclista, használja a [FaceList - Get] API-t úgy, hogy a _returnRecognitionModel_ paraméter **értéke igaz**legyen.

Tekintse meg a következő kódpéldát a .NET ügyféltárhoz.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Ez a kód létrehoz `My face collection`egy arclistát, amelyet a _recognition_02_ modell használatával hoz létre a szolgáltatások kinyeréséhez. Ha az arclistában egy új észlelt archoz hasonló arcokat keres, akkor ezt az arcot a _recognition_02_ modell használatával kell észlelnie ([Face - Detect]). Az előző szakaszhoz, a modellnek is konzisztensnek kell lennie.

Nincs változás a [Face - Find Similar] API-ban; csak az észlelésben adja meg a modellverziót.

## <a name="verify-faces-with-specified-model"></a>Lapok ellenőrzése a megadott modellel

A [Face - Ellenőrizze,] hogy az API ellenőrzi, hogy két arc ugyanahhoz a személyhez tartozik-e. Nincs változás az Ellenőrzés API-ban a felismerési modellek tekintetében, de csak az ugyanazzal a modellel észlelt arcokat hasonlíthatja össze. Tehát, a kettő szembenéz akarat mindkettő `recognition_01` szükség `recognition_02`-hoz lenni kinyomoz használ vagy .

## <a name="evaluate-different-models"></a>Különböző modellek értékelése

Ha össze szeretné hasonlítani az _recognition_01_ és _recognition_02_ modellek teljesítményét az adatokon, a következőket kell a következőkre hasonlítania:

1. Hozzon létre két **PersonGroup** _s-t recognition_01,_ illetve _recognition_02._
1. A képadatok segítségével észlelheti az arcokat, és regisztrálhatja őket **a Person**s-ben e két **PersonGroup**s esetében, és elindíthatja a képzési folyamatot a [PersonGroup - Train] API-val.
1. Teszt [arccal - Azonosítsa] mindkét **PersonGroup**s-t, és hasonlítsa össze az eredményeket.

Ha általában meg a megbízhatósági küszöbértéket (egy érték között nulla és egy, amely meghatározza, hogy mennyire magabiztos a modell kell azonosítani egy arcot), előfordulhat, hogy különböző küszöbértékeket kell használnia a különböző modellek. Az egyik modell küszöbértékét nem kell megosztani egy másikkal, és nem feltétlenül ugyanazt az eredményt eredményezi.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan adhatja meg a különböző Face szolgáltatás API-kkal használandó felismerési modellt. Ezután kövesse a rövid útmutatót az arcfelismerés használatának megkezdéséhez.

* [Lap .NET SDK](../Quickstarts/csharp-sdk.md)
* [Arc Python SDK](../Quickstarts/python-sdk.md)
* [Arc Go SDK](../Quickstarts/go-sdk.md)

[Face – Detect]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Hasonló keresése]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face – Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Arc - Ellenőrzés]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Lefésedel]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - létrehozás]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList - létrehozás]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
