---
title: 'Példa: Használja a nagy méretű szolgáltatását – Face API'
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
ms.openlocfilehash: 35ab2d36a5d6c9977398fdbc16ba22eb1d9656a4
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65229838"
---
# <a name="example-how-to-use-the-large-scale-feature"></a>Példa: A nagy méretű szolgáltatás használata

Ez az útmutató olyan meglévő vertikális egy speciális cikkünket **is lehet PersonGroup** és **FaceList** való **LargePersonGroup** és **LargeFaceList**jelölik. Ez az útmutató bemutatja, az áttelepítési folyamat és a egy alapszintű ismeretét feltételezi **is lehet PersonGroup**, **FaceList**, a [Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) művelet, és az arcok felismerése a műveletek. Tekintse meg a [Arcfelismerési](../concepts/face-recognition.md) ezek kapcsolatos további fogalmi útmutató.

LargePersonGroup és LargeFaceList együttesen nevezzük nagyméretű műveleteket. Legfeljebb 1 000 000 248 arcok legfeljebb személyek LargePersonGroup tartalmazhat, és LargeFaceList arcok legfeljebb 1 000 000 tartalmazhat. A nagy méretű műveletek hasonlóak a hagyományos is lehet PersonGroup és FaceList, de van néhány jelentős különbség új architektúrája miatt. 

A példák a Face API ügyfélkódtár használatával C# nyelven íródtak.

> [!NOTE]
> Ahhoz, hogy az Identification és FindSimilar funkciókra nagy léptékre is engedélyezni lehessen a Face teljesítményét, be kell vezetni a LargeFaceList és a LargePersonGroup kollekciók előzetes feldolgozására betanítási műveletet. A betanítási idő a tényleges kapacitástól függően másodpercek és fél óra között változhat. A betanítási időszakban is lehetséges az Identification és a FindSimilar funkciók végrehajtása, ha korábban végeztek már sikeres tanítást. Azonban a hátránya az, hogy az újonnan hozzáadott személyek és arcok nem fognak megjelenni az eredményekben addig, amíg a migráció után meg nem történik a nagyobb léptéken is a betanítás.

## <a name="step-1-initialize-the-client-object"></a>1. lépés: Az ügyfél objektum inicializálása

Az Face API ügyfélkódtár használatakor az előfizetési kulcs és az előfizetési végpont megadása a FaceServiceClient osztály konstruktorán keresztül történik. Példa:

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionRegion = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/";
FaceServiceClient FaceServiceClient = new FaceServiceClient(SubscriptionKey, SubscriptionRegion);
```

Az előfizetési kulcs és a hozzátartozó végpont az Azure portál Marketplace oldaláról szerezhető be.
Lásd az [előfizetéseket](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>2. lépés: Kód áttelepítése

Ez a szakasz csak a PersonGroup és a FaceList alapú megvalósítások LargePersonGroup és LargeFaceList használatára történő áttelepítési folyamatával foglalkozik. Bár LargePersonGroup/LargeFaceList eltér a tervezési és megvalósítási belső is lehet PersonGroup vagy FaceList, az API-felületek hasonlóak a visszamenőleges kompatibilitás érdekében.

Az adatmigráció nem támogatott, ehelyett a LargePersonGroup/LargeFaceList újbóli létrehozását kell elvégezni.

### <a name="migrate-persongroup-to-largepersongroup"></a>LargePersonGroup is lehet PersonGroup áttelepítése

Az áttelepítés is lehet PersonGroup LargePersonGroup használata egyszerű, azok pontosan ugyanaz-szintű műveletek megosztásként.

A PersonGroup/Person esetében elég csak az API elérési utat és az SDK osztályt/modult módosítani a LargePersonGroup és LargePersonGroup megvalósításánál.

Adja hozzá az összes arcokat és azok a személyek az új LargePersonGroup az is lehet PersonGroup kell. Lásd: [hogyan hozzáadása arcokat](how-to-add-faces.md) referenciaként.

### <a name="migrate-facelist-to-largefacelist"></a>LargeFaceList FaceList áttelepítése

| FaceList API-k | LargeFaceList API-k |
|:---:|:---:|
| Létrehozás | Létrehozás |
| Törlés | Törlés |
| Beolvasás | Beolvasás |
| Lista | Lista |
| Frissítés | Frissítés |
| - | Betanítás |
| - | Betanítási állapot lekérdezése |

Az előző táblázat a FaceList és a LargeFaceList listaszintű műveleteinek összehasonlítása. Amint látható, a LargeFaceList új műveletekkel is rendelkezik a FaceList-hez képest, a Betanítás és a Betanítási állapot lekérdezése. A LargeFaceList betanítása a [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) művelet előfeltétele, míg a FaceList esetében nincs betanítási követelmény. Az alábbi kódrészlet a LargeFaceList betanítását megváró segédfüggvény.

```CSharp
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
    await FaceServiceClient.TrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await FaceServiceClient.GetLargeFaceListTrainingStatusAsync(largeFaceListId);

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

Korábban az arcokat és FindSimilar FaceList egy tipikus használatát a következő módon jelenik meg:

```CSharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateFaceListAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToFaceListAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

Ha az áttelepítés LargeFaceList, válik a következőket:

```CSharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateLargeFaceListAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToLargeFaceListAsync(LargeFaceListId, stream);
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
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

Ahogy fent is látható, az adatok kezelése és a FindSimilar rész szinte azonos. Az egyetlen kivétel, hogy friss előfeldolgozási betanítás műveletnek kell megtörténnie a LargeFaceList esetében, hogy a FindSimilar működjön.

## <a name="step-3-train-suggestions"></a>3. lépés: Javaslatok betanítása

Bár a betanítás művelet felgyorsítja a [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) és [Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) műveleteket, a betanítási idő különösen nagyobb méreteknél kárt szenved. A különböző méretek esetén becsült betanítási időt sorolja fel a következő táblázat:

| Méret (arc vagy személy) | Becsült betanítási idő |
|:---:|:---:|
| 1,000 | 1–2 s |
| 10,000 | 5–10 s |
| 100,000 | 1–2 perc |
| 1,000,000 | 10–30 perc |

A nagy léptékű funkció jobb kihasználására célszerű néhány stratégiát megfontolni.

## <a name="step-31-customize-time-interval"></a>3.1. lépés: Időintervallum testreszabása

Amint azt a `TrainLargeFaceList()` mutatja, van egy `timeIntervalInMilliseconds`, ami késlelteti a végtelen betanítási állapot ellenőrző folyamatot. A több arcot tartalmazó LargeFaceList esetén nagyobb időköz választása csökkenti a meghívások számát és költségét. Az időközt a LargeFaceList várt kapacitásának függvényében kell beállítani.

Az azonos stratégia LargePersonGroup is vonatkozik. Például, amikor egy LargePersonGroup képzési 1 000 000 személyekkel a `timeIntervalInMilliseconds` 60 000 (1 perces intervallum) lehet.

## <a name="step-32-small-scale-buffer"></a>3.2. lépés: Kisméretű puffer

Személyek és arcok csak betanítás után kereshetők a LargePersonGroup, illetve a LargeFaceList elemei között. Dinamikus esetben állandó az új személyek, arcok hozzáadása és azonnal kereshetőnek is kell lenniük, a betanítás azonban a kívántnál hosszabb ideig tarthat. A probléma mérséklésére egy lehetőség az extra kicsi méretű LargePersonGroup LargeFaceList használata pufferként, csak az újonnan felvett bejegyzéseknek. Ezt a puffert a sokkal kisebb mérete miatt rövidebb idő alatt be lehet tanítani és az azonnali keresésnek működnie kell ezen az átmeneti pufferen. Ez a puffer és a fő LargePersonGroup/LargeFaceList betanítása együttesen használandó úgy, hogy a fő betanítást ritkább időközönként kell végezni, például az éjszaka közepén, naponta.

Példa munkafolyamat:
1. Hozzon létre egy fő szerepű (fő kollekciót) és egy puffer szerepű (puffer kollekció) LargePersonGroup/LargeFaceList kollekciót. A puffer kollekciót csak az újonnan felvett személyek, arcok számára használja.
1. Az új személyeket, arcokat a fő és a puffer kollekcióhoz is adja hozzá.
1. Rövid időközönként csak a puffer kollekcióra végezzen betanítást, hogy az újonnan felvett elemek hatályba lépjenek.
1. Az Identification/FindSimilar hívást mind a fő kollekcióra, mind a puffer kollekcióra végezze el és az eredményeket egyesítse.
1. Amikor a puffer kollekció mérete elér egy határt vagy a rendszer holt idejében, hozzon létre egy új puffer kollekciót és indítsa el a betanítást a fő kollekcióra.
1. Amikor a fő kollekción véget ér a betanítás, törölje a régi puffer kollekciót.

## <a name="step-33-standalone-training"></a>3.3. lépés önálló képzés

Ha viszonylag hosszú várakozási idő is elfogadható, akkor nincs szükség közvetlenül az új adatok felvétele után elindítani a betanítás műveletet. Ehelyett a betanítás művelet a fő működésről leválasztva indítható rendszeresen. Ez a stratégia elfogadható várakozási idő mellett megfelelő a dinamikus helyzetekre, és a statikus helyzetekre alkalmazva tovább csökkenthető a betanítási gyakoriság.

Tegyük fel, hogy van egy `TrainLargePersonGroup` függvény, hasonló, mint a `TrainLargeFaceList`. Egy tipikus megvalósításában a különálló meghívásával LargePersonGroup képzést a [ `Timer` ](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) osztály `System.Timers` lenne:

```CSharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    FaceServiceClient.CreateLargePersonGroupAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Setup a standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1 minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1 hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces and Identification except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

Bővebb információk adatkezeléssel és azonosításhoz kapcsolódó megvalósításokkal kapcsolatban: [Hogyan lehet arcokat hozzáadni](how-to-add-faces.md) és [Hogyan lehet arcokat azonosítani a képen](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a>Összegzés

Ebben az útmutatóban megtanulta, hogyan történhet a meglevő PersonGroup/FaceList kódok (nem adat) áttelepítése a LargePersonGroup és LargeFaceList kollekciókhoz:

- A LargePersonGroup és a LargeFaceList hasonlóan működik, mint a PersonGroup/FaceList, azzal a különbséggel, hogy a LargeFaceList esetén betanítási művelet szükséges.
- A nagy méretű adathalmazoknál az adatok dinamikus frissítésére megfelelő betanítási stratégiát kell választani.

## <a name="next-steps"></a>További lépések

Hajtsa végre az arcok ad hozzá egy is lehet PersonGroup vagy hajtsa végre a műveletet egy is lehet PersonGroup azonosítása megismeréséhez használati útmutatója.

- [Hogyan lehet arcokat hozzáadni](how-to-add-faces.md)
- [Hogyan lehet arcokat azonosítani a képen](HowtoIdentifyFacesinImage.md)