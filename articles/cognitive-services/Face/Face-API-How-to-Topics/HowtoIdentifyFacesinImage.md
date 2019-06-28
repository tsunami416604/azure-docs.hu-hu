---
title: 'Példa: Arcazonosítás képeken – Face API'
titleSuffix: Azure Cognitive Services
description: A Face API használatával arcokat azonosíthat a képeken.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: f02f6ebb83f7fbc274797e944d59a5f1e973075c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67438474"
---
# <a name="example-identify-faces-in-images"></a>Példa: Arcazonosítás képeken

Ez az útmutató bemutatja, hogyan ismeretlen arcazonosítás is lehet PersonGroup objektumok, ismert személyektől, előre létrehozott használatával. A minták nyelven írták C# az Azure Cognitive Services Face API ügyféloldali kódtár használatával.

## <a name="preparation"></a>Előkészítés

Ez a minta azt mutatja be:

- Hogyan hozhat létre olyan is lehet PersonGroup. Az is lehet PersonGroup ismert személyek listáját tartalmazza.
- Hogyan arcok hozzárendelése minden egyes személy. Ezek az arcok kiindulópontként személyek azonosítására szolgál. Azt javasoljuk, hogy használja-e téglalapot törölje elülső nézeteket. Ilyen például, egy fénykép-azonosítót. Fényképek jó halmaza ugyanazt az embert felületei különböző kockázatot, ruházati színek vagy hairstyles tartalmazza.

Ez a minta a bemutató elvégzéséhez előkészítése:

- Néhány kép az illető arcáról. [Töltse le a minta fényképek](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) Anna, számlázási és Clare.
- Teszt fényképek sorozata. A fényképek előfordulhat, hogy, vagy előfordulhat, hogy tartalmazza az arcok Anna, számlázási vagy Clare. Azonosító teszteléséhez használhatók. Néhány minta képeket is kijelölhet a fenti hivatkozással.

## <a name="step-1-authorize-the-api-call"></a>1\. lépés: Engedélyezze az API-hívás

A Face API minden meghívásához előfizetési kulcs szükséges. Ezt a kulcsot kell átadni a lekérdezési sztring paramétereként, vagy a kérelem fejlécében megadott. Az előfizetési kulcsot a lekérdezési karakterlánc keresztül haladnak, lásd: a kérelem URL-CÍMÉT a [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) példaként:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Alternatív megoldásként adja meg az előfizetési kulcsot a HTTP-kérelem fejléce **ocp-apim-subscription-key: &lt;Az előfizetői&gt;** .
Egy ügyféloldali kódtár használata esetén az előfizetési kulcs áthalad az FaceClient osztály a konstruktor. Példa:
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });
```
 
Az előfizetési kulcsot, keresse fel az Azure Marketplace-en az Azure Portalról. További információkért lásd: [előfizetések](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step-2-create-the-persongroup"></a>2\. lépés: Az is lehet PersonGroup létrehozása

Ebben a lépésben egy "MyFriends" nevű is lehet PersonGroup Anna, számlázási és Clare tartalmazza. Mindegyikükhöz több arcot jegyeztünk be. Az arcok fel kell deríteni a lemezképek alapján. Minden lépés elvégzése után, a kapott PersonGroup az alábbi ábrához hasonló:

![MyFriends](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>2\.1. lépés: Adja meg a személyeknek az is lehet PersonGroup.
A személy az identitás alapegysége. Egy személyhez tartozhat egy vagy több ismert arc is. Egy is lehet PersonGroup személyek gyűjteménye. Mindenki, aki egy adott is lehet PersonGroup van meghatározva. Azonosító egy is lehet PersonGroup alapján történik. A feladat, hogy hozzon létre egy is lehet PersonGroup, és hozza létre a személyek, például Anna, számlázási és Clare.

Először hozzon létre egy új is lehet PersonGroup használatával a [is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API-t. A megfelelő ügyféloldali kódtár API-t a FaceClient osztály CreatePersonGroupAsync módszer. A csoport azonosítója, amely a csoport létrehozásához megadott egy egyedülálló megoldás minden egyes előfizetés esetén. Akkor is is beolvasása, frissítése vagy törlése Persongroup más is lehet PersonGroup API-k használatával. 

Után egy csoport van definiálva, meghatározhatja a benne lévő személyek a [személy is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API-t. Az ügykódtár metódusa a CreatePersonAsync. Létrehozásuk után egy ARC adhat hozzá minden egyes személy.

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
### <a name="step2-2"></a> 2.2. lépés: Arcfelismerés, és regisztrálja őket a megfelelő személyekhez
Az észleléshez „POST” webkérést küldünk a [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API-nak, a képfájlt a HTTP-kérés törzsébe tesszük. Ha használja az ügyféloldali kódtár, arcfelismerés történik a DetectAsync metódus az FaceClient osztály segítségével.

Minden olyan lapot észlelt, hívja [is lehet PersonGroup személy – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) a megfelelő személynek történő hozzáadáshoz.

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
Ha a lemezkép lapot tartalmaz, csak a legnagyobb face egészül ki. A személy is hozzáadhat további arcokat. Adja át a karakterlánc formátuma "targetFace bal oldali, felül, szélessége, magasság =" való [is lehet PersonGroup személy – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API-k targetFace lekérdezési paraméter. Is használhatja a targetFace nem kötelező paraméter a AddPersonFaceAsync metódus hozzáadása más arcokat. Minden hozzáadott személy lapot kap egy megőrzött arcok egyedi azonosítót. Ezt az Alkalmazásazonosítót használhatja [is lehet PersonGroup személy – Face törlése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) és [arc – azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>3\. lépés: Az is lehet PersonGroup betanítása

Az is lehet PersonGroup azonosítójának használatával, végrehajtása előtt kell betanítani. Az is lehet PersonGroup kell lennie retrained, miután hozzáad vagy eltávolít minden olyan személy, vagy ha egy személy regisztrált face szerkesztése. A betanítást a [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API használatával végezzük. Használja az ügyféloldali kódtár, esetén a TrainPersonGroupAsync metódus hívása:
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
Képzési az egy aszinkron folyamat. Előfordulhat, hogy nem kell befejeződött volna után is a TrainPersonGroupAsync metódus adja vissza. Szüksége lehet a képzés állapotának lekérdezése. Használja a [is lehet PersonGroup - képzési állapotának beolvasása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) ügyfélkódtár API-t vagy GetPersonGroupTrainingStatusAsync metódust. A következő kód bemutatja egy egyszerű logikai is lehet PersonGroup Várakozás képzés Befejezés:
 
```csharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceClient.PersonGroup.GetTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != Status.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>4\. lépés: A face elleni egy meghatározott is lehet PersonGroup azonosítása

A Face API azonosítók hajt végre, ha kiszámítja az a hasonlóságot egy teszt arc csoporton belül az arcok között. A legtöbb összehasonlítható személyek számára a tesztelési face adja vissza. Ez a folyamat segítségével történik a [arc - azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API-t vagy az ügyféloldali kódtár a IdentifyAsync metódus.

A tesztelési face fel kell deríteni az előző lépések használatával. Ezután a face ID identification API egy második argumentumként átadott. Egyszerre több face azonosítók azonosítható legyen. Az eredmény tartalmazza az összes azonosított eredmény. Alapértelmezés szerint a szoftverazonosító folyamat, amely legjobban megfelel a teszt face csak egy személy adja vissza. Ha szeretné, adja meg a nem kötelező paraméter maxNumOfCandidatesReturned ahhoz, hogy az azonosító folyamat további jelöltek adja vissza.

A következő kód bemutatja a szoftverazonosító folyamat:

```csharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceClient.Face.DetectAsync(s);
    var faceIds = faces.Select(face => face.FaceId).ToArray();
 
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

Miután elvégezte a lépéseket, próbálja meg más arcokat azonosíthat. Tekintse meg, ha az arcok Anna, számlázási vagy Clare megfelelően azonosíthatók az arcfelismerés feltöltött képek megfelelően. Lásd az alábbi példákat:

![Különböző arcok azonosítása](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>5\. lépés: A nagyméretű méretezési kérelem

Egy is lehet PersonGroup az előző tervezési korlát alapján legfeljebb 10 000 személyek képes tárolni.
További információ az akár milliós esetekről: [A nagy méretű szolgáltatás használata](how-to-use-large-scale.md).

## <a name="summary"></a>Összefoglalás

Ebben az útmutatóban megismerhette a folyamat egy is lehet PersonGroup létrehozása és a egy személy azonosítására. Az alábbi szolgáltatások ismertetése és mutatja be:

- Arcfelismerés használatával a [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) API-t.
- Hozzon létre Persongroup a [is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API-t.
- Személyek létrehozása használatával a [személy is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API-t.
- Egy is lehet PersonGroup betanításához használatával a [is lehet PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API-t.
- Az is lehet PersonGroup elleni ismeretlen arcokat azonosíthat használatával a [arc - azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API-t.

## <a name="related-topics"></a>Kapcsolódó témakörök

- [Arcok felismerése fogalmak](../concepts/face-recognition.md)
- [A kép arcok észlelése](HowtoDetectFacesinImage.md)
- [Arcok hozzáadása](how-to-add-faces.md)
- [A nagy méretű szolgáltatással](how-to-use-large-scale.md)
