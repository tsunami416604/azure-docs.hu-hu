---
title: Speech Services REST API-k – beszédszolgáltatások
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogy a hang-szöveg és a szöveg-hang transzformációs REST API-k használata. Ebben a cikkben megismerkedhet engedélyezési beállítások, a lekérdezési beállítások, struktúra kérést és választ kapnak.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 765d93780ad45eaaca61d4deb5f6607ef70ee432
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2019
ms.locfileid: "54413685"
---
# <a name="speech-service-rest-apis"></a>Beszédfelismerési szolgáltatás REST API-k

Alternatív megoldásként a [beszéd SDK](speech-sdk.md), a Speech Service lehetővé teszi a hang-szöveg és a egy REST API-készletet az szöveg-hang transzformációs alakíthatja át. Minden elérhető végpontot nem egy régió tartozik. Az alkalmazás egy előfizetési kulcsot szeretné használni a végpont szükséges.

A REST API-k használatával, mielőtt megismerése:
* A hang-szöveg transzformációs kérelmek, a REST API használatával csak hang 10 másodperces tartalmazhat.
* A hang-szöveg transzformációs REST API-t csak végső eredményt adja vissza. Részleges eredményeket nem tartozik.
* A szöveg-hang transzformációs REST API-t igényel engedélyeztetési fejléc. Ez azt jelenti, hogy a szolgáltatás eléréséhez egy jogkivonatcsere végrehajtásához szükséges. További információért lásd: [Hitelesítés](#authentication).

## <a name="authentication"></a>Hitelesítés

Vagy a hang-szöveg transzformációs vagy szöveg-hang transzformációs REST API minden kérelemhez szükséges engedélyeztetési fejléc. Ez a táblázat mutatja be, hogy mely fejlécek az egyes szolgáltatások támogatottak:

| Támogatott engedélyezési fejléceket | Speech-to-text | Szövegfelolvasás |
|------------------------|----------------|----------------|
| OCP-Apim-Subscription-Key | Igen | Nem |
| Hitelesítés: Tulajdonosi | Igen | Igen |

Használatakor a `Ocp-Apim-Subscription-Key` fejléc, már csak számára meg kell adnia az előfizetési kulcs. Példa:

```
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Használatakor a `Authorization: Bearer` fejléc, Ön szükséges kérheti a `issueToken` végpont. Ebben a kérelemben cserél az előfizetési kulcs hozzáférési jogkivonat helyeként, 10 percig érvényes. A következő néhány szakaszban fog elsajátíthatja egy token beszerzéséhez, -tokennel, és frissítse a jogkivonatot.

### <a name="how-to-get-an-access-token"></a>Hozzáférési jogkivonat beszerzése

A hozzáférési jogkivonatot kapjon kell kérheti a `issueToken` végpont használatával a `Ocp-Apim-Subscription-Key` és az előfizetési kulcs.

Ezek a régiók és végpontok támogatottak:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Ezek a minták segítségével hozzon létre a hozzáférési jogkivonat kérése.

#### <a name="http-sample"></a>HTTP-minta

Ebben a példában egy egyszerű HTTP-kérelem egy token beszerzéséhez. Cserélje le `YOUR_SUBSCRIPTION_KEY` az Speech Service előfizetési kulccsal végzett. Ha az előfizetés nem, az USA nyugati régiójában, cserélje le a `Host` saját régiójában gazdagép nevű fejléc.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

A válasz törzse tartalmazza a hozzáférési jogkivonat Java webes jogkivonat (JWT) formátumú.

#### <a name="powershell-sample"></a>PowerShell-minta

Ebben a példában egy egyszerű PowerShell-parancsfájlt, hogy hozzáférési jogkivonatot kapjon. Cserélje le `YOUR_SUBSCRIPTION_KEY` az Speech Service előfizetési kulccsal végzett. Ellenőrizze, hogy a helyes-e a végpontot használja a régiót, amelyben megegyezik az előfizetés. Ebben a példában az USA nyugati RÉGIÓJA van beállítva.

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>a cURL-minta

a cURL egy Linux (és a Linux Windows alrendszere) elérhető parancssori eszköz. A cURL-parancs bemutatja, hogyan hozzáférési jogkivonatot kapjon. Cserélje le `YOUR_SUBSCRIPTION_KEY` az Speech Service előfizetési kulccsal végzett. Ellenőrizze, hogy a helyes-e a végpontot használja a régiót, amelyben megegyezik az előfizetés. Ebben a példában az USA nyugati RÉGIÓJA van beállítva.

```cli
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>C#-minta

Ez C# osztály mutatja be a hozzáférési jogkivonat beszerzése. Át a az Speech Service előfizetési kulcs, ha az osztály példányosítania. Ha az előfizetés nem szerepel az USA nyugati régiója, az értékének módosítása `FetchTokenUri` a régióban, az előfizetés megfelelően.

```cs
/*
    * This class demonstrates how to get a valid access token.
    */
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="how-to-use-an-access-token"></a>Hozzáférési token használatával

A hozzáférési jogkivonatot kell küldeni a service-t a `Authorization: Bearer <TOKEN>` fejléc. Minden hozzáférési jogkivonatot a 10 percig érvényes. Bármikor megtekintheti az új jogkivonatot, azonban a hálózati forgalom és a késés minimalizálása érdekében azt javasoljuk, ugyanezt a tokent kilenc percig.

Íme egy példa HTTP-kérelem a szöveg-hang transzformációs REST API:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.tts.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Hello, world!
</voice></speak>
```

### <a name="how-to-renew-an-access-token-using-c"></a>Hozzáférési token használatával megújításaC#

Ez C# kódja az osztály korábban bemutatott protokollkompatibilitását. A `Authentication` osztály automatikusan megkap egy új hozzáférési jogkivonat kilenc percenként egy időzítő segítségével. Ez a megközelítés biztosítja, hogy érvényes token mindig elérhető legyen a program futása közben.

> [!NOTE]
> Időzítő helyett, ha a legutóbbi jogkivonat lett lekérve időbélyeg is tárolhatja. Ezután kérhet egy újat csak akkor, ha hamarosan lejár. Ez a megközelítés elkerülhető, hogy új jogkivonatok feleslegesen kér, és lehetnek megfelelőbbek programok, amelyek ritkán beszédalapú kéréseket.

Ahogy korábban is, győződjön meg arról, hogy a `FetchTokenUri` értéke megegyezik az előfizetés régiót. Az előfizetési kulcs akkor át, ha az osztály példányosítania.

```cs
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;
    private Timer accessTokenRenewer;

    //Access token expires every 10 minutes. Renew it every 9 minutes.
    private const int RefreshTokenDuration = 9;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchToken(FetchTokenUri, subscriptionKey).Result;

        // renew the token on set duration.
        accessTokenRenewer = new Timer(new TimerCallback(OnTokenExpiredCallback),
                                        this,
                                        TimeSpan.FromMinutes(RefreshTokenDuration),
                                        TimeSpan.FromMilliseconds(-1));
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private void RenewAccessToken()
    {
        this.token = FetchToken(FetchTokenUri, this.subscriptionKey).Result;
        Console.WriteLine("Renewed token.");
    }

    private void OnTokenExpiredCallback(object stateInfo)
    {
        try
        {
            RenewAccessToken();
        }
        catch (Exception ex)
        {
            Console.WriteLine(string.Format("Failed renewing access token. Details: {0}", ex.Message));
        }
        finally
        {
            try
            {
                accessTokenRenewer.Change(TimeSpan.FromMinutes(RefreshTokenDuration), TimeSpan.FromMilliseconds(-1));
            }
            catch (Exception ex)
            {
                Console.WriteLine(string.Format("Failed to reschedule the timer to renew access token. Details: {0}", ex.Message));
            }
        }
    }

    private async Task<string> FetchToken(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

## <a name="speech-to-text-api"></a>Hang-szöveg transzformációs API

A hang-szöveg transzformációs REST API-t csak rövid kimondott szöveget támogat. Kérelmek tartalmazhat teljes időtartama: 14 másodperc hanganyagra legfeljebb 10 másodperc. A REST API-t csak a végső eredmények, nem részleges vagy köztes eredményeket adja vissza.

Küldenek, hosszabb hang esetében az alkalmazás követelmény, fontolja meg a [beszéd SDK](speech-sdk.md) vagy [beszédátírási batch](batch-transcription.md).

### <a name="regions-and-endpoints"></a>Régiók és végpontok

Ezekben a régiókban támogatottak hang-szöveg transzformációs beszédátírási a REST API használatával. Győződjön meg arról, hogy a végpontot, amely megfelel az előfizetés régiót választja.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="query-parameters"></a>Lekérdezési paraméterek

Ezeket a paramétereket a lekérdezési karakterláncban a REST-kérés szerepelhet.

| Paraméter | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| `language` | A felismert használja a beszélt nyelv azonosítja. Lásd: [támogatott nyelvek](language-support.md#speech-to-text). | Szükséges |
| `format` | Megadja a eredményformátum. Elfogadott értékek a következők `simple` és `detailed`. Egyszerű eredmények tartalmazzák a `RecognitionStatus`, `DisplayText`, `Offset`, és `Duration`. Részletes válaszok megbízhatósági értékeket és a négy különböző reprezentációinak több eredmények belefoglalása. Az alapértelmezett beállítás `simple`. | Optional |
| `profanity` | Adja meg a felismerési eredményeket cenzúrázása kezelése. Elfogadott értékek a következők `masked`, csillag, amely lecseréli cenzúrázása `removed`, amely minden cenzúrázása az eredményt, távolítsa el vagy `raw`, amely tartalmazza a vulgáris, az eredmény. Az alapértelmezett beállítás `masked`. | Optional |

### <a name="request-headers"></a>Kérelemfejlécek

Ez a táblázat felsorolja a szükséges és választható fejlécek a hang-szöveg transzformációs kéréseket.

|Fejléc| Leírás | Kötelező / választható |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Az előfizetési kulcs beszédszolgáltatás. | Vagy a fejléc vagy `Authorization` megadása kötelező. |
| `Authorization` | Egy engedélyezési jogkivonatot előzi meg a word `Bearer`. További információért lásd: [Hitelesítés](#authentication). | Vagy a fejléc vagy `Ocp-Apim-Subscription-Key` megadása kötelező. |
| `Content-type` | A formátum és a megadott hang adatok kodek ismerteti. Elfogadott értékek a következők `audio/wav; codecs=audio/pcm; samplerate=16000` és `audio/ogg; codecs=opus`. | Szükséges |
| `Transfer-Encoding` | Megadja, hogy darabolt hang adatot küld, ahelyett, hogy egyetlen fájlt. Csak akkor használja ezt a fejlécet, ha hang adatokat. | Optional |
| `Expect` | Ha használja a darabolásos átvitel, küldjön `Expect: 100-continue`. A beszédfelismerési szolgáltatás nyugtázza a kiindulási kérelemhez, és további adatokat várja.| Szükséges darabolt hang adatot küldenek. |
| `Accept` | Ha meg van adva, kell lennie `application/json`. A beszédfelismerési szolgáltatás JSON-eredményeket biztosít. Bizonyos webes kérés keretrendszereket, adjon meg egy nem kompatibilis alapértelmezett értéket, ha nincs megadva, így az mindig célszerű tartalmazzák `Accept`. | Nem kötelező, de ajánlott. |

### <a name="audio-formats"></a>Hangformátumok

Hang küldése a HTTP törzsében `POST` kérelmet. A jelen táblázatban lévő formátumok valamelyikében kell lennie:

| Formátum | Kodek | Átviteli sebesség | Mintavételi frekvencia |
|--------|-------|---------|-------------|
| WAV | A PCM | 16-bit | 16 kHz, mono |
| OGG | OPUS | 16-bit | 16 kHz, mono |

>[!NOTE]
>A fenti formátumok REST API-t és a WebSocket a Speech Service-ben támogatottak. A [beszéd SDK](speech-sdk.md) jelenleg csak támogatja a WAV PCM kodekkel formázása.

### <a name="sample-request"></a>Mintakérelem

Ez az általános HTTP-kérés. Az alábbi mintát is tartalmaz, a gazdagépnév és a kötelező fejlécet. Fontos megjegyezni, hogy a szolgáltatás emellett arra számít hívásaiból, amely nem érhető el ebben a példában. Leírtak szerint korábban darabolás ajánlott, azonban nem kötelező.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="http-status-codes"></a>HTTP-állapotkódok

A HTTP-állapotkód: minden válasz azt jelzi, hogy a sikeres vagy gyakori hibák.

| HTTP-állapotkód | Leírás | Lehetséges ok |
|------------------|-------------|-----------------|
| 100 | Folytatás | A kezdeti kérés elfogadva. Folytassa a többi adatot küld. (Használja a darabolásos átviteli.) |
| 200 | OK | A kérelem sikeres volt. a választörzs mérete a JSON-objektum. |
| 400 | Hibás kérés | Nyelvkód nem biztosított, vagy nem támogatott nyelvet; hangfájl érvénytelen. |
| 401 | Nem engedélyezett | Az előfizetői vagy engedélyezési jogkivonat érvénytelen a megadott régióban, vagy érvénytelen végpont. |
| 403 | Tiltott | Hiányzik az előfizetési kulcs vagy engedélyezési jogkivonat. |

### <a name="chunked-transfer"></a>Darabolásos átvitel

Darabolásos átvitel (`Transfer-Encoding: chunked`) segít minimálisra csökkenteni a felismerés késés, mert lehetővé teszi a beszédfelismerési szolgáltatás feldolgozza a hangfájl folyamatban átvitel közben. A REST API-t nem biztosít részleges vagy köztes eredményeket. Ez a beállítás kizárólag növelni a válaszkészséget a funkcionalitást.

A mintakód bemutatja, hogyan hang tömbökben küldése. Csak az első adatrészletben kell tartalmaznia a hangfájl fejléc. `request` egy HTTPWebRequest objektumot a megfelelő REST-végponthoz csatlakozik. `audioFile` a hangfájl lemezen út.

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

### <a name="response-parameters"></a>Válasz paraméterek

Eredményei JSON-fájlként. A `simple` formátum tartalmazza ezeket a legfelső szintű mezőket.

| Paraméter | Leírás  |
|-----------|--------------|
|`RecognitionStatus`|Állapot, mint például `Success` sikeres felismeréséhez. Lásd a következő táblázatot.|
|`DisplayText`|A felismert szöveget után nagybetűk, írásjelek, más néven Inverz szöveg normalizálási (átalakítása a kimondott szöveg rövidebb űrlapok, például a 200-as "kétszáz" vagy "Dr. János""orvos Smith"), és a vulgáris maszkolási. Jelenleg csak a sikeres.|
|`Offset`|Az idő (100 nanoszekundumos egységek), amelyen a felismert speech az audio-adatfolyam kezdődik.|
|`Duration`|Az időtartam (100 nanoszekundumos egység) a felismert beszéd, a hang adatfolyamban.|

A `RecognitionStatus` mező tartalmazza ezeket az értékeket:

| status | Leírás |
|--------|-------------|
| `Success` | Sikeres volt-e a felismerés és az `DisplayText` mező szerepel. |
| `NoMatch` | Beszéd az audio-adatfolyam észlelt, de nincs szó azoktól a Célnyelv sem felel meg is. Általában azt jelenti, a beszédfelismerési nyelv, a felhasználó van, és beszéljen egy másik nyelven. |
| `InitialSilenceTimeout` | A hang stream elején szereplő csak csend, és a szolgáltatás, beszédfelismerés várakozás túllépte az időkorlátot. |
| `BabbleTimeout` | A hang stream elején szereplő csak zaj, és a szolgáltatás, beszédfelismerés várakozás túllépte az időkorlátot. |
| `Error` | A beszédfelismerést szolgáltatás belső hibába ütközött, és nem lehetett folytatni. Próbálja meg újra, ha lehetséges. |

> [!NOTE]
> Ha a hanganyag csak káromkodás tartalmaz, és a `profanity` lekérdezési paraméter értéke `remove`, a szolgáltatás nem ad vissza egy beszéd eredményt.

A `detailed` formátuma azonos adatokat tartalmaz a `simple` formájában, valamint a `NBest`, alternatív értelmezéseket a speech recognition ugyanaz az eredmény listáját. Ezeket az eredményeket a legnagyobb valószínűséggel sorrendje valószínűleg legalább az első bejegyzés ugyanaz, mint a fő felismerés eredményét.  Használatakor a `detailed` formátumban, `DisplayText` verzióként `Display` az egyes eredményez a `NBest` listája.

Az egyes objektumok a `NBest` lista a következőket tartalmazza:

| Paraméter | Leírás |
|-----------|-------------|
| `Confidence` | A megbízhatósági pontszám a bejegyzés a 0,0 (nincs megbízhatóság) 1.0-s (teljes megbízhatósági) |
| `Lexical` | A felismert szöveget lexikai formájában: a tényleges szavak ismerhető fel. |
| `ITN` | Inverz szöveg normalizált ("kanonikus") formájában a felismert szöveget, phone számok, rövidítések ("orvos smith", "dr smith") és egyéb átalakítások alkalmazza. |
| `MaskedITN` | A vulgáris maszkolási alkalmazza, ha a rendszer kéri fel űrlapján. |
| `Display` | A megjelenítési formája a felismert szöveget, írásjelek és a nagybetűk hozzáadva. Ez a paraméter megegyezik a `DisplayText` feltéve ha formátum beállítása `simple`. |

### <a name="sample-responses"></a>Minta válaszok

Ez a jellemző választ `simple` felismerése.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Ez a jellemző választ `detailed` felismerése.

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="text-to-speech-api"></a>Szöveg-hang transzformációs API

A szöveg-hang transzformációs REST API támogatja a Neurális és a standard szintű szöveg-hang transzformációs beszédhangot, amelyek mindegyike támogatja egy adott nyelvhez és dialect, területi beállítás azonosítjuk.

* Beszédhangot teljes listáját lásd: [nyelvi támogatás](language-support.md#text-to-speech).
* Régiónkénti rendelkezésre állás kapcsolatos információkért lásd: [régiók](regions.md#text-to-speech).

### <a name="request-headers"></a>Kérelemfejlécek

Ez a táblázat felsorolja a szükséges és választható fejlécek a hang-szöveg transzformációs kéréseket.

| Fejléc | Leírás | Kötelező / választható |
|--------|-------------|---------------------|
| `Authorization` | Egy engedélyezési jogkivonatot előzi meg a word `Bearer`. További információkért lásd: [hitelesítési](#authentication). | Szükséges |
| `Content-Type` | A megadott szöveg a tartalom típusát határozza meg. Elfogadott érték: `application/ssml+xml`. | Szükséges |
| `X-Microsoft-OutputFormat` | A hangkimeneti formátum meghatározása. Elfogadott értékek teljes listáját lásd: [hang kimenetek](#audio-outputs). | Szükséges |
| `User-Agent` | Az alkalmazás neve. Legfeljebb 255 karakter hosszú lehet. | Szükséges |

### <a name="audio-outputs"></a>Hang kimenetek

Ez a lista az egyes kérelmek, a küldött támogatott hangformátumok a `X-Microsoft-OutputFormat` fejléc. Minden egyes magában foglalja egy átviteli sebesség és a kódolási típusként. A beszédfelismerési szolgáltatás támogatja a 24-KHz, 16-KHz, és 8-KHz hang adja vissza.

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> Ha a kiválasztott hang- és kimeneti formátum különböző átviteli sebességet, a hanganyag szükség szerint módosítva a felbontása. Azonban nem támogatja a 24khz beszédhangot `audio-16khz-16kbps-mono-siren` és `riff-16khz-16kbps-mono-siren` kimeneti formátumot.

### <a name="request-body"></a>A kérés törzse

A szervezet minden egyes `POST` kérelem érkezik [Speech összefoglaló Markup Language (SSML)](speech-synthesis-markup.md). SSML lehetővé teszi a hang- és a szöveg-hang transzformációs szolgáltatás által visszaadott szintetizált nyelvének kiválasztását. Támogatott beszédhangot teljes listáját lásd: [nyelvi támogatás](language-support.md#text-to-speech).

> [!NOTE]
> Ha egy egyéni beszédfelismerési használja, a kérelem törzsében elküldött pedig egyszerű szövegként (ASCII vagy UTF-8).

### <a name="sample-request"></a>Mintakérelem

A HTTP-kérelem SSML beszédfelismerési és nyelvi megadásához használja. A szervezet legfeljebb 1000 karakter.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-status-codes"></a>HTTP-állapotkódok

A HTTP-állapotkód: minden válasz azt jelzi, hogy a sikeres vagy gyakori hibák.

| HTTP-állapotkód | Leírás | Lehetséges ok |
|------------------|-------------|-----------------|
| 200 | OK | A kérelem sikeres volt. a választörzs hangfájl mérete. |
| 400 | Hibás kérelem | Egy kötelező paraméter hiányzik, üres vagy null értékű. Másik lehetőségként átadott vagy egy kötelező vagy választható paraméter értéke érvénytelen. Egy gyakori probléma egy fejlécet, amely túl hosszú. |
| 401 | Nem engedélyezett | A kérelem nem engedélyezett. Ellenőrizze, hogy az előfizetési kulcs, vagy a jogkivonat érvényes, és a megfelelő régióban. |
| 413 | Kérelem az entitás túl nagy | A SSML bemeneti adat 1024 karakternél hosszabb. |
| 429 | Túl sok kérelem | Túllépte a kvótát, vagy engedélyezett az előfizetéséhez kérelmek száma. |
| 502 | Hibás átjáró | Hálózati vagy kiszolgálóoldali probléma. Érvénytelen fejlécek is jelezhet. |

Ha a HTTP-állapot `200 OK`, a válasz törzse tartalmazza a kért formátumban hangfájl. Ez a fájl átvitele, a puffer mentett, vagy egy fájlba menti, lejátszhatók.

## <a name="next-steps"></a>További lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
