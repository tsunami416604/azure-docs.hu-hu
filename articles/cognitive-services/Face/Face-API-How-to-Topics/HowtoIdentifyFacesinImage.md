---
title: 'Példa: Arcok azonosítása a képeken – Face API'
titleSuffix: Azure Cognitive Services
description: A Face API használatával arcokat azonosíthat a képeken.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: a26f7d6057f92fd3ab92405ecca6965dbd6e37ad
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129071"
---
# <a name="example-how-to-identify-faces-in-images"></a>Példa: Hogyan lehet arcokat azonosítani a képeken

Ez az útmutató bemutatja, hogyan lehet ismeretlen arcokat azonosítani az előre, ismert emberek alapján létrehozott PersonGroups használatával. A példák a Face API ügyfélkódtár használatával C# nyelven íródtak.

## <a name="concepts"></a>Alapelvek

Ha nem ismeri az útmutatóban használt alábbi fogalmakat, a [szószedetben](../Glossary.md) bármikor utánanézhet a jelentésüknek:

- Face – Detect
- Face – Identify
- PersonGroup

## <a name="preparation"></a>Előkészítés

Ebben a példában bemutatjuk a következőket:

- Hogyan történik a PersonGroup létrehozása – Ez a PersonGroup ismert emberek listáját tartalmazza.
- Arcok hozzárendelése az egyes személyekhez – Ezek az arcok kiindulópontként szolgálnak a személyek azonosításakor. Jól látható, előre néző arcokat ajánlott használni, amilyen a fényképes személyi igazolványban is van. Akkor jó a fénykép készlet, ha ugyanarról a személyről többféle testhelyzetben, ruhában és hajviselettel készült arc is van.

A minta bemutató elvégzéséhez szüksége lesz egy csomó képre:

- Néhány kép az illető arcáról. [Ide kattintva letöltheti a minta fényképeket](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) Anna, Bill és Clare képeivel.
- Egy sor tesztelési fényképre az azonosítási próbákhoz, amelyek vagy tartalmazzák Anna, Bill és Clare arcát vagy nem. Néhány mintaképeket a fenti hivatkozások közül is választhat.

## <a name="step-1-authorize-the-api-call"></a>1. lépés: API-hívás engedélyezése

A Face API minden meghívásához előfizetési kulcs szükséges. Ezt a kulcsot vagy egy lekérdezési karakterlánc paramétereként lehet átadni vagy a kérelem fejlécében lehet megadni. Az előfizetési kulcs lekérdezési karakterláncon keresztüli átadásához, példaként tekintse meg a [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) URL-kérését példaként:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Alternatív megoldásként az előfizetési kulcs a HTTP-kérés fejlécében is megadható: **ocp-apim-subscription-key: &lt;előfizetési kulcs&gt;** Ügyféloldali kódtár használata esetén az előfizetési kulcs átadása a FaceServiceClient osztály konstruktorán keresztül történik. Például:
 
```CSharp 
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```
 
Az előfizetési kulcs az Azure Portal Marketplace oldaláról szerezhető be. Lásd [Előfizetések](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step-2-create-the-persongroup"></a>2. lépés: PersonGroup létrehozása

Ebben a lépésben létrehoztuk a „MyFriends” nevű PersonGroup-ot, amelyben három személy van: Anna, Bill és Clare. Mindegyikükhöz több arcot jegyeztünk be. Az arcokat fel kell ismerni a képekről. Minden lépés elvégzése után, a kapott PersonGroup az alábbi ábrához hasonló:

![HowToIdentify1](../Images/group.image.1.jpg)

### <a name="21-define-people-for-the-persongroup"></a>2.1 Emberek definíciója a PersonGroup-hoz
A személy az identitás alapegysége. Egy személyhez tartozhat egy vagy több ismert arc is. A PersonGroup azonban emberek gyűjteménye, és minden személyt egy adott PersonGroupon belül definiálunk. Az azonosítás a PersonGroup alapján történik. Tehát a feladat egy PersonGroup létrehozása és benne emberek létrehozása például Anna, Bill és Clare.

Először egy új PersonGroupot kell létrehozni. Ezt a [PersonGroup – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API segítségével hajtjuk végre. A megfelelő ügyfélkódtár API a FaceServiceClient osztály CreatePersonGroupAsync metódusa. A csoport létrehozásához megadott csoportazonosító minden előfizetésre egyedi – a PersonGroups lekérése, frissítése és törlése más PersonGroup API-k használatával is lehetséges. A csoport definiálása után lehet embereket definiálni bennük a [PersonGroup Person – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API-val. Az ügykódtár metódusa a CreatePersonAsync. Miután a rendszer létrehozza a személyeket, mindegyikhez hozzáadhat arcokat.

```CSharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceServiceClient.CreatePersonGroupAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceServiceClient.CreatePersonAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step2-2"></a> 2.2 Arcok észlelése és hozzárendelése a megfelelő személyhez
Az észleléshez „POST” webkérést küldünk a [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API-nak, a képfájlt a HTTP-kérés törzsébe tesszük. Ügyfélkódtár használatakor az arcfelismerést a FaceServiceClient osztály DetectAsync metódusával hajtjuk végre.

Minden észlelt arcra meg lehet hívni a [PersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)-t, hogy hozzáadjuk a megfelelő személyhez.

A következő kód bemutatja a folyamatot, ahogyan a képen az arc észlelése után hozzáadja azt a személyhez:
```CSharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceServiceClient.AddPersonFaceAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
Figyelje meg, ha a kép több arcot is tartalmaz, akkor csak a legnagyobbat adja hozzá. Hozzáadhat más arcokat a személyhez úgy, hogy „targetFace = bal, felső, szélesség, magasság” formájú karakterláncot ad át a [PersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API targetFace lekérdezési paraméterében, vagy használhatja a az AddPersonFaceAsync metódus nem kötelező targetFace paraméterét más arcok hozzáadására. Minden személyhez adott arc kap egy egyedi maradandó arcazonosítót, amely használható a [PersonGroup Person – Delete Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) és [Face – Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) esetében is.

## <a name="step-3-train-the-persongroup"></a>3. lépés: PersonGroup betanítása

A PersonGroup csak betanítás után használható azonosítás végzésére. Sőt, személyek eltávolítása és hozzáadása valamint valamely személy bejegyzett arcának módosítása után is újra kell tanítani. A betanítást a [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API használatával végezzük. Az ügyfélkódtár használata esetén egyszerűen csak meg kell hívni a TrainPersonGroupAsync metódust:
 
```CSharp 
await faceServiceClient.TrainPersonGroupAsync(personGroupId);
```
 
A betanítás aszinkron folyamat. Előfordulhat, hogy még nem fejeződött be, amikor a TrainPersonGroupAsync metódus végzett. Szükség lehet a betanítási állapot lekérdezésére a [PersonGroup – Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) API vagy az ügyfélkódtár GetPersonGroupTrainingStatusAsync metódusa segítségével. A következő kód bemutat a PersonGroup betanítás befejeződésére várakozó egyszerű logikát:
 
```CSharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceServiceClient.GetPersonGroupTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != Status.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>4. lépés: Arc azonosítása definiált PersonGroup alapján

Azonosítás végzésekor a Face API képes kiszámítani a teszt arc csoporton belüli arcokhoz viszonyított hasonlóságát, és visszaadja a teszt archoz leginkább hasonló személy(eke)t. Ezt a [Face – Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API vagy az ügyfélkódtár IdentifyAsync metódusa végzi.

A teszt arcot az előző lépésekkel kell felismerni, utána az arcazonosítót második argumentumként megkapja az Identify API. Egyszerre több arcazonosító is azonosítható, az eredmény tartalmazni fogja valamennyi azonosítási eredményt. Alapértelmezés szerint az azonosítás csak egy személyt ad vissza, aki a teszt archoz leginkább hasonlít. Igény szerint megadhatja a nem kötelező maxNumOfCandidatesReturned paramétert ahhoz, hogy az azonosítás több jelöltet is adjon vissza.

A következő kód bemutatja az azonosítás folyamatát:

```CSharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceServiceClient.DetectAsync(s);
    var faceIds = faces.Select(face => face.FaceId).ToArray();
 
    var results = await faceServiceClient.IdentifyAsync(personGroupId, faceIds);
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
            var person = await faceServiceClient.GetPersonAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

A lépések befejezése után megpróbálhat azonosítani különböző arcokat és megnézheti, hogy Anna, Bill és Clare azonosíthatók-e jól, az arcfelismeréshez feltöltött képek alapján. Lásd az alábbi példákat:

![HowToIdentify2](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>5. lépés: A nagy méretű szolgáltatás kérése

Amint ismeretes, a PersonGroup legfeljebb 10 000 személyt képes tárolni az előző tervezés korlátai miatt.
További információ az akár milliós esetekről: [A nagy méretű szolgáltatás használata](how-to-use-large-scale.md).

## <a name="summary"></a>Összegzés

Ebben az útmutatóban megismerte, hogyan történik a PersonGroup létrehozása és egy személy azonosítása. Az alábbiakban röviden felidézzük a korábban ismertetett és bemutatott funkciókat:

- Arcfelismerés a [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) API használatával
- PersonGroups csoportok létrehozása a [PersonGroup – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API használatával
- Személyek létrehozása a [PersonGroup Person – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API használatával
- PersonGroup betanítása a [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API használatával
- Ismeretlen arcok azonosítása a PersonGroup alapján a [Face – Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API használatával

## <a name="related-topics"></a>Kapcsolódó témakörök

- [Hogyan lehet arcokat megtalálni a képen](HowtoDetectFacesinImage.md)
- [Hogyan lehet arcokat hozzáadni](how-to-add-faces.md)
- [Hogyan használható a nagy léptékű funkció](how-to-use-large-scale.md)
