---
title: Webhookok – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A webhookok a HTTP-hívás ideális megoldást jelentenek a hosszú ideig futó folyamatok, például importálások, adaptációk, pontossági tesztek vagy hosszú ideig futó fájlok átirata esetén.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 3d07e540bf88c956f61b5d3b2a98702cad616985
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558800"
---
# <a name="webhooks-for-speech-services"></a>Webhookok a Speech Serviceshez

A webhookok olyanok, mint a HTTP-visszahívások, amelyek lehetővé teszik az alkalmazás számára az adatok elfogadását a Speech Servicesből, amikor elérhetővé válik. A webhookok használatával optimalizálhatja a REST API-k használatát azáltal, hogy nem kell folyamatosan lekérdezni a választ. A következő néhány szakaszban megtudhatja, hogyan használhatók a webhookok a Speech Services használatával.

## <a name="supported-operations"></a>Támogatott műveletek

A Speech Services támogatja a webhookokat a hosszú ideig futó műveletekhez. Az alább felsorolt műveletek mindegyike elindíthat egy HTTP-visszahívást a befejezés után.

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

Most hozzon létre egy webhookot.

## <a name="create-a-webhook"></a>Webhook létrehozása

Hozzon létre egy webhookot az offline átíráshoz. A forgatókönyv: a felhasználó hosszú ideig futó hangfájlt használ, amelyet aszinkron módon kell átírni a Batch-átírási API-val.

Webhookok létrehozásához post-kérést kell létrehozni a https://\<region\>. Cris.ai/API/speechtotext/v2.1/transcriptions/Hooks.

A kérelem konfigurációs paraméterei JSON-ként vannak megadva:

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
A Batch átírási API-nak küldött összes `name`post kérelemhez a szükséges. A `description` és`properties` paraméterek megadása nem kötelező.

A `Active` tulajdonsággal visszakapcsolhatja a visszahívást az URL-be és ki a webhook-Regisztráció törlése és újbóli létrehozása nélkül. Ha a folyamat befejeződése után csak egyszer kell visszahívnia, törölje a webhookot, és állítsa a `Active` tulajdonságot false (hamis) értékre.

Az esemény típusát `TranscriptionCompletion` az Events (események) tömbben kell megadnia. A rendszer visszahívja a végpontot, amikor egy átirat egy terminál állapotba kerül`Succeeded` ( `Failed`vagy). A regisztrált URL-címre való visszahíváskor a kérelemben szerepelni `X-MicrosoftSpeechServices-Event` fog egy olyan fejléc, amely tartalmazza a regisztrált események egyikét. Regisztrált eseménytípus esetében egy kérelem van.

Létezik egy eseménytípus, amelyre nem lehet előfizetni. Ez az `Ping` esemény típusa. A rendszer elküldi az ilyen típusú kérelmet az URL-címre, amikor a ping URL-cím használatakor befejezte a webhook létrehozását (lásd alább).  

A konfigurációban a `url` tulajdonság megadása kötelező. A rendszer elküldi a POST kérelmeket erre az URL-címre. A `secret` a hasznos adatok sha256-kivonatának létrehozásához használható a titkos kulcs HMAC. A kivonat a regisztrált URL- `X-MicrosoftSpeechServices-Signature` címre való visszahíváskor fejlécként van beállítva. Ez a fejléc Base64 kódolású.

Ez a példa azt szemlélteti, hogyan lehet érvényesíteni C#a hasznos adatokat a következő használatával:

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
Ebben a kódrészletben a `secret` dekódolása és ellenőrzése megtörtént. Azt is megfigyelheti, hogy a webhook eseménytípus be lett kapcsolva. Jelenleg van egy esemény egy befejezett átírással. A kód minden eseménynél ötször újrapróbálkozik (egy másodperc késleltetéssel), mielőtt feladja.

### <a name="other-webhook-operations"></a>Egyéb webhook-műveletek

Az összes regisztrált webhook beszerzése: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

Egy adott webhook beszerzése: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

Egy adott webhook eltávolítása: TÖRLÉSE https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note]
> A fenti példában a régió a következő: "westus". Ezt arra a régióra kell cserélni, amelyben létrehozta a Speech Services-erőforrást a Azure Portal.

Post https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping törzs: üres

POST-kérés küldése a regisztrált URL-címre. A kérelemben egy `X-MicrosoftSpeechServices-Event` ping értékű fejléc szerepel. Ha a webhook egy titkos kulccsal lett regisztrálva, akkor az tartalmaz `X-MicrosoftSpeechServices-Signature` egy fejlécet, amely az adattartalom sha256-kivonatát tartalmazza a titkos kulcs HMAC kulcsaként. A kivonat Base64 kódolású.

Post https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test törzs: üres

POST-kérelmet küld a regisztrált URL-címre, ha az előfizetett eseménytípus (átirat) entitása megtalálható a rendszeren, és a megfelelő állapotban van. A rendszer a webhookot meghívó utolsó entitásból hozza létre a hasznos adatokat. Ha egyetlen entitás sincs jelen, a bejegyzés a 204-as számú üzenettel fog válaszolni. Ha tesztelési kérelem is elvégezhető, akkor a 200-as számú választ ad. A kérés törzse ugyanolyan alakú, mint a GET kérelem egy adott entitáshoz, amelyhez a webhook előfizetett (például átirat). A kérelemben szereplő `X-MicrosoftSpeechServices-Event` és `X-MicrosoftSpeechServices-Signature` fejlécek a következő módon lesznek leírva:.

### <a name="run-a-test"></a>Teszt futtatása

A webhely https://bin.webhookrelay.com használatával gyorsan elvégezhető a tesztelés. Innen lekérheti a visszahívási URL-címeket, hogy paraméterként adja át a HTTP-BEJEGYZÉST a dokumentumban korábban ismertetett webhook létrehozásához.

Kattintson a Create Bucket (gyűjtő létrehozása) elemre, és kövesse a képernyőn megjelenő utasításokat a Hook beszerzéséhez. Ezután az ezen a lapon található információk segítségével regisztrálja a hookot a Speech Service-ben. Egy továbbító üzenet hasznos adatai – a transzkripció befejezésére válaszul a következőképpen néz ki:

```json
{
    "results": [],
    "recordingsUrls": [
        "my recording URL"
    ],
    "models": [
        {
            "modelKind": "AcousticAndLanguage",
            "datasets": [],
            "id": "a09c8c8b-1090-443c-895c-3b1cf442dec4",
            "createdDateTime": "2019-03-26T12:48:46Z",
            "lastActionDateTime": "2019-03-26T14:04:47Z",
            "status": "Succeeded",
            "locale": "en-US",
            "name": "v4.13 Unified",
            "description": "Unified",
            "properties": {
                "Purpose": "OnlineTranscription,BatchTranscription,LanguageAdaptation",
                "ModelClass": "unified-v4"
            }
        }
    ],
    "statusMessage": "None.",
    "id": "d41615e1-a60e-444b-b063-129649810b3a",
    "createdDateTime": "2019-04-16T09:35:51Z",
    "lastActionDateTime": "2019-04-16T09:38:09Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Simple transcription",
    "description": "Simple transcription description",
    "properties": {
        "PunctuationMode": "DictatedAndAutomatic",
        "ProfanityFilterMode": "Masked",
        "AddWordLevelTimestamps": "True",
        "AddSentiment": "True",
        "Duration": "00:00:02"
    }
}
```
Az üzenet tartalmazza a rögzítés átírásához használt rögzítési URL-címet és modelleket.

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
