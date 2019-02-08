---
title: 'Példa: Arcok – Face API hozzáadása'
titleSuffix: Azure Cognitive Services
description: A Face API használatával arcokat adhat a képekre.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 722a09b782c902642b599460835151928c16c5f4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859028"
---
# <a name="example-how-to-add-faces"></a>Példa: Arcok hozzáadása

Ez az útmutató ismerteti az ajánlott eljárást nagy mennyiségű emberek és arcok PersonGrouphoz adására.
Ugyanez a stratégia vonatkozik a FaceList és a LargePersonGroup esetére is.
A példák a Face API ügyfélkódtár használatával C# nyelven íródtak.

## <a name="step-1-initialization"></a>1. lépés: Az inicializálás

Több változó deklarálása és a kérések ütemezését végző segédfüggvény megvalósítása.

- `PersonCount` a személy száma összesen.
- `CallLimitPerSecond` a másodpercenkénti meghívások maximális száma az előfizetés csomag szerint.
- `_timeStampQueue` a kérés időbélyegeket rögzítő üzenetsor.
- `await WaitCallLimitPerSecondAsync()` vár, amíg a következő kérés küldése érvényessé nem válik.

```CSharp
const int PersonCount = 10000;
const int CallLimitPerSecond = 10;
static Queue<DateTime> _timeStampQueue = new Queue<DateTime>(CallLimitPerSecond);

static async Task WaitCallLimitPerSecondAsync()
{
    Monitor.Enter(_timeStampQueue);
    try
    {
        if (_timeStampQueue.Count >= CallLimitPerSecond)
        {
            TimeSpan timeInterval = DateTime.UtcNow - _timeStampQueue.Peek();
            if (timeInterval < TimeSpan.FromSeconds(1))
            {
                await Task.Delay(TimeSpan.FromSeconds(1) - timeInterval);
            }
            _timeStampQueue.Dequeue();
        }
        _timeStampQueue.Enqueue(DateTime.UtcNow);
    }
    finally
    {
        Monitor.Exit(_timeStampQueue);
    }
}
```

## <a name="step-2-authorize-the-api-call"></a>2. lépés: Engedélyezze az API-hívás

Az ügyfélkódtár használatakor az előfizetési kulcs megadása a FaceServiceClient osztály konstruktorán keresztül történik. Példa:

```CSharp
FaceServiceClient faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

Az előfizetési kulcs az Azure Portal Marketplace oldaláról szerezhető be. Lásd az [előfizetéseket](https://www.microsoft.com/cognitive-services/en-us/sign-up).

## <a name="step-3-create-the-persongroup"></a>3. lépés: Az is lehet PersonGroup létrehozása

A „MyPersonGroup” nevű PersonGroup létrejön a személyek mentésére.
A kérelem időket a `_timeStampQueue` sorba állítja, az általános ellenőrzés biztosítása érdekében.

```CSharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-to-the-persongroup"></a>4. lépés: Az is lehet PersonGroup személyeket létrehozása

A személyek egyidejűleg jönnek létre és a hívási limit túllépésének megelőzésére `await WaitCallLimitPerSecondAsync()` is alkalmazásra kerül.

```CSharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceServiceClient.CreatePersonAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>5. lépés: Arcok személyek hozzáadása

Arcok hozzáadása több személyhez párhuzamosan, egy konkrét személyhez pedig szekvenciálisan történik.
Újra csak a `await WaitCallLimitPerSecondAsync()` meghívásával biztosítjuk, hogy a kérések gyakorisága a határértékeken belül maradjon.

```CSharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceServiceClient.AddPersonFaceAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>Összegzés

Ebben az útmutatóban megismerte, hogyan történhet nagy számú embert és arcot tartalmazó PersonGroup létrehozása. Több emlékeztető:

- Ez a stratégia vonatkozik a FaceList és a LargePersonGroup esetére is.
- A LargePersonGroup-hoz személyek és a FaceList-hez arcok hozzáadása/törlése végezhető párhuzamosan.
- Egy konkrét FaceList-re vagy a LargePersonGroup esetén egy személyre vonatkozóan ugyanazt a műveletet egymás után kell elvégezni.
- Az egyszerűség kedvéért az esetleges kivételek lekezelésétől eltekintünk az útmutatóban. Ha a robusztusságot növelni szeretné, használni kell a megfelelő újrapróbálkozási szabályokat.

Az alábbiakban röviden felidézzük a korábban ismertetett és bemutatott funkciókat:

- PersonGroup csoportok létrehozása a [PersonGroup – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API használatával
- Személyek létrehozása a [PersonGroup Person – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API használatával
- Arcok hozzáadása személyekhez a [PersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API használatával

## <a name="related-topics"></a>Kapcsolódó témakörök

- [Hogyan lehet arcokat azonosítani a képen](HowtoIdentifyFacesinImage.md)
- [Hogyan lehet arcokat megtalálni a képen](HowtoDetectFacesinImage.md)
- [Hogyan használható a nagy léptékű funkció](how-to-use-large-scale.md)
