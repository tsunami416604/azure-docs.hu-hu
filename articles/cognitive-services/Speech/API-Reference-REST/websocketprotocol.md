---
title: A Bing Speech WebSocket protokoll |} A Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Bing Speech protokoll dokumentációját a websockets protokoll alapján.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 0bbc6b638d11335e6d46501fa651996f05957dd5
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341820"
---
# <a name="bing-speech-websocket-protocol"></a>A Bing Speech WebSocket protokoll

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Bing Speech az felhőalapú platform, amelynek funkciói közül a legtöbb haladó algoritmusok átalakítását beszélt hangot képes szöveggé érhető el. A Bing Speech protokoll határozza meg a [kapcsolat beállítása](#connection-establishment) ügyfélalkalmazások számára, és a szolgáltatás és a speech recognition üzenetek között igényló kicserélt között ([üzenetek ügyfél által kezdeményezett](#client-originated-messages) és [szolgáltatás által kezdeményezett üzenetek](#service-originated-messages)). Emellett [telemetriai üzeneteket](#telemetry-schema) és [hibakezelés](#error-handling) ismerteti.

## <a name="connection-establishment"></a>Kapcsolat létrehozása

A Speech Service protokoll követi WebSocket standard [IETF RFC 6455](https://tools.ietf.org/html/rfc6455). A WebSocket-kapcsolat elindításakor, HTTP-kérést, amely tartalmazza az ügyfél el kívánja frissíteni a kapcsolat HTTP szemantika használata helyett a WebSocket jelző HTTP-fejléceket. A kiszolgáló azt jelzi, hogy részt venni a WebSocket kapcsolaton visszaadó HTTP hajlandóságát `101 Switching Protocols` választ. A kézfogás adatcsere, követően ügyfél és a szolgáltatás ne zárja be a szoftvercsatorna, és megkezdheti a küldhet és fogadhat adatokat egy üzenet-alapú protokoll használatával.

A WebSocket-kézfogás megkezdéséhez az ügyfélalkalmazás egy HTTPS GET kérést küld a szolgáltatásnak. Ez magában foglalja a standard WebSocket frissítési fejlécek együtt más fejlécek, konkrétan a speech.

```HTTP
GET /speech/recognition/interactive/cognitiveservices/v1 HTTP/1.1
Host: speech.platform.bing.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
Authorization: t=EwCIAgALBAAUWkziSCJKS1VkhugDegv7L0eAAJqBYKKTzpPZOeGk7RfZmdBhYY28jl&p=
X-ConnectionId: A140CAF92F71469FA41C72C7B5849253
Origin: https://speech.platform.bing.com
```

A szolgáltatás fűzi hozzá:

```HTTP
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: upgrade
Sec-WebSocket-Key: 2PTTXbeeBXlrrUNsY15n01d/Pcc=
Set-Cookie: SpeechServiceToken=AAAAABAAWTC8ncb8COL; expires=Wed, 17 Aug 2016 15:39:06 GMT; domain=bing.com; path="/"
Date: Wed, 17 Aug 2016 15:03:52 GMT
```

Szükséges összes beszédalapú kéréseket a [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) titkosítást. Titkosítatlan beszédalapú kéréseket használata nem támogatott. A következő TLS-verziót támogatja:

* TLS 1.2

### <a name="connection-identifier"></a>Kapcsolat azonosítója

Beszédszolgáltatás megköveteli, hogy minden ügyfél egyedi azonosító a kapcsolat azonosítására. Az ügyfelek *kell* közé tartozik a *X-ConnectionId* fejléc, amikor elindítja a WebSocket-kézfogás. A *X-ConnectionId* fejléc lehet egy [univerzálisan egyedi azonosítót](https://en.wikipedia.org/wiki/Universally_unique_identifier) (UUID) értéket. WebSocket frissítési kéréseket, amelyek nem tartalmaznak a *X-ConnectionId*, ne adjon meg egy értéket a *X-ConnectionId* fejléc, vagy nem tartalmaznak egy érvényes UUID értéknek utasítja el a szolgáltatást egy HTTP-`400 Bad Request` választ.

### <a name="authorization"></a>Engedélyezés

A standard szintű WebSocket kézfogás fejlécek kívül a beszédalapú kéréseket igényelnek egy *engedélyezési* fejléc. Kapcsolat nélküli ezt a fejlécet a rendszer elutasítja a szolgáltatás egy HTTP-kérések `403 Forbidden` választ.

A *engedélyezési* fejléc tartalmaznia kell egy JSON webes jogkivonat (JWT) hozzáférési jogkivonatot.

Fizessen elő, és szerezze be API-kulcsokat, JWT-hozzáférési jogkivonatok érvényes használt kapcsolatos információkért lásd: a [Cognitive Services-előfizetés](https://azure.microsoft.com/try/cognitive-services/) lapot.

Az API-kulcsot a jogkivonat-szolgáltatás kerülnek. Példa:

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

A következő fejléc-információkat hozzáférés a jogkivonatokhoz szükség.

| Name (Név) | Formátum | Leírás |
|----|----|----|
| OCP-Apim-Subscription-Key | ASCII | Előfizetési kulcs |

A jogkivonat-szolgáltatás adja vissza, mint a JWT jogkivonat `text/plain`. A JWT átadott majd egy `Base64 access_token` , mint a kézfogás- *engedélyezési* előtaggal van ellátva a sztring fejlécet `Bearer`. Példa:

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>A cookie-k

Az ügyfelek *kell* támogatja a HTTP-cookie-k a megadott [RFC 6265](https://tools.ietf.org/html/rfc6265).

### <a name="http-redirection"></a>HTTP-átirányítás

Az ügyfelek *kell* támogatja a standard átirányítási módszerek által megadott a [HTTP-protokoll specifikációinak](http://www.w3.org/Protocols/rfc2616/rfc2616.html).

### <a name="speech-endpoints"></a>Beszédfelismerési végpontokat

Az ügyfelek *kell* beszédszolgáltatás megfelelő végpont használata. A végpont mód és a nyelvi alapul. A táblázatban néhány példa látható.

| Mód | Útvonal | Szolgáltatás URI-ja |
| -----|-----|-----|
| Interaktív | /Speech/Recognition/Interactive/cognitiveservices/V1 |https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| Beszélgetés | /Speech/Recognition/Conversation/cognitiveservices/V1 |https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| Diktálás | /Speech/Recognition/dictation/cognitiveservices/V1 |https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

További információkért lásd: a [szolgáltatás URI](../GetStarted/GetStartedREST.md#service-uri) lapot.

### <a name="report-connection-problems"></a>A jelentés kapcsolati problémák

Az ügyfelek azonnal jelentse az összes probléma történt a kapcsolat létrehozásakor. A jelentéskészítési sikertelen kapcsolatok üzenet protokollja leírt [kapcsolati hibák telemetriai adatainak](#connection-failure-telemetry).

### <a name="connection-duration-limitations"></a>Kapcsolat időtartama korlátozások

Tipikus web service HTTP-kapcsolatok összehasonlítva WebSocket-kapcsolatok az elmúlt egy *hosszú* idő. Beszédszolgáltatás helyez a WebSocket-kapcsolatok a szolgáltatás időtartamára korlátozások:

 * Minden aktív WebSocket kapcsolaton maximális időtartamának ez 10 perc. A kapcsolat akkor aktív, ha a szolgáltatás vagy az ügyfél WebSocket üzeneteket küld a kapcsolaton keresztül. A szolgáltatás megszakítja a kapcsolatot, figyelmeztetés nélkül, ha eléri a korlátot. Az ügyfelek kell kidolgozni a felhasználói esetek nem igénylik a kapcsolatot, vagy a maximális kapcsolódási élettartamának aktív marad.

 * Minden olyan inaktív WebSocket-kapcsolat által felhasználható maximális időtartam: 180 másodperc. A kapcsolat nem aktív, ha a szolgáltatás és az ügyfél nem WebSocket üzenetet küldött a kapcsolaton keresztül. A maximális inaktív élettartam elérése után a szolgáltatás leállítja az inaktív WebSocket kapcsolaton.

## <a name="message-types"></a>Üzenettípus

Az ügyfél és a szolgáltatás között a WebSocket-kapcsolat létrejötte után az ügyfél és a szolgáltatás küldhet üzeneteket. Ez a szakasz ismerteti ezen WebSocket üzenetek formátumát.

[IETF RFC 6455](https://tools.ietf.org/html/rfc6455) Megadja, hogy WebSocket üzeneteket küldhetnek adatokat szöveges vagy bináris kódolás. A két kódolásokat eltérő az átvitel közbeni formátumot használ. Minden egyes formátum hatékony kódolást, továbbítására és az üzenet hasznos adattartalmából dekódolása van optimalizálva.

### <a name="text-websocket-messages"></a>SMS-EK WebSocket

WebSocket üzenetet biztosítunk egy hasznos, amely egy szakaszt, fejlécek és a egy szervezet a jól ismert dupla kocsivissza soremelés pár használt HTTP-üzenetek elválasztva áll szöveges adatokat. És a HTTP-üzenetek, például szöveg WebSocket üzeneteket adja meg a fejlécek *name: érték* formátum egy egyetlen kocsivissza soremelés pár elválasztva. WebSocket szöveges üzenetben szöveg *kell* használata [UTF-8](https://tools.ietf.org/html/rfc3629) kódolást.

WebSocket üzenetet meg kell adnia egy üzenet elérési utat a fejléc *elérési út*. A fejléc értéke a speech protokoll üzenettípus, ez a dokumentum későbbi szakaszában meghatározott egyikének kell lennie.

### <a name="binary-websocket-messages"></a>Bináris WebSocket-üzenetek

Bináris WebSocket-üzeneteket egy bináris tartalom biztosítunk. A Speech Service protokoll hang továbbított és bináris WebSocket üzenetek használatával kapott a szolgáltatástól. Minden más üzenetet, ha szöveget WebSocket üzeneteket. 

WebSocket-üzenetet, például bináris WebSocket üzenetek állnak a fejléc és a egy szervezet szakaszt. Az első 2 bájt bináris WebSocket üzenet megadása a [big endian](https://en.wikipedia.org/wiki/Endianness) sorrendben fejrészében 16 bites egész szám méretét. A minimális fejléc szakasz mérete 0 bájt. A maximális mérete 8192 bájt. A fejlécek bináris WebSocket üzenet szövegének *kell* használata [US-ASCII](https://tools.ietf.org/html/rfc20) kódolást.

Egy bináris WebSocket-üzenetet a fejlécek hasonlóan WebSocket üzenetet ugyanebben a formátumban van kódolva. A *név-érték:* formátum egy egyetlen kocsivissza soremelés pár választja el egymástól. Bináris WebSocket üzenetek meg kell adnia egy üzenet elérési utat a fejléc *elérési út*. A fejléc értéke a speech protokoll üzenettípus, ez a dokumentum későbbi szakaszában meghatározott egyikének kell lennie.

Szöveg és a bináris WebSocket-üzenetek a Speech Service protokoll használatban vannak. 

## <a name="client-originated-messages"></a>Ügyfél által kezdeményezett üzenetek

A kapcsolat létrejötte után az ügyfél és a szolgáltatás megkezdheti a üzeneteket küldeni. Ez a szakasz ismerteti a formátum és hasznos, amelyek az ügyfélalkalmazások beszédszolgáltatás küldenek üzeneteket. A szakasz [szolgáltatás által kezdeményezett üzenetek](#service-originated-messages) mutat be, amely beszédszolgáltatás származnak, és az ügyfélalkalmazások számára küldött üzeneteket.

A fő, a szolgáltatásoknak az ügyfél által küldött üzenetek `speech.config`, `audio`, és `telemetry` üzeneteket. Javasolt minden üzenetet részletesen, mielőtt a közös szükséges, üzenetfejlécekben ezeket az üzeneteket ismerteti.

### <a name="required-message-headers"></a>Szükséges fejlécek

A következő fejlécek az összes ügyfél által kezdeményezett szükségesek.

| Fejléc | Érték |
|----|----|
| Útvonal | Ebben a dokumentumban meghatározottak szerint az üzenet elérési út |
| X-RequestId: | A "no-dash" formátumban UUID |
| X-időbélyeg | Ügyfél UTC óra ISO 8601 formátumú időbélyeg |

#### <a name="x-requestid-header"></a>X-RequestId: fejléc

Ügyfél által kezdeményezett kérelem egyedileg azonosítja a *X-RequestId:* üzenetfejléchez. Ezt a fejlécet minden ügyfél által kezdeményezett üzenet megadása kötelező. A *X-RequestId:* fejléc értéknek kell lennie egy UUID "no-dash" formában, például *123e4567e89b12d3a456426655440000*. Ez *nem* kanonikus formájában kell *123e4567-e89b-12d3-a456-426655440000*. Kérelmek nélkül egy *X-RequestId:* fejléc formátuma nem megfelelő az UUID azonosítók oka a szolgáltatás leáll a WebSocket-kapcsolatot használó fejléc értéket.

#### <a name="x-timestamp-header"></a>X-időbélyeg fejléc

Beszédszolgáltatás egy ügyfélalkalmazás által küldött minden üzenet *kell* tartalmaznak egy *X-időbélyeg* fejléc. Ez a fejléc értéke az idő, amikor az az ügyfél elküldi az üzenetet. Kérelmek nélkül egy *X-időbélyeg* fejléc vagy a fejléc érték formátuma nem megfelelő használó okozhat a szolgáltatást, hogy állítsa le a WebSocket-kapcsolatot.

A *X-időbélyeg* Fejlécérték formátumúnak kell lennie, az "yyyy"-"hh"-"dd'T' HH": "hh": "ss". " fffffffZ "hol található az"fffffff"a másodperc tört. Például "12,5" azt jelenti, hogy "12 + 5/10 másodpercen és"12.526"azt jelenti, hogy" 12 plusz 526/1000 másodperc". Ebben a formátumban megfelel-e az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) és, szemben a szabványos HTTP *dátum* fejléc ezredmásodperces feloldási nyújthat. Az ügyfélalkalmazások a legközelebbi ezredmásodperces az időbélyegek előfordulhat, hogy kerekíteni. Annak érdekében, hogy az eszköz óra pontosan nyomon követi az idő az ügyfélalkalmazások számára szükséges egy [Network Time Protocol (NTP) kiszolgáló](https://en.wikipedia.org/wiki/Network_Time_Protocol).

### <a name="message-speechconfig"></a>üzenet `speech.config`

Beszédszolgáltatás tudnia kell, az alkalmazás biztosít a lehetséges legjobb beszédfelismerés jellemzőit. A szükséges jellemzőket adatokat az eszköz és az operációs rendszer, az alkalmazás használja, amelyen a vonatkozó információkat tartalmaz. Azt adja meg ezt az információt a `speech.config` üzenet.

Az ügyfelek *kell* küldése egy `speech.config` azokat a csatlakozást beszédszolgáltatás, és mielőtt bármelyik küldenek után azonnal üzenet `audio` üzeneteket. Kell küldenie egy `speech.config` kapcsolatonként csak egyszer jelenik meg.

| Mező | Leírás |
|----|----|
| WebSocket-üzenetek kódolása | Szöveg |
| Törzs | A tartalom egy JSON-struktúrát |

#### <a name="required-message-headers"></a>Szükséges fejlécek

| Fejléc neve | Érték |
|----|----|
| Útvonal | `speech.config` |
| X-időbélyeg | Ügyfél UTC óra ISO 8601 formátumú időbélyeg |
| Content-Type | az Application/json; charset = utf-8 |

Csakúgy, mint a Speech Service protokoll ügyfél által kezdeményezett összes üzenet a `speech.config` üzenet *kell* tartalmaznak egy *X-időbélyeg* fejlécet, amely rögzíti, amikor az üzenet el lett küldve az ügyfél UTC idő a szolgáltatásnak. A `speech.config` üzenet *nem* szükséges egy *X-RequestId:* fejléc, mert ez az üzenet nincs hozzárendelve egy adott speech kérelmet.

#### <a name="message-payload"></a>Üzenet adattartalma
A hasznos a `speech.config` üzenet egy JSON-struktúrát, amely tartalmazza az alkalmazás adatait. Az alábbi példa bemutatja ezeket az információkat. Ügyfél és eszköz környezeti információkat tartalmaznak a *környezet* eleme a JSON-struktúrát. 

```JSON
{
  "context": {
    "system": {
      "version": "2.0.12341",
    },
    "os": {
      "platform": "Linux",
      "name": "Debian",
      "version": "2.14324324"
    },
    "device": {
      "manufacturer": "Contoso",
      "model": "Fabrikan",
      "version": "7.341"
      }
    },
  }
}
```

##### <a name="system-element"></a>Rendszer-elem

A system.version eleme a `speech.config` üzenet a beszéd, az ügyfélalkalmazás vagy az eszköz által használt SDK szoftverek változatát tartalmazza. Az érték szerepel-e az űrlap *major.minor.build.branch*. Kihagyhatja a *ág* összetevő, ha nem alkalmazható.

##### <a name="os-element"></a>Az operációs rendszer elem

| Mező | Leírás | Használat |
|-|-|-|
| os.platform | Az operációs rendszer platform, amely az alkalmazást, például, a Windows, Android, iOS vagy Linux |Szükséges |
| os.Name | Az operációs rendszer termék nevében, például a Debian vagy a Windows 10-es | Szükséges |
| os.Version | A képernyőn az operációs rendszer verziójának *major.minor.build.branch* | Szükséges |

##### <a name="device-element"></a>Eszköz elem

| Mező | Leírás | Használat |
|-|-|-|
| Device.Manufacturer | Az eszköz hardver gyártója | Szükséges |
| Device.Model | Az eszköz modellje | Szükséges |
| Device.Version | Az eszköz szoftverének verziója, eszköz gyártója által biztosított. Ezt az értéket megadja az eszközt, hogy a gyártó által nyomon követhetők egy verziója. | Szükséges |

### <a name="message-audio"></a>üzenet `audio`

Ügyfél beszédfeldolgozó alkalmazásokat küldje el hang beszédszolgáltatás az audio-adatfolyam átalakítása hang adattömbök sorozata. Hang az egyes adattömbök a beszélt hangot képes, amely lehet a a szolgáltatás által megjelenített érzéseket szegmense végzi. Egy olyan hang adattömb maximális mérete 8192 bájt. Audio-adatfolyam üzenetek *bináris WebSocket üzenetek*.

Az ügyfelek használják a `audio` a szolgáltatás egy hang adattömbök küldendő üzenet. Ügyfelek olvassa el a tömbökben a mikrofon hang, és ezek adattömbök beszédszolgáltatás beszédátírási küldése. Az első `audio` üzenetet egy megfelelően formázott fejlécet, amely megfelelően megadja, hogy a hanganyag megfelel-e a szolgáltatás által támogatott formátumok egyikét kell tartalmaznia. További `audio` üzeneteket tartalmaznak, csak a bináris hang adatfolyam a mikrofon adatsorból beolvasott adatok.

Az ügyfelek igény szerint küldhet egy `audio` nulla hosszúságú szervezethez üzenet. Ez az üzenet arról tájékoztatja a szolgáltatást, amely az ügyfél tudni fogja, hogy a felhasználó leállította a beszéd, az utterance (kifejezés) befejeződött, és a mikrofon ki van kapcsolva.

Beszédszolgáltatás használja az első `audio` üzenet, amely tartalmaz egy egyedi azonosítóját, hogy jelezze a egy új kérés/válasz ciklus kezdete vagy *kapcsolja*. Miután megkapta a szolgáltatás egy `audio` üzenet új kérelem azonosító bármelyik előző kapcsolja társított várólistán lévő vagy el nem küldött üzeneteket elveti.

| Mező | Leírás |
|-------------|----------------|
| WebSocket-üzenetek kódolása | Bináris |
| Törzs | A hang adattömbök bináris adatait. Maximális mérete 8192 bájt. |

#### <a name="required-message-headers"></a>Szükséges fejlécek

A következő fejléceket szükség az összes `audio` üzeneteket.

| Fejléc         |  Érték     |
| ------------- | ---------------- |
| Útvonal | `audio` |
| X-RequestId: | A "no-dash" formátumban UUID |
| X-időbélyeg | Ügyfél UTC óra ISO 8601 formátumú időbélyeg |
| Content-Type | A hang tartalomtípus. A típusnak kell lennie, vagy *audio/x-wav* (PCM) vagy *audio/szintetikus* (szintetikus). |

#### <a name="supported-audio-encodings"></a>Hang kódolást támogatja

Ez a szakasz ismerteti a hangkodekek Speech Service által támogatott.

##### <a name="pcm"></a>A PCM

Beszédszolgáltatás tömörítetlen pulse kód modulációs hang fogad el. Hang a szolgáltatás a küldendő [WAV](https://en.wikipedia.org/wiki/WAV) formátumú, ezért az első hang bontják részekre *kell* tartalmaznak egy érvényes [erőforrás adatcsere fájlformátum](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format) (RIFF) fejléc. Ha az ügyfél és a egy hang adattömbök, amely egy kapcsolja kezdeményez *nem* egy érvényes RIFF fejléc, a szolgáltatás elutasítja a kérelmet, és megszakítja a WebSocket-kapcsolatot.

A PCM hang *kell* a 16 bit / minta és a egy csatornát, 16 kHz mintát venni (*riff – 16khz – 16 bites-mono-pcm*). Beszédszolgáltatás nem támogatja a sztereó audiostreamek lejátszásával, és audiostreamek lejátszásával, a megadott átviteli sebesség, mintavételi gyakoriság vagy csatornák száma nem használó elutasítja.

##### <a name="opus"></a>Opus

Opus nyitva, jogdíjmentes, rendkívül sokoldalú hang kodek. Beszédszolgáltatás Opus támogatja az állandó átviteli sebességgel `32000` vagy `16000`. Csak a `OGG` Opus tárolója jelenleg támogatott által meghatározott a `audio/ogg` MIME-típus.

Opus használatához módosítsa a [JavaScript minta](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101) , és módosítsa a `RecognizerSetup` metódus való visszatéréshez.

```javascript
return SDK.CreateRecognizerWithCustomAudioSource(
            recognizerConfig,
            authentication,
            new SDK.MicAudioSource(
                     new SDK.OpusRecorder(
                     {
                         mimeType: "audio/ogg",
                         bitsPerSecond: 32000
                     }
              )
          ));
```

#### <a name="detect-end-of-speech"></a>Beszéd végén észlelése

Az emberek nem explicit módon jelezni Amikor végzett, és beszéljen. Minden bemeneti a audio-adatfolyamokat a speech végén kezelésére két lehetősége van a speech elfogadó alkalmazás: szolgáltatás end-, beszéd észlelési és az ügyfél end-, beszéd észlelése. E két választási szolgáltatás end-, beszéd észlelési általában jobb felhasználói élményt nyújt.

##### <a name="service-end-of-speech-detection"></a>Szolgáltatás end-, beszéd észlelése

Az automatikus beszédfelismerés ideális megoldást készíthet, az alkalmazások lehetővé teszik a szolgáltatás észleli, ha a felhasználó befejezte, és beszéljen. Az ügyfelek küldött hang, mikrofon *hang* adattömböket, amíg a szolgáltatás észleli a csend és küld vissza a `speech.endDetected` üzenet.

##### <a name="client-end-of-speech-detection"></a>Ügyfél end-, beszéd észlelése

Ügyfélalkalmazások, amelyek lehetővé teszik a felhasználónak, hogy jelezze a speech valamilyen módon végén is adhat a szolgáltatás, amely jel. Egy ügyfélalkalmazás Előfordulhat például, a "Stop" vagy "Vypnutí" gombra, hogy a felhasználó lenyomja az. Hogy jelezze a teljes, beszéd, az ügyfélalkalmazások küldése egy *hang* adattömbök üzenet nulla hosszúságú szervezethez. Beszédszolgáltatás ezt az üzenetet, a bejövő audio-adatfolyam végén értelmezi.

### <a name="message-telemetry"></a>üzenet `telemetry`

Ügyfélalkalmazások *kell* egyes kapcsolja végén elismeri Speech Service telemetriai adatokat gyűjthessen az kapcsolja elküldésével. Kapcsolja be teljes nyugtázása lehetővé teszi, hogy a Speech Service annak érdekében, hogy megfelelően a kérés- és az elvégzéséhez szükséges összes üzenet érkezett az ügyfél által. Kapcsolja be teljes nyugtázása is lehetővé teszi, hogy ellenőrizze, hogy az ügyfélalkalmazások elvárt beszédszolgáltatás. Ez az információ felbecsülhetetlen segítséget a speech-kompatibilis alkalmazás hibaelhárítása.

Ügyfelek küldésével kell fogadnia egy kapcsolja végén egy `telemetry` üzenet fogadását követően a `turn.end` üzenet. Az ügyfelek megpróbáljon-tudomásul veszi a `turn.end` minél hamarabb. Ha egy ügyfélalkalmazás nem kapcsolja vége tudomásul veszi, a Speech Service előfordulhat, hogy megszakítja a kapcsolatot hiba. Az ügyfelek csak egyet kell küldenie `telemetry` üzenet az egyes kérések és válaszok azonosítja a *X-RequestId:* értéket.

| Mező | Leírás |
| ------------- | ---------------- |
| WebSocket-üzenetek kódolása | Szöveg |
| Útvonal | `telemetry` |
| X-időbélyeg | Ügyfél UTC óra ISO 8601 formátumú időbélyeg |
| Content-Type | `application/json` |
| Törzs | Egy JSON-struktúrát, amely a kapcsolja ügyfél-információkat tartalmaz |

A séma törzséhez tartozó a `telemetry` üzenet van definiálva a [Telemetriai séma](#telemetry-schema) szakaszban.

#### <a name="telemetry-for-interrupted-connections"></a>Megszakított kapcsolatokat telemetriája

Ha a hálózati kapcsolat egy bekapcsolása során bármilyen okból meghiúsul, és az ügyfél nem *nem* kap egy `turn.end` üzenetet a szolgáltatást, az ügyfél elküld egy `telemetry` üzenet. Ez az üzenet ismerteti a sikertelen kérelmek, a következő alkalommal, amikor az ügyfél kapcsolódott a szolgáltatáshoz. Az ügyfelek nem rendelkeznek egy kapcsolat küldése azonnal megpróbálja a `telemetry` üzenet. Az üzenet előfordulhat, hogy az ügyfélen pufferelt és jövőbeli felhasználó által kért kapcsolaton keresztül. A `telemetry` a sikertelen kérelmek üzenet *kell* használja a *X-RequestId:* a sikertelen kérelmek értéket. Azt lehet küldeni a szolgáltatáshoz, amint létrejön a kapcsolat, fogadásra további üzenetek nélkül.

## <a name="service-originated-messages"></a>Szolgáltatás által kezdeményezett üzenetek

Ez a szakasz ismerteti a Speech Service származnak, és az ügyfél küld üzeneteket. Beszédszolgáltatás tartja karban a beállításjegyzék ügyfél képességeit, és állít elő, az üzenetek által igényelt minden egyes ügyfél számára, ezért nem minden ügyfél, amely az itt ismertetett összes üzenetek fogadásához. Üzenetek kivonatosan értéke által hivatkozott a *elérési út* fejléc. Ha például egy WebSocket szöveges üzenetet nevezzük a *elérési* érték `speech.hypothesis` speech.hypothesis üzenetnek számít.

### <a name="message-speechstartdetected"></a>üzenet `speech.startDetected`

A `speech.startDetected` az üzenet azt jelzi, hogy beszédszolgáltatás az audio-adatfolyam észlelt speech.

| Mező | Leírás |
| ------------- | ---------------- |
| WebSocket-üzenetek kódolása | Szöveg |
| Útvonal | `speech.startDetected` |
| Content-Type | az Application/json; charset = utf-8 |
| Törzs | A JSON-szerkezet speech kezdetét észlelésekor a feltételek kapcsolatos információkat tartalmazó. A *eltolás* ebben a struktúrában mező eltolását (100 nanoszekundumos egységekben) Ha speech észlelt az audio-adatfolyamot, a stream elején viszonyítva. |

#### <a name="sample-message"></a>Mintaüzenet

```HTML
Path: speech.startDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 100000
}
```

### <a name="message-speechhypothesis"></a>üzenet `speech.hypothesis`

Beszédfelismerés, során beszédszolgáltatás rendszeres időközönként állít elő, feltételezéseket szavakkal kapcsolatos ismeri fel a szolgáltatás. Beszédszolgáltatás elküldi ezeket feltételezéseket az ügyfél körülbelül minden 300 ezredmásodperc. A `speech.hypothesis` megfelelő *csak* a speech felhasználói élmény fokozása számára. Minden olyan függőséget, a tartalom vagy a szöveg pontosságát az üzeneteknek nem szükségesek.

 A `speech.hypothesis` üzenet ezekre az ügyfelekre, amely rendelkezik néhány szöveges renderelési képesség, és visszajelzést küldene közel valós idejű, a folyamatban lévő felismerés, a személy, aki elnököt alkalmazható.

| Mező | Leírás |
| ------------- | ---------------- |
| WebSocket-üzenetek kódolása | Szöveg |
| Útvonal | `speech.hypothesis` |
| X-RequestId: | A "no-dash" formátumban UUID |
| Content-Type | application/json |
| Törzs | A beszédfelismerés elmélet JSON struktúrában |

#### <a name="sample-message"></a>Mintaüzenet

```HTML
Path: speech.hypothesis
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Text": "this is a speech hypothesis",
  "Offset": 0,
  "Duration": 23600000
}
```

A *eltolás* elem eltolását (100 nanoszekundumos egységekben) Ha a kifejezés ismerhető, képest az audio-adatfolyamot kezdete.

A *időtartama* elem azt határozza meg, az időtartam (100 nanoszekundumos egység), a beszéd kifejezés helyett szerepel.

Az ügyfelek nem kell hagyatkozzon feltételezésekre a gyakoriságát, időzítési vagy egy beszéd elmélet vagy a konzisztencia, a szöveg bármilyen két speech feltételezéseket található szöveget. A feltételezéseket csak pillanatképei a szolgáltatásban az átírási folyamatba. Egy stabil felhalmozódásához beszédátírási nem jelentik. Például egy első speech változat tartalmazhat "finom szórakoztató" szavakat, és a második elmélet tartalmazhat szavak "vicces található." Beszédszolgáltatás nem hajtsa végre (például a kis-és nagybetűk, az írásjelek) utáni feldolgozási a speech elmélet a szöveget.

### <a name="message-speechphrase"></a>üzenet `speech.phrase`

Amikor beszédszolgáltatás határozza meg, hogy arról, hogy vannak-e elegendő információt, amelyek nem változnak, a szolgáltatás észszerűek felismerés eredmény egy `speech.phrase` üzenet. Beszédszolgáltatás ezek eredményt ad, miután észlelte, hogy a felhasználó befejezte a mondat vagy kifejezést.

| Mező | Leírás |
| ------------- | ---------------- |
| WebSocket-üzenetek kódolása | Szöveg |
| Útvonal | `speech.phrase` |
| Content-Type | application/json |
| Törzs | A beszédfelismerés kifejezés JSON-struktúra |

A beszédfelismerés kifejezés JSON-sémája a következő mezőket tartalmazzák: `RecognitionStatus`, `DisplayText`, `Offset`, és `Duration`. Ezek a mezők kapcsolatos további információkért lásd: [Beszédátírási válaszok](../concepts.md#transcription-responses).

#### <a name="sample-message"></a>Mintaüzenet

```HTML
Path: speech.phrase
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": 0,
  "Duration": 12300000
}
```

### <a name="message-speechenddetected"></a>üzenet `speech.endDetected`

A `speech.endDetected` üzenet Megadja, hogy az ügyfélalkalmazás álljon le, a szolgáltatás az audio streamelési.

| Mező | Leírás |
| ------------- | ---------------- |
| WebSocket-üzenetek kódolása | Szöveg |
| Útvonal | `speech.endDetected` |
| Törzs | A JSON-szerkezet, amely tartalmazza az eltolást, beszéd végén észlelésekor. Az eltolás jelenik meg az 100 nanoszekundumos egységek eltolás kezdete és hang felismerés szolgálja ki. |
| Content-Type | az Application/json; charset = utf-8 |

#### <a name="sample-message"></a>Mintaüzenet

```HTML
Path: speech.endDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 0
}
```

A *eltolás* elem eltolását (100 nanoszekundumos egységekben) Ha a kifejezés ismerhető, képest az audio-adatfolyamot kezdete.

### <a name="message-turnstart"></a>üzenet `turn.start`

A `turn.start` jelzi a egy kapcsolja be a szolgáltatás szempontjából. A `turn.start` üzenet mindig az első válasz üzenet bármilyen kérést kap. Ha nem kapja meg a `turn.start` üzenet, azt feltételezik, hogy a szolgáltatás kapcsolati állapota érvénytelen.

| Mező | Leírás |
| ------------- | ---------------- |
| WebSocket-üzenetek kódolása | Szöveg |
| Útvonal | `turn.start` |
| Content-Type | az Application/json; charset = utf-8 |
| Törzs | JSON-struktúra |

#### <a name="sample-message"></a>Mintaüzenet

```HTML
Path: turn.start
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "context": {
    "serviceTag": "7B33613B91714B32817815DC89633AFA"
  }
}
```

A törzse a `turn.start` üzenet egy JSON-struktúrát, amely tartalmazza a kapcsolja elején környezet. A *környezet* elem tartalmaz egy *serviceTag* tulajdonság. A tulajdonság határozza meg, hogy a szolgáltatáshoz társított a kapcsolja be a címke értéke. Ezt az értéket a Microsoft által használható, ha az alkalmazás kapcsolatos hibák elhárítása segítségre van szüksége.

### <a name="message-turnend"></a>üzenet `turn.end`

A `turn.end` szempontjából a szolgáltatás egy kapcsolja végén jelzi. A `turn.end` üzenet mindig az utolsó válaszüzenet bármilyen kérést kap. Ügyfelek használhatják az üzenet fogadását, karbantartási tevékenységek és az inaktív állapotból való jel. Ha nem kapja meg a `turn.end` üzenet, azt feltételezik, hogy a szolgáltatás kapcsolati állapota érvénytelen. Ezekben az esetekben zárja be a meglévő kapcsolatot a szolgáltatással, és csatlakoztassa újra.

| Mező | Leírás |
| ------------- | ---------------- |
| WebSocket-üzenetek kódolása | Szöveg |
| Útvonal | `turn.end` |
| Törzs | None |

#### <a name="sample-message"></a>Mintaüzenet

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>Telemetria séma

A törzse a *telemetriai* üzenet egy JSON-struktúrát, amely tartalmazza az ügyfél-információt egy kapcsolja be vagy kapcsolódási kísérlet. A struktúra, amely rögzíti, amikor az ügyfél események következnek be ügyfél időbélyegeket tevődik össze. Minden egyes időbélyeg az "X-időbélyeg fejléc." című szakaszban leírtak szerint ISO 8601 formátumban kell lennie. *Telemetria* , amely nem ad meg minden kötelező mezőt a JSON-szerkezetében lévő vagy a megfelelő időbélyegző-formátum nem használó üzenetek előfordulhat, hogy a szolgáltatást, hogy állítsa le a kapcsolatot az ügyfélnek. Az ügyfelek *kell* adjon érvényes értéket minden kötelező mezők. Az ügyfelek *kell* opcionális mezőket, amikor megfelelő értéket ad meg. A minták ebben a szakaszban látható értékek csak.

Telemetria séma a következő részre oszlik: érkezett üzenet időbélyegzőket és a metrikákat. A formátum és az egyes részek használatát a következő szakaszban van megadva.

### <a name="received-message-time-stamps"></a>Fogadott üzenethez időbélyegek

Az ügyfelek kapnak, miután a szolgáltatás sikeresen csatlakozott összes üzenetek esetében idő a nyugta az értékek tartalmaznia kell. Ezeket az értékeket kell rögzítenie az idő során az ügyfél *kapott* minden üzenetet a hálózatról. Az érték nem kell rögzíteni időszakban. Például az ügyfél kell rögzíti az idő során azt *megtudjuk* üzenetet. A fogadott üzenethez időbélyegeket megadott tömbjét *név-érték:* párokat. A pár nevét adja meg a *elérési út* az üzenet értékét. A pár értékét megadja az üzenet érkezésekor ügyfél időpontját. Vagy, a megadott névvel egynél több üzenet érkezett, ha a pár értéke azt jelzi, ha ezeket az üzeneteket érkeztek időbélyegeket tömbjét.

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

Az ügyfelek *kell* fel ezeket az üzeneteket az időbélyegek JSON-törzse a szolgáltatás által küldött összes üzenet átvételét. Ha egy ügyfél nem tud üzenetet igazolhatom, a szolgáltatás előfordulhat, hogy állítsa le a kapcsolatot.

### <a name="metrics"></a>Mérőszámok

Az ügyfelek tartalmaznia kell egy kérés élettartama során fellépő eseményekkel kapcsolatos információkat. A következő metrikák támogatottak: `Connection`, `Microphone`, és `ListeningTrigger`.

### <a name="metric-connection"></a>A metrika `Connection`

A `Connection` metrika megadja az ügyfél kapcsolódási kísérleteit részleteit. A metrika időbélyeggel tartalmaznia kell, amikor a WebSocket-kapcsolat volt elindult és befejeződött. A `Connection` metrika kötelező *csak az első kapcsolja be a kapcsolat a*. Ennek a későbbi tartalmazza ezt az információt nem szükségesek. Ha egy ügyfél több kapcsolódási kísérletek előtt létrejön a kapcsolat, információt *összes* a kapcsolódási kísérletek tartalmaznia kell. További információkért lásd: [kapcsolati hibák telemetriai adatainak](#connection-failure-telemetry).

| Mező | Leírás | Használat |
| ----- | ----------- | ----- |
| Name (Név) | `Connection` | Szükséges |
| Azonosító | A kapcsolat-értéket, amely használták a *X-ConnectionId* a kapcsolódási kérelem fejléce | Szükséges |
| Indítás | Az idő, amikor az ügyfél küldött-e a kapcsolódási kérelem | Szükséges |
| Befejezés | Az idő, amikor az ügyfelet, hogy a kapcsolat sikeresen létrejött-e értesítést kapott, vagy hiba esetekben, el lett utasítva, elutasítva, vagy nem sikerült | Szükséges |
| Hiba | A következő hiba történt, ha van ilyen leírása. A kapcsolat nem volt sikeres, ha az ügyfelek kell hagyja ki ezt a mezőt. Ez a mező hossza legfeljebb 50 karakter hosszú lehet. | Hiba történt az esetekben nincs egyéb megadva szükséges |

A hiba leírása legfeljebb 50 karakter hosszú lehet kell lennie, és ideális egyikének kell lennie a következő táblázatban felsorolt értékeket. Ha a hibajelzést kiváltó körülmény nem egyezik az alábbi értékek egyikére, ügyfelek használhatják-e a hibajelzést kiváltó körülmény tömör leírása használatával [CamelCasing](https://en.wikipedia.org/wiki/Camel_case) szóköz nélkül. A képes titkosítottan küldeni egy *telemetriai* üzenet a szolgáltatásban, így csak átmeneti kapcsolatot igényel, vagy ideiglenes hibaállapotok jelenteni lehet a a *telemetriai* üzenet. A hibák *véglegesen* ügyfelet kapcsolatot a szolgáltatás letiltása megakadályozza, hogy a az ügyfél bármilyen üzenetet küld a szolgáltatást, beleértve a *telemetriai* üzenetek.

| Hiba | Használat |
| ----- | ----- |
| DNSfailure | Az ügyfél nem tudott csatlakozni a szolgáltatáshoz, a hálózati verem DNS hiba miatt. |
| NoNetwork | Az ügyfél próbált kapcsolatot, de a hálózati protokollkészlet jelentette, hogy nincsenek fizikai hálózati nem érhető el. |
| NoAuthorization | Az Ügyfélkapcsolat-engedélyezési jogkivonat a kapcsolat tett kísérlet során nem sikerült. |
| NoResources | Az ügyfél a kapcsolat tett kísérlet során elfogyott a néhány helyi erőforrás (például memóriája). |
| Tiltott | Az ügyfél nem tudott kapcsolódni a szolgáltatáshoz, mivel a szolgáltatás által visszaadott HTTP `403 Forbidden` a WebSocket frissítésére irányuló kérelem az állapotkódot. |
| Nem engedélyezett | Az ügyfél nem tudott kapcsolódni a szolgáltatáshoz, mivel a szolgáltatás által visszaadott HTTP `401 Unauthorized` a WebSocket frissítésére irányuló kérelem az állapotkódot. |
| Hibás kérés | Az ügyfél nem tudott kapcsolódni a szolgáltatáshoz, mivel a szolgáltatás által visszaadott HTTP `400 Bad Request` a WebSocket frissítésére irányuló kérelem az állapotkódot. |
| ServerUnavailable | Az ügyfél nem tudott kapcsolódni a szolgáltatáshoz, mivel a szolgáltatás által visszaadott HTTP `503 Server Unavailable` a WebSocket frissítésére irányuló kérelem az állapotkódot. |
| Kiszolgálóhibái | Az ügyfél nem tudott kapcsolódni a szolgáltatáshoz, mivel a szolgáltatás által visszaadott egy `HTTP 500` állapotkód belső hiba történt a frissítési WebSocket-kérés. |
| Időtúllépés | Az ügyfél-kapcsolódási kérelem túllépte az időkorlátot a szolgáltatás válaszára nélkül. A *záró* mező tartalmazza az idő, amikor az ügyfél túllépte az időkorlátot, és várakozik a kapcsolatot. |
| Ügyfélhibái | Az ügyfél egy belső ügyfélhiba miatt megszakította a kapcsolatot. | 

### <a name="metric-microphone"></a>A metrika `Microphone`

A `Microphone` metrika szükség az összes speech bekapcsolja. Ez a metrika az óraszáma hangbemenet aktívan használatos beszéd kérés ügyfél időt méri.

Használja az alábbi példák útmutatóul a rögzítés *Start* idő értékei a `Microphone` metrika az ügyfélalkalmazásban található:

* Egy ügyfélalkalmazás megköveteli, hogy egy felhasználó kell nyomnia a fizikai gombra kattintva indítsa el a mikrofon. A gomb megnyomása után az ügyfélalkalmazás a bemeneti olvas be a mikrofon, és elküldi beszédszolgáltatás. A *Start* értékét a `Microphone` metrika gomb megnyomása után a idejét rögzíti, amikor a mikrofon inicializálva, és készen áll a információk megadása. A *záró* értékét a `Microphone` metrika rögzíti az idő, amikor az ügyfélalkalmazás leállt, a szolgáltatás az audio streamelési után kapott a `speech.endDetected` üzenet a szolgáltatásból.

* Egy ügyfélalkalmazás egy kulcsszót spotter, amely a "mindig" figyel. Csak azt követően a kulcsszó spotter észleli a kimondott eseményindító kifejezés nem az ügyfélalkalmazás a bemeneti adatok gyűjtése a mikrofon majd azokat elküldi a Speech Service. A *Start* értékét a `Microphone` metrika rögzíti az idő, amikor a kulcsszó spotter értesítést kap az ügyfél a bemenetet a mikrofon használatának megkezdéséhez. A *záró* értékét a `Microphone` metrika rögzíti az idő, amikor az ügyfélalkalmazás leállt, a szolgáltatás az audio streamelési után kapott a `speech.endDetected` üzenet a szolgáltatásból.

* Egy ügyfélalkalmazás hozzáféréssel rendelkezik az állandó hang adatfolyamba és a csend/speech észlelési hajt végre az adott hang stream egy *speech feltételészlelési modul*. A *Start* értékét a `Microphone` metrika a idejét rögzíti, amikor a *speech feltételészlelési modul* az audio-adatfolyamot bemeneti használatához az ügyfél értesítést kap. A *záró* értékét a `Microphone` metrika rögzíti az idő, amikor az ügyfélalkalmazás leállt, a szolgáltatás az audio streamelési után kapott a `speech.endDetected` üzenet a szolgáltatásból.

* Egy ügyfélalkalmazás a második kapcsolja több kapcsolja kérés feldolgozása folyamatban van, és a egy szolgáltatás válaszüzenetet, kapcsolja be a második kapcsolja a bemeneti gyűjtéséhez a mikrofon arról tájékoztatja. A *Start* értékét a `Microphone` metrika rögzíti az idő, amikor az ügyfélalkalmazás lehetővé teszi, hogy a mikrofon, és elkezdte használni a bemeneti audio adatforrásból. A *záró* értékét a `Microphone` metrika rögzíti az idő, amikor az ügyfélalkalmazás leállt, a szolgáltatás az audio streamelési után kapott a `speech.endDetected` üzenet a szolgáltatásból.

A *záró* idő értékét a `Microphone` metrika rögzíti az idő, amikor az ügyfélalkalmazás hangbemenet streamelési leállt-e. A legtöbb esetben ez az esemény akkor fordul elő, röviddel utána a kapott az ügyfél a `speech.endDetected` üzenet a szolgáltatásból. Ügyfélalkalmazások előfordulhat, hogy ellenőrizze, hogy azok még megfelelően felelnek meg a protokoll biztosításával, hogy a *záró* idő értékét a `Microphone` metrika később, mint a nyugta idő értéke akkor fordul elő a `speech.endDetected` üzenet. És a egy kapcsolja vége és a egy másik kapcsolja kezdetét késleltetés általában van, mert az ügyfelek ellenőrzik előfordulhat, hogy protokoll irányítópultja, biztosítva, hogy a *Start* idejével a `Microphone` bármely ezt követő kapcsolja be a metrika megfelelően a idejét rögzíti, amikor az ügyfél *lépések* a mikrofon hang bemeneti stream a szolgáltatás használatával.

| Mező | Leírás | Használat |
| ----- | ----------- | ----- |
| Name (Név) | Mikrofon | Szükséges |
| Indítás | Az idő, amikor az ügyfél a mikrofon vagy más hang stream hangbemenet első lépéseiben vagy eseményindító kapott a kulcsszó spotter | Szükséges |
| Befejezés | Az idő, amikor az ügyfél leállt, a mikrofon- vagy audióanyagot stream használatára | Szükséges |
| Hiba | A következő hiba történt, ha van ilyen leírása. A mikrofon műveletek sikeres volt, ha az ügyfelek kell hagyja ki ezt a mezőt. Ez a mező hossza legfeljebb 50 karakter hosszú lehet. | Hiba történt az esetekben nincs egyéb megadva szükséges |

### <a name="metric-listeningtrigger"></a>A metrika `ListeningTrigger`
A `ListeningTrigger` metrika méri az idő, amikor a felhasználó hajt végre a műveletet, amely a bemeneti kezdeményezi. A `ListeningTrigger` metrika nem kötelező, de az ügyfelek által biztosított Ez a mérőszám a kivezetésre ehhez.

Használja az alábbi példák útmutatóul a rögzítés *Start* és *záró* idő értékei a `ListeningTrigger` metrika az ügyfélalkalmazásban.

* Egy ügyfélalkalmazás megköveteli, hogy egy felhasználó kell nyomnia a fizikai gombra kattintva indítsa el a mikrofon. A *Start* érték, ez a mérőszám a gomb leküldéses rögzíti. A *záró* érték a gomb leküldéses végeztével rögzíti.

* Egy ügyfélalkalmazás egy kulcsszót spotter, amely a "mindig" figyel. A kulcsszó után spotter észleli a kimondott eseményindító kifejezést, az ügyfélalkalmazás a bemeneti olvas be a mikrofon, és elküldi beszédszolgáltatás. A *Start* a hang, a trigger kifejezést, majd észlelt a kulcsszó spotter érkezésekor idejét rögzíti, ez a mérőszám értéke. A *záró* érték rögzíti az idő, amikor az utolsó szót az eseményindító kifejezés volt beszélt, a felhasználó által.

* Egy ügyfélalkalmazás hozzáféréssel rendelkezik az állandó hang adatfolyamba és a csend/speech észlelési hajt végre az adott hang stream egy *speech feltételészlelési modul*. A *Start* a idejét rögzíti, ez a mérőszám értéke, amely a *speech feltételészlelési modul* hang, majd észlelt nevén speech kapott. A *záró* érték a idejét rögzíti, amikor a *speech feltételészlelési modul* speech észlelt.

* Egy ügyfélalkalmazás a második kapcsolja több kapcsolja kérés feldolgozása folyamatban van, és a egy szolgáltatás válaszüzenetet, kapcsolja be a második kapcsolja a bemeneti gyűjtéséhez a mikrofon arról tájékoztatja. Az ügyfélalkalmazás kell *nem* tartalmaznak egy `ListeningTrigger` ez kapcsolja be a metrika.

| Mező | Leírás | Használat |
| ----- | ----------- | ----- |
| Name (Név) | ListeningTrigger | Optional |
| Indítás | Az idő, amikor az ügyfél figyel-e eseményindító elindítása | Szükséges |
| Befejezés | Az idő, amikor az ügyfél figyel-e eseményindító befejeződött | Szükséges |
| Hiba | A következő hiba történt, ha van ilyen leírása. Ha a trigger művelet sikeres volt, az ügyfelek kell hagyja ki ezt a mezőt. Ez a mező hossza legfeljebb 50 karakter hosszú lehet. | Hiba történt az esetekben nincs egyéb megadva szükséges |

#### <a name="sample-message"></a>Mintaüzenet

Az alábbi minta ReceivedMessages és a metrikák kijelzőkkel telemetriai üzenetet jeleníti meg:

```HTML
Path: telemetry
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000
X-Timestamp: 2016-08-16T15:03:54.183Z

{
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.171Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ],
  "Metrics": [
    {
      "Name": "Connection",
      "Id": "A140CAF92F71469FA41C72C7B5849253",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:48.000Z",
    },
    {
      "Name": "ListeningTrigger",
      "Start": "2016-08-16T15:03:48.776Z",
      "End": "2016-08-16T15:03:48.777Z",
    },
    {
      "Name": "Microphone",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:51.921Z",
    },
  ],
}
```

## <a name="error-handling"></a>Hibakezelés

Ez a szakasz ismerteti a hibaüzenetek és a feltételeket, amelyek az alkalmazás kell kezelje típusú.

### <a name="http-status-codes"></a>HTTP-állapotkódok

A WebSocket-frissítési kérés során beszédszolgáltatás lehet visszaadni a szabványos HTTP-állapotkódok, mint például `400 Bad Request`stb. Az alkalmazásnak megfelelően kell kezelnie a hibaállapotok.

#### <a name="authorization-errors"></a>Engedélyezési hibák

Ha a nem megfelelő engedély a WebSocket frissítés során beszédszolgáltatás adja vissza egy olyan HTTP `403 Forbidden` állapotkódot. Erről a hibakódról is beindító eseményeket, többek között a következők:

* Hiányzó *engedélyezési* fejléc

* Érvénytelen engedélyezési jogkivonat

* Lejárt engedélyezési jogkivonat

A `403 Forbidden` hibaüzenet nem beszédszolgáltatás problémára utalhat. Ez a hibaüzenet azt jelzi, hogy az ügyfélalkalmazás probléma.

### <a name="protocol-violation-errors"></a>Protokollmegsértési hibák

Beszédszolgáltatás kliensből protokoll szabálysértések észleli, ha a szolgáltatás leállítja a WebSocket kapcsolatot, miután egy *állapotkód* és *OK* befejezésére. Ügyfélalkalmazások ezek az információk segítségével elháríthatja és kijavíthatja a szabálysértések.

#### <a name="incorrect-message-format"></a>Helytelen üzenet formátuma

Ha egy ügyfél egy szöveges vagy bináris üzenetet küld a szolgáltatásnak, hogy a megfelelő formátumban megadott Ez az meghatározás kódolása nem, a szolgáltatás lezárja a kapcsolatot egy *1007 érvénytelen hasznos adatok* állapotkódot. 

A szolgáltatás ezzel az állapotkóddal különböző okok miatt, adja vissza, a következő példákban szemléltetett módon:

* "Hibás üzenetek formátuma. Bináris üzenetnek érvénytelen fejléc mérete előtagot." Az ügyfél küldött bináris, amely egy érvénytelen fejléc mérete előtaggal rendelkezik.

* "Hibás üzenetek formátuma. Üzenet bináris mérete4 fejléc érvénytelen." Az ügyfél küldött bináris, amely a megadott egy érvénytelen fejléc mérete.

* "Hibás üzenetek formátuma. Bináris üzenetfejlécekben az UTF-8 dekódolás sikertelen." Az ügyfél egy bináris UTF-8 nem megfelelően kódolt fejlécek tartalmazó üzenetet küldött.

* "Hibás üzenetek formátuma. Szöveges üzenet nem tartalmaz adatot." Az ügyfél küldött szöveges üzenetet, amely nem tartalmaz törzs adatokat.

* "Hibás üzenetek formátuma. Szöveges üzenet az UTF-8 dekódolás sikertelen." Az ügyfél, amely nem megfelelően kódolt szöveges üzenetet küldi az UTF-8.

* "Hibás üzenetek formátuma. Szöveges üzenet tartalmazza nincs fejléc-elválasztó." Az ügyfél küldött szöveges üzenetet, amely nem tartalmazó fejléc elválasztó, vagy használja a helytelen fejléc-elválasztó.

#### <a name="missing-or-empty-headers"></a>Hiányzik vagy üres fejlécek

Ha egy ügyfél küld egy üzenet, amely nem rendelkezik a szükséges fejlécek *X-RequestId:* vagy *elérési*, a szolgáltatás lezárja a kapcsolatot egy *1002 protokollhiba* állapotkódot. Az üzenet "fejléc hiányzik vagy üres. Fejléc a(z) {name}.

#### <a name="requestid-values"></a>Kérelemazonosító: értékek

Ha egy ügyfél üzenetet küld, amely meghatározza egy *X-RequestId:* fejléc formátuma nem megfelelő, a szolgáltatás megszünteti a kapcsolatot, és adja vissza egy *1002 protokollhiba* állapotát. Az üzenet az "érvénytelen kérés. X-RequestId: fejléc értéke nincs megadva a no-dash UUID formátumban."

#### <a name="audio-encoding-errors"></a>Hang kódolási hibák

Ha egy ügyfél küld egy hang adatrészlet hangformátum és a egy kapcsolja kezdeményezi, vagy a kódolás nem felelnek meg a kötelező specifikációnak, a szolgáltatás megszünteti a kapcsolatot, és adja vissza egy *1007 érvénytelen hasznos adatok* állapotkódot. Az üzenet azt jelzi, hogy a hiba a forrás kódolási formátum.

#### <a name="requestid-reuse"></a>Kérelemazonosító: % újrafelhasználása

Miután egy bekapcsolása befejeződik, ha egy ügyfél elküld egy üzenet, amely újból felhasználja az, hogy kapcsolja be a kérés azonosítóját, a szolgáltatás lezárja a kapcsolatot, és adja vissza egy *1002 protokollhiba* állapotkódot. Az üzenet az "érvénytelen kérés. Ismételt kérés azonosítók nem engedélyezett."

## <a name="connection-failure-telemetry"></a>Kapcsolódási hibák telemetriai adatainak

A legjobb lehetséges felhasználói élmény biztosítása érdekében az ügyfelek tájékoztatnia kell az időbélyegek egy adott kapcsolattal fontos ellenőrzőpontokat a Speech Service használatával a *telemetriai* üzenet. Ugyanilyen fontos, hogy az ügyfelek tájékoztatja a szolgáltatást a kapcsolatok is próbált, de nem sikerült.

Minden kapcsolódási kísérlet sikertelen, az ügyfelek számára hozzon létre egy *telemetriai* üzenet egyedi *X-RequestId:* fejléc értéke. Az ügyfél nem tudott kapcsolatot, mert a *ReceivedMessages* JSON-törzse a mező nem hagyható. Csak a `Connection` bejegyzést a *metrikák* mező részét képezi. Ez a bejegyzés tartalmazza a kezdő és záró időbélyegeket, valamint a hibajelzést kiváltó körülmény történt.

### <a name="connection-retries-in-telemetry"></a>Kapcsolat létrehozásával a telemetriában

Az ügyfelek különbséget kell tenni *újrapróbálkozások* a *több kapcsolódási kísérletek* által a csatlakozási kísérlet kiváltó eseményt. Csatlakozási kísérletek végeznek programozott módon bármely felhasználói beavatkozás nélkül újrapróbálkozások. Több kapcsolódási kísérletek, amely a felhasználói adatbevitelre válasz hajtsák végre több kapcsolódási kísérletek. Az ügyfelek adjon az egyes felhasználói indított csatlakozási kísérletek egy egyedi *X-RequestId:* és *telemetriai* üzenet. Az ügyfelek újból felhasználhatja a *X-RequestId:* programozott újrapróbálkozások. Ha több próbálkozás történtek az egyetlen kapcsolódási kísérlet, minden egyes újrapróbálkozási kísérlet alkalmazás része egy `Connection` bejegyzést a *telemetriai* üzenet.

Tegyük fel például, egy felhasználó beszél a kulcsszó trigger kapcsolat elindításához és az első kapcsolódási kísérletre DNS-hibák miatt meghiúsul. Azonban, hogy programozott módon történik az ügyfél által próbálkozik sikeres lesz. Mivel az ügyfél a kapcsolat további, felhasználói beavatkozást nem igénylő újra próbálkozik, az ügyfél használja-e egyetlen *telemetriai* több üzenet `Connection` írja le a kapcsolati bejegyzések.

Másik példaként tegyük fel, hogy egy felhasználó beszél a kulcsszó trigger kapcsolat elindításához és a csatlakozási kísérlet meghiúsul három próbálkozás után. Az ügyfél ezután biztosít, megpróbál kapcsolódni a szolgáltatás leáll, és tájékoztatja a felhasználót, hogy probléma merült fel. A felhasználó ezután beszél a kulcsszó eseményindító újra. Ennek során tegyük fel, hogy az ügyfél csatlakozik a szolgáltatáshoz. Miután csatlakozott, az ügyfél azonnal elküldi egy *telemetriai* az üzenethez, és a szolgáltatás, amely tartalmazza a három `Connection` bejegyzéseket, amelyek ismertetik a kapcsolati hibák. Fogadás után a `turn.end` üzenetet, az ügyfél elküld egy másik *telemetriai* a sikeres kapcsolat leíró üzenetet.

## <a name="error-message-reference"></a>Hibaüzenet-dokumentáció

### <a name="http-status-codes"></a>HTTP-állapotkódok

| HTTP-állapotkód | Leírás | Hibaelhárítás |
| - | - | - |
| 400 Hibás kérés | Az ügyfél, amely helytelen volt WebSocket kapcsolaton kérést küldött. | Ellenőrizze, hogy a szükséges paramétereket és a HTTP-fejlécek adott-e meg, és, hogy helyesek-e az értékeket. |
| 401-es nem engedélyezett | Az ügyfél nem tartalmazza a szükséges engedélyezési információkat. | Ellenőrizze, hogy küld a *engedélyezési* fejléc a WebSocket kapcsolaton. |
| 403 Tiltott | Az ügyfél engedélyezési adatokat küldött, de érvénytelen volt. | Ellenőrizze, hogy nem küld egy lejárt vagy érvénytelen érték a *engedélyezési* fejléc. |
| 404 – Nem található | Az ügyfél egy nem támogatott URL-cím elérési út elérésére történt kísérlet. | Ellenőrizze, hogy a helyes URL-címet használ a WebSocket-kapcsolat. |
| 500 kiszolgáló hiba | A szolgáltatás belső hibába ütközött, és nem sikerült teljesíteni a kérést. | A legtöbb esetben ez a hiba nem átmeneti. Ismételje meg a kérelmet. |
| 503 A szolgáltatás nem érhető el | A szolgáltatás nem érhető el kezelni a kérést. | A legtöbb esetben ez a hiba nem átmeneti. Ismételje meg a kérelmet. |

### <a name="websocket-error-codes"></a>WebSocket-hibakódok

| WebSocketsStatus kódot | Leírás | Hibaelhárítás |
| - | - | - |
| 1000 normál megszüntetésre | A szolgáltatás hiba nélkül a WebSocket-kapcsolat zárva. | Ha a WebSocket megszüntetés nem várt, újraolvasásához megértse, hogyan és mikor a szolgáltatás leállíthatja a WebSocket-kapcsolatot a dokumentációban. |
| 1002 protokollhiba történt | Az ügyfél nem tudott protokoll előírásokat. | Győződjön meg arról, hogy ismerje a protokoll dokumentációját, és egyértelmű követelményeivel kapcsolatos. Olvassa el a hiba lehetséges okai tekintse meg, ha van-e megsértése protokolljának követelményei előző dokumentációjában az. |
| 1007 érvénytelen hasznos adatok | Az ügyfél egy protokoll üzenetben küldött adatok érvénytelenek. | Ellenőrizze az utolsó üzenet a hibákat a szolgáltatásba küldött. Olvassa el a terhelési hibák előző dokumentációjában az. |
| 1011 kiszolgálóhiba | A szolgáltatás belső hibába ütközött, és nem sikerült teljesíteni a kérést. | A legtöbb esetben ez a hiba nem átmeneti. Ismételje meg a kérelmet. |

## <a name="related-topics"></a>Kapcsolódó témakörök

Tekintse meg a [JavaScript SDK](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) , amely a WebSocket-alapú beszédfelismerési szolgáltatás protokoll megvalósítását.
