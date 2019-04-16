---
title: Webhookok – beszédszolgáltatások
titlesuffix: Azure Cognitive Services
description: Webhookok a megoldás optimalizálásához, hosszú esetén ideális HTTP-visszahívások futó folyamatok importálja, betanítás, pontossági tesztek vagy hosszú ideig futó fájlok beszédátírás hasonlóan.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 7b47d4fc3aa4a1a50e441e668a856703c67045ae
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59581009"
---
# <a name="webhooks-for-speech-services"></a>Webhookok az beszédszolgáltatások

Webhookok hasonlóak a HTTP-visszahívásokat nevezzük, amelyek lehetővé teszik az alkalmazás a beszédszolgáltatások adatokat fogad, mikor válik elérhetővé. Webhookok segítségével optimalizálhatja a REST API-k használatát nem kell folyamatosan lekérdezésére választ. A következő néhány szakaszban megismerheti a webhookok használata a beszédszolgáltatások lesz.

## <a name="supported-operations"></a>Támogatott műveletek

A beszédszolgáltatások webhookok támogatása az összes hosszú ideig futó műveletek. Az alábbi műveletek mindegyike egy HTTP-visszahívás befejezése után is indíthat. 

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

Következő lépésként hozzunk létre egy webhook.

## <a name="create-a-webhook"></a>A webhook létrehozása

Hozzunk létre egy webhookot az-offline beszédátírási. A forgatókönyv: egy felhasználó egy hosszú ideig futó hangfájl, amelyek szeretnék lefényképezze aszinkron módon történik a Batch Beszédátírási API-val rendelkezik. 

A kérelem konfigurációs paramétereket JSON-fájlként áll rendelkezésre:

```json
{
  "configuration": {
    "url": "https://your.callback.url/goes/here",
    "secret": "<my_secret>"
  },
  "events": [
    "TranscriptionCompletion"
  ],
  "active": true,
  "name": "TranscriptionCompletionWebHook",
  "description": "This is a Webhook created to trigger an HTTP POST request when my audio file transcription is completed.",
  "properties": {
      "Active" : "True"
  }

}
```
A Batch Beszédátírási API minden POST kérelemhez szükséges egy `name`. A `description` és `properties` paraméter megadása nem kötelező.

A `Active` tulajdonság a Váltás hívása vissza az URL-CÍMÉT, kapcsolja ki, törölje és hozza létre újból a webhook-regisztráció nélkül használható. Ha csak szeretné a visszahívási egyszer a folyamat, amelyet követően befejeződött, törölje a webhook és a kapcsoló a `Active` FALSE tulajdonságot.

Az esemény típusa `TranscriptionCompletion` megtalálható-e az események tömb. Bude volat vissza a végpontra, amikor egy beszédátírási bekerült egy állapotot (`Succeeded` vagy `Failed`). Hívja meg ismét a regisztrált URL-címre, amikor a kérelem fogja tartalmazni az `X-MicrosoftSpeechServices-Event` a regisztrált esemény típusú tartalmazó fejléc. Nincs regisztrált esemény típus szerint egy kérelmet. 

Nincs több esemény típusa, amely nem lehet előfizetni. Ez a `Ping` eseménytípus. Az ilyen típusú kérelmet küld az URL-címet, ha befejeződött a webhook létrehozása, ha a pingelés URL-cím (lásd alább) használatával.  

A konfigurációban a `url` tulajdonságot kötelező megadni. POST kéréseket az URL-címet kapnak. A `secret` hozzon létre egy SHA256-kivonat, a tartalom a titkos kulcsot egy HMAC-val kulcsaként szolgál. A kivonata be van állítva a `X-MicrosoftSpeechServices-Signature` fejléc vissza a regisztrált URL-cím hívása során. Ez a fejléc Base64-kódolású.

Ez a példa bemutatja, hogyan érvényesítheti egy hasznos funkciójával C#:

```csharp

private const string EventTypeHeaderName = "X-MicrosoftSpeechServices-Event";
private const string SignatureHeaderName = "X-MicrosoftSpeechServices-Signature";

[HttpPost]
public async Task<IActionResult> PostAsync([FromHeader(Name = EventTypeHeaderName)]WebHookEventType eventTypeHeader, [FromHeader(Name = SignatureHeaderName)]string signature)
{
    string body = string.Empty;
    using (var streamReader = new StreamReader(this.Request.Body))
    {
        body = await streamReader.ReadToEndAsync().ConfigureAwait(false);
        var secretBytes = Encoding.UTF8.GetBytes("my_secret");
        using (var hmacsha256 = new HMACSHA256(secretBytes))
        {
            var contentBytes = Encoding.UTF8.GetBytes(body);
            var contentHash = hmacsha256.ComputeHash(contentBytes);
            var storedHash = Convert.FromBase64String(signature);
            var validated = contentHash.SequenceEqual(storedHash);
        }
    }
 
    switch (eventTypeHeader)
    {
        case WebHookEventType.Ping:
            // Do your ping event related stuff here (or ignore this event)
            break;
        case WebHookEventType.TranscriptionCompletion:
            // Do your subscription related stuff here.
            break;
        default:
            break;
    }
 
    return this.Ok();
}

```
Ez a kódrészlet a a `secret` dekódolni, és ellenőrzi. Azt is láthatja, hogy a webhook-esemény típusa lett átadva. Jelenleg nincs befejezett beszédátírási egy eseményt. A kód központosítását előtt minden esemény (egy másodperces késleltetéssel) ötször újrapróbálkozik.

### <a name="other-webhook-operations"></a>Egyéb webhook-műveletek

Regisztrált összes webhookok lekérése: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

Egy adott webhook beolvasása: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

Egy adott webhook eltávolítása: TÖRLÉSE https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note] 
> A fenti példában a régióban 'westus'. Ez az a régió, ahol az Azure Portalon létrehozta a beszédszolgáltatások erőforrás kell helyettesíteni.

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping törzse: üres

Egy POST kérést küld a regisztrált URL-CÍMÉT. A kérés tartalmaz egy `X-MicrosoftSpeechServices-Event` egy érték ping-fejléc. Ha a webhook regisztrálva lett az egy titkos kulcsot, tartalmazni fog egy `X-MicrosoftSpeechServices-Signature` a titkos kulcs HMAC-val rendelkező SHA256-kivonata egy fejlécet. A kivonatot a Base64-kódolású. 

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test törzse: üres

Egy POST kérést küld a regisztrált URL-címet, ha az előfizetett esemény típusa (beszédátírási) entitás szerepel a rendszerben, és a megfelelő állapotban van. A hasznos lenne meghívása a webhook utolsó entitásból jön létre. Ha nem entitás nem található, a bejegyzés a 204 válaszol. Egy tesztelési kérelem lehet tenni, ha a 200-as válaszol. A kérelem törzse nem ugyanaz az alakzat, mint a GET kérelemre adott entitáshoz tartozó a webhook (például beszédátírási) az előfizetett. A kérelem fog rendelkezni a `X-MicrosoftSpeechServices-Event` és `X-MicrosoftSpeechServices-Signature` fejlécek előtt leírtak szerint.

### <a name="run-a-test"></a>Egy teszt futtatása

Gyors teszteléséhez teheti meg az webhelyen https://bin.webhookrelay.com. Innen szerezheti be hívás URL-címek paraméterként átadni egy webhookot, a dokumentum a korábban ismertetett létrehozásához a HTTP POST használatával biztonsági másolatot.

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
