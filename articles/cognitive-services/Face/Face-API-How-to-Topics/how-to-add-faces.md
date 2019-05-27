---
title: 'Példa: Arcok ad hozzá egy is lehet PersonGroup – Face API'
titleSuffix: Azure Cognitive Services
description: A Face API használatával arcokat adhat a képekre.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 83aef90702e4a4cc4fd9bdfda486841f9b2a63a4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124498"
---
# <a name="add-faces-to-a-persongroup"></a>Arcok ad hozzá egy is lehet PersonGroup

Ez az útmutató bemutatja, hogyan személyek és oldalak nagy számú hozzáadása is lehet PersonGroup objektumhoz. Az azonos stratégia LargePersonGroup FaceList és LargeFaceList objektumra is vonatkozik. Ez a minta nyelven van megírva C# az Azure Cognitive Services Face API .NET ügyféloldali kódtár használatával.

## <a name="step-1-initialization"></a>1. lépés: Az inicializálás

Az alábbi kód kijelenti, hogy több változó és valósítja meg a face ütemezése segítő függvény hozzáadása kéri:

- `PersonCount` a személy száma összesen.
- `CallLimitPerSecond` a másodpercenkénti meghívások maximális száma az előfizetés csomag szerint.
- `_timeStampQueue` a kérés időbélyegeket rögzítő üzenetsor.
- `await WaitCallLimitPerSecondAsync()` megvárja, amíg érvényes a következő kérelem elküldéséhez.

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

## <a name="step-2-authorize-the-api-call"></a>2. lépés: Engedélyezze az API-hívás

Egy ügyféloldali kódtár használata esetén az FaceServiceClient osztály a konstruktor adja át az előfizetési kulcs. Példa:

```csharp
FaceServiceClient faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

Az előfizetési kulcsot, keresse fel az Azure Marketplace-en az Azure Portalról. További információkért lásd: [előfizetések](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>3. lépés: Az is lehet PersonGroup létrehozása

A „MyPersonGroup” nevű PersonGroup létrejön a személyek mentésére.
A kérelem időket a `_timeStampQueue` sorba állítja, az általános ellenőrzés biztosítása érdekében.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>4. lépés: Az is lehet PersonGroup tartozó személyek létrehozása

Személyek egyidejűleg jön létre, és `await WaitCallLimitPerSecondAsync()` is alkalmazza a rendszer a hívás túllépő elkerülése érdekében.

```csharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceServiceClient.CreatePersonAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>5. lépés: Arcok személyek hozzáadása

Más személyek hozzáadott arcok lehessen egyidejűleg feldolgozni. A hozzáadott egy adott személy arcok feldolgozása sorrendben történik.
Ismét `await WaitCallLimitPerSecondAsync()` meghívásainak annak biztosításához, hogy a kérelem gyakoriság korlátozás hatókörén belül.

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
            await faceServiceClient.AddPersonFaceAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>Összegzés

Ebben az útmutatóban megismerhette a is lehet PersonGroup rendelkező személyek és oldalak nagy mennyiségben történő létrehozásának folyamatán. Több emlékeztető:

- Ez a stratégia Facelist és LargePersonGroups is vonatkozik.
- Felvételét vagy törlését az arcokat különféle Facelist vagy személyek LargePersonGroups lehessen egyidejűleg feldolgozni.
- Felvételét vagy törlését egy adott FaceList vagy személy egy LargePersonGroup az arcokat egymás után kell elvégezni.
- Az egyszerűség kedvéért egy lehetséges kivétel kezelése ebben az útmutatóban nincs megadva. Kívánja további háttértárat, ha a megfelelő újrapróbálkozási házirend alkalmazása.

Az alábbi szolgáltatások ismertetése és mutatja be:

- Hozzon létre Persongroup a [is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API-t.
- Személyek létrehozása használatával a [személy is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API-t.
- Arcok személyek hozzáadása használatával a [is lehet PersonGroup személy – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API-t.

## <a name="related-topics"></a>Kapcsolódó témakörök

- [Arcazonosítás képen egy](HowtoIdentifyFacesinImage.md)
- [A kép arcok észlelése](HowtoDetectFacesinImage.md)
- [A nagy méretű szolgáltatással](how-to-use-large-scale.md)
