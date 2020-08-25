---
title: 'Példa: arcok hozzáadása PersonGroup-Face'
titleSuffix: Azure Cognitive Services
description: Ez az útmutató bemutatja, hogyan adhat hozzá nagy számú személyt és arcot egy PersonGroup objektumhoz az Azure Cognitive Services Face szolgáltatással.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 240905d538afc5c0f4b7f0e0bf400fac23c3183f
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "76169830"
---
# <a name="add-faces-to-a-persongroup"></a>Arcok hozzáadása egy PersonGroup

Ez az útmutató bemutatja, hogyan adhat hozzá nagy számú személyt és arcot egy PersonGroup objektumhoz. Ugyanez a stratégia a LargePersonGroup, a FaceList és a LargeFaceList objektumra is vonatkozik. Ez a minta C# nyelven íródott az Azure Cognitive Services Face .NET ügyféloldali kódtár használatával.

## <a name="step-1-initialization"></a>1. lépés: Inicializálás

A következő kód több változót deklarál, és egy segítő függvényt valósít meg az arc-hozzáadási kérelmek beosztásához:

- `PersonCount` a személy száma összesen.
- `CallLimitPerSecond` a másodpercenkénti meghívások maximális száma az előfizetés csomag szerint.
- `_timeStampQueue` a kérés időbélyegeket rögzítő üzenetsor.
- `await WaitCallLimitPerSecondAsync()` megvárja, amíg a következő kérelem elküldése nem érvényes.

```csharp
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

## <a name="step-2-authorize-the-api-call"></a>2. lépés: API-hívás engedélyezése

Ügyféloldali kódtár használatakor át kell adnia az előfizetési kulcsot a **FaceClient** osztály konstruktorának. Például:

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

Az előfizetési kulcs beszerzéséhez nyissa meg az Azure Marketplace-t a Azure Portal. További információ: [előfizetések](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>3. lépés: PersonGroup létrehozása

A „MyPersonGroup” nevű PersonGroup létrejön a személyek mentésére.
A kérelem időket a `_timeStampQueue` sorba állítja, az általános ellenőrzés biztosítása érdekében.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>4. lépés: a PersonGroup személyek létrehozása

A személyek párhuzamosan jönnek létre, és a `await WaitCallLimitPerSecondAsync()` rendszer azt is alkalmazza, hogy ne lépje túl a hívási korlátot.

```csharp
Person[] persons = new Person[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceClient.PersonGroupPerson.CreateAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>5. lépés: Arcok adása a személyekhez

A különböző személyeknek hozzáadott arcok egyidejűleg lesznek feldolgozva. Egy adott személyhez hozzáadott arcok sorrendben lesznek feldolgozva.
Újra `await WaitCallLimitPerSecondAsync()` meghívja a rendszer, hogy a kérelmek gyakorisága a korlátozás hatókörén belül legyen.

```csharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>Összefoglalás

Ebben az útmutatóban megtanulta, hogyan hozhat létre egy PersonGroup nagy számú személlyel és arcával. Több emlékeztető:

- Ez a stratégia a FaceLists és a LargePersonGroups is vonatkozik.
- Az arcok hozzáadása vagy törlése a LargePersonGroups különböző FaceLists vagy személyekkel párhuzamosan történik.
- Az arcok egy adott FaceList vagy személyhez való hozzáadása vagy törlése egymás után történik.
- Az egyszerűség kedvéért a lehetséges kivételek kezelése ebben az útmutatóban elmarad. Ha nagyobb megbízhatóságot szeretne növelni, alkalmazza a megfelelő újrapróbálkozási házirendet.

A következő funkciókat ismertetjük és mutatták be:

- Hozzon létre PersonGroups a [PersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API használatával.
- Hozzon létre személyeket a [PersonGroup-létrehozó](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API használatával.
- Arcok hozzáadása személyekhez a [PersonGroup személy – Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API használatával.

## <a name="related-topics"></a>Kapcsolódó témakörök

- [Képeken lévő arcok azonosítása](HowtoIdentifyFacesinImage.md)
- [Arcok felismerése a képeken](HowtoDetectFacesinImage.md)
- [A nagy léptékű funkció használata](how-to-use-large-scale.md)
