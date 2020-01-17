---
title: 'Példa: arcok hozzáadása PersonGroup-Face API'
titleSuffix: Azure Cognitive Services
description: Ez az útmutató bemutatja, hogyan adhat hozzá nagy számú személyt és arcot egy PersonGroup objektumhoz az Azure Cognitive Services Face API.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 891614caddf729acb58bc363df977031ad62fb07
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156709"
---
# <a name="add-faces-to-a-persongroup"></a>Arcok hozzáadása egy PersonGroup

Ez az útmutató bemutatja, hogyan adhat hozzá nagy számú személyt és arcot egy PersonGroup objektumhoz. Ugyanez a stratégia a LargePersonGroup, a FaceList és a LargeFaceList objektumra is vonatkozik. Ez a minta az Azure C# Cognitive Services Face API .net Ügyféloldali kódtár használatával íródik.

## <a name="step-1-initialization"></a>1\. lépés: Inicializálás

A következő kód több változót deklarál, és egy segítő függvényt valósít meg az arc-hozzáadási kérelmek beosztásához:

- `PersonCount` a személy száma összesen.
- `CallLimitPerSecond` a másodpercenkénti meghívások maximális száma az előfizetés csomag szerint.
- `_timeStampQueue` a kérés időbélyegeket rögzítő üzenetsor.
- `await WaitCallLimitPerSecondAsync()` vár, amíg a következő kérelem elküldése nem érvényes.

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

## <a name="step-2-authorize-the-api-call"></a>2\. lépés: API-hívás engedélyezése

Ügyféloldali kódtár használatakor át kell adnia az előfizetési kulcsot a **FaceClient** osztály konstruktorának. Példa:

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

Az előfizetési kulcs beszerzéséhez nyissa meg az Azure Marketplace-t a Azure Portal. További információ: [előfizetések](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>3\. lépés: PersonGroup létrehozása

A „MyPersonGroup” nevű PersonGroup létrejön a személyek mentésére.
A kérelem időket a `_timeStampQueue` sorba állítja, az általános ellenőrzés biztosítása érdekében.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>4\. lépés: a PersonGroup személyek létrehozása

A rendszer egyidejűleg hozza létre a személyeket, és `await WaitCallLimitPerSecondAsync()` is alkalmazza, hogy elkerülje a hívási korlátot.

```csharp
Person[] persons = new Person[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceClient.PersonGroupPerson.CreateAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>5\. lépés: Arcok adása a személyekhez

A különböző személyeknek hozzáadott arcok egyidejűleg lesznek feldolgozva. Egy adott személyhez hozzáadott arcok sorrendben lesznek feldolgozva.
`await WaitCallLimitPerSecondAsync()` meghívja a rendszer, hogy meggyőződjön arról, hogy a kérelmek gyakorisága a korlátozás hatókörén belül van.

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
- [Képeken lévő arcok észlelése](HowtoDetectFacesinImage.md)
- [A nagyméretű szolgáltatás használata](how-to-use-large-scale.md)
