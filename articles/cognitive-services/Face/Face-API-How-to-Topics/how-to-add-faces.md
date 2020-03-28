---
title: 'Példa: Arcok hozzáadása a PersonGroup - Face csoporthoz'
titleSuffix: Azure Cognitive Services
description: Ez az útmutató bemutatja, hogyan adhat hozzá nagyszámú személyt és arcot egy PersonGroup objektumhoz az Azure Cognitive Services Face szolgáltatással.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 240905d538afc5c0f4b7f0e0bf400fac23c3183f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169830"
---
# <a name="add-faces-to-a-persongroup"></a>Lapok hozzáadása a személycsoporthoz

Ez az útmutató bemutatja, hogyan adhat hozzá nagyszámú személyt és arcot egy PersonGroup objektumhoz. Ugyanez a stratégia vonatkozik a LargePersonGroup, facelist és LargeFaceList objektumokra is. Ez a minta C# nyelven íródott az Azure Cognitive Services Face .NET ügyfélkódtár használatával.

## <a name="step-1-initialization"></a>1. lépés: Inicializálás

A következő kód több változót deklarál, és egy segítő függvényt valósít meg az archozzáadási kérelmek ütemezéséhez:

- `PersonCount` a személy száma összesen.
- `CallLimitPerSecond` a másodpercenkénti meghívások maximális száma az előfizetés csomag szerint.
- `_timeStampQueue` a kérés időbélyegeket rögzítő üzenetsor.
- `await WaitCallLimitPerSecondAsync()`megvárja, amíg érvényes a következő kérelem elküldése.

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

Ügyfélkódtár használata esetén át kell adnia az előfizetési kulcsot a **FaceClient** osztály konstruktorának. Példa:

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

Az előfizetési kulcs beszerezéséhez nyissa meg az Azure Marketplace-t az Azure Portalon. További információ: [Előfizetések](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>3. lépés: PersonGroup létrehozása

A „MyPersonGroup” nevű PersonGroup létrejön a személyek mentésére.
A kérelem időket a `_timeStampQueue` sorba állítja, az általános ellenőrzés biztosítása érdekében.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>4. lépés: Személyek létrehozása a PersonGroup számára

A személyek egyidejűleg `await WaitCallLimitPerSecondAsync()` jönnek létre, és a hívási korlát túllépését is biztosítják.

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

A különböző személyekhez hozzáadott arcokat egyidejűleg dolgozzák fel. Egy adott személyhez hozzáadott arcok at egymás után dolgozza fel a program.
`await WaitCallLimitPerSecondAsync()` Ismét a kérelem gyakorisága a korlátozás hatálya alá tartozik.

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

Ebben az útmutatóban megtanultad a PersonGroup létrehozásának folyamatát, amelyben rengeteg személy és arc van. Több emlékeztető:

- Ez a stratégia a FaceLists és a LargePersonGroups listákra is vonatkozik.
- Az arcok hozzáadása vagy törlése különböző facelistákhoz vagy a LargePersonGroups-ban lévő személyekhez egyidejűleg kerül feldolgozásra.
- Az arcok hozzáadása vagy törlése egy adott FaceList listához vagy egy LargePersonGroup-személyhez egymás után történik.
- Az egyszerűség kedvéért a lehetséges kivételek kezelése nem szerepel ebben az útmutatóban. Ha nagyobb robusztusságot szeretne növelni, alkalmazza a megfelelő újrapróbálkozási házirendet.

A következő jellemzőket ismertették és mutatták be:

- Személycsoportok létrehozása a [PersonGroup – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API használatával.
- Személyek létrehozása a [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API használatával.
- Arcok hozzáadása személyekhez a [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API használatával.

## <a name="related-topics"></a>Kapcsolódó témakörök

- [A képen lévő arcok azonosítása](HowtoIdentifyFacesinImage.md)
- [Arcok felismerése a képeken](HowtoDetectFacesinImage.md)
- [A nagy léptékű funkció használata](how-to-use-large-scale.md)
