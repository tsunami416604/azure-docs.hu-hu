---
title: Beszédszolgáltatás REST API-k
description: A beszédfelismerési szolgáltatás REST API-hivatkozás.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/09/2018
ms.author: v-jerkin
ms.openlocfilehash: 8f01130d46bce1e3b3e0b37f26e25d552c6002e5
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2018
ms.locfileid: "46498113"
---
# <a name="speech-service-rest-apis"></a>Beszédszolgáltatás REST API-k

A REST API-k, az egyesített beszédszolgáltatás hasonlóak az API-k által biztosított a [Bing Speech API](https://docs.microsoft.com/azure/cognitive-services/Speech). A végpontok a végpontokat, a Bing Speech-szolgáltatás által használt eltérnek. Regionális végpontok érhetők el, és a egy előfizetési kulcsot használ a végponthoz tartozó kell használnia.

## <a name="speech-to-text"></a>Diktálás

A végpontok a Speech to Text REST API az alábbi táblázatban láthatók. Használja az egyik, amely megfelel az előfizetés régiót.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

> [!NOTE]
> Ha testre szabta az akusztikai modell vagy a nyelvi modell, vagy a írásmódja, használja az egyéni végpontra.

Az API-t csak rövid beszédet támogat. Kérelmek legfeljebb 10 másodpercet, hang és tartalmazhat az elmúlt 14 másodperc teljes legfeljebb. A REST API-t csak a végső eredmények, nem átmeneti vagy részleges eredményt adja vissza. A beszédfelismerési szolgáltatás is rendelkezik egy [beszédátírási batch](batch-transcription.md) API, amely hosszabb hang is lefényképezze.

### <a name="query-parameters"></a>Lekérdezési paraméterek

Az alábbi paramétereket a lekérdezési karakterláncban a REST-kérés szerepelhet.

|Paraméter neve|Kötelező/választható|Jelentés|
|-|-|-|
|`language`|Szükséges|A nyelvet, hogy a azonosítóját. Lásd: [támogatott nyelvek](supported-languages.md#speech-to-text).|
|`format`|Optional<br>alapértelmezett érték: `simple`|Eredményformátum, `simple` vagy `detailed`. Egyszerű eredmények tartalmazzák a `RecognitionStatus`, `DisplayText`, `Offset`, és időtartama. Részletes eredmények tartalmazzák a több jelöltek megbízhatósági értékeket és a négy különböző értük felelősséget.|
|`profanity`|Optional<br>alapértelmezett érték: `masked`|A felismerési eredményeket cenzúrázása kezelésének módját. Előfordulhat, hogy `masked` (cenzúrázása cseréli csillagok), `removed` (eltávolítja az összes cenzúrázása), vagy `raw` (tartalmazza a vulgáris).

### <a name="request-headers"></a>Kérelemfejlécek

A következő mezőket a HTTP-kérés fejlécében érkeznek.

|Fejléc|Jelentés|
|------|-------|
|`Ocp-Apim-Subscription-Key`|A beszédfelismerési szolgáltatás előfizetési kulcs. Vagy a fejléc vagy `Authorization` meg kell adni.|
|`Authorization`|Egy engedélyezési jogkivonatot előzi meg a word `Bearer`. Vagy a fejléc vagy `Ocp-Apim-Subscription-Key` meg kell adni. Lásd: [hitelesítési](#authentication).|
|`Content-type`|A formátum és hang adatok kodek ismerteti. Jelenleg ez az érték lehet `audio/wav; codec=audio/pcm; samplerate=16000`.|
|`Transfer-Encoding`|Választható. Adja meg, ha meg kell `chunked` hang adatok több kisebb tömbökre egyetlen fájl helyett a küldésének engedélyezéséhez.|
|`Expect`|Ha használja a darabolásos átvitel, küldjön `Expect: 100-continue`. A beszédfelismerési szolgáltatás nyugtázza a kiindulási kérelemhez, és további adatokat várja.|
|`Accept`|Választható. Ha meg van adva, tartalmaznia kell `application/json`, ahogy a beszédfelismerési szolgáltatás JSON formátumban eredményeket biztosít. (Bizonyos webes kérés keretrendszereket, adjon meg egy nem kompatibilis alapértelmezett értéket, ha nincs megadva, így az mindig célszerű tartalmazzák `Accept`)|

### <a name="audio-format"></a>Formát zvuku

A törzs a HTTP küldése a hanganyag `PUT` kérése és 16 bites WAV PCM egyetlen csatornát (mono) 16 KHz formátumúnak kell lennie.

### <a name="chunked-transfer"></a>Darabolásos átvitel

Darabolásos átvitel (`Transfer-Encoding: chunked`) segít minimálisra csökkenteni a felismerés késés, mert lehetővé teszi a beszédfelismerési szolgáltatás feldolgozza a hangfájl folyamatban átvitel közben. A REST API-t nem biztosít teljes vagy részleges köztes eredményeket; Ez a beállítás kizárólag növelni a válaszkészséget a funkcionalitást.

A következő kód azt ábrázolja, hogyan küldhet hang tömbökben. `request` egy HTTPWebRequest objektumot a megfelelő REST-végponthoz csatlakozik. `audioFile` a hangfájl lemezen út.

```csharp
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

### <a name="example-request"></a>Kérelem (példa)

Egy tipikus kérelem a következő:

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="http-status"></a>HTTP-állapot

A HTTP-állapot, a válasz azt jelzi, hogy a sikeres vagy gyakori hibaállapotok.

HTTP-kód|Jelentés|Lehetséges ok
-|-|-|
100|Folytatás|A kezdeti kérés elfogadva. Folytassa a többi adatot küld. (Használja a darabolásos átviteli.)
200|OK|A kérelem sikeres volt. a választörzs mérete a JSON-objektum.
400|Hibás kérés|Nyelvkód nem biztosított, vagy nem támogatott nyelvet; hangfájl érvénytelen.
401|Nem engedélyezett|Az előfizetői vagy engedélyezési jogkivonat érvénytelen a megadott régióban, vagy érvénytelen végpont.
403|Tiltott|Hiányzik az előfizetési kulcs vagy engedélyezési jogkivonat.

### <a name="json-response"></a>JSON-válasz

Eredmények a rendszer JSON formátumban adja vissza. A `simple` formátum csak a következő legfelső szintű mezőket tartalmazza.

|Mező neve|Tartalom|
|-|-|
|`RecognitionStatus`|Állapot, mint például `Success` sikeres felismeréséhez. Lásd a következő táblázatot.|
|`DisplayText`|A felismert szöveget után nagybetűk, írásjelek, más néven Inverz szöveg normalizálási (átalakítása a kimondott szöveg rövidebb űrlapok, például a 200-as "kétszáz" vagy "Dr. János""orvos Smith"), és a vulgáris maszkolási. Jelenleg csak a sikeres.|
|`Offset`|Az idő (100 nanoszekundumos egységek), amelyen a felismert speech az audio-adatfolyam kezdődik.|
|`Duration`|Az időtartam (100 nanoszekundumos egység) a felismert beszéd, a hang adatfolyamban.|

A `RecognitionStatus` mező a következő értékeket tartalmazhat.

|Állapotérték|Leírás
|-|-|
| `Success` | A beszédfelismerést sikeres volt, és a megjelenő mezőben szerepel. |
| `NoMatch` | Beszéd az audio-adatfolyam észlelt, de nincs szó azoktól a Célnyelv sem felel meg is. Általában azt jelenti, a beszédfelismerési nyelv, a felhasználó van, és beszéljen egy másik nyelven. |
| `InitialSilenceTimeout` | A hang stream elején szereplő csak csend, és a szolgáltatás, beszédfelismerés várakozás túllépte az időkorlátot. |
| `BabbleTimeout` | A hang stream elején szereplő csak zaj, és a szolgáltatás, beszédfelismerés várakozás túllépte az időkorlátot. |
| `Error` | A beszédfelismerést szolgáltatás belső hibába ütközött, és nem lehetett folytatni. Próbálja meg újra, ha lehetséges. |

> [!NOTE]
> Ha a hanganyag csak káromkodás tartalmaz, és a `profanity` lekérdezési paraméter értéke `remove`, a szolgáltatás nem ad vissza egy beszéd eredményt. 

A `detailed` formátum tartalmazza, ugyanazokat a mezőket a `simple` formájában, valamint az egy `NBest` mező. A `NBest` mező az alternatív értelmezéseket a azonos beszéd, ahol az a legvalószínűbb valószínűleg legalább listája. Az első bejegyzés ugyanaz, mint a fő felismerés eredményét. Mindegyik bejegyzés a következő mezőket tartalmazzák:

|Mező neve|Tartalom|
|-|-|
|`Confidence`|A megbízhatósági pontszám a bejegyzés a 0,0 (nincs megbízhatóság) 1.0-s (teljes megbízhatósági)
|`Lexical`|A felismert szöveget lexikai formájában: a tényleges szavak ismerhető fel.
|`ITN`|Inverz szöveg normalizált ("kanonikus") formájában a felismert szöveget, phone számok, rövidítések ("orvos smith", "dr smith") és egyéb átalakítások alkalmazza.
|`MaskedITN`| A vulgáris maszkolási alkalmazza, ha a rendszer kéri fel űrlapján.
|`Display`| A megjelenítési formája a felismert szöveget, írásjelek és a nagybetűk hozzáadva. Ugyanaz, mint a `DisplayText` a legfelső szintű eredményei.

### <a name="sample-responses"></a>Minta válaszok

Az alábbiakban található a tipikus választ `simple` felismerése.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Az alábbi, jellemző választ `detailed` felismerése.

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

## <a name="text-to-speech"></a>Szövegfelolvasás

Az alábbiakban a Speech service Text to Speech API a REST-végpontokat. A végpont, amely megfelel az előfizetés régiót használni.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

> [!NOTE]
> Ha egyéni hangtípust hozta létre, használja a hozzárendelt egyéni végpont.

A beszédfelismerési szolgáltatás mellett a Bing Speech által támogatott 16-Khz kimenet 24-KHz hangkimeneti támogatja. Négy 24-KHz kimeneti formátumok érhetők el használatra a `X-Microsoft-OutputFormat` HTTP-fejléc, mivel két 24-KHz beszédhangot, `Jessa24kRUS` és `Guy24kRUS`.

Területi beállítás | Nyelv   | Nem | A felhasználónév-leképezés
-------|------------|--------|------------
hu-HU  | Amerikai angol | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-US, Jessa24kRUS)" 
hu-HU  | Amerikai angol | Férfi   | "A Microsoft Server beszéd szöveg Speech Voice (en-US, Guy24kRUS)"

Rendelkezésre álló beszédhangot teljes listája megtalálható [támogatott nyelvek](supported-languages.md#text-to-speech).

### <a name="request-headers"></a>Kérelemfejlécek

A következő mezőket a HTTP-kérés fejlécében érkeznek.

|Fejléc|Jelentés|
|------|-------|
|`Authorization`|Egy engedélyezési jogkivonatot előzi meg a word `Bearer`. Kötelező. Lásd: [hitelesítési](#authentication).|
|`Content-Type`|A bemeneti tartalomtípus: `application/ssml+xml`.|
|`X-Microsoft-OutputFormat`|A kimeneti audio formátum. Lásd a következő táblázatot.|
|`User-Agent`|Alkalmazás neve. Szükséges; 255-nél kevesebb karaktert kell tartalmaznia.|

A rendelkezésre álló hangkimeneti formátumok (`X-Microsoft-OutputFormat`) egy átviteli sebesség és a egy kódolást.

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `audio-16khz-16kbps-mono-siren`
`riff-16khz-16kbps-mono-siren`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

> [!NOTE]
> Ha a kiválasztott hang- és kimeneti formátum különböző átviteli sebességet, a hanganyag szükség szerint módosítva a felbontása. Azonban nem támogatja a 24khz beszédhangot `audio-16khz-16kbps-mono-siren` és `riff-16khz-16kbps-mono-siren` kimeneti formátumot. 

### <a name="request-body"></a>Kérelem törzse

A szöveg beszéddé alakítandó legyen elküldve, a szervezet egy HTTP `POST` kérheti a vagy egyszerű szöveg (ASCII vagy UTF-8) vagy [Speech összefoglaló Markup Language](speech-synthesis-markup.md) (SSML) formátum (UTF-8). Egyszerű szöveges kérelmeket a szolgáltatás alapértelmezett beszédfelismerési és nyelvi használja. SSML, használjon egy másik küldése.

### <a name="sample-request"></a>Mintakérelem

A következő HTTP-kérelem válassza ki a hang-SSML szervezet használ. A szervezetnek kell nem lehet hosszabb, mint 1000 karakter.

```xml
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

### <a name="http-response"></a>HTTP-válasz

A HTTP-állapot, a válasz azt jelzi, hogy a sikeres vagy gyakori hibaállapotok.

HTTP-kód|Jelentés|Lehetséges ok
-|-|-|
200|OK|A kérelem sikeres volt. a választörzs hangfájl mérete.
400 |Hibás kérelem |Egy kötelező paraméter hiányzik, üres vagy null értékű. Másik lehetőségként átadott vagy egy kötelező vagy választható paraméter értéke érvénytelen. Egy gyakori probléma egy fejlécet, amely túl hosszú.
401|Nem engedélyezett |A kérelem nem engedélyezett. Ellenőrizze, hogy az előfizetési kulcs, vagy a jogkivonat érvényes, és a megfelelő régióban.
413|Kérelem az entitás túl nagy|A SSML bemeneti adat 1024 karakternél hosszabb.
|502|Hibás átjáró    | Hálózati vagy kiszolgálóoldali probléma. Érvénytelen fejlécek is jelezhet.

Ha a HTTP-állapot `200 OK`, a válasz törzse tartalmazza a kért formátumban hangfájl. Ez a fájl lehet, hogy játszható le, amíg azt át, vagy puffer vagy újabb lejátszás vagy egyéb felhasználás fájlba menti.

## <a name="authentication"></a>Hitelesítés

Egy kérést küld a beszédfelismerési szolgáltatás REST API-t igényel, vagy egy előfizetési kulcsot, vagy egy hozzáférési jogkivonatot. Általánosságban véve a legegyszerűbb az előfizetési kulcs küldése közvetlenül; a beszédfelismerési szolgáltatás, majd szerzi be a hozzáférési jogkivonatot. Azonban válaszidő minimalizálása érdekében érdemes inkább egy hozzáférési jogkivonatot.

Ön jogkivonat beszerzése az előfizetési kulcs egy regionális Speech Service segítségével `issueToken` végponton, az alábbi táblázatban látható. A végpont, amely megfelel az előfizetés régiót használni.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Minden hozzáférési jogkivonatot a 10 percig érvényes. Egy új jogkivonatot bármikor szerezheti be – Ha kívánja, többek között a következők csak előtt minden Speech REST API-kérelem. A hálózati forgalom és a késés minimalizálása érdekében azonban javasoljuk hogy ugyanezt a tokent kilenc perc.

A következő szakaszok bemutatják egy token beszerzése és hogyan használható a kérelemben.

### <a name="getting-a-token-http"></a>Jogkivonatok: HTTP

Alább egy mintát egy token beszerzése a HTTP-kérelem van. Cserélje le `YOUR_SUBSCRIPTION_KEY` az Speech service előfizetési kulccsal végzett. Ha az előfizetés nem szerepel az USA nyugati régiója, cserélje le a `Host` saját régiójában gazdanevű fejléc.

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

A kérés adott válasz törzse a hozzáférési jogkivonat Java webes jogkivonat (JWT) formátumú.

### <a name="getting-a-token-powershell"></a>Jogkivonatok: PowerShell

Az alábbi Windows PowerShell-parancsprogram mutatja be a hozzáférési jogkivonat beszerzése. Cserélje le `YOUR_SUBSCRIPTION_KEY` az Speech service előfizetési kulccsal végzett. Az előfizetés nem szerepel az USA nyugati régiója, az adott URI állomásnevét megfelelően módosítja.

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

### <a name="getting-a-token-curl"></a>Jogkivonatok: cURL

a cURL egy Linux (és a Linux Windows alrendszere) elérhető parancssori eszköz. Az alábbi cURL-parancs bemutatja, hogyan hozzáférési jogkivonat beszerzése. Cserélje le `YOUR_SUBSCRIPTION_KEY` az Speech service előfizetési kulccsal végzett. Az előfizetés nem szerepel az USA nyugati régiója, az adott URI állomásnevét megfelelően módosítja.

> [!NOTE]
> A parancs az olvashatóság érdekében több sorban jelenik meg, de meg kell egy shell parancssornál ugyanabban a sorban.

```
curl -v -X POST 
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" 
 -H "Content-type: application/x-www-form-urlencoded" 
 -H "Content-Length: 0" 
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="getting-a-token-c"></a>Jogkivonatok: C#

A C# osztály az alábbi mutatja be a hozzáférési jogkivonat beszerzése. Adja át a Speech service előfizetési kulcs, az osztály hárítható el. Ha az előfizetés nem szerepel az USA nyugati régiója, módosítsa a állomásnevét `FetchTokenUri` megfelelően.

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

### <a name="using-a-token"></a>Egy jogkivonat használatával

Szeretne használni egy tokent a REST API-kérelem, adja meg azt a a `Authorization` fejléc a következő szót `Bearer`. Például Íme egy példa szöveget a Speech REST-kérelmet, amely tartalmazza a jogkivonatot. Helyettesítse be a tényleges token `YOUR_ACCESS_TOKEN` és a helyes állomásnevet használja a `Host` fejléc.

```xml
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

### <a name="renewing-authorization"></a>Engedély megújítása

Az engedélyezési jogkivonatot 10 perc múlva lejár. Az engedély megújítása szerint egy új token beszerzése után járjon le – például a következő kilenc perc múlva. 

Az alábbi C#-kódot az osztály korábban bemutatott protokollkompatibilitását. A `Authentication` osztály automatikusan kap egy új hozzáférési jogkivonat egy időzítő segítségével kilenc percenként. Ez a megközelítés biztosítja, hogy érvényes token mindig elérhető legyen a program futása közben.

> [!NOTE]
> Időzítő helyett, ha a legutóbbi jogkivonat lett lekérve, majd egy új kérelem csak akkor, ha hamarosan lejáró időbélyeg tárolhatja. Ezt a módszert elkerülhető, hogy új jogkivonatok feleslegesen kér, és a programok, amelyek ritkán beszédalapú kéréseket a megfelelőbb lehet.

Ahogy korábban is, győződjön meg arról, hogy a `FetchTokenUri` értéke megegyezik az előfizetés régiót. Az osztály hárítható el, adja át az előfizetési kulcs.

```cs
/*
    * This class demonstrates how to maintain a valid access token.
    */
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

## <a name="next-steps"></a>További lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [Akusztikai modell testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modell testreszabása](how-to-customize-language-model.md)

