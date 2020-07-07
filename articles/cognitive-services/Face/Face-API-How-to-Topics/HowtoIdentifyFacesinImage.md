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
ms.date: 07/02/2020
ms.author: sbowles
ms.openlocfilehash: 607f67258c5d069590f934891c09ccada780c977
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85918744"
---
# <a name="example-identify-faces-in-images"></a>Példa: arcok felismerése képeken

Ez az útmutató bemutatja, hogyan azonosíthatók az ismeretlen arcok a PersonGroup-objektumok használatával, amelyek előre ismert személyekből jönnek létre. A minták C# nyelven íródnak az Azure Cognitive Services Face ügyféloldali kódtár használatával.

Ez a példa a következőket mutatja be:

- PersonGroup létrehozása. Ez a PersonGroup az ismert személyek listáját tartalmazza.
- Arcok társítása az egyes személyekhez. Ezek az arcok a személyek azonosítására szolgáló alapkonfigurációként használatosak. Javasoljuk, hogy az arcok tiszta elülső nézeteit használja. Ilyen például a fénykép azonosítója. A fényképek jó gyűjteménye magában foglalja a különböző pózok, a ruházati színek és a frizurák azonos személyeit.

## <a name="prerequisites"></a>Előfeltételek
* A [.net Core](https://dotnet.microsoft.com/download/dotnet-core)jelenlegi verziója.
* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" hozzon létre egy Face-erőforrást "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson az **erőforrás keresése** elemre, és másolja a kulcsot.
* Néhány fotó az azonosított személyek arcával. Töltse le a Anna, a Bill és a Clare [minta fotóit](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) .
* Tesztelési fényképek sorozata. Előfordulhat, hogy a fényképek nem tartalmazzák az azonosított személyek arcát. Használjon néhány képet a minta fényképek hivatkozásból.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

Hozzon létre egy új .NET Core-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben. 

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `face-identify` . Ez a parancs egy egyszerű ""Helló világ!"alkalmazás" C#-projektet hoz létre egyetlen forrásfájlban: *program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

```dotnetcli
dotnet build
```

A Build kimenete nem tartalmazhat figyelmeztetést vagy hibát. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="step-1-authorize-the-api-call"></a>1. lépés: API-hívás engedélyezése

A Face API minden meghívásához előfizetési kulcs szükséges. Ezt a kulcsot átadhatja egy lekérdezési karakterlánc paraméternek, vagy megadható a kérelem fejlécében. Ha ügyféloldali kódtárat használ, az előfizetési kulcsot a **FaceClient** osztály konstruktorán keresztül adja át a rendszer. Adja hozzá a következő kódot a *program.cs* -fájl **fő** metódusához.
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<subscription key>"),
    new System.Net.Http.DelegatingHandler[] { });
```

## <a name="step-2-create-the-persongroup"></a>2. lépés: PersonGroup létrehozása

Ebben a lépésben egy "MyFriends" nevű **PersonGroup** Anna, Bill és Clare nevet tartalmaz. Mindegyikükhöz több arcot jegyeztünk be. Az arcokat a képekből kell észlelni. A fenti lépések elvégzése után a következő képhez hasonló **PersonGroup** rendelkezik:

![MyFriends](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>2,1. lépés: személyek definiálása a PersonGroup
A személy az identitás alapegysége. Egy személyhez tartozhat egy vagy több ismert arc is. A **PersonGroup** emberek gyűjteménye. Mindegyik személy egy adott **PersonGroup**belül van definiálva. Az azonosítás egy **PersonGroup**történik. A feladat egy **PersonGroup**létrehozása, majd a benne lévő személyek létrehozása, például Anna, Bill és Clare.

Először hozzon létre egy új **PersonGroup** a [PersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API használatával. A megfelelő ügyféloldali függvénytár API a **FaceClient** osztály **CreatePersonGroupAsync** metódusa. A csoport létrehozásához megadott csoportazonosító minden előfizetés esetében egyedi. A **PersonGroup**más **PersonGroup** API-k használatával is beolvashatja, frissítheti vagy törölheti. 

Egy csoport definiálása után a [PersonGroup személyre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) szabott API-val megadhatja a benne lévő személyek listáját. Az ügyféloldali függvénytár metódusa a **CreatePersonAsync**. A létrehozásuk után hozzáadhat egy arcot az egyes személyekhez.

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
### <a name="step-22-detect-faces-and-register-them-to-the-correct-person"></a><a name="step2-2"></a>2,2. lépés: az arcok észlelése és a megfelelő személyhez való regisztrálása
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

## <a name="step-3-train-the-persongroup"></a>3. lépés: PersonGroup betanítása

A **PersonGroup** meg kell tanítani az azonosítás elvégzéséhez a használatával. A **PersonGroup** egy személy hozzáadása vagy eltávolítása, illetve egy személy regisztrált arca szerkesztését követően kell újra betanítani. A betanítást a [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API használatával végezzük. Az ügyféloldali kódtár használatakor ez a **TrainPersonGroupAsync** metódus hívása:
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
A képzés aszinkron folyamat. Előfordulhat, hogy a **TrainPersonGroupAsync** metódus visszatérése után sem fejeződik be. Előfordulhat, hogy le kell kérdezni a betanítási állapotot. Használja az [PersonGroup beszerzése a betanítási állapot API-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) t vagy a **GetPersonGroupTrainingStatusAsync** metódust. A következő kód egy egyszerű logikát mutat be, amely a **PersonGroup** -képzés befejezésére vár:
 
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

Ha a Face szolgáltatás azonosítja az azonosításokat, az egy csoporton belüli összes arc között kiszámítja a tesztek hasonlóságát. A tesztelési felületen a leginkább összehasonlítható személyeket adja vissza. Ezt a folyamatot a [Face-IDENTIFY API-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) n vagy az ügyféloldali kódtár IdentifyAsync metódusán keresztül végezheti el.

A tesztelési arcot az előző lépések segítségével kell észlelni. Ezt követően a rendszer második argumentumként átadja a Face ID-t az azonosító API-nak. Egyszerre több arc azonosító is azonosítható. Az eredmény az összes azonosított eredményt tartalmazza. Alapértelmezés szerint az azonosítási folyamat csak egy olyan személyt ad vissza, amely a legjobban megfelel a tesztnek. Ha szeretné, megadhatja a _maxNumOfCandidatesReturned_ választható paramétert, hogy az azonosítási folyamat több pályázót is visszaadjon.

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

## <a name="step-5-request-for-large-scale"></a>5. lépés: nagy léptékű kérelem

A **PersonGroup** az előző tervezési korlátozás alapján akár 10 000 személyt is megtarthatnak. További információ az akár milliós esetekről: [A nagy méretű szolgáltatás használata](how-to-use-large-scale.md).

## <a name="summary"></a>Összefoglalás

Ebben az útmutatóban megtanulta, hogyan hozhat létre egy **PersonGroup** , és hogyan azonosítható egy személy. A következő funkciókat ismertetjük és mutatták be:

- Az arcok észlelése az [arc-észlelési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) API használatával.
- Hozzon létre PersonGroups a [PersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API használatával.
- Hozzon létre személyeket a [PersonGroup-létrehozó](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API használatával.
- PersonGroup betanítása a [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API használatával.
- Azonosítsa az ismeretlen arcokat a PersonGroup a [Face-IDENTIFY](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API használatával.

## <a name="related-topics"></a>Kapcsolódó témakörök

- [Arcfelismerés – fogalmak](../concepts/face-recognition.md)
- [Arcok felismerése a képeken](HowtoDetectFacesinImage.md)
- [Arcok hozzáadása](how-to-add-faces.md)
- [A nagy léptékű funkció használata](how-to-use-large-scale.md)
