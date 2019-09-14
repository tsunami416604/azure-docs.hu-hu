---
title: Bing Speech WebSocket protokoll | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: A Bing Speech a WebSockets-alapú protokoll dokumentációja
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e7f51d49624d5019bec058a2d12f6ca2f1366938
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966889"
---
# <a name="bing-speech-websocket-protocol"></a>Bing Speech WebSocket protokoll

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

A Bing Speech egy felhőalapú platform, amely a beszélt hang szöveggé alakításához elérhető legfejlettebb algoritmusokat tartalmazza. A Bing Speech protokoll határozza meg a [kapcsolat beállítását](#connection-establishment) az ügyfélalkalmazások és a szolgáltatás között, valamint az ügyfelek közötti Exchange-alapú beszédfelismerési üzeneteket (az[ügyfél által kezdeményezett üzenetek](#client-originated-messages) és a [szolgáltatás által kezdeményezett](#service-originated-messages) üzenetek). ). Emellett a [telemetria-üzenetek](#telemetry-schema) és a [hibakezelés](#error-handling) is szerepel.

## <a name="connection-establishment"></a>Kapcsolatok létrehozása

A Speech Service protokoll a WebSocket standard Specification [IETF RFC 6455-es](https://tools.ietf.org/html/rfc6455)verzióját követi. A WebSocket-kapcsolat egy HTTP-fejléceket tartalmazó HTTP-kérelemként indul el, amely arra utal, hogy az ügyfél a HTTP-szemantika használata helyett WebSocket-kapcsolatra kíván frissíteni. A kiszolgáló azt jelzi, hogy a WebSocket-kapcsolaton belüli részvételre való hajlandóság `101 Switching Protocols` http-válasz visszaadásával. A kézfogás cseréje után az ügyfél és a szolgáltatás is megnyithatja a szoftvercsatornát, és megkezdheti az üzenetküldést és a fogadást egy Message-alapú protokoll használatával.

A WebSocket-kézfogás elindításához az ügyfélalkalmazás egy HTTPS GET kérelmet küld a szolgáltatásnak. A szolgáltatás szabványos WebSocket-frissítési fejléceket tartalmaz, valamint más, a beszédre jellemző fejléceket is tartalmaz.

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

A szolgáltatás a következőket válaszolja:

```HTTP
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: upgrade
Sec-WebSocket-Key: 2PTTXbeeBXlrrUNsY15n01d/Pcc=
Set-Cookie: SpeechServiceToken=AAAAABAAWTC8ncb8COL; expires=Wed, 17 Aug 2016 15:39:06 GMT; domain=bing.com; path="/"
Date: Wed, 17 Aug 2016 15:03:52 GMT
```

Minden beszédfelismerési kérelemhez [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) -titkosítás szükséges. A titkosítatlan beszédfelismerési kérelmek használata nem támogatott. A következő TLS-verzió támogatott:

* TLS 1,2

### <a name="connection-identifier"></a>Kapcsolatazonosító

A beszédfelismerési szolgáltatás megköveteli, hogy minden ügyfél tartalmazzon egy egyedi azonosítót a kapcsolat azonosításához. A WebSocket-kézfogás indításakor az ügyfeleknek tartalmazniuk *kell* az *X-ConnectionId* fejlécet. Az *X-ConnectionId* fejlécének [univerzálisan egyedi azonosító](https://en.wikipedia.org/wiki/Universally_unique_identifier) (UUID) értéknek kell lennie. A WebSocket olyan frissítési kéréseket tartalmaz, amelyek nem tartalmazzák az *x-ConnectionId*, ne adjon meg értéket az *x-ConnectionId* fejléchez, vagy ne tartalmazzon érvényes UUID értéket a szolgáltatás a http `400 Bad Request` -választal együtt.

### <a name="authorization"></a>Authorization

A normál WebSocket-kézfogási fejléceken kívül a beszédfelismerési kérelmekhez *engedélyezési* fejléc szükséges. A szolgáltatás a fejléc nélkül nem fogadja el a kapcsolatkérelmeket a http `403 Forbidden` -választal.

Az *engedélyezési* fejlécnek tartalmaznia kell egy JSON web token (JWT) hozzáférési tokent.

Az érvényes JWT-hozzáférési tokenek lekéréséhez használt API-kulcsok előfizetésével és beszerzésével kapcsolatos további információkért tekintse meg a [Cognitive Services előfizetés](https://azure.microsoft.com/try/cognitive-services/) oldalát.

Az API-kulcsot a rendszer átadja a jogkivonat-szolgáltatásnak. Példa:

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

A jogkivonat-hozzáféréshez a következő fejléc-információk szükségesek.

| Name (Név) | Formátum | Leírás |
|----|----|----|
| OCP-Apim-Subscription-Key | ASCII | Előfizetési kulcs |

A jogkivonat-szolgáltatás a JWT hozzáférési tokent `text/plain`adja vissza. Ezt követően a rendszer `Base64 access_token` átadja a JWT a kézfogásként a sztringként `Bearer`megadott *engedélyezési* fejlécként. Példa:

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>Cookie-k

Az ügyfeleknek támogatniuk *kell* a HTTP-cookie-kat az [RFC 6265](https://tools.ietf.org/html/rfc6265)-ben megadott módon.

### <a name="http-redirection"></a>HTTP-átirányítás

Az ügyfeleknek támogatniuk *kell* a [http protokoll specifikációjában](https://www.w3.org/Protocols/rfc2616/rfc2616.html)megadott szabványos átirányítási mechanizmusokat.

### <a name="speech-endpoints"></a>Beszédfelismerési végpontok

Az ügyfeleknek a beszédfelismerési szolgáltatás megfelelő végpontját *kell* használniuk. A végpont felismerési mód és nyelv alapján történik. A táblázat néhány példát mutat be.

| Mód | Path | Szolgáltatás URI-ja |
| -----|-----|-----|
| Interaktív | /speech/recognition/interactive/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| Beszélgetés | /speech/recognition/conversation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| Diktálás | /speech/recognition/dictation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

További információ: [szolgáltatás URI-ja](../GetStarted/GetStartedREST.md#service-uri) .

### <a name="report-connection-problems"></a>Kapcsolódási problémák jelentése

Az ügyfeleknek azonnal jelenteniük kell a kapcsolódás során észlelt problémákat. A sikertelen kapcsolatok jelentéskészítéséhez szükséges telemetria a következő témakörben található: [kapcsolódási hiba](#connection-failure-telemetry).

### <a name="connection-duration-limitations"></a>A kapcsolatok időtartamára vonatkozó korlátozások

A szokásos webszolgáltatás HTTP-kapcsolataival összehasonlítva a WebSocket-kapcsolatok *sokáig* tartanak. A Speech Service a szolgáltatáshoz való WebSocket-kapcsolatok időtartamára vonatkozó korlátozásokat helyez el:

 * Az aktív WebSocket-kapcsolatok maximális időtartama 10 perc. A kapcsolat akkor aktív, ha a szolgáltatás vagy az ügyfél WebSocket-üzeneteket küld a kapcsolaton keresztül. A szolgáltatás figyelmeztetés nélkül leállítja a kapcsolódást a korlát elérésekor. Az ügyfeleknek olyan felhasználói forgatókönyvek kidolgozására van szükségük, amelyek nem igénylik, hogy a kapcsolatok a maximális kapcsolódási időtartamon vagy annak közelében maradjanak aktívak.

 * Az inaktív WebSocket-kapcsolatok maximális időtartama 180 másodperc. A kapcsolat inaktív, ha sem a szolgáltatás, sem az ügyfél nem küldött WebSocket-üzenetet a kapcsolaton keresztül. A maximális inaktív élettartam elérésekor a szolgáltatás leállítja az inaktív WebSocket-kapcsolatát.

## <a name="message-types"></a>Üzenetek típusai

Miután létrejött a WebSocket-kapcsolat az ügyfél és a szolgáltatás között, az ügyfél és a szolgáltatás egyaránt képes üzeneteket küldeni. Ez a szakasz a WebSocket-üzenetek formátumát ismerteti.

Az [IETF RFC 6455](https://tools.ietf.org/html/rfc6455) azt adja meg, hogy a WebSocket-üzenetek szöveges vagy bináris kódolás használatával továbbítják az adatokat. A két kódolás eltérő formátumokat használ. Minden formátum az üzenet tartalmának hatékony kódolására, továbbítására és visszafejtésére van optimalizálva.

### <a name="text-websocket-messages"></a>Szöveges WebSocket-üzenetek

A Text WebSocket-üzenetek a fejlécek egy szakaszát és egy, a HTTP-üzenetekhez használt, ismerős Double-Hinte-Return sortöréssel elválasztott szöveges adatokat tartalmaznak. És – például a HTTP-üzenetek esetében – a Text WebSocket-üzenetek a *Name: Value* formátumban elválasztott fejléceket adják meg. Egy szöveges WebSocket-üzenetben szereplő szövegnek [UTF-8](https://tools.ietf.org/html/rfc3629) kódolást *kell* használnia.

A szöveges WebSocket-üzenetekben meg kell adni egy üzenet elérési útját a fejléc *elérési útjában*. Ennek a fejlécnek az értékének a dokumentumban később definiált Speech Protocol típusú üzenetek egyikének kell lennie.

### <a name="binary-websocket-messages"></a>Bináris WebSocket-üzenetek

A bináris WebSocket-üzenetek bináris adattartalmat hordoznak. A Speech Service protokollban a hangátvitelt a szolgáltatás a bináris WebSocket-üzenetek használatával továbbítja és fogadja. Minden más üzenet szöveges WebSocket-üzenet.

Hasonló szövegű WebSocket-üzenetek, a bináris WebSocket-üzenetek fejlécből és szövegtörzsből állnak. A bináris WebSocket-üzenet első 2 bájtja a [endian](https://en.wikipedia.org/wiki/Endianness) sorrendben határozza meg a fejléc szakasz 16 bites egész méretét. A fejléc minimális mérete 0 bájt. A maximális méret 8 192 bájt. A bináris WebSocket-üzenetek fejlécében szereplő szövegnek [US-ASCII](https://tools.ietf.org/html/rfc20) kódolást *kell* használnia.

A bináris WebSocket-üzenetekben lévő fejlécek formátuma ugyanúgy történik, mint a Text WebSocket-üzenetekben. A *név: az érték* formátuma a single-Hinte-Return sortörés pár. A bináris WebSocket-üzeneteknek meg kell adniuk egy üzenet elérési útját a fejléc *elérési útjában*. Ennek a fejlécnek az értékének a dokumentumban később definiált Speech Protocol típusú üzenetek egyikének kell lennie.

A Speech Service protokoll mind a Text, mind a bináris WebSocket-üzeneteket használja.

## <a name="client-originated-messages"></a>Ügyfél által kezdeményezett üzenetek

A kapcsolódás után az ügyfél és a szolgáltatás is megkezdheti az üzenetek küldését. Ez a szakasz az ügyfélalkalmazások által a beszédfelismerési szolgáltatásnak küldött üzenetek formátumát és hasznos adatait ismerteti. A [Service-ből származó üzenetek](#service-originated-messages) szakasz a Speech Service-ből származó és az ügyfélalkalmazások számára küldött üzeneteket jeleníti meg.

Az ügyfél által a szolgáltatásoknak `speech.config`küldött fő üzenetek, `audio`és `telemetry` üzenetek. Az üzenetek részletes megvizsgálása előtt az összes ilyen üzenethez meg kell adni az általános szükséges fejléceket.

### <a name="required-message-headers"></a>Szükséges üzenetek fejlécei

Az összes ügyfél által kezdeményezett üzenethez a következő fejlécek szükségesek.

| Fejléc | Value |
|----|----|
| Path | A dokumentumban megadott üzenet elérési útja |
| X-RequestId | UUID "No-Dash" formátumban |
| X-Timestamp | Ügyfél UTC órajelének időbélyege ISO 8601 formátumban |

#### <a name="x-requestid-header"></a>X – kérelemazonosító fejléc

Az ügyfél által létrehozott kérelmeket az *X-kérelemazonosító* üzenet fejléce egyedileg azonosítja. Ez a fejléc szükséges az összes ügyfél által kezdeményezett üzenethez. Az *X-kérelemazonosító* fejléc ÉRTÉKének UUID-nek kell lennie a "No-Dash" formában, például *123e4567e89b12d3a456426655440000*. *Nem* lehet a Canonical Form *123e4567-e89b-12d3-A456-426655440000*. Olyan kérelemazonosító-fejléc nélküli kérelmek, amelyek nem megfelelő formátumú UUID-ket használnak, mert a szolgáltatás leállítja a WebSocket *-* kapcsolatokat.

#### <a name="x-timestamp-header"></a>X-timestamp fejléc

A beszédfelismerési szolgáltatásnak egy ügyfélalkalmazás által küldött minden üzenetnek tartalmaznia *kell* egy *X-timestamp* fejlécet. A fejléc értéke az az idő, amikor az ügyfél elküldi az üzenetet. Az *X-timestamp* fejléc nélküli és a helytelen formátumot használó fejléc-értékkel rendelkező kérelmek miatt a szolgáltatás leállítja a WebSocket-kapcsolatot.

Az *X-timestamp* fejléc értékének "YYYY'-'MM'-'dd'T'HH": "PP": "ss" formátumúnak kell lennie. a "fffffff" fffffffZ egy másodperc töredéke. Például "12,5": "12 + 5/10 másodperc" és "12,526": "12 plusz 526/1000 másodperc". Ez a formátum megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, a szabványos *http-* fejléctől eltérően pedig ezredmásodperces felbontást adhat meg. Az ügyfélalkalmazások az időbélyegzőket a legközelebbi ezredmásodpercig kerekítik. Az ügyfélalkalmazások számára biztosítani kell, hogy az eszköz órája pontosan nyomon követi az időt egy [Network Time Protocol (NTP) kiszolgáló](https://en.wikipedia.org/wiki/Network_Time_Protocol)használatával.

### <a name="message-speechconfig"></a>Üzenetet`speech.config`

A beszédfelismerési szolgáltatásnak ismernie kell az alkalmazás jellemzőit a lehető legjobb beszédfelismerés biztosításához. A szükséges jellemzők adatai az eszközre és az alkalmazásra vonatkozó operációs rendszerre vonatkozó információkat tartalmaznak. Ezt az információt az `speech.config` üzenetben adja meg.

Az ügyfeleknek azonnal el `speech.config` kell küldeniük egy üzenetet, miután meghozták a kapcsolatot a beszédfelismerési szolgáltatással, és nem küldenek `audio` üzeneteket. Csak egyszer kell `speech.config` üzenetet küldenie kapcsolatonként.

| Mező | Leírás |
|----|----|
| WebSocket-üzenetek kódolása | Text |
| Body | A hasznos adatok JSON-struktúraként |

#### <a name="required-message-headers"></a>Szükséges üzenetek fejlécei

| Fejléc neve | Value |
|----|----|
| Path | `speech.config` |
| X-Timestamp | Ügyfél UTC órajelének időbélyege ISO 8601 formátumban |
| Content-Type | alkalmazás/JSON; charset = UTF-8 |

Ahogy az összes ügyfél által létrehozott üzenet a Speech Service protokollban, az `speech.config` üzenetnek tartalmaznia *kell* egy *X-timestamp* fejlécet, amely az ügyfél UTC-órájának időpontját rögzíti, amikor az üzenetet elküldték a szolgáltatásnak. Az `speech.config` üzenet *nem igényel* *X-kérelemazonosító* fejlécet, mert ez az üzenet nincs társítva egy adott beszédfelismerési kérelemhez.

#### <a name="message-payload"></a>Üzenet tartalma
Az `speech.config` üzenet tartalma egy JSON-struktúra, amely az alkalmazással kapcsolatos információkat tartalmaz. A következő példa ezeket az információkat jeleníti meg. Az ügyfél és az eszköz környezeti információi a JSON-struktúra *környezeti* eleme részét képezik.

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

##### <a name="system-element"></a>Rendszerelem

Az `speech.config` üzenet System. Version eleme tartalmazza az ügyfélalkalmazás vagy az eszköz által használt Speech SDK szoftver verzióját. Az érték a főverzió. *alverzió. Build. ág*formában van. Ha nem alkalmazható, kihagyhatja az *ág* összetevőt.

##### <a name="os-element"></a>Operációs rendszer eleme

| Mező | Leírás | Használat |
|-|-|-|
| os.platform | Az alkalmazást futtató operációsrendszer-platform, például Windows, Android, iOS vagy Linux |Szükséges |
| os.name | Az operációs rendszer termékének neve, például Debian vagy Windows 10 | Szükséges |
| operációs rendszer verziója | Az operációs rendszer verziója a főverzió. *alverzió. Build. ág* formátumban | Szükséges |

##### <a name="device-element"></a>Eszköz eleme

| Mező | Leírás | Használat |
|-|-|-|
| device.manufacturer | Az eszköz hardverének gyártója | Szükséges |
| Device. Model | Az eszköz modellje | Szükséges |
| device.version | Az eszköz gyártója által biztosított szoftververzió. Ez az érték határozza meg az eszköz gyártó által nyomon követhető verzióját. | Szükséges |

### <a name="message-audio"></a>Üzenetet`audio`

A beszédfelismerést támogató ügyfélalkalmazások hangtömbökbe konvertálják a hangadatfolyamot. A hangmennyiségek mindegyike a szolgáltatás által átmásolt, a beszélt hang egy szegmensét hordozza. Egy adott hangtömb maximális mérete 8 192 bájt. A hangadatfolyam-üzenetek *bináris WebSocket-üzenetek*.

Az ügyfelek az `audio` üzenettel hangköteget küldenek a szolgáltatásnak. Az ügyfelek a mikrofonban lévő hanganyagokból olvasanak hangokat, és elküldik ezeket a tömböket a beszédfelismerési szolgáltatásnak. Az első `audio` üzenetnek tartalmaznia kell egy jól formázott fejlécet, amely megfelelően meghatározza, hogy a hang megfelel-e a szolgáltatás által támogatott kódolási formátumok egyikének. A `audio` további üzenetekben csak a mikrofonból beolvasott bináris hangadatfolyam-adatok szerepelnek.

Előfordulhat, hogy az `audio` ügyfelek egy nulla hosszúságú törzsű üzenetet küldenek. Ez az üzenet közli a szolgáltatással, hogy az ügyfél tudja, hogy a felhasználó megszakította a beszédet, a teljes beállítás befejeződött, és a mikrofon ki van kapcsolva.

A beszédfelismerési szolgáltatás az `audio` első olyan üzenetet használja, amely egy egyedi kérelem-azonosítót tartalmaz az új kérés/válasz ciklus vagy a *turn*kezdetének jelzéséhez. Miután a szolgáltatás egy új `audio` kérelem-azonosítóval rendelkező üzenetet kap, a rendszer elveti az összes olyan várólistán lévő vagy el nem küldött üzenetet, amely az előző bekapcsoláshoz van társítva.

| Mező | Leírás |
|-------------|----------------|
| WebSocket-üzenetek kódolása | Binary |
| Body | A hanganyag bináris adathalmaza. A maximális méret 8 192 bájt. |

#### <a name="required-message-headers"></a>Szükséges üzenetek fejlécei

Az összes `audio` üzenethez a következő fejlécek szükségesek.

| Fejléc         |  Value     |
| ------------- | ---------------- |
| Path | `audio` |
| X-RequestId | UUID "No-Dash" formátumban |
| X-Timestamp | Ügyfél UTC órajelének időbélyege ISO 8601 formátumban |
| Content-Type | A hangtartalom típusa A típusnak *hang/x-WAV* (PCM) vagy *audio/selyem* (selyem) típusúnak kell lennie. |

#### <a name="supported-audio-encodings"></a>Támogatott hangkódolások

Ez a szakasz a Speech Service által támogatott hangkodekeket ismerteti.

##### <a name="pcm"></a>A PCM

A beszédfelismerési szolgáltatás fogadja a nem tömörített Pulse Code (PCM) hanganyagot. A rendszer [WAV](https://en.wikipedia.org/wiki/WAV) formátumban továbbítja a hangot a szolgáltatásnak, ezért az első hangdarabnak érvényes [Resource Interchange File Format](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format) (RIFF) fejlécet *kell* tartalmaznia. Ha az ügyfél egy olyan hangblokkot kezdeményez, amely *nem* tartalmaz érvényes riff-fejlécet, a szolgáltatás elutasítja a kérést, és leállítja a WebSocket-kapcsolatát.

A PCM *hanganyagot* 16 kHz-nél 16 bites mintavételezéssel és egy csatornával (*riff-16khz-16bit-mono-PCM*) kell mintát venni. A beszédfelismerési szolgáltatás nem támogatja a sztereó hangstreameket, és elutasítja azokat a hangstreameket, amelyek nem a megadott átviteli sebességet, mintavételi sebességet vagy csatornák számát használják.

##### <a name="opus"></a>Opus

Az Opus egy nyílt, jogdíj nélküli, sokoldalú hangkodek. A beszédfelismerési szolgáltatás az Opus `32000` -et a vagy `16000`a állandó átviteli sebességgel támogatja. Jelenleg csak `OGG` az opusz tárolója támogatott, amelyet a `audio/ogg` MIME-típus ad meg.

Az Opus használatához módosítsa a [JavaScript-mintát](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101) , és változtassa meg a `RecognizerSetup` metódust a visszatéréshez.

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

#### <a name="detect-end-of-speech"></a>A beszédfelismerés végének észlelése

Az emberek nem jelzik explicit módon, hogy mikor állnak készen. Minden olyan alkalmazás, amelyik bemenetként fogadja a beszédet, két lehetőség áll rendelkezésére a beszéd végének egy hangfolyamban való kezelésére: a beszédfelismerés és az ügyfél-beszédfelismerés észlelése. E két lehetőség közül a szolgáltatás-beszédfelismerési funkció általában jobb felhasználói élményt nyújt.

##### <a name="service-end-of-speech-detection"></a>Szolgáltatás-beszédfelismerési funkció észlelése

Az ideális ingyenes beszédfelismerési élmény kiépítéséhez az alkalmazások lehetővé teszik a szolgáltatás számára, hogy észlelje, amikor a felhasználó elvégezte a beszédet. Az ügyfelek hangtömbökként küldenek hangot a mikrofonból, amíg a szolgáltatás nem észleli a csendet, `speech.endDetected` és egy üzenettel válaszol vissza.

##### <a name="client-end-of-speech-detection"></a>Ügyfél-beszédfelismerési észlelés

Azok az ügyfélalkalmazások, amelyek lehetővé teszik, hogy a felhasználó valamilyen módon jelezze a beszéd végét, így a szolgáltatást is megadhatja. Előfordulhat például, hogy egy ügyfélalkalmazás "Leállítás" vagy "némítás" gombra kattint, amelyet a felhasználó megnyomhat. A beszédfelismerés befejezéséhez az ügyfélalkalmazások nulla hosszúságú szövegtörzset tartalmazó *hangdarab üzenetet* küldenek. A beszédfelismerési szolgáltatás a bejövő hangadatfolyam végére értelmezi ezt az üzenetet.

### <a name="message-telemetry"></a>Üzenetet`telemetry`

Az ügyfélalkalmazások *kötelesek* az egyes körök végét visszaigazolni úgy, hogy telemetria küldenek a turn to speech szolgáltatásnak. A végponti visszaigazolás lehetővé teszi a beszédfelismerési szolgáltatás számára, hogy a kérés befejezéséhez szükséges összes üzenetet és a válaszát az ügyfél megfelelően fogadja. A kulcsrakész visszaigazolás azt is lehetővé teszi, hogy a beszédfelismerési szolgáltatás ellenőrizze, hogy az ügyfélalkalmazások a várt módon működnek-e. Ez az információ nem hasznos, ha segítségre van szüksége a beszédfelismerést támogató alkalmazás hibaelhárításához.

Az ügyfeleknek el kell ismerniük egy sor végét, ha `telemetry` az `turn.end` üzenet fogadása után hamarosan üzenetet küldenek. Az ügyfeleknek a lehető leghamarabb meg `turn.end` kell erősíteniük a nyugtát. Ha egy ügyfélalkalmazás nem tudja nyugtázni a turn End-t, a beszédfelismerési szolgáltatás hibát jelez a kapcsolaton. Az ügyfeleknek csak egy `telemetry` üzenetet kell küldeniük az *X-kérelemazonosító* érték által azonosított kérésekhez és válaszokhoz.

| Mező | Leírás |
| ------------- | ---------------- |
| WebSocket-üzenetek kódolása | Text |
| Path | `telemetry` |
| X-Timestamp | Ügyfél UTC órajelének időbélyege ISO 8601 formátumban |
| Content-Type | `application/json` |
| Body | Egy JSON-struktúra, amely a turnre vonatkozó ügyféladatokat tartalmaz |

Az `telemetry` üzenet törzsének sémája a [telemetria séma](#telemetry-schema) szakaszban van meghatározva.

#### <a name="telemetry-for-interrupted-connections"></a>Telemetria a megszakított kapcsolatok esetében

Ha a hálózati kapcsolat bármely okból meghiúsul, és az ügyfél *nem* kap `turn.end` üzenetet a szolgáltatástól, `telemetry` az ügyfél üzenetet küld. Ez az üzenet azt a sikertelen kérést írja le, amikor az ügyfél a következő alkalommal létesít kapcsolódást a szolgáltatáshoz. Az ügyfeleknek nem kell azonnal megkísérelni a kapcsolódást az `telemetry` üzenet elküldéséhez. Lehet, hogy az üzenet pufferelt az ügyfélen, és egy jövőbeli, felhasználó által kért kapcsolaton keresztül küldi el. A sikertelen kérelemhez tartozó üzenetnekasikertelenkérelemX-kérelemazonosítóértékétkellhasználnia`telemetry` . Előfordulhat, hogy a rendszer azonnal elküldi a szolgáltatást, amint a csatlakozás létrejött, és nem vár más üzenetek küldésére és fogadására.

## <a name="service-originated-messages"></a>Szolgáltatásból származó üzenetek

Ez a szakasz a Speech Service-ből származó és az ügyfélnek küldött üzeneteket ismerteti. A beszédfelismerési szolgáltatás megtartja az ügyfél képességeinek beállításjegyzékét, és létrehozza az egyes ügyfelek által igényelt üzeneteket, így nem minden ügyfél kapja meg az itt ismertetett összes üzenetet. A rövidség kedvéért az üzenetekre az *elérési út* fejlécének értéke hivatkozik. Például egy WebSocket szöveges üzenetre hivatkozunk, amelynek a *path* értéke `speech.hypothesis` Speech. hipotézisi üzenet.

### <a name="message-speechstartdetected"></a>Üzenetet`speech.startDetected`

Az `speech.startDetected` üzenet azt jelzi, hogy a beszédfelismerési szolgáltatás beszédet észlelt az audio streamben.

| Mező | Leírás |
| ------------- | ---------------- |
| WebSocket-üzenetek kódolása | Text |
| Path | `speech.startDetected` |
| Content-Type | alkalmazás/JSON; charset = UTF-8 |
| Body | A beszédfelismerés kezdetének észlelésére vonatkozó feltételekkel kapcsolatos információkat tartalmazó JSON-struktúra. A struktúra *eltolási* mezője meghatározza az eltolást (100 ns egységben), amikor a hangadatfolyamban a rendszer beszédet észlelt az adatfolyam elejéhez képest. |

#### <a name="sample-message"></a>Mintaüzenet

```HTML
Path: speech.startDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 100000
}
```

### <a name="message-speechhypothesis"></a>Üzenetet`speech.hypothesis`

A beszédfelismerés során a beszédfelismerési szolgáltatás rendszeres időközönként hipotéziseket hoz létre a szolgáltatás által felismert szavakról. A beszédfelismerési szolgáltatás körülbelül 300 ezredmásodpercen belül elküldi ezeket a hipotéziseket az ügyfélnek. A `speech.hypothesis` *csak* a felhasználói beszéd élményének javítására alkalmas. Az üzenetekben lévő szöveg tartalmának vagy pontosságának semmilyen függősége nem szükséges.

 Az `speech.hypothesis` üzenet azokra az ügyfelekre vonatkozik, amelyek valamilyen szöveges megjelenítési képességgel rendelkeznek, és közel valós időben visszajelzést kívánnak adni a folyamatban lévő felismerésről a beszélő személy számára.

| Mező | Leírás |
| ------------- | ---------------- |
| WebSocket-üzenetek kódolása | Text |
| Path | `speech.hypothesis` |
| X-RequestId | UUID "No-Dash" formátumban |
| Content-Type | application/json |
| Body | A Speech hipotézis JSON-szerkezete |

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

Az *eltolás* elem megadja az eltolást (100 – ns egységekben), amikor a kifejezés felismerhető, a hangadatfolyam elejéhez képest.

Az *időtartam* elem megadja a Speech kifejezés időtartamát (100 ns egységben).

Az ügyfeleknek nem kell feltételezni a beszédfelismerési hipotézisben szereplő gyakoriságot, időzítést vagy szöveget, vagy a szöveg egységességét két beszédes hipotézisben. A hipotézisek csak Pillanatképek a szolgáltatás átírási folyamata során. Nem az átírások állandó összegyűjtését jelentik. Például az első Speech hipotézis tartalmazhatja a "remek szórakozást", a második hipotézis pedig a "Funny" szót is tartalmazhatja. A beszédfelismerési szolgáltatás nem végez utólagos feldolgozást (például a nagybetűket és a központozást) a Speech hipotézis szövegében.

### <a name="message-speechphrase"></a>Üzenetet`speech.phrase`

Ha a beszédfelismerési szolgáltatás megállapítja, hogy elegendő információval rendelkezik egy olyan felismerési eredmény létrehozásához, amely nem `speech.phrase` változik, a szolgáltatás üzenetet hoz létre. A beszédfelismerési szolgáltatás ezeket az eredményeket akkor állítja elő, amikor azt észleli, hogy a felhasználó befejezte a mondatot vagy a kifejezést.

| Mező | Leírás |
| ------------- | ---------------- |
| WebSocket-üzenetek kódolása | Text |
| Path | `speech.phrase` |
| Content-Type | application/json |
| Body | A Speech kifejezés JSON-szerkezete |

A JSON-séma a `RecognitionStatus`következő mezőket tartalmazza: `DisplayText` `Offset`,, és `Duration`. További információ ezekről a mezőkről: [átírási válaszok](../concepts.md#transcription-responses).

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

### <a name="message-speechenddetected"></a>Üzenetet`speech.endDetected`

Az `speech.endDetected` üzenet azt adja meg, hogy az ügyfélalkalmazás le kell állítania a hang folyamatos átvitelét a szolgáltatásba.

| Mező | Leírás |
| ------------- | ---------------- |
| WebSocket-üzenetek kódolása | Text |
| Path | `speech.endDetected` |
| Body | A rendszer a beszédfelismerés végén észlelt eltolást tartalmazó JSON-struktúrát. Az eltolás a NS egységek eltolása az elismeréshez használt hang elejétől számítva 100. |
| Content-Type | alkalmazás/JSON; charset = UTF-8 |

#### <a name="sample-message"></a>Mintaüzenet

```HTML
Path: speech.endDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 0
}
```

Az *eltolás* elem megadja az eltolást (100 – ns egységekben), amikor a kifejezés felismerhető, a hangadatfolyam elejéhez képest.

### <a name="message-turnstart"></a>Üzenetet`turn.start`

A `turn.start` a szolgáltatás szemszögéből jelzi a bekapcsolás kezdetét. Az `turn.start` üzenet mindig az első válaszüzenet, amelyet minden kérelemhez megkap. Ha nem kap `turn.start` üzenetet, tegyük fel, hogy a szolgáltatás kapcsolatainak állapota érvénytelen.

| Mező | Leírás |
| ------------- | ---------------- |
| WebSocket-üzenetek kódolása | Text |
| Path | `turn.start` |
| Content-Type | alkalmazás/JSON; charset = UTF-8 |
| Body | JSON-struktúra |

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

Az `turn.start` üzenet törzse egy JSON-struktúra, amely a turn kezdetének kontextusát tartalmazza. A *környezeti* elem *serviceTag* tulajdonságot tartalmaz. Ez a tulajdonság határozza meg azt a címke-értéket, amelyet a szolgáltatás a turnhöz társított. Ezt az értéket a Microsoft is használhatja, ha segítségre van szüksége az alkalmazás hibáinak elhárításához.

### <a name="message-turnend"></a>Üzenetet`turn.end`

A `turn.end` jel végén a szolgáltatás szemszögéből kerül sor. Az `turn.end` üzenet mindig az utolsó válaszüzenet, amelyet bármelyik kérelemhez megkap. Az ügyfelek a törlési tevékenységek jeleként használhatják az üzenet fogadását, és inaktív állapotra válthatnak. Ha nem kap `turn.end` üzenetet, tegyük fel, hogy a szolgáltatás kapcsolatainak állapota érvénytelen. Ezekben az esetekben zárjuk le a szolgáltatáshoz való meglévő kapcsolatot, és kapcsolódjon újra.

| Mező | Leírás |
| ------------- | ---------------- |
| WebSocket-üzenetek kódolása | Text |
| Path | `turn.end` |
| Body | Nincsenek |

#### <a name="sample-message"></a>Mintaüzenet

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>Telemetria séma

A *telemetria* üzenet törzse egy JSON-struktúra, amely a turn vagy a megkísérelt kapcsolatok ügyfél-információit tartalmazza. A struktúra olyan ügyfél-időbélyegek áll, amelyek az ügyfél eseményeinek rögzítésekor történnek. Minden időbélyegző ISO 8601 formátumúnak kell lennie, az "X-timestamp header" című szakaszban leírtak szerint. Azon *telemetria* , amelyek nem határozzák meg a JSON-struktúra összes kötelező mezőjét, vagy amelyek nem használják a helyes időbélyegző-formátumot, a szolgáltatás megszakítja a kapcsolódást az ügyfélhez. Az ügyfeleknek érvényes értékeket *kell* megadniuk az összes kötelező mezőhöz. Ha szükséges, az ügyfeleknek *meg kell* adni a választható mezők értékét. Az ebben a szakaszban szereplő mintákban látható értékek csak illusztrációk.

A telemetria séma a következő részekre oszlik: üzenet időbélyegek és mérőszámok. Az egyes részek formátuma és használata a következő szakaszokban van meghatározva.

### <a name="received-message-time-stamps"></a>Fogadott üzenet időbélyegei

Az ügyfeleknek tartalmazniuk kell a szolgáltatáshoz való sikeres csatlakozás után kapott összes üzenet kézhezvételi időpontját. Ezeknek az értékeknek rögzíteniük kell az időpontot, amikor az ügyfél az egyes üzeneteket a hálózatról *fogadta* . Az érték nem rögzíthet semmilyen más időpontot. Az ügyfélnek például nem szabad rögzítenie az üzenetben szereplő időpontot. A fogadott üzenet időbélyegei *név: érték* párok tömbben vannak megadva. A pár neve megadja az üzenet *elérési útjának* értékét. A pár értéke határozza meg az ügyfél időpontját az üzenet fogadásakor. Vagy ha a megadott névvel egynél több üzenet érkezett, a pair értéke az időbélyegek tömbje, amely jelzi, hogy mikor érkeztek meg az üzenetek.

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

Az ügyfeleknek *meg kell* ismerniük a szolgáltatás által küldött összes üzenet fogadását, beleértve a JSON-törzsben lévő üzenetek időbélyegét. Ha az ügyfél nem tudja nyugtázni az üzenet fogadását, előfordulhat, hogy a szolgáltatás megszakítja a kapcsolódást.

### <a name="metrics"></a>Mérőszámok

Az ügyfeleknek tartalmazniuk kell a kérelem élettartama során bekövetkezett eseményekkel kapcsolatos információkat. A következő metrikák támogatottak: `Connection`, `Microphone`és `ListeningTrigger`.

### <a name="metric-connection"></a>Metrika`Connection`

A `Connection` metrika az ügyfél csatlakozási kísérleteinek adatait adja meg. A metrikanak tartalmaznia kell a WebSocket-kapcsolat elindítása és befejeződése időbélyegét. A `Connection` metrika *csak a kapcsolatok első bekapcsolásához*szükséges. Ezt az információt nem kötelező további bekapcsolni. Ha egy ügyfél több csatlakozási kísérletet tesz elérhetővé a kapcsolatok létrehozása előtt, a rendszer az *összes* csatlakozási kísérletről információt tartalmaz. További információ: a [sikertelen telemetria](#connection-failure-telemetry).

| Mező | Leírás | Használat |
| ----- | ----------- | ----- |
| Name (Név) | `Connection` | Szükséges |
| Id | A kapcsolatkérelem *X-ConnectionId* fejlécében használt kapcsolatazonosító értéke | Szükséges |
| Start | Az az idő, amikor az ügyfél elküldte a kapcsolódási kérelmet | Szükséges |
| vége | Az az idő, amikor az ügyfél értesítést kapott arról, hogy a kapcsolódás sikeresen megtörtént, vagy hiba esetén a visszautasítva, visszautasítva vagy sikertelen | Szükséges |
| Hiba | Az észlelt hiba leírása, ha van ilyen. Ha a kapcsolatok sikeresek voltak, az ügyfeleknek ki kell hagyniuk ezt a mezőt. A mező maximális hossza 50 karakter. | A hibák esetében szükséges, ellenkező esetben kihagyva |

A hiba leírásának legfeljebb 50 karakterből kell állnia, és ideális esetben az alábbi táblázatban felsorolt értékek egyikének kell lennie. Ha a hiba feltétele nem felel meg ezeknek az értékeknek, a [CamelCasing](https://en.wikipedia.org/wiki/Camel_case) szóköz nélkül használhatja a hiba feltételének tömör leírását. A *telemetria* -üzenetek küldésének lehetősége a szolgáltatáshoz való kapcsolódást igényel, így csak átmeneti vagy ideiglenes hibák jelentkezhetnek be a *telemetria* üzenetben. Azok a hibák, amelyek *végleg* blokkolják az ügyfelet a szolgáltatással létesített kapcsolatok létrehozásában, megakadályozzák, hogy az ügyfél bármilyen üzenetet küldjön a szolgáltatásnak, beleértve a *telemetria* üzeneteket is.

| Hiba | Használat |
| ----- | ----- |
| DNSfailure | Az ügyfél nem tudott kapcsolódni a szolgáltatáshoz, mert a hálózati veremben DNS-hiba történt. |
| Nem hálózat | Az ügyfél megkísérelt csatlakozni, de a hálózati verem azt jelezte, hogy nem volt elérhető fizikai hálózat. |
| Nincs hitelesítés | Az ügyfél kapcsolódása meghiúsult a kapcsolathoz tartozó engedélyezési jogkivonat beszerzésére tett kísérlet során. |
| Nem erőforrások | Az ügyfél bizonyos helyi erőforrásokból (például a memóriából) kifogyott a kapcsolódási kísérlet során. |
| Tiltott | Az ügyfél nem tudott kapcsolódni a szolgáltatáshoz, mert a szolgáltatás http `403 Forbidden` -állapotkódot adott vissza a WebSocket-frissítési kérelemben. |
| Nem engedélyezett | Az ügyfél nem tudott kapcsolódni a szolgáltatáshoz, mert a szolgáltatás http `401 Unauthorized` -állapotkódot adott vissza a WebSocket-frissítési kérelemben. |
| Hibás kérés | Az ügyfél nem tudott kapcsolódni a szolgáltatáshoz, mert a szolgáltatás http `400 Bad Request` -állapotkódot adott vissza a WebSocket-frissítési kérelemben. |
| ServerUnavailable | Az ügyfél nem tudott kapcsolódni a szolgáltatáshoz, mert a szolgáltatás http `503 Server Unavailable` -állapotkódot adott vissza a WebSocket-frissítési kérelemben. |
| ServerError | Az ügyfél nem tudott kapcsolódni a szolgáltatáshoz, mert a szolgáltatás `HTTP 500` belső hibakódot adott vissza a WebSocket-frissítési kérelemben. |
| Időtúllépés | Az ügyfél csatlakoztatási kérelme időtúllépés nélkül megszakadt a szolgáltatástól kapott válasz hiányában. A *befejezési* mező azt az időpontot tartalmazza, amikor az ügyfél időkorlátja lejárt, és a csatlakozásra várakozik. |
| Vonatkozó ügyfélhibái | Az ügyfél egy belső ügyfél hibája miatt megszakította a kapcsolatokat. |

### <a name="metric-microphone"></a>Metrika`Microphone`

Az `Microphone` összes Speech bekapcsolás mérőszáma szükséges. Ez a mérőszám azt az időtartamot méri az ügyfélen, amely során a hangbemenet aktívan használatban van egy beszédfelismerési kérelemhez.

Az alábbi példák útmutatásul szolgálnak a `Microphone` metrika kezdő időértékeinek rögzítéséhez az ügyfélalkalmazás esetében:

* Az ügyfélalkalmazás megköveteli, hogy a felhasználónak egy fizikai gombot kell megnyomnia a mikrofon elindításához. A gomb megnyomása után az ügyfélalkalmazás beolvassa a bemenetet a mikrofonból, és elküldi a beszédfelismerési szolgáltatásnak. A `Microphone` metrika indítási értéke a gomb lenyomása után, amikor a mikrofon inicializálása megtörtént, és készen áll a bevitel megadására. A `Microphone` metrika záró értéke azt az időpontot rögzíti, amikor az ügyfélalkalmazás leállította a hangot a szolgáltatásnak, `speech.endDetected` miután megkapta az üzenetet a szolgáltatástól.

* Az ügyfélalkalmazás olyan kulcsszó-megfigyelőt használ, amely "mindig" figyel. Csak azt követően, hogy a felderítő kifejezés észlelt egy szóbeli triggert, az ügyfélalkalmazás begyűjti a bemenetet a mikrofonból, és elküldi a Speech Service-nek. A `Microphone` metrika indítási értéke azt az időpontot adja meg, amikor a felderítő kulcsszó értesíti az ügyfelet, hogy elkezdi használni a bemenetet a mikrofonból. A `Microphone` metrika záró értéke azt az időpontot rögzíti, amikor az ügyfélalkalmazás leállította a hangot a szolgáltatásnak, `speech.endDetected` miután megkapta az üzenetet a szolgáltatástól.

* Az ügyfélalkalmazás egy állandó hangadatfolyamhoz fér hozzá, és a *beszédfelismerési modulban*az adott hangadatfolyamon a csend/beszéd észlelését hajtja végre. A `Microphone` metrika indítási értéke azt az időpontot adja meg, amikor a *beszédfelismerési modul* értesítette az ügyfelet, hogy kezdjen el bemenetet használni az audio streamből. A `Microphone` metrika záró értéke azt az időpontot rögzíti, amikor az ügyfélalkalmazás leállította a hangot a szolgáltatásnak, `speech.endDetected` miután megkapta az üzenetet a szolgáltatástól.

* Egy ügyfélalkalmazás dolgozza fel a többfordulatos kérelmek második bekapcsolását, és egy szolgáltatás válaszüzenete értesíti a mikrofont a második turn bemenetének bekapcsolásához. A `Microphone` metrika indítási értéke rögzíti azt az időpontot, amikor az ügyfélalkalmazás engedélyezi a mikrofont, és megkezdi az adott Hangforrásból érkező bemenet használatát. A `Microphone` metrika záró értéke azt az időpontot rögzíti, amikor az ügyfélalkalmazás leállította a hangot a szolgáltatásnak, `speech.endDetected` miután megkapta az üzenetet a szolgáltatástól.

A `Microphone` metrika záró időértéke azt az időpontot rögzíti, amikor az ügyfélalkalmazás leállította a hangbemenetet. A legtöbb esetben ez az esemény röviddel azután fordul elő, hogy az ügyfél `speech.endDetected` megkapta az üzenetet a szolgáltatástól. Az ügyfélalkalmazások ellenőrizhetik, hogy megfelelően megfelelnek-e a protokollnak azáltal, hogy a `Microphone` metrika *befejezési* időértéke későbbi, mint az `speech.endDetected` üzenet bevételezési ideje. És mivel általában az egyik fordulat vége és egy másik sor kezdete között késés van, az ügyfelek ellenőrizhetik a protokollok megfelelőségét annak biztosításával, hogy a `Microphone` metrika *kezdési* időpontja minden későbbi alkalommal helyesen rögzítse azt az időpontot, amikor a az ügyfél a mikrofon használatával *megkezdte* a hangbemenet továbbítását a szolgáltatásba.

| Mező | Leírás | Használat |
| ----- | ----------- | ----- |
| Name (Név) | Mikrofon | Szükséges |
| Start | Az az idő, amikor az ügyfél megkezdte a hangbemenet használatát a mikrofonból vagy más hangadatfolyamból, vagy triggert kapott a kulcsszó-felderítőből | Szükséges |
| vége | Az az idő, amikor az ügyfél leállt a mikrofon vagy a hangadatfolyam használatával | Szükséges |
| Hiba | Az észlelt hiba leírása, ha van ilyen. Ha a mikrofon műveletei sikeresek voltak, az ügyfeleknek ki kell hagyniuk ezt a mezőt. A mező maximális hossza 50 karakter. | A hibák esetében szükséges, ellenkező esetben kihagyva |

### <a name="metric-listeningtrigger"></a>Metrika`ListeningTrigger`
A `ListeningTrigger` metrika azt az időpontot méri, amikor a felhasználó végrehajtja a beszédfelismerési bemenetet kezdeményező műveletet. A `ListeningTrigger` metrika nem kötelező, de a mérőszámot megadható ügyfelek számára ajánlott.

Az alábbi példák útmutatásul szolgálnak a `ListeningTrigger` metrika *indítási* és *befejezési* időértékeinek rögzítéséhez az ügyfélalkalmazás számára.

* Az ügyfélalkalmazás megköveteli, hogy a felhasználónak egy fizikai gombot kell megnyomnia a mikrofon elindításához. A metrika *indítási* értéke a gomb leküldésének időpontját rögzíti. A *befejezési* érték a gomb lenyomásának időpontját rögzíti.

* Az ügyfélalkalmazás olyan kulcsszó-megfigyelőt használ, amely "mindig" figyel. Miután a "felderítő" kulcsszó észleli a kimondott trigger kifejezését, az ügyfélalkalmazás beolvassa a bemenetet a mikrofonból, és elküldi azt a Speech Service-nek. A metrika *kezdő* értéke azt az időpontot adja meg, amikor a "felderítő" kulcsszót a rendszer az trigger kifejezésként észlelt hangként kapott. A *záró* érték azt az időpontot rögzíti, amikor a felhasználó elmondta az trigger kifejezés utolsó szavát.

* Az ügyfélalkalmazás egy állandó hangadatfolyamhoz fér hozzá, és a *beszédfelismerési modulban*az adott hangadatfolyamon a csend/beszéd észlelését hajtja végre. A metrika *indítási* értéke rögzíti azt az időpontot, ameddig a *beszédfelismerési modul* olyan hangot kapott, amelyet a rendszer beszédként észlelt. A *záró* érték azt az időpontot rögzíti, amikor a *beszédfelismerési modul* beszédet észlelt.

* Egy ügyfélalkalmazás dolgozza fel a többfordulatos kérelmek második bekapcsolását, és egy szolgáltatás válaszüzenete értesíti a mikrofont a második turn bemenetének bekapcsolásához. Az ügyfélalkalmazás *nem* tartalmazhat `ListeningTrigger` metrikát ehhez a bekapcsoláshoz.

| Mező | Leírás | Használat |
| ----- | ----------- | ----- |
| Name (Név) | ListeningTrigger | Választható |
| Start | Az az idő, amikor az ügyfél-figyelő trigger elindult | Szükséges |
| vége | Az az idő, amikor az ügyfél figyelésének befejezése befejeződött | Szükséges |
| Hiba | Az észlelt hiba leírása, ha van ilyen. Ha az aktiválási művelet sikeres volt, az ügyfeleknek ki kell hagyniuk ezt a mezőt. A mező maximális hossza 50 karakter. | A hibák esetében szükséges, ellenkező esetben kihagyva |

#### <a name="sample-message"></a>Mintaüzenet

Az alábbi minta egy telemetria üzenetet mutat be, amely a ReceivedMessages és a metrikák részeit is tartalmazza:

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

Ez a szakasz azokat a hibaüzeneteket és feltételeket ismerteti, amelyeket az alkalmazásnak kezelnie kell.

### <a name="http-status-codes"></a>HTTP-állapotkódok

A WebSocket frissítési kérelme során a beszédfelismerési szolgáltatás a szabványos HTTP-állapotkódok bármelyikét visszaküldheti `400 Bad Request`, például: stb. Az alkalmazásnak megfelelően kell kezelnie ezeket a hibákra vonatkozó feltételeket.

#### <a name="authorization-errors"></a>Engedélyezési hibák

Ha a WebSocket frissítése során helytelen engedély van megadva, a Speech szolgáltatás http `403 Forbidden` -állapotkódot ad vissza. A hibakódot kiváltó feltételek közé a következők tartoznak:

* Hiányzó *engedélyezési* fejléc

* Érvénytelen engedélyezési jogkivonat

* Lejárt engedélyezési jogkivonat

A `403 Forbidden` hibaüzenet nem jelez problémát a beszédfelismerési szolgáltatással kapcsolatban. Ez a hibaüzenet az ügyfélalkalmazás problémáját jelzi.

### <a name="protocol-violation-errors"></a>Protokollok megsértésével kapcsolatos hibák

Ha a beszédfelismerési szolgáltatás bármely protokoll megsértését észleli egy ügyfélről, a szolgáltatás leállítja a WebSocket-kapcsolat *állapotát* , miután visszatért egy állapotkódot, és a lezárás *oka* . Az ügyfélalkalmazások ezeket az információkat használhatják a szabálysértések elhárításához és javításához.

#### <a name="incorrect-message-format"></a>Helytelen formátumú üzenet

Ha az ügyfél szöveges vagy bináris üzenetet küld a szolgáltatásnak, amely nem az ebben a specifikációban megadott megfelelő formátumban van kódolva, a szolgáltatás lezárja a kapcsolódást egy *1007 érvénytelen adattartalom* -adatállapot-kóddal.

A szolgáltatás számos okból visszaadja ezt az állapotkódot, ahogy az alábbi példákban is látható:

* "Helytelen az üzenet formátuma. A bináris üzenet fejlécének mérete érvénytelen. " Az ügyfél olyan bináris üzenetet küldött, amelyben érvénytelen a fejléc méretének előtagja.

* "Helytelen az üzenet formátuma. A bináris üzenet fejlécének mérete érvénytelen. " Az ügyfél olyan bináris üzenetet küldött, amely érvénytelen fejléc-méretet adott meg.

* "Helytelen az üzenet formátuma. A bináris üzenetek fejlécének kódolása UTF-8-ra meghiúsult. " Az ügyfél olyan bináris üzenetet küldött, amely az UTF-8-ban helytelenül kódolt fejléceket tartalmaz.

* "Helytelen az üzenet formátuma. A szöveges üzenet nem tartalmaz adatelemet. " Az ügyfél olyan szöveges üzenetet küldött, amely nem tartalmaz szövegtörzset.

* "Helytelen az üzenet formátuma. A szöveges üzenet dekódolása UTF-8-ra meghiúsult. " Az ügyfél olyan szöveges üzenetet küldött, amely nem volt megfelelően kódolva UTF-8-ban.

* "Helytelen az üzenet formátuma. A szöveges üzenet nem tartalmaz fejléc-elválasztó karaktert. " Az ügyfél olyan szöveges üzenetet küldött, amely nem tartalmaz fejléc-elválasztó karaktert, vagy nem használta a helytelen fejléc-elválasztó karaktert.

#### <a name="missing-or-empty-headers"></a>Hiányzó vagy üres fejlécek

Ha az ügyfél olyan üzenetet küld, amely nem rendelkezik a szükséges fejlécek *X-kérelemazonosító* vagy *elérési úttal*, a szolgáltatás *1002 protokollhiba miatt* bezárja a kapcsolatát. Az üzenet "hiányzik/üres" fejléc. {Fejléc neve}. "

#### <a name="requestid-values"></a>Kérelemazonosító-értékek

Ha az ügyfél olyan üzenetet küld, amely helytelen formátumú *X-kérelemazonosító* fejlécet ad meg, a szolgáltatás bezárja a kapcsolatokat, és *1002 protokollhiba* állapotot ad vissza. Az üzenet "érvénytelen kérés. Az X-kérelemazonosító fejléc értéke nem kötőjeles UUID formátumban lett megadva. "

#### <a name="audio-encoding-errors"></a>Audió kódolási hibák

Ha az ügyfél olyan hangrészletet küld, amely egy bekapcsolást kezdeményez, és a hangformátum vagy a kódolás nem felel meg a kötelező specifikációnak, a szolgáltatás bezárja a kapcsolódást, és egy *1007 érvénytelen adattartalom* -állapotkódot ad vissza. Az üzenet a kódolási hiba forrásának formátumát jelzi.

#### <a name="requestid-reuse"></a>Kérelemazonosító újrafelhasználása

Ha egy bekapcsolás után az ügyfél olyan üzenetet küld, amely a kérés azonosítóját újrahasználja, a szolgáltatás bezárja a kapcsolatát, és egy *1002 protokollhiba* -állapotkódot ad vissza. Az üzenet "érvénytelen kérés. A kérés-azonosítók újrafelhasználása nem engedélyezett. "

## <a name="connection-failure-telemetry"></a>Sikertelen telemetria

A lehető legjobb felhasználói élmény biztosítása érdekében az ügyfeleknek tájékoztatnia kell a Speech Service-t a kapcsolatok fontos ellenőrzőpontokról a *telemetria* üzenet használatával. Ugyanilyen fontos, hogy az ügyfelek tájékoztassák a megkísérelt kapcsolódási szolgáltatásokat, de nem sikerült.

Minden sikertelen kapcsolódási kísérlet esetén az ügyfelek egyedi *X-kérelemazonosító fejléc-* értékkel hoznak létre egy *telemetria* üzenetet. Mivel az ügyfél nem tudott kapcsolatot létesíteni, a JSON-törzsben lévő *ReceivedMessages* mező kihagyható. Csak a `Connection` *metrikák* mezőben szereplő bejegyzés szerepel. Ez a bejegyzés tartalmazza a kezdő és a záró időbélyeget, valamint az észlelt hiba feltételét.

### <a name="connection-retries-in-telemetry"></a>Kapcsolódási kísérletek a telemetria-ben

Az ügyfeleknek meg kell különböztetniük az *újrapróbálkozásokat* *több kapcsolódási kísérlettől* a kapcsolódási kísérletet kiváltó eseménytől. A programozott módon végrehajtott kapcsolódási kísérletek felhasználói bevitel nélkül újrapróbálkoznak. A felhasználói bevitelre adott válaszként végrehajtott több kapcsolódási kísérlet több kapcsolódási kísérletet tesz elérhetővé. Az ügyfelek egyedi *X-kérelemazonosító* és *telemetria* -üzenetet adnak az egyes felhasználók által aktivált kapcsolatok számára. Az ügyfelek újrahasznosítják az *X-kérelemazonosító* a programozott újrapróbálkozásokhoz. Ha egyetlen kapcsolódási kísérlethez több újrapróbálkozás is megtörtént, a rendszer minden újrapróbálkozási kísérletet belefoglal `Connection` a *telemetria* üzenetbe.

Tegyük fel például, hogy egy felhasználó a kulcsszó triggert adja meg egy kapcsolat indításához, és az első kapcsolódási kísérlet DNS-hibák miatt meghiúsul. Az ügyfél által programozott módon végrehajtott második kísérlet azonban sikeres lesz. Mivel az ügyfél újrapróbálkozott a kapcsolódással anélkül, hogy további adatokat kellene megadnia a felhasználótól, az ügyfél `Connection` egyetlen, több bejegyzést tartalmazó *telemetria* üzenetet használ a kapcsolatok leírásához.

Egy másik Példaként tegyük fel, hogy egy felhasználó a kulcsszó triggert a kapcsolat elindításához, és a kapcsolódási kísérlet három újrapróbálkozás után meghiúsul. Az ügyfél ezután megszakítja a kapcsolódást a szolgáltatáshoz, és tájékoztatja a felhasználót, hogy hiba történt. A felhasználó ezután újra megbeszéli a kulcsszó-triggert. Ezúttal tegyük fel, hogy az ügyfél csatlakozik a szolgáltatáshoz. A csatlakozás után az ügyfél azonnal *telemetria* üzenetet küld a szolgáltatásnak, amely a kapcsolódási hibákat leíró három `Connection` bejegyzést tartalmaz. Az `turn.end` üzenet fogadása után az ügyfél egy másik *telemetria* üzenetet küld, amely leírja a sikeres kapcsolatokat.

## <a name="error-message-reference"></a>Hibaüzenet-hivatkozás

### <a name="http-status-codes"></a>HTTP-állapotkódok

| HTTP-állapotkód | Leírás | Hibaelhárítás |
| - | - | - |
| 400 hibás kérelem | Az ügyfél nem megfelelő WebSocket-kapcsolati kérelmet küld. | Győződjön meg arról, hogy az összes szükséges paramétert és HTTP-fejlécet adta meg, és hogy az értékek helyesek. |
| 401 jogosulatlan | Az ügyfél nem tartalmazta a szükséges hitelesítési adatokat. | Győződjön meg arról, hogy az *engedélyezési* fejlécet a WebSocket-kapcsolaton keresztül küldi el. |
| 403 Tiltott | Az ügyfél elküldötte az engedélyezési adatokat, de érvénytelen volt. | Győződjön meg arról, hogy nem küld lejárt vagy érvénytelen értéket az *engedélyezési* fejlécben. |
| 404 – Nem található | Az ügyfél megpróbált elérni egy nem támogatott URL-elérési utat. | Győződjön meg arról, hogy a megfelelő URL-címet használja a WebSocket-kapcsolathoz. |
| 500 kiszolgálóhiba | A szolgáltatás belső hibát észlelt, és nem tudja kielégíteni a kérelmet. | A legtöbb esetben ez a hiba átmeneti. Próbálja megismételni a kérelmet. |
| 503 A szolgáltatás nem érhető el | A szolgáltatás nem volt elérhető a kérelem kezelésére. | A legtöbb esetben ez a hiba átmeneti. Próbálja megismételni a kérelmet. |

### <a name="websocket-error-codes"></a>WebSocket-hibakódok

| WebSocketsStatus-kód | Leírás | Hibaelhárítás |
| - | - | - |
| 1000 normál lezárás | A szolgáltatás hiba nélkül lezárta a WebSocket-kapcsolatát. | Ha a WebSocket bezárása váratlan volt, olvassa el újra a dokumentációt, és győződjön meg arról, hogy a szolgáltatás hogyan és mikor vonhatja le a WebSocket-kapcsolatokat. |
| 1002 protokollhiba | Az ügyfél nem tudta betartani a protokollok követelményeit. | Győződjön meg arról, hogy megértette a protokoll dokumentációját, és egyértelművé teszi a követelményeket. A hibák okairól a korábbi dokumentációban tájékozódhat, ha megsérti a protokollok követelményeit. |
| 1007 érvénytelen adattartalom-adatok | Az ügyfél érvénytelen adattartalmat küldött egy protokoll-üzenetben. | A szolgáltatásnak küldött utolsó üzenetben keresse meg a hibákat. A hasznos adatokkal kapcsolatos hibákról a korábbi dokumentációban olvashat. |
| 1011 kiszolgálóhiba | A szolgáltatás belső hibát észlelt, és nem tudja kielégíteni a kérelmet. | A legtöbb esetben ez a hiba átmeneti. Próbálja megismételni a kérelmet. |

## <a name="related-topics"></a>Kapcsolódó témakörök

Tekintse meg a WebSocket-alapú Speech Service protokoll megvalósítását szolgáló [JavaScript SDK](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) -t.
