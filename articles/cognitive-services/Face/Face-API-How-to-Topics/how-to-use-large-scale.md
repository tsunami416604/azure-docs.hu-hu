---
title: 'Példa: A nagy méretű szolgáltatás – Face API használata'
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
ms.openlocfilehash: dcbec817f771324219a68de96eb5dd262a887fc1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449042"
---
# <a name="example-use-the-large-scale-feature"></a>Példa: A nagy léptékű funkció használata

Ez az útmutató olyan vertikális meglévő is lehet PersonGroup és FaceList objektumokból LargePersonGroup és LargeFaceList objektumokra, illetve egy speciális cikkünket. Ez az útmutató azt ismerteti, hogy az áttelepítési folyamat. Azt is lehet PersonGroup és FaceList objektumok alapszintű ismeretét feltételezi, hogy a [Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) művelet, és az arcok felismerése funkciók. Az e kapcsolatos további információkért tekintse meg a [Arcfelismerési](../concepts/face-recognition.md) fogalmi útmutató.

LargePersonGroup és LargeFaceList együttesen nevezzük nagyméretű műveleteket. LargePersonGroup akár 1 millió személyek, minden egyes 248 arcok legfeljebb is tartalmazhat. LargeFaceList arcok legfeljebb 1 millió is tartalmazhat. A nagy méretű műveletek hasonlóak a hagyományos is lehet PersonGroup és FaceList, de van néhány különbség miatt az új architektúrával. 

A minták nyelven írták C# az Azure Cognitive Services Face API ügyféloldali kódtár használatával.

> [!NOTE]
> Ahhoz, hogy a Face keresési teljesítményének azonosítása és FindSimilar nagy méretű, vezessen be egy Train műveletet, hogy a LargeFaceList és LargePersonGroup előfeldolgozása. Képzési függ a másodperc, a tényleges kapacitási alapján körülbelül fél óra. A képzési időszakban azonosítása és FindSimilar végrehajtani, ha egy sikeres képzési működő elkészítése előtt. A hátránya, hogy az új hozzáadott személyek és arcok nem jelennek meg az eredmény nagyobb méretű betanítási új bejegyzés az áttelepítés befejeződéséig.

## <a name="step-1-initialize-the-client-object"></a>1\. lépés: Az ügyfél objektum inicializálása

A Face API-ügyfélkódtárát használatakor az előfizetési kulcs és az előfizetési végpont keresztül átadott FaceClient osztály a konstruktor. Példa:

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Az előfizetési kulcsot a megfelelő végponttal, keresse fel az Azure Marketplace-en az Azure Portalról.
További információkért lásd: [előfizetések](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>2\. lépés: Kód áttelepítése

Ez a szakasz is lehet PersonGroup vagy FaceList megvalósítási áttelepítése LargePersonGroup vagy LargeFaceList összpontosít. Bár a LargePersonGroup vagy LargeFaceList eltér az is lehet PersonGroup vagy FaceList a tervezési és megvalósítási belső, az API-felületek hasonlóak a visszamenőleges kompatibilitás érdekében.

Adatok migrálása nem támogatott. Újra létrehozza a LargePersonGroup vagy LargeFaceList helyette.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Egy is lehet PersonGroup át egy LargePersonGroup

Áttelepítés egy LargePersonGroup egy is lehet PersonGroup használata egyszerű. Pontosan ugyanaz-szintű műveletek osztoznak.

Megvalósítás is lehet PersonGroup vagy személy kapcsolatos fontos módosítása csak az API elérési utak vagy az SDK osztály/modul LargePersonGroup és LargePersonGroup személy.

Adja hozzá az arcokat és személyek az is lehet PersonGroup, az új LargePersonGroup. További információkért lásd: [adja hozzá az arcok](how-to-add-faces.md).

### <a name="migrate-a-facelist-to-a-largefacelist"></a>Egy FaceList át egy LargeFaceList

| FaceList API-k | LargeFaceList API-k |
|:---:|:---:|
| Hozzon létre | Hozzon létre |
| Törlés | Törlés |
| Lekérés | Lekérés |
| Lista | Lista |
| frissítés | frissítés |
| - | Betanítás |
| - | Betanítási állapot lekérdezése |

Az előző táblázat a FaceList és a LargeFaceList listaszintű műveleteinek összehasonlítása. Amint látható, LargeFaceList tartalmaz új művelet, tanítási és képzési állapotának beolvasása FaceList képest. A LargeFaceList képzési előfeltétele, a [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) műveletet. Képzési nem szükséges a FaceList. Az alábbi kódrészlet egy kell várni egy LargeFaceList képzése segítőfüggvény:

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

Korábban hozzáadott arcokat és FindSimilar tipikus felhasználása FaceList kikeresi a következőhöz hasonló:

```CSharp
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

Ha az áttelepítés LargeFaceList, válik a következő:

```CSharp
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

Ahogy korábban az adatok kezelését és a FindSimilar rész szinte azonosak. Az egyetlen kivétel, hogy egy friss előfeldolgozási Train műveletet meg kell végeznie a LargeFaceList, mielőtt FindSimilar működik.

## <a name="step-3-train-suggestions"></a>3\. lépés: Javaslatok betanítása

Bár a vonat művelet felgyorsítja [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) és [azonosító](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), romlik a betanítási idő, különösen akkor, ha a várható a nagy méretű. A különböző skálákkal becsült képzési időpontja az alábbi táblázatban szerepel.

| Nagy számú arcok vagy személyek | Becsült képzési idő |
|:---:|:---:|
| 1,000 | 1 – 2 mp-ben |
| 10,000 | 5 – 10 másodperc |
| 100,000 | 1 – 2 perc |
| 1,000,000 | 10-30 min |

Jobban tudják használni a nagy méretű funkciót, javasoljuk a következő stratégiák.

### <a name="step-31-customize-time-interval"></a>3\.1. lépés: Időintervallum testreszabása

Amint azt `TrainLargeFaceList()`, adott idő alatt ezredmásodpercben késleltetheti a végtelen képzési állapotának ellenőrzése folyamatban van. A több arcot tartalmazó LargeFaceList esetén nagyobb időköz választása csökkenti a meghívások számát és költségét. Testre szabhatja az időintervallum szerint a LargeFaceList várt kapacitását.

Az azonos stratégia LargePersonGroup is vonatkozik. Például ha, betanításához egy LargePersonGroup 1 millió személyekkel `timeIntervalInMilliseconds` lehet 60 000, egy 1 perces időszak, amely.

### <a name="step-32-small-scale-buffer"></a>3\.2. lépés: Semmibe puffer

Azok a személyek vagy egy LargePersonGroup vagy egy LargeFaceList arcok csak azután betanítása kereshetők meg. A dinamikus forgatókönyvekben új személyek, illetve az arcok folyamatosan kerülnek, és azonnal kereshető kell lennie, de képzési hosszabb, mint a kívánt is igénybe vehet. 

A probléma mérséklése érdekében használjon egy extra semmibe LargePersonGroup vagy LargeFaceList pufferként csak az újonnan hozzáadott bejegyzéseket. Ezt a puffert miatt a kisebb méretet taníthat be rövidebb ideig tart. A közvetlen keresési funkció az átmeneti tárolóba a működnie kell. A fő LargePersonGroup vagy LargeFaceList képzési együtt használja ezt a puffert a fő képzési sparser időközönként futtatásával. Példák az éjszakai és napi közepén.

Példa munkafolyamat:

1. Hozzon létre egy fő LargePersonGroup vagy LargeFaceList, azaz a fő gyűjtemény. Hozzon létre egy puffer LargePersonGroup vagy LargeFaceList puffer gyűjteményéhez. A puffer gyűjteménye csak az újonnan hozzáadott személyek vagy arcokat.
1. Új személyek, illetve az arcok hozzáadása a buffer-gyűjtemény és a fő gyűjtésére.
1. A puffer gyűjtemény adott rövid idő alatt, győződjön meg arról, hogy az újonnan felvett bejegyzések érvénybe csak betanításához.
1. Hívja meg az azonosító vagy FindSimilar ellen a fő gyűjtemény és a puffer gyűjteményben is. Egyesíti az eredményeket.
1. A gyűjtemény pufferméret küszöbértékre vagy inaktív rendszer egyszerre növekszik, amikor új puffer gyűjtemény létrehozása. A fő gyűjtemény Train művelet indítása.
1. A fő gyűjteményen a vonat művelet végeztével, törölje a régi puffer gyűjtemény.

### <a name="step-33-standalone-training"></a>3\.3. lépés: Önálló képzés

Egy viszonylag hosszú várakozási ideje fogadható el, ha nem szükséges a megfelelő műveletet a Train eseményindító hozzáadása új adatok után. Ehelyett a betanítás művelet a fő működésről leválasztva indítható rendszeresen. Ez a stratégia akkor dinamikus szituációkra elfogadható késése mellett. További Train gyakoriságának csökkentése érdekében statikus forgatókönyvek alkalmazhatók.

Tegyük fel, hogy van egy `TrainLargePersonGroup` funkcióhoz hasonló `TrainLargeFaceList`. A különálló képzés a figyelőn egy LargePersonGroup tipikus megvalósításában a [ `Timer` ](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) osztály `System.Timers` van:

```CSharp
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

Adatkezelési és -implementációk azonosító kapcsolatos további információ: [adja hozzá az arcok](how-to-add-faces.md) és [Arcazonosítás képen egy](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a>Összefoglalás

Ebben az útmutatóban megtanulta, hogyan telepítheti át a meglévő is lehet PersonGroup vagy FaceList kód, nem adatokat, a LargePersonGroup vagy LargeFaceList:

- LargePersonGroup és LargeFaceList működik is lehet PersonGroup vagy FaceList, hasonló, azzal a különbséggel, hogy a vonat művelet LargeFaceList van szüksége.
- A nagy méretű adatkészletek dinamikus frissítése a megfelelő Train stratégia igénybe vehet.

## <a name="next-steps"></a>További lépések

Hajtsa végre az arcok ad hozzá egy is lehet PersonGroup vagy hajtsa végre a műveletet egy is lehet PersonGroup azonosítása megismeréséhez használati útmutatója.

- [Arcok hozzáadása](how-to-add-faces.md)
- [Arcazonosítás képen egy](HowtoIdentifyFacesinImage.md)