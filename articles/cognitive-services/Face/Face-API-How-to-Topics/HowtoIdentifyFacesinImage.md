---
title: Lapok képek Arcfelismerési API-val azonosítására |} Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Kognitív szolgáltatásban a Arcfelismerési API segítségével azonosíthatja a képek felületei.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 3f75db176055d9f784ec978497d7cae077ff629f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349543"
---
# <a name="how-to-identify-faces-in-images"></a>A képek lapok azonosítása

Ez az útmutató bemutatja, hogyan ismeretlen lapok használatával PersonGroups, ismert személyek előre létrehozott azonosításához. A minták a Arcfelismerési API-ügyfél szalagtárat használó C# nyelven íródtak.

## <a name="concepts"></a> Alapfogalmak

Ha nem ismeri a jelen útmutató a következő fogalmakat, keressen a definíciók a [szószedet](../Glossary.md) bármikor:

- Szembesülhetnek - észlelése
- Szembesülhetnek – azonosítása
- PersonGroup

## <a name="preparation"></a> Előkészítése

A példában bemutatjuk a következőket:

- Egy PersonGroup - létrehozása a PersonGroup ismert személyek listáját tartalmazza.
- Lapok hozzárendelése minden olyan személy - e lapok kiindulópontként azonosítására szolgálnak személyeket. Javasoljuk, hogy használjon egyértelmű első lapokon, mint például a fényképes ID azonosítójával. Fényképek jó készlete ugyanaz a személy különböző kockázatot, munkaruházat színek vagy haj stílusok felületei kell tartalmaznia.

Ez a minta a bemutató elvégzéséhez elő kell készíteni a álló, lemezcsoport típusú képek:

- A személy arcfelismerési néhány fényképeket. [Ide kattintva letöltheti a minta fényképek](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) Anna, számlázási és Clare.
- Teszt fényképek, így nem tartalmazhat Anna, számlázási vagy Clare felületei vagy több azonosító tesztelésére vesz igénybe. Néhány példa képet is választhat az előző kapcsolat.

## <a name="step1"></a> 1. lépés: Engedélyezze az API-hívás

Minden hívás a Arcfelismerési API-val egy előfizetés kulcs szükséges. Ez a kulcs vagy a lekérdezési karakterlánc paraméterként keresztül történő továbbítása során, vagy a kérelem fejlécében megadott. Az Előfizetés kulcs lekérdezési karakterlánc keresztül továbbítani, tekintse meg a kérelem URL-címe a [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) példa:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Alternatív megoldásként az Előfizetés kulcs is megadható a kérelem HTTP-fejlécben: **ocp-apim-előfizetés-kulcs: &lt;előfizetés kulcs&gt;**  egy ügyféloldali kódtár használata esetén az Előfizetés kulcs átadott keresztül FaceServiceClient osztály konstruktor. Példa:
 
```CSharp 
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```
 
Az előfizetés kulcsot az Azure-portálon a piactér lapján lehet lekérni. Lásd: [előfizetések](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step2"></a> 2. lépés: A PersonGroup létrehozása

Ebben a lépésben létrehozott egy "MyFriends", amely tartalmazza a személyek három nevű PersonGroup: Anna, számlázási és Clare. Az egyes több lapok regisztrálva van. A lapok kell észlelhető a lemezképeket. Végül az alábbi lépések közül választhat egy PersonGroup, például az alábbi képen:

![HowToIdentify1](../Images/group.image.1.jpg)

### <a name="step2-1"></a> 2.1 a PersonGroup a személyek meghatározása
Egy személy egy alapvető egysége azonosítása. Egy személy regisztrált egy vagy több ismert felületei is rendelkezik. Azonban a PersonGroup személyek gyűjteménye, és minden olyan személy egy adott PersonGroup van meghatározva. Az azonosító egy PersonGroup elleni történik. Igen a feladata a hozzon létre egy PersonGroup, és hozza létre az emberek, például Anna, számlázási és Clare.

Először is szeretne létrehozni egy új PersonGroup. Ez a végrehajtása a [PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API. A megfelelő ügyféloldali kódtár API a FaceServiceClient osztály CreatePersonGroupAsync mód. A csoport azonosító megadása a csoport létrehozásához az egyes előfizetésekhez egyedi – is beolvasása, frissítése vagy törlése PersonGroups más PersonGroup API-k használatával. Után egy csoport van definiálva, személyek majd definiálható belül használatával a [PersonGroup személy - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API. Az ügyfél könyvtár metódus CreatePersonAsync. Miután jönnek létre arcfelismerési adhat hozzá minden olyan személy.

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
### <a name="step2-2"></a> 2.2 észlelése lapokat, és regisztrálja őket a megfelelő személy
Észlelési végezhető el a "POST" webes kérelmet küld a [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API-t a képfájl, a HTTP-kérés törzsében. Az ügyféloldali kódtár használata esetén arcfelismerési észlelés végrehajtása az FaceServiceClient osztály DetectAsync metódusával.

A minden lapot észlelt hívása [PersonGroup személy – hozzáadása Arcfelismerési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) veheti fel a megfelelő személy.

A következő kód bemutatja a lemezkép egy ARC észleli, és adja hozzá a személy folyamatát:
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
Figyelje meg, hogy ha a lemezkép lapot tartalmaz, csak a legnagyobb arcfelismerési kerül. Hozzáadhat más lapok személy úgy, hogy a karakterlánc formátuma "targetFace balra, felső, szélesség, magasság =" a [PersonGroup személy - hozzáadása Arcfelismerési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API targetFace lekérdezési paramétert, vagy használja a targetFace nem kötelező paraméter a AddPersonFaceAsync metódus más lapok hozzáadni. Minden személy hozzá lapot kap egy megőrzött arcfelismerési egyedi Azonosítót, amelynek használható [PersonGroup személy – törlése Arcfelismerési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) és [szembesülhetnek – azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).
## <a name="step3"></a> 3. lépés: A PersonGroup képzése

Ki kell képezni a PersonGroup, azonosítóját elvégzése előtt használja azt. Ezenkívül Önhöz kell retrained után hozzáadásával vagy eltávolításával bármely személy, vagy ha bármely személy a regisztrált arcfelismerési szerkeszteni. A képzési végezhető el a [PersonGroup – vonat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API. Az ügyféloldali kódtár használatával, esetén egyszerűen TrainPersonGroupAsync metódus hívása:
 
```CSharp 
await faceServiceClient.TrainPersonGroupAsync(personGroupId);
```
 
A képzés egy aszinkron folyamatban. Akkor lehet, hogy nem kell végzett TrainPersonGroupAsync metódus visszaadott után is. Szükség lehet a képzés állapotot lekérdezése [PersonGroup - képzési állapotának beolvasása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) az ügyféloldali kódtár API-t vagy GetPersonGroupTrainingStatusAsync metódust. A következő kód bemutatja egy egyszerű logikai PersonGroup várakozással befejezéséhez betanítása:
 
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

## <a name="step4"></a> 4. lépés: Egy meghatározott PersonGroup elleni arc azonosítása
Azonosítók végrehajtásakor a Arcfelismerési API számíthatja a hasonlóság csoportban lévő összes lapok között teszt készült, és a legtöbb összehasonlítható személyeket, hogy tesztelési felülethez adja vissza. Ezt a [szembesülhetnek – azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API vagy ügyfélkódtár IdentifyAsync metódusában.

A tesztelési arcfelismerési kell észlelhető az előző lépéseket követve, és majd a tapasztalt Azonosítót a rendszer elküldi a azonosítása API második argumentumként. Egyszerre több arcfelismerési azonosítók úgy azonosítható, és az eredmény az azonosítás eredményeket fogja tartalmazni. Alapértelmezés szerint a csak egy személy, amely a legjobban megfelel a teszt arcfelismerési adja vissza. Tetszés szerint megadhatja a nem kötelező paraméter maxNumOfCandidatesReturned ahhoz, hogy a további elemek vissza azonosítása.

A következő kód bemutatja, azonosítsa a folyamatot:
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

Ha befejezte a lépéseket, megpróbálhatja különböző lapjaira azonosításához, és tekintse meg, ha a lapok Anna, számlázási vagy Clare megfelelően meghatározható, a tapasztalt észlelési feltöltött lemezkép alapján. Lásd az alábbi példákat:

![HowToIdentify2](../Images/identificationResult.1.jpg )

## <a name="step5"></a> 5. lépés: A kérés a nagyméretű

Ismert, mert egy PersonGroup tárolására képes legfeljebb 10 000 személyek előző kialakítás korlátai miatt.
További információt legfeljebb millió forgatókönyvek, lásd: [a nagy méretű szolgáltatásának használatáról](how-to-use-large-scale.md).

## <a name="summary"></a> Összefoglalás

Ebben az útmutatóban megtanulhatta, egy PersonGroup létrehozása és egy személyt azonosításának folyamata. Gyors emlékeztető korábban ismertetése és bemutatta azok a funkciók a következők:

- Észlelése előtt álló használatával a [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) API
- PersonGroups használatával létrehozása a [PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API
- Személyek használatával létrehozása a [PersonGroup személy - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API
- Egy PersonGroup használatával betanítása a [PersonGroup – vonat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API
- Ismeretlen lapokat a PersonGroup használatának azonosítása a [szembesülhetnek – azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API

## <a name="related"></a> Kapcsolódó témakörök

- [A kép néz történő Adatmásolást](HowtoDetectFacesinImage.md)
- [Lapok hozzáadása](how-to-add-faces.md)
- [A felügyeleti teendők központjaként szolgáltatás használata](how-to-use-large-scale.md)
