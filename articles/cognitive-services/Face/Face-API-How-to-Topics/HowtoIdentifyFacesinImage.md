---
title: 'Példa: arcok azonosítása képekben – Face'
titleSuffix: Azure Cognitive Services
description: Ez az útmutató bemutatja, hogyan azonosíthatók az ismeretlen arcok a PersonGroup-objektumok használatával, amelyek előre ismert személyekből jönnek létre.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 0b1cf99fe6e2aa4d7fcb12c3fb96b10b42c7c0b7
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169919"
---
# <a name="example-identify-faces-in-images"></a>Példa: arcok felismerése képeken

Ez az útmutató bemutatja, hogyan azonosíthatók az ismeretlen arcok a PersonGroup-objektumok használatával, amelyek előre ismert személyekből jönnek létre. A minták az Azure Cognitive Services C# Face ügyféloldali kódtár használatával íródnak.

## <a name="preparation"></a>Előkészítés

Ez a példa a következőket mutatja be:

- PersonGroup létrehozása. Ez a PersonGroup az ismert személyek listáját tartalmazza.
- Arcok társítása az egyes személyekhez. Ezek az arcok a személyek azonosítására szolgáló alapkonfigurációként használatosak. Javasoljuk, hogy az arcok tiszta elülső nézeteit használja. Ilyen például a fénykép azonosítója. A fényképek jó gyűjteménye magában foglalja a különböző pózok, a ruházati színek és a frizurák azonos személyeit.

A minta bemutatásának elvégzéséhez készítse elő a következőket:

- Néhány kép az illető arcáról. Töltse le a Anna, a Bill és a Clare [minta fotóit](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) .
- Tesztelési fényképek sorozata. Előfordulhat, hogy a fényképek nem tartalmazzák Anna, Bill vagy Clare arcát. Ezek az azonosítás tesztelésére szolgálnak. Válassza ki az előző hivatkozáshoz tartozó mintaképeket is.

## <a name="step-1-authorize-the-api-call"></a>1\. lépés: API-hívás engedélyezése

A Face API minden meghívásához előfizetési kulcs szükséges. Ezt a kulcsot átadhatja egy lekérdezési karakterlánc paraméternek, vagy megadható a kérelem fejlécében. Az előfizetési kulcs lekérdezési karakterláncon keresztüli átadásához tekintse meg az [arc-észlelési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) kérelem URL-címét példaként:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Alternatív megoldásként írja be az előfizetési kulcsot a HTTP-kérelem fejlécében **OCP-APIM-Subscription-Key: &lt;előfizetési kulcs&gt;** .
Ha ügyféloldali kódtárat használ, az előfizetési kulcsot a FaceClient osztály konstruktorán keresztül adja át a rendszer. Példa:
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });
```
 
Az előfizetési kulcs beszerzéséhez nyissa meg az Azure Marketplace-t a Azure Portal. További információ: [előfizetések](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step-2-create-the-persongroup"></a>2\. lépés: PersonGroup létrehozása

Ebben a lépésben egy "MyFriends" nevű PersonGroup Anna, Bill és Clare nevet tartalmaz. Mindegyikükhöz több arcot jegyeztünk be. Az arcokat a képekből kell észlelni. Minden lépés elvégzése után, a kapott PersonGroup az alábbi ábrához hasonló:

![MyFriends](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>2,1. lépés: személyek definiálása a PersonGroup
A személy az identitás alapegysége. Egy személyhez tartozhat egy vagy több ismert arc is. A PersonGroup emberek gyűjteménye. Mindegyik személy egy adott PersonGroup belül van definiálva. Az azonosítás egy PersonGroup történik. A feladat egy PersonGroup létrehozása, majd a benne lévő személyek létrehozása, például Anna, Bill és Clare.

Először hozzon létre egy új PersonGroup a [PersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API használatával. A megfelelő ügyféloldali függvénytár API a FaceClient osztály CreatePersonGroupAsync metódusa. A csoport létrehozásához megadott csoportazonosító minden előfizetés esetében egyedi. A PersonGroups más PersonGroup API-kkal is beolvashatja, frissítheti vagy törölheti. 

Egy csoport definiálása után a [PersonGroup személyre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) szabott API-val megadhatja a benne lévő személyek listáját. Az ügykódtár metódusa a CreatePersonAsync. A létrehozásuk után hozzáadhat egy arcot az egyes személyekhez.

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
### <a name="step2-2"></a>2,2. lépés: az arcok észlelése és a megfelelő személyhez való regisztrálása
Az észleléshez „POST” webkérést küldünk a [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API-nak, a képfájlt a HTTP-kérés törzsébe tesszük. Az ügyféloldali kódtár használatakor az Arcfelismerés az észlelés egyikén keresztül történik. A FaceClient osztály aszinkron metódusai.

Minden észlelt arc esetében hívja a [PersonGroup személyt – adjon hozzá egy arcot](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) a megfelelő személyhez való hozzáadásához.

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
Ha a képen egynél több arc található, csak a legnagyobb arcot adja hozzá a rendszer. Más arcokat is hozzáadhat a személyhez. Adjon át egy karakterláncot "targetFace = Left, Top, Width, height" formátumban a [PersonGroup person – Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API targetFace lekérdezési paramétereként. A AddPersonFaceAsync metódus targetFace opcionális paramétere is felhasználható más arcok hozzáadására. A személyhez hozzáadott minden arc egyedi, megőrzött arc-azonosítót kap. Ezt az azonosítót [PersonGroup személy – az arc](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) és az arc törlése [– azonosíthatja](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>3\. lépés: PersonGroup betanítása

A PersonGroup meg kell tanítani az azonosítás elvégzéséhez a használatával. A PersonGroup egy személy hozzáadása vagy eltávolítása, illetve egy személy regisztrált arca szerkesztését követően kell újra betanítani. A betanítást a [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API használatával végezzük. Az ügyféloldali kódtár használatakor ez a TrainPersonGroupAsync metódus hívása:
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
A képzés aszinkron folyamat. Előfordulhat, hogy a TrainPersonGroupAsync metódus visszatérése után sem fejeződik be. Előfordulhat, hogy le kell kérdezni a betanítási állapotot. Használja az [PersonGroup beszerzése a betanítási állapot API-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) t vagy a GetPersonGroupTrainingStatusAsync metódust. A következő kód egy egyszerű logikát mutat be, amely a PersonGroup-képzés befejezésére vár:
 
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

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>4\. lépés: Arc azonosítása definiált PersonGroup alapján

Ha a Face szolgáltatás azonosítja az azonosításokat, az egy csoporton belüli összes arc között kiszámítja a tesztek hasonlóságát. A tesztelési felületen a leginkább összehasonlítható személyeket adja vissza. Ezt a folyamatot a [Face-IDENTIFY API-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) n vagy az ügyféloldali kódtár IdentifyAsync metódusán keresztül végezheti el.

A tesztelési arcot az előző lépések segítségével kell észlelni. Ezt követően a rendszer második argumentumként átadja a Face ID-t az azonosító API-nak. Egyszerre több arc azonosító is azonosítható. Az eredmény az összes azonosított eredményt tartalmazza. Alapértelmezés szerint az azonosítási folyamat csak egy olyan személyt ad vissza, amely a legjobban megfelel a tesztnek. Ha szeretné, megadhatja a maxNumOfCandidatesReturned választható paramétert, hogy az azonosítási folyamat több pályázót is visszaadjon.

A következő kód az azonosítási folyamatot mutatja be:

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

A lépések befejezése után próbálkozzon a különböző arcok azonosításával. Ellenőrizze, hogy az Anna, a Bill vagy a Clare arca helyesen azonosítható-e a Face észleléshez feltöltött képek alapján. Lásd az alábbi példákat:

![Különböző arcok azonosítása](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>5\. lépés: nagy léptékű kérelem

A PersonGroup az előző tervezési korlátozás alapján akár 10 000 személyt is megtarthatnak.
További információ az akár milliós esetekről: [A nagy méretű szolgáltatás használata](how-to-use-large-scale.md).

## <a name="summary"></a>Összefoglalás

Ebben az útmutatóban megtanulta, hogyan hozhat létre egy PersonGroup, és hogyan azonosítható egy személy. A következő funkciókat ismertetjük és mutatták be:

- Az arcok észlelése az [arc-észlelési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) API használatával.
- Hozzon létre PersonGroups a [PersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API használatával.
- Hozzon létre személyeket a [PersonGroup-létrehozó](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API használatával.
- PersonGroup betanítása a [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API használatával.
- Azonosítsa az ismeretlen arcokat a PersonGroup a [Face-IDENTIFY](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API használatával.

## <a name="related-topics"></a>Kapcsolódó témakörök

- [Arcfelismerés – fogalmak](../concepts/face-recognition.md)
- [Képeken lévő arcok észlelése](HowtoDetectFacesinImage.md)
- [Arcok hozzáadása](how-to-add-faces.md)
- [A nagyméretű szolgáltatás használata](how-to-use-large-scale.md)
