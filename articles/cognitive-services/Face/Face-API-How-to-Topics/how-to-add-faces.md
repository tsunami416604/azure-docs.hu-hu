---
title: Adja hozzá a lapok Arcfelismerési API-val |} Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Használja a Arcfelismerési API kognitív Services oldal hozzáadása a képek.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 3306c13d6c3d231ddbda38cfcbc5419fcdbd30db
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346999"
---
# <a name="how-to-add-faces"></a>Lapok hozzáadása

Ez az útmutató ismerteti az ajánlott eljárás, személyek és felületei nagy számú hozzáadása egy PersonGroup.
Az azonos stratégia is FaceList és LargePersonGroup vonatkozik.
A minták a Arcfelismerési API-ügyfél szalagtárat használó C# nyelven íródtak.

## <a name="step1"></a> 1. lépés: az inicializálás

Több változók van deklarálva, és segítő függvény megvalósított ütemezni a kérelmeket.

- `PersonCount` az a személy teljes száma.
- `CallLimitPerSecond` az előfizetés szint szerint másodpercenként maximális hívások van.
- `_timeStampQueue` a várólisták jegyezze fel a kérelem időbélyegeket van.
- `await WaitCallLimitPerSecondAsync()` megvárja, amíg érvényes a következő kérés küldése.

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

## <a name="step2"></a> 2. lépés: Engedélyezze az API-hívás

Egy ügyféloldali kódtár használata esetén az Előfizetés kulcs áthalad a FaceServiceClient osztály konstruktor. Példa:

```CSharp
FaceServiceClient faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

Az előfizetés kulcsot az Azure-portálon a piactér lapján lehet lekérni. Lásd: [előfizetések](https://www.microsoft.com/cognitive-services/en-us/sign-up).

## <a name="step3"></a> 3. lépés: A PersonGroup létrehozása

Egy "MyPersonGroup" nevű PersonGroup menteni a személyek jön létre.
A kérelem ideje a várólistában levő `_timeStampQueue` az átfogó ellenőrzést biztosításához.

```CSharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step4"></a> 4. lépés: A PersonGroup személyeket létrehozása

Személyek egyidejűleg jönnek létre és `await WaitCallLimitPerSecondAsync()` elkerülése érdekében a hívás túllépő is vonatkozik.

```CSharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceServiceClient.CreatePersonAsync(personGroupId, personName);
});
```

## <a name="step5"></a> 5. lépés: A személyek lapok hozzáadása

Hozzáadása a különböző személyek lapok feldolgozása párhuzamosan, az egy adott személy akkor szekvenciális.
Ebben az esetben `await WaitCallLimitPerSecondAsync()` meghívták annak érdekében, hogy a kérelem gyakorisága korlátozás hatókörén belül van.

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

## <a name="summary"></a> Összefoglalás

Ebben az útmutatóban megtanulhatta, személyek és felületei nagy számú egy PersonGroup létrehozásának folyamatán. Több emlékeztetők:

- Ezt a stratégiát is FaceList és LargePersonGroup vonatkozik.
- Különböző FaceLists vagy személyek LargePersonGroup lapok hozzáadása vagy törlése egyidejűleg lehet feldolgozni.
- Egy adott FaceList vagy LargePersonGroup személy ugyanazokat a műveleteket egymás után kell elvégezni.
- Az egyszerűség kedvéért megtartásához lehetséges kivétel kezelése a jelen útmutató nincs megadva. Ha azt szeretné, további robusztusság javítására, megfelelő újrapróbálkozási házirendje kell alkalmazni.

Gyors emlékeztető korábban ismertetése és bemutatta azok a funkciók a következők:

- PersonGroups használatával létrehozása a [PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API
- Személyek használatával létrehozása a [PersonGroup személy - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API
- Lapok használatával személyek hozzáadása a [PersonGroup személy - hozzáadása Arcfelismerési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API

## <a name="related"></a> Kapcsolódó témakörök
- [A kép néz azonosítása](HowtoIdentifyFacesinImage.md)
- [A kép néz történő Adatmásolást](HowtoDetectFacesinImage.md)
- [A felügyeleti teendők központjaként szolgáltatás használata](how-to-use-large-scale.md)
