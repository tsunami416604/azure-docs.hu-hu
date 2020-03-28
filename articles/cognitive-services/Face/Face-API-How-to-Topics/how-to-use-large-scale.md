---
title: 'Példa: Használja a Nagyméretű funkciót - Arc'
titleSuffix: Azure Cognitive Services
description: Ez az útmutató a meglévő PersonGroup és FaceList objektumokról a LargePersonGroup és a LargeFaceList objektumokra való felskálázásról szól.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.openlocfilehash: dc0964e40e9214e414d865c06006f1d36e97eeb2
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169783"
---
# <a name="example-use-the-large-scale-feature"></a>Példa: Használja a nagyméretű funkciót

Ez az útmutató egy speciális cikk arról, hogyan skálázható a meglévő PersonGroup és FaceList objektumok LargePersonGroup és LargeFaceList objektumok, illetve. Ez az útmutató bemutatja az áttelepítési folyamatot. A PersonGroup és a FaceList objektumok, a [Betanítás](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) művelet és az arcfelismerő függvények alapvető ismeretét feltételezi. Ha többet szeretne megtudni ezekről a témákról, olvassa el az [Arcfelismerés](../concepts/face-recognition.md) fogalmi útmutatóját.

A LargePersonGroup és a LargeFaceList együttesen nagyméretű műveleteknek nevezzük. A LargePersonGroup legfeljebb 1 millió személyt tartalmazhat, egyenként legfeljebb 248 arccal. A LargeFaceList legfeljebb 1 millió arcot tartalmazhat. A nagyméretű műveletek hasonlóak a hagyományos PersonGroup és FaceList, de van néhány különbség, mert az új architektúra. 

A minták c# nyelven vannak megírva az Azure Cognitive Services Face ügyfélkódtár használatával.

> [!NOTE]
> Az Azonosítás és a Kereséshasonló nagyméretű arckeresési teljesítményének engedélyezéséhez vezessen be egy Train műveletet a LargeFaceList és a LargePersonGroup előfeldolgozásához. A betanítási idő másodperctől körülbelül fél óráig változik a tényleges kapacitás alapján. A képzési időszak alatt lehetőség van azonosításés FindSimilar elvégzésére, ha korábban sikeres képzést végeztek. A hátránya az, hogy az új hozzáadott személyek és arcok nem jelennek meg az eredmény, amíg egy új utáni migráció nagyszabású képzés befejeződött.

## <a name="step-1-initialize-the-client-object"></a>1. lépés: Az ügyfélobjektum inicializálása

A Face ügyfélkódtár használatakor az előfizetési kulcs és az előfizetési végpont a FaceClient osztály konstruktorán keresztül kerül átadásra. Példa:

```csharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Az előfizetési kulcs beszerezése a megfelelő végpontdal, nyissa meg az Azure Marketplace-en az Azure Portalon.
További információ: [Előfizetések](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>2. lépés: A kód áttelepítése

Ez a szakasz a PersonGroup vagy a FaceList implementáció t A LargePersonGroup vagy a LargeFaceList rendszerbe való áttelepítésére összpontosít. Bár a LargePersonGroup vagy a LargeFaceList a tervezés és a belső megvalósítás során különbözik a PersonGroup vagy a FaceList elemtől, az API-felületek hasonlóak a visszamenőleges kompatibilitás érdekében.

Az adatok áttelepítése nem támogatott. A LargePersonGroup vagy a LargeFaceList újra létre.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Személycsoport áttelepítése LargePersonGroup csoportba

A PersonGroup csoportról a LargePersonGroup csoportra való áttérés egyszerű. Pontosan ugyanazok a csoportszintű műveletek.

A PersonGroup- vagy a személyhez kapcsolódó megvalósításhoz csak az API-elérési utakat vagy az SDK-osztályt/modult kell módosítani LargePersonGroup és LargePersonGroup Person-ra.

Adja hozzá a PersonGroup összes arcát és személyét az új LargePersonGroup csoporthoz. További információt az Arcok hozzáadása című témakörben [talál.](how-to-add-faces.md)

### <a name="migrate-a-facelist-to-a-largefacelist"></a>FaceList áttelepítése LargeFaceList-re

| FaceList API-k | LargeFaceList API-k |
|:---:|:---:|
| Létrehozás | Létrehozás |
| Törlés | Törlés |
| Lekérés | Lekérés |
| Lista | Lista |
| Frissítés | Frissítés |
| - | Betanítás |
| - | Betanítási állapot lekérdezése |

Az előző táblázat a FaceList és a LargeFaceList listaszintű műveleteinek összehasonlítása. Mint látható, LargeFaceList jön-val új műveletek, Vonat és Get Training Status, összehasonlítva FaceList. A LargeFaceList betanítása a [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) művelet előfeltétele. A FaceList-hez nincs szükség edzésre. A következő kódrészlet egy segítő függvény, amely megvárja a LargeFaceList betanítását:

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

Korábban a FaceList tipikus használata hozzáadott arcokkal és FindSimilar a következőkre nézett ki:

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

Amint azt korábban bemutatuk, az adatkezelés és a FindSimilar rész majdnem megegyezik. Az egyetlen kivétel az, hogy egy friss előfeldolgozási train műveletnek be kell fejeződnie a LargeFaceList-ben, mielőtt a FindSimilar működik.

## <a name="step-3-train-suggestions"></a>3. lépés: Vonatjavaslatok

Bár a vonat működése felgyorsítja [a FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) és [az Identification műveletet,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)a képzési idő szenved, különösen akkor, ha nagy léptékben jön. A különböző léptékekben becsült betanítási idő az alábbi táblázatban található.

| Arcok vagy személyek méretezése | Becsült képzési idő |
|:---:|:---:|
| 1,000 | 1-2 mp |
| 10,000 | 5-10 mp |
| 100 000 | 1-2 min |
| 1,000,000 | 10-30 min |

A nagyszabású funkció jobb kihasználása érdekében a következő stratégiákat javasoljuk.

### <a name="step-31-customize-time-interval"></a>3.1. lépés: Az időintervallum testreszabása

Ahogy az `TrainLargeFaceList()`a alkalmazásban látható, a végtelen betanítási állapot ellenőrzési folyamatának késleltetéséhez ezredmásodpercben van időintervallum. A több arcot tartalmazó LargeFaceList esetén nagyobb időköz választása csökkenti a meghívások számát és költségét. Az időintervallum testreszabása a LargeFaceList várható kapacitásának megfelelően.

Ugyanez a stratégia vonatkozik a LargePersonGroup-ra is. Ha például egy LargePersonGroup-ot 1 `timeIntervalInMilliseconds` millió személlyel együtt tanít be, az 60 000 lehet, ami egy 1 perces időköz.

### <a name="step-32-small-scale-buffer"></a>3.2. lépés: Kis léptékű puffer

A LargePersonGroup vagy a LargeFaceList csoportban lévő személyek vagy arcok csak a betanítás után kereshetők. Egy dinamikus forgatókönyvben az új személyek vagy arcok folyamatosan hozzáadódnak, és azonnal kereshetőnek kell lenniük, de a képzés a kívántnál tovább tarthat. 

A probléma enyhítése érdekében használjon egy extra kis méretű LargePersonGroup vagy LargeFaceList pufferként csak az újonnan hozzáadott bejegyzéseket. Ez a puffer a kisebb méret miatt rövidebb időt vesz igénybe. Az ideiglenes puffer azonnali keresési képessége működnie kell. Használja ezt a puffert a fő LargePersonGroup vagy LargeFaceList betanításával kombinálva, ha a főbetanítást sparser-intervallumon futtatja. Példák az éjszaka közepén és naponta.

Példa munkafolyamat:

1. Hozzon létre egy fő LargePersonGroup vagy LargeFaceList, amely a fő gyűjtemény. Hozzon létre egy puffert LargePersonGroup vagy LargeFaceList, amely a puffer gyűjtemény. A puffergyűjtemény csak újonnan hozzáadott személyekre vagy lapokra szolgál.
1. Új személyek vagy lapok hozzáadása a fő gyűjteményhez és a puffergyűjteményhez is.
1. Csak rövid időintervallummal tanítsa be a puffergyűjteményt, hogy az újonnan hozzáadott bejegyzések érvénybe lépjenek.
1. Hívja meg az Identification vagy a FindSimilar (Webhelyazonosítás) vagy a FindSimilar hívását a fő gyűjtemény és a puffergyűjtemény ellen. Egyesítse az eredményeket.
1. Ha a puffergyűjtemény mérete küszöbértékre vagy rendszertétlen időpontra nő, hozzon létre egy új puffergyűjteményt. Indítsa el a Vonat műveletet a fő gyűjteményen.
1. Törölje a régi puffergyűjteményt, miután a Vonat művelet befejeződött a fő gyűjteményen.

### <a name="step-33-standalone-training"></a>3.3. lépés: Önálló képzés

Ha egy viszonylag hosszú késés elfogadható, nem szükséges, hogy indítsa el a Vonat művelet közvetlenül az új adatok hozzáadása után. Ehelyett a betanítás művelet a fő működésről leválasztva indítható rendszeresen. Ez a stratégia elfogadható késésű dinamikus forgatókönyvekhez alkalmas. Statikus forgatókönyvekre alkalmazható a vonat frekvenciájának további csökkentése érdekében.

Tegyük fel, `TrainLargePersonGroup` hogy `TrainLargeFaceList`van egy függvény hasonló a. Az önálló képzés tipikus megvalósítása egy LargePersonGroup-on [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) `System.Timers` az osztály meghívásával:

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

Az adatkezeléssel és az azonosítással kapcsolatos implementációkkal kapcsolatos további információt az [Arcok hozzáadása](how-to-add-faces.md) és az Arcok azonosítása a képen című [témakörben talál.](HowtoIdentifyFacesinImage.md)

## <a name="summary"></a>Összefoglalás

Ebben az útmutatóban megtanulta, hogyan telepítheti át a meglévő PersonGroup vagy FaceList kódot, nem pedig az adatokat a LargePersonGroup vagy a LargeFaceList list-re:

- A LargePersonGroup és a LargeFaceList a PersonGroup-hoz vagy a FaceList-hez hasonlóan működik, azzal a különbséggel, hogy a Betanítás műveletre a LargeFaceList-nek van szüksége.
- A nagyméretű adatkészletek dinamikus adatfrissítéséhez vigye a megfelelő betanítási stratégiát.

## <a name="next-steps"></a>További lépések

Az útmutató útmutatóból megtudhatja, hogyan adhat arcokat egy személycsoporthoz, illetve hogyan hajthat végre az Azonosítás műveletet egy személycsoporton.

- [Arcok hozzáadása](how-to-add-faces.md)
- [A képen lévő arcok azonosítása](HowtoIdentifyFacesinImage.md)
