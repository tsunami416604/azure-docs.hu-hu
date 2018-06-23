---
title: A felügyeleti teendők központjaként szolgáltatással a Arcfelismerési API |} Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: A felügyeleti teendők központjaként szolgáltatással kognitív szolgáltatások Arcfelismerési API-t.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: f7b3ac57cf6b24c8a90b4ea59757d3a2cfafd781
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347191"
---
# <a name="how-to-use-the-large-scale-feature"></a>A felügyeleti teendők központjaként szolgáltatás használata

Ez az útmutató a kód áttelepítés növelheti a meglévő PersonGroup és FaceList LargePersonGroup és LargeFaceList rendre speciális cikk is.
Ez az útmutató azt feltételezi, hogy ismerjék a PersonGroup és FaceList alapvető használati és az áttelepítési folyamat ismerteti.
Első ismeri a alapvető műveleteket, talál egyéb oktatóprogramok például [képek lapok azonosítása](HowtoIdentifyFacesinImage.md),

Microsoft Arcfelismerési API legutóbb kiadott lehetővé nagy méretű forgatókönyveket, LargePersonGroup és LargeFaceList együttesen nagyméretű műveletek a két funkció.
LargePersonGroup legfeljebb 1 000 000 személyek minden legfeljebb 248 felületei is tartalmazhat, és LargeFaceList legfeljebb 1 000 000 lapok tárolására képes.

A felügyeleti teendők központjaként műveletek hasonló a hagyományos PersonGroup és FaceList, de van néhány fontos különbség új architektúrája miatt.
Ez az útmutató azt feltételezi, hogy ismerjék a PersonGroup és FaceList alapvető használati és az áttelepítési folyamat ismerteti.
A minták a Arcfelismerési API-ügyfél szalagtárat használó C# nyelven íródtak.

Ahhoz, hogy a tapasztalt keresési teljesítmény összetevőkhöz azonosítás és FindSimilar a nagy méretű, egy tanítási művelet előre feldolgozzák a LargeFaceList és LargePersonGroup bevezetni kell.
A képzési időt másodpercben attól függően, hogy a tényleges kapacitási körülbelül fél órát a változik.
A képzési időszak alatt továbbra is lehetséges azonosítása és FindSimilar elvégzése, ha sikeres képzési nagyrészt még az előtt.
Azonban a hátránya, hogy az új hozzáadott személyek/lapok nem jelennek az eredmény egy nagy méretű képzést áttelepítés új után befejezéséig.

## <a name="concepts"></a> Alapfogalmak

Ha nem ismeri a jelen útmutató a következő fogalmakat, a definíciók megtalálható a [szószedet](../Glossary.md):

- LargePersonGroup: Gyűjteménye személyek legfeljebb 1 000 000 kapacitással rendelkező átjáróeszközt.
- LargeFaceList: Gyűjteménye lapok legfeljebb 1 000 000 kapacitással rendelkező átjáróeszközt.
- Vonat: Előtti folyamat azonosítása/FindSimilar teljesítmény biztosítása érdekében.
- Azonosítás: Egy vagy több lapok PersonGroup vagy LargePersonGroup azonosításához.
- FindSimilar: Keresési hasonló lapok FaceList vagy LargeFaceList.

## <a name="initialization"></a> 1. lépés: Engedélyezze az API-hívás

A Arcfelismerési API ügyféloldali kódtár használata esetén az előfizetés és előfizetés-végpont továbbítja a rendszer a FaceServiceClient osztály konstruktor. Példa:

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionRegion = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/";
FaceServiceClient FaceServiceClient = new FaceServiceClient(SubscriptionKey, SubscriptionRegion);
```

Az Előfizetés kulcs megfelelő végpont az Azure-portálon a piactér lapján lehet lekérni.
Lásd: [előfizetések](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="migrate"></a> 2. lépés: Kód áttelepítési művelet:

Ez a szakasz csak a áttelepítése PersonGroup/FaceList megvalósítását, hogy LargePersonGroup/LargeFaceList összpontosít.
Bár LargePersonGroup/LargeFaceList eltér a tervezési és megvalósítási belső PersonGroup/FaceList, az API-felületeket hasonlóak kompatibilitás vissza.

Ehelyett újra létrehozni a LargePersonGroup/LargeFaceList telepítette, adatok áttelepítése nem támogatott.

## <a name="largepersongroup"></a> 2.1. lépés: LargePersonGroup PersonGroup áttelepítése

Az áttelepítés LargePersonGroup PersonGroup nem smooth, mert az pontosan azonos csoport szintű műveletek osztoznak.

PersonGroup/személy kapcsolatos végrehajtására, csak az API elérési utak vagy az SDK osztály vagy modul módosítsa LargePersonGroup és LargePersonGroup személy szükség.

Tekintetében adatáttelepítés, lásd: [hogyan hozzáadása előtt álló](how-to-add-faces.md) referenciaként.

## <a name="largefacelist"></a> 2.2. lépés: LargeFaceList FaceList áttelepítése

| FaceList API-k | LargeFaceList API-k |
|:---:|:---:|
| Létrehozás | Létrehozás |
| Törlés | Törlés |
| Lekérés | Lekérés |
| Lista | Lista |
| Frissítés | Frissítés |
| - | Tanítás |
| - | Képzési állapotának beolvasása |

Az előző táblázatban az lista szintű műveletek közötti FaceList és LargeFaceList-összehasonlítást láthat.
Amint van, LargeFaceList tartalmaz új műveleteket, tanítási és képzési állapotának beolvasása FaceList képest.
A betanítása LargeFaceList első előfeltétele a [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) művelet közben ott nincs vonat FaceList szükséges.
Az alábbi részlet egy segítő működnek, várjon, amíg egy LargeFaceList képzésének.

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

Korábban a adja hozzá a lapokat és FindSimilar FaceList tipikus használati lenne.

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

Ha az áttelepítés LargeFaceList, azt kell válnia

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

Ahogy fent is látható, az adatok kezelése és FindSimilar részét szinte azonosak.
Az egyetlen kivétel, hogy egy friss előre feldolgozásra vonat műveletet meg kell végeznie a LargeFaceList, mielőtt FindSimilar működik.

## <a name="train"></a>3. lépés: A vonat javaslatok

Bár a vonat művelet felgyorsítja a [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) és [azonosító](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), a képzési idő szenved, különösen akkor, ha nagy méretű életbe.
A különböző méretű becsült képzési idő, az alábbi táblázatban szerepel:

| A skála (oldal vagy személyek) | Becsült idő betanítása |
|:---:|:---:|
| 1,000 | 1-2/s |
| 10,000 | 5 – 10 s |
| 100,000 | 1 - 2 perc |
| 1,000,000 | 10 – 30 perc |

Jobb használatára a felügyeleti teendők központjaként szolgáltatást, néhány stratégiák ajánlott figyelembe kell venni.

## <a name="interval"></a>3.1. lépés: Az időtartam alatt testreszabása

Látható módon a `TrainLargeFaceList()`, van egy `timeIntervalInMilliseconds` késleltetése folyamat ellenőrzése végtelen képzési állapotát.
A további lapok LargeFaceList nagyobb időköze csökkenti a hívás számát és költség.
Az időtartam alatt a LargeFaceList várt kapacitásának megfelelően kell szabható testre.

Azonos stratégia LargePersonGroup is vonatkozik.
Amikor egy LargePersonGroup betanítása 1 000 000 személyekkel, például a `timeIntervalInMilliseconds` (más néven lehet 60 000 1 percenként).

## <a name="buffer"></a>3.2-es lépés csak kevés számítógépet érintő puffer

Személyek/lapok LargePersonGroup/LargeFaceList a rendszer kereshető után csak betanítása közben.
Dinamikus konfigurációban új személyek/lapokat folyamatosan kerülnek, és azonnal kereshető kell, de az képzési hosszabb, mint a kívánt is beletelhet.
Ez a probléma orvoslása érdekében használható egy extra csak kevés számítógépet érintő LargePersonGroup LargeFaceList puffer csak az újonnan hozzáadott bejegyzéseket.
A puffer miatt sokkal kisebb méretű betanítása rövidebb ideig tart, és a közvetlen keresést az ideiglenes puffert a kell működnie.
Ez a puffer hajtja végre a fő képzési több ritka időközönkénti, például a közepes éjszakai és a napi a fő LargePersonGroup LargeFaceList képzési együtt használja.

Egy példa-munkafolyamat:
1. Hozzon létre egy fő LargePersonGroup/LargeFaceList (fő gyűjtemény) és a puffer LargePersonGroup/LargeFaceList (puffer gyűjtemény). A puffer gyűjteménye csak az újonnan hozzáadott személyek/lapokat.
1. Új személyek/lapok hozzáadása a puffer gyűjtemény és a fő gyűjtésére.
1. A puffer gyűjtemény egy rövid időintervallum annak biztosítása érdekében az újonnan felvett bejegyzések lép hatályba csak betanításához.
1. Hívja fel a azonosítás/FindSimilar ellen mind a fő gyűjteményt, és a puffer gyűjtemény, és egyesíti az eredményeket.
1. Gyűjtemény pufferméret küszöbértékre, illetve a rendszer tétlenség növekszik, ha hozzon létre egy új puffer gyűjteményt, és elindítani a vonat fő gyűjteményen.
1. Törölje a régi puffer gyűjteményt a Befejezés után képzési a fő gyűjteményen.

## <a name="standalone"></a>3.3 lépés önálló képzési

Ha egy viszonylag hosszú várakozási elfogadható, nincs szükség új adatok hozzáadása után a vonat művelet indításához.
Ehelyett a vonat művelet ossza fel a fő logikát is, és rendszeresen elindul.
Ennek a stratégiának megfelelő-e dinamikus forgatókönyvek elfogadható késése mellett, és további csökkentése érdekében a vonat gyakoriság statikus forgatókönyvek alkalmazhatók.

Tegyük fel, hogy van egy `TrainLargePersonGroup` funkcióhoz hasonló a `TrainLargeFaceList`.
A különálló tipikus végrehajtásának képzés LargePersonGroup a figyelőn a [ `Timer` ](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) osztályt `System.Timers` lenne:

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

Adatkezelés és -implementációkban azonosító kapcsolatos további információt lásd: [hogyan hozzáadása előtt álló](how-to-add-faces.md) és [hogyan kép azonosítása néz](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a> Összefoglalás

Ebben az útmutatóban megtanulhatta, hogyan telepíthetők át a meglévő PersonGroup/FaceList kód (adatokat nem) a LargePersonGroup/LargeFaceList:

- LargePersonGroup és LargeFaceList hasonlóan működik a PersonGroup/FaceList, azzal a különbséggel LargeFaceList Train-műveletet igényel.
- Dinamikus adatok frissítése a nagyméretű adatkészlet megfelelő vonat stratégia igénybe vehet.

## <a name="related"></a> Kapcsolódó témakörök

- [A kép néz azonosítása](HowtoIdentifyFacesinImage.md)
- [Lapok hozzáadása](how-to-add-faces.md)
