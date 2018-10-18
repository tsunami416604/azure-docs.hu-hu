---
title: 'Példa: A nagy léptékű funkció használata – Face API'
titleSuffix: Azure Cognitive Services
description: Nagy léptékű funkció használata a Face API-ban.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: e8bbf78da84ddb77ce956e37f91be46e96144991
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123079"
---
# <a name="example-how-to-use-the-large-scale-feature"></a>Példa: Hogyan használható a nagy léptékű funkció

Ez az útmutató a meglevő PersonGroup és FaceList helyett rendre a LargePersonGroup és a LargeFaceList használatára történő átállással kapcsolatos kódmigrációról szóló haladó szintű cikk.
Az útmutató a PersonGroup és FaceList alapvető ismeretét feltételezve bemutatja az áttelepítési folyamatot.
Az alap működés megismeréséhez tekintsen meg más oktatóanyagokat, például: [Arcok azonosítása a képeken](HowtoIdentifyFacesinImage.md),

A Face API nemrég megjelentetett két új funkciót a nagy léptékű (LargePersonGroup és LargeFaceList) esetekre, amelyeket együttesen nagy léptékű műveleteknek hívunk.
A LargePersonGroup legfeljebb 1 000 000 személyt képes tárolni egyenként 248 arccal, a LargeFaceList pedig legfeljebb 1 000 000 arcot képes tárolni.

A nagy léptékű műveletek hasonlóak, mint a hagyományos PersonGroup és FaceList esetében, de van néhány jelentős különbség is az új architektúra miatt.
Az útmutató a PersonGroup és FaceList alapvető ismeretét feltételezve bemutatja az áttelepítési folyamatot.
A példák a Face API ügyfélkódtár használatával C# nyelven íródtak.

Ahhoz, hogy az Identification és FindSimilar funkciókra nagy léptékre is engedélyezni lehessen a Face teljesítményét, be kell vezetni a LargeFaceList és a LargePersonGroup kollekciók előzetes feldolgozására betanítási műveletet.
A betanítási idő a tényleges kapacitástól függően másodpercek és fél óra között változhat.
A betanítási időszakban is lehetséges az Identification és a FindSimilar funkciók végrehajtása, ha korábban végeztek már sikeres tanítást.
Azonban a hátránya az, hogy az újonnan hozzáadott személyek és arcok nem fognak megjelenni az eredményekben addig, amíg a migráció után meg nem történik a nagyobb léptéken is a betanítás.

## <a name="concepts"></a>Alapelvek

Ha nem ismeri az útmutatóban használt alábbi fogalmakat, a definíciók megtalálhatók a [szószedetben](../Glossary.md):

- LargePersonGroup: Személyek kollekciója 1 000 000 fős maximális kapacitással.
- LargeFaceList: Arcok kollekciója 1 000 000 darabos maximális kapacitással.
- Betanítás: Az Identification/FindSimilar teljesítményét garantáló előzetes folyamat.
- Identification: A PersonGroup vagy LargePersonGroup alapján egy vagy több arc azonosítása.
- FindSimilar: A LargeFaceList vagy FaceList kollekcióban hasonló arcokat keres.

## <a name="step-1-authorize-the-api-call"></a>1. lépés: API-hívás engedélyezése

Az Face API ügyfélkódtár használatakor az előfizetési kulcs és az előfizetési végpont megadása a FaceServiceClient osztály konstruktorán keresztül történik. Például:

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionRegion = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/";
FaceServiceClient FaceServiceClient = new FaceServiceClient(SubscriptionKey, SubscriptionRegion);
```

Az előfizetési kulcs és a hozzátartozó végpont az Azure portál Marketplace oldaláról szerezhető be.
Lásd [Előfizetések](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration-in-action"></a>2. lépés: Kód áttelepítési művelet

Ez a szakasz csak a PersonGroup és a FaceList alapú megvalósítások LargePersonGroup és LargeFaceList használatára történő áttelepítési folyamatával foglalkozik.
Bár a LargePersonGroup/LargeFaceList kialakításában és belső megvalósításában más, mint a PersonGroup/FaceList, az API-felületek hasonlóak a visszafelé kompatibilitás érdekében.

Az adatmigráció nem támogatott, ehelyett a LargePersonGroup/LargeFaceList újbóli létrehozását kell elvégezni.

## <a name="step-21-migrate-persongroup-to-largepersongroup"></a>2.1. lépés: Átállás a PersonGroup használatáról a LargePersonGroup használatra

Az áttelepítés a PersonGroup -> LargePersonGroup irányban simán megy, mert a csoport szintű műveleteik teljesen megegyeznek.

A PersonGroup/Person esetében elég csak az API elérési utat és az SDK osztályt/modult módosítani a LargePersonGroup és LargePersonGroup megvalósításánál.

Az adat migráció tekintetében lásd referenciaként lásd: [Hogyan lehet arcokat hozzáadni](how-to-add-faces.md).

## <a name="step-22-migrate-facelist-to-largefacelist"></a>2.2. lépés: Átállás a FaceList használatáról a LargeFaceList használatára

| FaceList API-k | LargeFaceList API-k |
|:---:|:---:|
| Létrehozás | Létrehozás |
| Törlés | Törlés |
| Lekérés | Lekérés |
| Lista | Lista |
| Frissítés | Frissítés |
| - | Betanítás |
| - | Betanítási állapot lekérdezése |

Az előző táblázat a FaceList és a LargeFaceList listaszintű műveleteinek összehasonlítása.
Amint látható, a LargeFaceList új műveletekkel is rendelkezik a FaceList-hez képest, a Betanítás és a Betanítási állapot lekérdezése.
A LargeFaceList betanítása a [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) művelet előfeltétele, míg a FaceList esetében nincs betanítási követelmény.
Az alábbi kódrészlet a LargeFaceList betanítását megváró segédfüggvény.

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

Előzőleg, a FaceList jellemző használata arcok hozzáadása és a FindSimilar lenne

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

Amilyen a LargeFaceList használatra történő átállás után lenne

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

Ahogy fent is látható, az adatok kezelése és a FindSimilar rész szinte azonos.
Az egyetlen kivétel, hogy friss előfeldolgozási betanítás műveletnek kell megtörténnie a LargeFaceList esetében, hogy a FindSimilar működjön.

## <a name="step-3-train-suggestions"></a>3. lépés: Betanítási javaslatok

Bár a betanítás művelet felgyorsítja a [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) és [Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) műveleteket, a betanítási idő különösen nagyobb méreteknél kárt szenved.
A különböző méretek esetén becsült betanítási időt sorolja fel a következő táblázat:

| Méret (arc vagy személy) | Becsült betanítási idő |
|:---:|:---:|
| 1,000 | 1–2 s |
| 10,000 | 5–10 s |
| 100 000 | 1–2 perc |
| 1,000,000 | 10–30 perc |

A nagy léptékű funkció jobb kihasználására célszerű néhány stratégiát megfontolni.

## <a name="step-31-customize-time-interval"></a>3.1. lépés: Az időköz testreszabása

Amint azt a `TrainLargeFaceList()` mutatja, van egy `timeIntervalInMilliseconds`, ami késlelteti a végtelen betanítási állapot ellenőrző folyamatot.
A több arcot tartalmazó LargeFaceList esetén nagyobb időköz választása csökkenti a meghívások számát és költségét.
Az időközt a LargeFaceList várt kapacitásának függvényében kell beállítani.

Ugyanez a stratégia vonatkozik a LargePersonGroup esetére is.
Például az 1 000 000 személyt tartalmazó LargePersonGroup betanításakor a `timeIntervalInMilliseconds` lehetne 60 000 (azaz 1-perces az időköz).

## <a name="step-32-small-scale-buffer"></a>3.2. lépés: Kisméretű puffer

Személyek és arcok csak betanítás után kereshetők a LargePersonGroup, illetve a LargeFaceList elemei között.
Dinamikus esetben állandó az új személyek, arcok hozzáadása és azonnal kereshetőnek is kell lenniük, a betanítás azonban a kívántnál hosszabb ideig tarthat.
A probléma mérséklésére egy lehetőség az extra kicsi méretű LargePersonGroup LargeFaceList használata pufferként, csak az újonnan felvett bejegyzéseknek.
Ezt a puffert a sokkal kisebb mérete miatt rövidebb idő alatt be lehet tanítani és az azonnali keresésnek működnie kell ezen az átmeneti pufferen.
Ez a puffer és a fő LargePersonGroup/LargeFaceList betanítása együttesen használandó úgy, hogy a fő betanítást ritkább időközönként kell végezni, például az éjszaka közepén, naponta.

Példa munkafolyamat:
1. Hozzon létre egy fő szerepű (fő kollekciót) és egy puffer szerepű (puffer kollekció) LargePersonGroup/LargeFaceList kollekciót. A puffer kollekciót csak az újonnan felvett személyek, arcok számára használja.
1. Az új személyeket, arcokat a fő és a puffer kollekcióhoz is adja hozzá.
1. Rövid időközönként csak a puffer kollekcióra végezzen betanítást, hogy az újonnan felvett elemek hatályba lépjenek.
1. Az Identification/FindSimilar hívást mind a fő kollekcióra, mind a puffer kollekcióra végezze el és az eredményeket egyesítse.
1. Amikor a puffer kollekció mérete elér egy határt vagy a rendszer holt idejében, hozzon létre egy új puffer kollekciót és indítsa el a betanítást a fő kollekcióra.
1. Amikor a fő kollekción véget ér a betanítás, törölje a régi puffer kollekciót.

## <a name="step-33-standalone-training"></a>3.3. lépés: Önálló betanítás

Ha viszonylag hosszú várakozási idő is elfogadható, akkor nincs szükség közvetlenül az új adatok felvétele után elindítani a betanítás műveletet.
Ehelyett a betanítás művelet a fő működésről leválasztva indítható rendszeresen.
Ez a stratégia elfogadható várakozási idő mellett megfelelő a dinamikus helyzetekre, és a statikus helyzetekre alkalmazva tovább csökkenthető a betanítási gyakoriság.

Tegyük fel, hogy van egy `TrainLargePersonGroup` függvény, hasonló, mint a `TrainLargeFaceList`.
Az önálló betanítás jellemző megvalósítása LargePersonGroup esetén a [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) meghívásával
az `System.Timers` osztálya lehetne:

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

## <a name="related-topics"></a>Kapcsolódó témakörök

- [Hogyan lehet arcokat azonosítani a képen](HowtoIdentifyFacesinImage.md)
- [Hogyan lehet arcokat hozzáadni](how-to-add-faces.md)
