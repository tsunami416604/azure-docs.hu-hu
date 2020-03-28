---
title: 'Példa: Arcok azonosítása a képeken – Arc'
titleSuffix: Azure Cognitive Services
description: Ez az útmutató bemutatja, hogyan lehet azonosítani az ismeretlen arcokat a PersonGroup objektumok használatával, amelyek et ismert személyektől hoztak létre előre.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 0b1cf99fe6e2aa4d7fcb12c3fb96b10b42c7c0b7
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169919"
---
# <a name="example-identify-faces-in-images"></a>Példa: Arcok azonosítása a képeken

Ez az útmutató bemutatja, hogyan lehet azonosítani az ismeretlen arcokat a PersonGroup objektumok használatával, amelyek et ismert személyektől hoztak létre előre. A minták c# nyelven vannak megírva az Azure Cognitive Services Face ügyfélkódtár használatával.

## <a name="preparation"></a>Előkészítés

Ez a minta a következőket mutatja be:

- PersonGroup létrehozása. Ez a személycsoport ismert személyek listáját tartalmazza.
- Arcok hozzárendelése minden személyhez. Ezek az arcok kiindulási alapként szolgálnak az emberek azonosítására. Azt javasoljuk, hogy az arcok tiszta frontális nézeteit használja. Erre példa a fényképes igazolvány. Egy jó sor fotó tartalmazza arcok ugyanazon személy különböző pózok, ruházati színek, vagy frizurák.

A minta bemutatásához készítsük el a következőket:

- Néhány kép az illető arcáról. [Töltsön le mintafotókat](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) Anna, Bill és Clare számára.
- Egy sor tesztfotó. Lehet, hogy a képeken anna, Bill vagy Clare arca van. Az azonosítás tesztelésére használják őket. Is válasszon néhány minta képeket az előző linkre.

## <a name="step-1-authorize-the-api-call"></a>1. lépés: API-hívás engedélyezése

A Face API minden meghívásához előfizetési kulcs szükséges. Ez a kulcs átadható egy lekérdezési karakterlánc paraméteren, vagy megadható a kérelem fejlécében. Ha az előfizetési kulcsot lekérdezési karakterláncon keresztül szeretné átadni, tekintse meg a [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) mint példa kérés URL-címét:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Másik lehetőségként adja meg az előfizetési kulcsot az **ocp-apim-subscription-key HTTP-kérelemfejlécben: &lt;Előfizetési kulcs.&gt;**
Ügyfélkódtár használata esetén az előfizetési kulcs a FaceClient osztály konstruktorán keresztül kerül átadásra. Példa:
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });
```
 
Az előfizetési kulcs beszerezéséhez nyissa meg az Azure Marketplace-t az Azure Portalon. További információ: [Előfizetések](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step-2-create-the-persongroup"></a>2. lépés: PersonGroup létrehozása

Ebben a lépésben a "MyFriends" nevű PersonGroup tartalmazza Annát, Billt és Clare-t. Mindegyikükhöz több arcot jegyeztünk be. Az arcokat a képekből kell észlelni. Minden lépés elvégzése után, a kapott PersonGroup az alábbi ábrához hasonló:

![Barátaim](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>2.1. lépés: Személyek meghatározása a Személycsoport számára
A személy az identitás alapegysége. Egy személyhez tartozhat egy vagy több ismert arc is. A PersonGroup emberek gyűjteménye. Minden személy egy adott személycsoporton belül van definiálva. Az azonosítás egy persongroup ellen történik. A feladat egy PersonGroup létrehozása, majd a benne lévő személyek létrehozása, például Anna, Bill és Clare.

Először hozzon létre egy új PersonGroup segítségével a PersonGroup - Create API.First, create a new PersonGroup by using the [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API. A megfelelő ügyfélkódtár API a FaceClient osztály CreatePersonGroupAsync metódusa. A csoport létrehozásához megadott csoportazonosító minden egyes előfizetéshez egyedi. A PersonGroups csoportokat más PersonGroup API-k használatával is lekaphatja, frissítheti vagy törölheti. 

A csoport definiálása után a [PersonGroup Person – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API használatával definiálhat személyeket. Az ügykódtár metódusa a CreatePersonAsync. A létrehozás után minden személyhez hozzáadhat egy arcot.

```csharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceClient.PersonGroupPerson.CreateAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step-22-detect-faces-and-register-them-to-the-correct-person"></a><a name="step2-2"></a>2.2. lépés: Arcok észlelése és regisztrálása a megfelelő személynek
Az észleléshez „POST” webkérést küldünk a [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API-nak, a képfájlt a HTTP-kérés törzsébe tesszük. Az ügyfélkódtár használatakor az arcfelismerés az Észlelés. A FaceClient osztály aszinkron metódusai.

Minden észlelt archoz hívja meg a [PersonGroup Person – Add Face -t,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) hogy a megfelelő személyhez adja hozzá.

A következő kód bemutatja a folyamatot, ahogyan a képen az arc észlelése után hozzáadja azt a személyhez:

```csharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
Ha a kép egynél több arcot tartalmaz, csak a legnagyobb lap kerül hozzáadásra. A személyhez más arcokat is hozzáadhat. Adjon át egy karakterláncot "targetFace = left, top, width, height" formátumban a [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API targetFace lekérdezési paraméterének. Az AddPersonFaceAsync metódus targetFace választható paraméterével is hozzáadhat más arcokat. A személyhez hozzáadott minden arc egyedi, megőrzött arcazonosítót kap. Ezt az azonosítót használhatja a [PersonGroup Person – Arc](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) és arc törlése [– Azonosítás](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>3. lépés: PersonGroup betanítása

A persongroup kell képezni, mielőtt egy azonosítót lehet végrehajtani használatával. A PersonGroup csoportot át kell képezni, miután hozzáad vagy eltávolít egy személyt, vagy ha módosítja egy személy regisztrált arcát. A betanítást a [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API használatával végezzük. Az ügyfélkódtár használata kor a TrainPersonGroupAsync metódus hívása:
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
A betanítás aszinkron folyamat. Lehet, hogy nem fejeződött be még a TrainPersonGroupAsync metódus visszatérése után sem. Előfordulhat, hogy le kell kérdeznie a betanítási állapotot. Használja a [PersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) API-t vagy az ügyféltár GetPersonGroupTrainingStatusAsync metódusát. A következő kód egy egyszerű logikát mutat be a PersonGroup képzés befejezésére való várakozásegyszerű logikájára:
 
```csharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceClient.PersonGroup.GetTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != TrainingStatusType.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>4. lépés: Arc azonosítása definiált PersonGroup alapján

Amikor a Face szolgáltatás azonosítókat hajt végre, kiszámítja a tesztarc hasonlóságát a csoporton belüli összes arc között. A legösszehasonlíthatóbb személyeket adja vissza a tesztfelülethez. Ez a folyamat a [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API vagy az ügyfélkódtár IdentifyAsync metódusán keresztül történik.

A tesztfelületet az előző lépésekkel kell észlelni. Ezután az arcazonosítót a rendszer második argumentumként adja át az azonosító API-nak. Egyszerre több arcazonosító is azonosítható. Az eredmény tartalmazza az összes azonosított eredményt. Alapértelmezés szerint az azonosítási folyamat csak egy olyan személyt ad vissza, aki a legjobban megfelel a tesztarcnak. Ha szeretné, adja meg a nem kötelező paraméter maxNumOfCandidatesReturned, hogy az azonosítási folyamat több jelöltet ad vissza.

A következő kód bemutatja az azonosítási folyamatot:

```csharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceClient.Face.DetectWithStreamAsync(s);
    var faceIds = faces.Select(face => face.FaceId.Value).ToArray();
 
    var results = await faceClient.Face.IdentifyAsync(faceIds, personGroupId);
    foreach (var identifyResult in results)
    {
        Console.WriteLine("Result of face: {0}", identifyResult.FaceId);
        if (identifyResult.Candidates.Length == 0)
        {
            Console.WriteLine("No one identified");
        }
        else
        {
            // Get top 1 among all candidates returned
            var candidateId = identifyResult.Candidates[0].PersonId;
            var person = await faceClient.PersonGroupPerson.GetAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

Miután befejezte a lépéseket, próbálja azonosítani a különböző arcokat. Nézd meg, hogy Anna, Bill vagy Clare arcai helyesen azonosíthatók-e az arcfelismerésre feltöltött képek alapján. Lásd az alábbi példákat:

![Azonosítsa a különböző arcokat](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>5. lépés: Igény a nagyszabású

Egy személycsoport legfeljebb 10 000 személyt tarthat el az előző tervezési korlátozás alapján.
További információ az akár milliós esetekről: [A nagy méretű szolgáltatás használata](how-to-use-large-scale.md).

## <a name="summary"></a>Összefoglalás

Ebben az útmutatóban megtanulta a PersonGroup létrehozásának és a személy azonosításának folyamatát. A következő jellemzőket ismertették és mutatták be:

- Arcok észlelése a [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) API használatával.
- Személycsoportok létrehozása a [PersonGroup – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API használatával.
- Személyek létrehozása a [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API használatával.
- Személycsoport betanítása a [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API használatával.
- Azonosítsa az ismeretlen arcokat a PersonGroup ellen a [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API használatával.

## <a name="related-topics"></a>Kapcsolódó témakörök

- [Arcfelismerési fogalmak](../concepts/face-recognition.md)
- [Arcok felismerése a képeken](HowtoDetectFacesinImage.md)
- [Arcok hozzáadása](how-to-add-faces.md)
- [A nagy léptékű funkció használata](how-to-use-large-scale.md)
