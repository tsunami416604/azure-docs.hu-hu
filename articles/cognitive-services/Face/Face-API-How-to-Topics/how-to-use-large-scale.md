---
title: 'Példa: A nagyméretű szolgáltatás használata – Face API'
titleSuffix: Azure Cognitive Services
description: Nagy léptékű funkció használata a Face API-ban.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.openlocfilehash: d8ecfb53b78277e4b0e4a85d60fb6712d0bc2292
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114842"
---
# <a name="example-use-the-large-scale-feature"></a>Példa: A nagy léptékű funkció használata

Ez az útmutató egy speciális cikk, amely bemutatja, hogyan méretezhető fel a meglévő PersonGroup és FaceList objektumok a LargePersonGroup és a LargeFaceList objektumokra. Ez az útmutató az áttelepítési folyamatot mutatja be. Feltételezi, hogy alapszintű ismerettel rendelkezik a PersonGroup és a FaceList objektumokkal, a [vonatok](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) működésével és az Arcfelismerés funkcióival. Ha többet szeretne megtudni ezekről a témákról, tekintse meg a [Face Recognition](../concepts/face-recognition.md) fogalmi útmutatót.

A LargePersonGroup és a LargeFaceList közösen nagy léptékű műveleteknek nevezzük. A LargePersonGroup legfeljebb 1 000 000 személyt tartalmazhat, amelyek mindegyike legfeljebb 248 arcot tartalmaz. A LargeFaceList akár 1 000 000 arcot is tartalmazhat. A nagyméretű műveletek hasonlóak a hagyományos PersonGroup és FaceList, de az új architektúra miatt néhány különbség van. 

A mintákat az Azure Cognitive Services C# Face API ügyféloldali kódtár használatával kell beírni.

> [!NOTE]
> Ha nagy léptékben szeretné engedélyezni az arc-keresési teljesítményt az azonosításhoz és a FindSimilar, vezessen be egy vonat-műveletet a LargeFaceList és a LargePersonGroup előfeldolgozásához. A betanítási idő másodperctől körülbelül fél órára változik a tényleges kapacitás alapján. A betanítási időszak alatt az azonosítást és a FindSimilar is elvégezheti, ha a sikeres betanítás már megtörtént. A hátránya az, hogy az új hozzáadott személyek és arcok nem jelennek meg az eredményben, amíg a nagy léptékű képzésbe való új áttelepítés be nem fejeződik.

## <a name="step-1-initialize-the-client-object"></a>1\. lépés: Az ügyfél objektum inicializálása

Az Face API ügyféloldali kódtár használatakor az előfizetési kulcsot és az előfizetési végpontot a FaceClient osztály konstruktorán keresztül adja át a rendszer. Példa:

```csharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Az előfizetési kulcs megfelelő végponthoz való beszerzéséhez nyissa meg az Azure Marketplace-t a Azure Portal.
További információ: előfizetések. [](https://azure.microsoft.com/services/cognitive-services/directory/vision/)

## <a name="step-2-code-migration"></a>2\. lépés: Kód áttelepítése

Ez a szakasz a PersonGroup vagy a FaceList implementációjának LargePersonGroup vagy LargeFaceList való áttelepítését ismerteti. Bár a LargePersonGroup vagy a LargeFaceList eltér a tervezési és belső megvalósítási PersonGroup vagy FaceList, az API-felületek a visszamenőleges kompatibilitáshoz hasonlóak.

Az adatáttelepítés nem támogatott. Ehelyett hozza létre újra a LargePersonGroup vagy a LargeFaceList.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>PersonGroup migrálása LargePersonGroup

A PersonGroup-ről LargePersonGroup-re történő áttelepítés egyszerű. Pontosan ugyanazokat a csoport szintű műveleteket osztják meg.

A PersonGroup vagy a személyekkel kapcsolatos megvalósításhoz csak az API-útvonalakat vagy SDK-osztályokat vagy-modulokat kell módosítani LargePersonGroup és LargePersonGroup.

Adja hozzá az összes arcot és személyt a PersonGroup az új LargePersonGroup. További információ: Faces [hozzáadása](how-to-add-faces.md).

### <a name="migrate-a-facelist-to-a-largefacelist"></a>FaceList migrálása LargeFaceList

| FaceList API-k | LargeFaceList API-k |
|:---:|:---:|
| Hozzon létre | Hozzon létre |
| Törlés | Törlés |
| Beolvasás | Beolvasás |
| List | List |
| frissítés | frissítés |
| - | Betanítás |
| - | Betanítási állapot lekérdezése |

Az előző táblázat a FaceList és a LargeFaceList listaszintű műveleteinek összehasonlítása. Ahogy az ábrán látható, a LargeFaceList új műveletekkel, betanítással és betanítási állapottal rendelkezik a FaceList képest. A LargeFaceList betanítása a [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) művelet előfeltétele. Nem szükséges betanítás a FaceList. A következő kódrészlet egy segítő függvény, amely a LargeFaceList betanítására vár:

```csharp
/// <summary>
/// Helper function to train LargeFaceList and wait for finish.
/// </summary>
/// <remarks>
/// The time interval can be adjusted considering the following factors:
/// - The training time which depends on the capacity of the LargeFaceList.
/// - The acceptable latency for getting the training status.
/// - The call frequency and cost.
///
/// Estimated training time for LargeFaceList in different scale:
/// -     1,000 faces cost about  1 to  2 seconds.
/// -    10,000 faces cost about  5 to 10 seconds.
/// -   100,000 faces cost about  1 to  2 minutes.
/// - 1,000,000 faces cost about 10 to 30 minutes.
/// </remarks>
/// <param name="largeFaceListId">The Id of the LargeFaceList for training.</param>
/// <param name="timeIntervalInMilliseconds">The time interval for getting training status in milliseconds.</param>
/// <returns>A task of waiting for LargeFaceList training finish.</returns>
private static async Task TrainLargeFaceList(
    string largeFaceListId,
    int timeIntervalInMilliseconds = 1000)
{
    // Trigger a train call.
    await FaceClient.LargeTrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await faceClient.LargeFaceList.TrainAsync(largeFaceListId);

        if (status.Status == Status.Running)
        {
            continue;
        }
        else if (status.Status == Status.Succeeded)
        {
            break;
        }
        else
        {
            throw new Exception("The train operation is failed!");
        }
    }
}
```

Korábban a FaceList és a FindSimilar egy tipikus használata a következőhöz hasonlóan néz ki:

```csharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.FaceList.CreateAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.FaceList.AddFaceFromStreamAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

A LargeFaceList-re való áttelepítéskor a következő lesz:

```csharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.LargeFaceList.CreateAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.LargeFaceList.AddFaceFromStreamAsync(LargeFaceListId, stream);
            }
        });

// Train() is newly added operation for LargeFaceList.
// Must call it before FindSimilarAsync() to ensure the newly added faces searchable.
await TrainLargeFaceList(LargeFaceListId);

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

Ahogy az előzőekben is látható, az adatkezelés és a FindSimilar rész majdnem ugyanaz. Az egyetlen kivétel az, hogy egy friss előfeldolgozási műveletnek a FindSimilar működése előtt végre kell hajtania a LargeFaceList.

## <a name="step-3-train-suggestions"></a>3\. lépés: Javaslatok betanítása

Bár a betanítási művelet felgyorsítja a [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) és az [azonosítást](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), a képzési idő szenved, különösen akkor, ha nagy léptékben érkezik. A következő táblázat felsorolja a becsült tanítási időt különböző léptékekben.

| Méretezés arcokhoz vagy személyekhez | Becsült betanítási idő |
|:---:|:---:|
| 1,000 | 1-2 MP |
| 10,000 | 5-10 mp |
| 100,000 | 1-2 perc |
| 1,000,000 | 10-30 perc |

A nagyméretű funkciók jobb kihasználásához a következő stratégiákat javasoljuk.

### <a name="step-31-customize-time-interval"></a>3,1. lépés: Időintervallum testreszabása

Ahogy az a- `TrainLargeFaceList()`ben is látható, a végtelen betanítási állapot-ellenőrzési folyamat késleltethető ezredmásodpercben. A több arcot tartalmazó LargeFaceList esetén nagyobb időköz választása csökkenti a meghívások számát és költségét. Szabja testre az időintervallumot a LargeFaceList várt kapacitásának megfelelően.

Ugyanez a stratégia a LargePersonGroup is vonatkozik. Ha például 1 000 000 személyt tartalmazó LargePersonGroup-t tanít be, `timeIntervalInMilliseconds` akkor az a 60 000, amely egy 1 perces intervallum.

### <a name="step-32-small-scale-buffer"></a>3,2. lépés: Kisméretű puffer

A LargePersonGroup vagy LargeFaceList lévő személyek vagy arcok csak a képzés után kereshetők. Dinamikus forgatókönyv esetén az új személyeket vagy arcokat folyamatosan hozzá kell adni, és azonnal kereshetőnek kell lenniük, de a képzés hosszabb időt is igénybe vehet. 

A probléma megoldásához használjon egy extra kis méretű LargePersonGroup-vagy LargeFaceList-puffert csak az újonnan hozzáadott bejegyzésekhez. A puffer kevesebb időt vesz igénybe a kisebb méret miatt. Az átmeneti puffer azonnali keresési funkciójának működnie kell. Használja ezt a puffert a Master LargePersonGroup vagy a LargeFaceList képzésével együtt azáltal, hogy a főképzést egy ritka intervallumban futtatja. Ilyenek például az éjszaka közepén és naponta.

Példa munkafolyamat:

1. Hozzon létre egy fő LargePersonGroup vagy LargeFaceList, amely a fő gyűjtemény. Hozzon létre egy puffert LargePersonGroup vagy LargeFaceList, amely a puffer gyűjteménye. A pufferek gyűjteménye csak újonnan felvett személyekhez vagy arcokhoz használható.
1. Vegyen fel új személyeket vagy arcokat a fő gyűjteménybe és a puffer-gyűjteménybe.
1. Csak rövid időintervallummal betaníthatja a puffer gyűjteményét, így biztosítva, hogy az újonnan hozzáadott bejegyzések érvénybe lépnek.
1. Az azonosítás vagy a FindSimilar a fő gyűjteményre és a puffer-gyűjteményre vonatkozóan hívható meg. Egyesítse az eredményeket.
1. Ha a puffer-gyűjtemény mérete egy küszöbértékre vagy egy rendszertétlen időpontra nő, hozzon létre egy új puffer-gyűjteményt. Aktiválja a betanítási műveletet a fő gyűjteményen.
1. Törölje a régi puffer-gyűjteményt, miután a Train művelet befejeződik a fő gyűjteményen.

### <a name="step-33-standalone-training"></a>3,3. lépés: Önálló képzés

Ha a viszonylag hosszú késés elfogadható, nem szükséges a vonat működésének elindításához közvetlenül az új adatértékek hozzáadása után. Ehelyett a betanítás művelet a fő működésről leválasztva indítható rendszeresen. Ez a stratégia alkalmas olyan dinamikus forgatókönyvekhez, amelyek elfogadható késéssel rendelkeznek. A vonatok gyakoriságának további csökkentése érdekében statikus forgatókönyvekre is alkalmazható.

Tegyük fel, `TrainLargePersonGroup` hogy `TrainLargeFaceList`a függvény hasonló a következőhöz:. A LargePersonGroup [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) önálló betanításának tipikus megvalósítása a következő osztály meghívásával `System.Timers` :

```csharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    faceClient.LargePersonGroup.CreateAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Set up standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1-minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1-hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces, and identification, except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

További információ az adatkezelésről és az azonosítással kapcsolatos megvalósításokról: [arcok hozzáadása](how-to-add-faces.md) és [arcok azonosítása egy képben](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a>Összegzés

Ebben az útmutatóban megtanulta, hogyan telepítheti át a meglévő PersonGroup-vagy FaceList-kódot a LargePersonGroup vagy a LargeFaceList:

- A LargePersonGroup és a LargeFaceList hasonló módon működnek, mint a PersonGroup vagy a FaceList, azzal a különbséggel, hogy a LargeFaceList a vonatok műveletét igényli.
- A nagyméretű adathalmazok dinamikus adatfrissítéséhez végezze el a megfelelő betanítási stratégiát.

## <a name="next-steps"></a>További lépések

A következő útmutatóból megtudhatja, hogyan adhat hozzá arcokat egy PersonGroup, vagy hogyan hajthatja végre az azonosítási műveletet egy PersonGroup.

- [Arcok hozzáadása](how-to-add-faces.md)
- [Képeken lévő arcok azonosítása](HowtoIdentifyFacesinImage.md)
