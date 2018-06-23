---
title: A Microsoft a beszédfelismerés WebSocket protokoll |} Microsoft Docs
description: A websocket elemek alapján beszéd szolgáltatás protokoll dokumentációja
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 17954536e8bdb49c09204c2e522586b79cb1bef5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347495"
---
# <a name="speech-service-websocket-protocol"></a>Beszéd szolgáltatás WebSocket protokoll

  Beszéd Service egy felhőalapú platform, amely a legtöbb speciális algoritmusok szöveg formátumba való átalakítása szóbeli Audio. A beszédfelismerés szolgáltatás protokoll-meghatározása a [csatlakozási telepítés](#connection-establishment) ügyfélalkalmazások és a szolgáltatás és a beszédfelismerés üzenetei megfelelők esetében között cserélődő között ([üzenetekügyféláltalkezdeményezett](#client-originated-messages)és [szolgáltatás által kezdeményezett üzenetek](#service-originated-messages)). Emellett [telemetriai üzenetek](#telemetry-schema) és [hibakezelés](#error-handling) ismerteti.

## <a name="connection-establishment"></a>Kapcsolat létrehozása

A beszédfelismerés szolgáltatás protokoll követi WebSocket szabványos [IETF RFC 6455](https://tools.ietf.org/html/rfc6455). A WebSocket-kapcsolat indul, mint a HTTP-fejléceket, a kapcsolat frissítése a WebSocket HTTP szemantikáját használata helyett az ügyfél desire jelző tartalmazó HTTP-kérelem. A kiszolgáló jelzi szándékát, hogy a WebSocket-kapcsolat részt HTTP vissza `101 Switching Protocols` választ. A kézfogás exchange, követően ügyfél és a szolgáltatás a szoftvercsatorna nyitva hagyja, és megkezdheti a üzenetalapú protokoll használatát küldhet és fogadhat adatokat.

A WebSocket-kézfogás megkezdéséhez az ügyfélalkalmazás HTTPS GET kérelmet küld a szolgáltatás. Ez magában foglalja a szabványos WebSocket frissítési fejlécekkel együtt más jellemző beszéd fejléc.

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

A szolgáltatás válaszol:

```HTTP
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: upgrade
Sec-WebSocket-Key: 2PTTXbeeBXlrrUNsY15n01d/Pcc=
Set-Cookie: SpeechServiceToken=AAAAABAAWTC8ncb8COL; expires=Wed, 17 Aug 2016 15:39:06 GMT; domain=bing.com; path="/"
Date: Wed, 17 Aug 2016 15:03:52 GMT
```

Minden beszéd kérelemhez szükséges a [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) titkosítás. A titkosítatlan beszéd kérelmek használata nem támogatott. A következő TLS-verziót támogatja:

* TLS 1.2

### <a name="connection-identifier"></a>Kapcsolat azonosítója

Beszéd szolgáltatás megköveteli, hogy minden ügyfél egyedi Azonosítót a kapcsolat azonosítására. Ügyfelek *kell* közé tartozik a *X-ConnectionId* fejléc, amikor elindítja a WebSocket-kézfogás. A *X-ConnectionId* kell lennie egy [univerzálisan egyedi azonosítóval](https://en.wikipedia.org/wiki/Universally_unique_identifier) (UUID azonosító) értékét. Nem tartalmazó WebSocket-frissítési kéréseket a *X-ConnectionId*, ne adjon meg egy értéket a *X-ConnectionId* fejléc, vagy nem tartalmaznak egy érvényes UUID érték visszautasítja az HTTP -bA`400 Bad Request` választ.

### <a name="authorization"></a>Engedélyezés

A szabványos WebSocket kézfogás fejlécek mellett beszéd kérésekhez egy *engedélyezési* fejléc. Kapcsolat nélküli ezt a fejlécet visszautasítja a szolgáltatást, amely HTTP kérelmek `403 Forbidden` választ.

A *engedélyezési* fejléc tartalmaznia kell egy JSON webes jogkivonat (JWT) hozzáférési jogkivonat.

Előfizetés, és szerezzen be érvényes JWT jogkivonatot beolvasni használt API-kulcsokat kapcsolatos információkért lásd: a [kognitív szolgáltatások előfizetés](https://azure.microsoft.com/try/cognitive-services/) lap.

Az API-kulcsot a jogkivonat-szolgáltatás lett átadva. Példa:

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

A következő fejléc-információ szükség a hozzáférés a jogkivonatokhoz.

| Name (Név) | Formátum | Leírás |
|----|----|----|
| Az OCP-Apim-előfizetés-kulcs | ASCII | Előfizetési kulcs |

A jogkivonat-szolgáltatás adja vissza, a JWT jogkivonat `text/plain`. A JWT átadása majd egy `Base64 access_token` a kézfogás, hogy egy *engedélyezési* a karakterlánc a következő előtaggal fejléc `Bearer`. Példa:

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>A cookie-k

Ügyfelek *kell* támogatja a HTTP cookie-k meghatározott [RFC 6265](https://tools.ietf.org/html/rfc6265).

### <a name="http-redirection"></a>A HTTP-átirányítás

Ügyfelek *kell* támogatja a megadott szabványos átirányítási módszerek a [HTTP protokoll specifikációja](http://www.w3.org/Protocols/rfc2616/rfc2616.html).

### <a name="speech-endpoints"></a>Beszéd-végpontok

Ügyfelek *kell* megfelelő végpont beszéd szolgáltatást használja. A végpont mód és a nyelvi alapul. A táblázatban néhány példa.

| Mód | Útvonal | Szolgáltatás URI-ja |
| -----|-----|-----|
| Interaktív | /Speech/Recognition/Interactive/cognitiveservices/V1 |https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| Beszélgetés | /Speech/Recognition/Conversation/cognitiveservices/V1 |https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| Diktálás | /Speech/Recognition/dictation/cognitiveservices/V1 |https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

További információkért lásd: a [szolgáltatás URI](../GetStarted/GetStartedREST.md#service-uri) lap.

### <a name="report-connection-problems"></a>A jelentés kapcsolati problémák

Ügyfelek azonnal jelenteniük kell az összes probléma történt a kapcsolat. A jelentéskészítési sikertelen kapcsolatok üzenet protokollja ismertetett [kapcsolati hibák telemetriai adatainak](#connection-failure-telemetry).

### <a name="connection-duration-limitations"></a>Kapcsolat időtartama korlátozásai

Szokásos webes szolgáltatás HTTP-kapcsolatoknál összehasonlítva az utolsó WebSocket-kapcsolatokat a *hosszú* idő. Beszéd Service korlátozásai helyez el a WebSocket-kapcsolatokat a szolgáltatás időtartama:

 * Minden aktív WebSocket-kapcsolat engedélyezett maximális időtartam érték 10 perc. Egy kapcsolat aktív, ha a szolgáltatás vagy az ügyfél WebSocket-üzeneteket küld a kapcsolaton keresztül. A szolgáltatás leáll figyelmeztetés nélkül kapcsolat, ha eléri a korlátot. Ügyfelek kell kidolgozni felhasználói esetek, amelyek nem igényelnek kevés vagy a maximális kapcsolódási élettartama aktív marad a kapcsolatot.

 * Bármely inaktív WebSocket-kapcsolat engedélyezett maximális időtartam értéke 180 másodperc. A kapcsolat nem aktív, ha a szolgáltatás, sem az ügyfél WebSocket üzenetet küldött a kapcsolaton keresztül. A maximális inaktív élettartam elérése után a szolgáltatás leáll az inaktív WebSocket-kapcsolat.

## <a name="message-types"></a>Üzenettípus

Az ügyfél és a szolgáltatás között a WebSocket-kapcsolat létrejötte után az ügyfél és a szolgáltatás is küldhet üzeneteket. Ez a szakasz ismerteti a WebSocket üzenetek formátumban.

[IETF RFC 6455](https://tools.ietf.org/html/rfc6455) határozza meg, hogy WebSocket üzenetek adatot tud továbbítani több szöveg vagy a bináris kódolás használatával. A két kódolások használjon különböző a tömörített. Minden egyes formátum hatékony kódolását, az átvitel és a üzenetadatokat dekódolás van optimalizálva.

### <a name="text-websocket-messages"></a>Szöveges WebSocket üzenetek

Szöveges WebSocket üzenetek lebonyolítására szöveges információ, amely a részében a fejlécek, a megszokott dupla kocsivissza soremelés pár használt HTTP-üzenetek elválasztott törzs áll a hasznos adatok között. És a HTTP-üzenetek, például szöveg WebSocket üzenetek adja meg a fejlécek *nevét: érték* formátum egyetlen kocsivissza soremelés párból elválasztva. SMS WebSocket szereplő szöveg *kell* használja [UTF-8](https://tools.ietf.org/html/rfc3629) kódolást.

Szöveges WebSocket üzenetek üzenet útvonalat kell megadni a fejlécben *elérési*. A fejléc értékének a jelen dokumentum későbbi szakaszában meghatározott beszéd protokoll üzenet típusok egyikét kell lennie.

### <a name="binary-websocket-messages"></a>Bináris WebSocket-üzenetek

Bináris WebSocket üzenetek portprofil a bináris hasznos adatok között. A beszédfelismerés szolgáltatás protokoll hang továbbítva, és kapott a szolgáltatástól bináris WebSocket üzenetek használatával. Az összes többi üzenet a szöveges WebSocket üzenetek. 

SMS-üzeneteinek WebSocket, például bináris WebSocket üzenetek áll fejléc és a szervezet szakasz. A bináris WebSocket üzenet első 2 bájt adja meg, a [bájtsorrendű](https://en.wikipedia.org/wiki/Endianness) sorrendben a fejlécszakasza 16 bites egész méretét. A minimális fejléc szakasz mérete 0 bájt. A maximális mérete 8192 bájt. A bináris WebSocket üzenetek szövegének *kell* használja [US-ASCII](https://tools.ietf.org/html/rfc20) kódolást.

Egy bináris WebSocket üzenet fejlécének kódolt ugyanabban a formában, ahogy WebSocket üzeneteket. A *name: value* formátum egyetlen kocsivissza soremelés párból választja el egymástól. Bináris WebSocket üzenetek üzenet útvonalat kell megadni a fejlécben *elérési*. A fejléc értékének a jelen dokumentum későbbi szakaszában meghatározott beszéd protokoll üzenet típusok egyikét kell lennie.

Szöveg- és bináris WebSocket üzenetek a beszédfelismerés szolgáltatás protokollt használnak. 

## <a name="client-originated-messages"></a>Ügyfél által kezdeményezett üzenetek

Miután létrejött a kapcsolat, mind az ügyfél és a szolgáltatás indítsa el küldhet üzeneteket. Ez a szakasz ismerteti a formátum és az ügyfélalkalmazások beszéd szolgáltatásnak küldött üzenetek hasznos. A szakasz [szolgáltatás által kezdeményezett üzenetek](#service-originated-messages) megadja a beszédfelismerés szolgáltatás származnak, és az ügyfélalkalmazások számára küldött üzeneteket.

A fő a szolgáltatásoknak az ügyfél által küldött üzenetek `speech.config`, `audio`, és `telemetry` üzeneteket. Előtt javasolt minden üzenetet részletesen, a közös szükséges, ezek az üzenetek az üzenetfejlécek ismerteti.

### <a name="required-message-headers"></a>Szükséges üzenetfejlécek

A következő fejlécek az összes ügyfél által kezdeményezett üzenet szükségesek.

| Fejléc | Érték |
|----|----|
| Útvonal | Az üzenet megadott elérési útjára ebben a dokumentumban |
| X-kérelemazonosító | A "no-vonal" formátum UUID |
| X-időbélyeg | Ügyfél UTC óra időbélyeg ISO 8601 formátumban |

#### <a name="x-requestid-header"></a>X-kérelemazonosító fejléc

Ügyfél által kezdeményezett kérelmekre egyedileg azonosítja a *X-kérelemazonosító* üzenetfejléc. Ezt a fejlécet minden ügyfél által kezdeményezett üzenetek szükség. A *X-kérelemazonosító* állomásfejléc-érték lehet UUID "no-vonal" formában, például *123e4567e89b12d3a456426655440000*. Az *nem* kanonikus formájában kell *123e4567-e89b-12d3-a456-426655440000*. Nélkül kér egy *X-kérelemazonosító* fejléc, vagy használja a megfelelő formátumú UUID-k oka a szolgáltatás leáll, a WebSocket-kapcsolat fejléc értéke.

#### <a name="x-timestamp-header"></a>X-időbélyeg fejléc

Egy ügyfélalkalmazás beszéd szolgáltatásnak küldött üzeneteket *kell* közé tartozik egy *X-időbélyeg* fejléc. A fejléc értéke az idő, amikor az ügyfél elküldi az üzenetet. Nem kér egy *X-időbélyeg* fejléc vagy a fejléc értéke nem megfelelő formátumú miatt a szolgáltatás leáll, a WebSocket-kapcsolat.

A *X-időbélyeg* állomásfejléc-érték formátumban kell megadni: a "yyyy"-"MM"-"dd'T" HH ":"mm":" ss "." fffffffZ "hol"fffffff"található másodperc töredéke alatt. Például "12,5" azt jelenti, hogy "12 + 5/10 másodperc és"12.526"azt jelenti, hogy" 12 plusz 526/1000 másodperc". Ezt a formátumot megfelel [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) és eltérően a szabványos HTTP-n *dátum* fejléc, az ezredmásodperces felbontást biztosít. Ügyfélalkalmazások előfordulhat, hogy kerekítése a legközelebbi ezredmásodperces az időbélyegek. Győződjön meg arról, hogy az eszköz órája pontosan nyomon követi az idő használatával kell ügyfélalkalmazások egy [Network Time Protocol (NTP) kiszolgáló](https://en.wikipedia.org/wiki/Network_Time_Protocol).

### <a name="message-speechconfig"></a>üzenet `speech.config`

Beszéd szolgáltatásnak kell tudni, hogy az alkalmazás számára adja meg a legjobb beszédfelismerés jellemzőit. A szükséges jellemzőket adatokat tartalmaz információkat az eszköz és az operációs rendszer, amely az alkalmazás megoldásaira épül. Ezt az információt megadnia a `speech.config` üzenet.

Ügyfelek *kell* küldése egy `speech.config` azokat a csatlakozást Beszéd szolgáltatáshoz, és azok küldeni a bármely után azonnal üzenet `audio` üzeneteket. Kell egy `speech.config` kapcsolatonként csak egyszer üzenet.

| Mező | Leírás |
|----|----|
| WebSocket üzenet kódolása | Szöveg |
| Törzs | A tartalom, JSON struktúrában |

#### <a name="required-message-headers"></a>Szükséges üzenetfejlécek

| Fejléc neve | Érték |
|----|----|
| Útvonal | `speech.config` |
| X-időbélyeg | Ügyfél UTC óra időbélyeg ISO 8601 formátumban |
| Content-Type | az Application/json; charset = utf-8 |

Csakúgy, mint a beszédfelismerés szolgáltatás protokoll ügyfél által kezdeményezett összes üzenetet a `speech.config` üzenet *kell* közé tartozik egy *X-időbélyeg* fejlécet tartalmazta, amely rögzíti az ügyfél UTC idő, amikor az üzenet a szolgáltatásnak. A `speech.config` üzenet *nem* szükséges egy *X-kérelemazonosító* fejléc, mert ez az üzenet nincs társítva egy adott beszéd kérelmet.

#### <a name="message-payload"></a>Üzenetadatokat
A hasznos a `speech.config` üzenet az alkalmazással kapcsolatos adatokat tartalmazó JSON struktúrában. A következő példa bemutatja, ezt az információt. Ügyfél és eszköz környezeti információkat tartalmazza a *környezetben* eleme a JSON struktúrában. 

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

##### <a name="system-element"></a>Rendszer elem

A system.version eleme a `speech.config` üzenet tartalmazza a beszédfelismerés SDK szoftvert a ügyfélalkalmazás vagy az eszköz által használt verzióját. A képernyőn értéke *major.minor.build.branch*. Akkor kihagyhatja a *fiókirodai* összetevő, ha nem alkalmazható.

##### <a name="os-element"></a>Az operációs rendszer elem

| Mező | Leírás | Használat |
|-|-|-|
| os.platform | Az operációs rendszer platform, például az alkalmazást futtató, a Windows, Android, iOS vagy Linux |Szükséges |
| os.Name | Az operációs rendszer termék neve, például a Debian vagy a Windows 10 | Szükséges |
| os.Version | A képernyőn az operációs rendszer verziójának *major.minor.build.branch* | Szükséges |

##### <a name="device-element"></a>Eszköz elem

| Mező | Leírás | Használat |
|-|-|-|
| Device.Manufacturer | Az eszköz hardver gyártója | Szükséges |
| Device.Model | Az eszköz típusa | Szükséges |
| Device.Version | Az eszköz szoftverének verziójával, az eszköz gyártója által biztosított. Ez az érték határozza meg az eszközre, amelyet a gyártó által nyomon követhetők egy verziója. | Szükséges |

### <a name="message-audio"></a>üzenet `audio`

Beszédfelismerő ügyfélalkalmazások küldje el hang beszéd szolgáltatás hang adattömbök sorozata formátumúra konvertálásakor a hangadatfolyam. Hang az egyes adattömbök hordoz magában, ha a szóbeli hang, amely a szolgáltatás által kért kell szegmense. Az egyetlen hang adattömb maximális mérete 8192 bájt. Hangadatfolyam üzenetek *bináris WebSocket üzenetek*.

Az ügyfelek használják a `audio` üzenetet egy hang adatrészlet a szolgáltatást. Ügyfelek olvasni az adattömböket a mikrofon hang, és ezek adattömbök elküldik a beszédfelismerés szolgáltatás vonatkozó. Az első `audio` üzenet egy megfelelően formázott fejléc megfelelően megadja, hogy a hang megfelel-e a szolgáltatás támogatja a kódolási formátumok egyikét kell tartalmaznia. További `audio` üzenetek csak a bináris hang adatfolyam a mikrofon adatsorból beolvasott adatok tartalmazhatnak.

Az ügyfelek nem kötelezően el tudja küldeni egy `audio` tartalmazó üzenet, amely egy nulla hosszúságú törzsében. Ez az üzenet közli, hogy az ügyfél ismeri, hogy a felhasználó leállította a beszéd, a utterance befejeződött, és a mikrofon ki van kapcsolva a szolgáltatást.

Beszéd szolgáltatás használja, az első `audio` üzenetet, amely tartalmazza egy egyedi azonosítóját, hogy jelezze az egy új kérés-válasz ciklus az elindítása vagy *kapcsolja*. Miután megkapta a szolgáltatás egy `audio` üzenet egy új kérelmet azonosítóval társított bármely korábbi kapcsolja aszinkron vagy el nem küldött üzeneteket elveti.

| Mező | Leírás |
|-------------|----------------|
| WebSocket üzenet kódolása | Bináris |
| Törzs | A bináris adatok a hang adatrészlet. Maximális mérete 8192 bájt. |

#### <a name="required-message-headers"></a>Szükséges üzenetfejlécek

A következő fejlécek az összes szükségesek `audio` üzeneteket.

| Fejléc         |  Érték     |
| ------------- | ---------------- |
| Útvonal | `audio` |
| X-kérelemazonosító | A "no-vonal" formátum UUID |
| X-időbélyeg | Ügyfél UTC óra időbélyeg ISO 8601 formátumban |
| Content-Type | A hang tartalomtípusa. A típusnak kell lennie, vagy *hang/x-wav* (PCM) vagy *hang/szintetikus* (szintetikus). |

#### <a name="supported-audio-encodings"></a>Támogatott hang kódolások

Ez a szakasz ismerteti a hang kodekek beszéd szolgáltatás támogatja.

##### <a name="pcm"></a>PCM

Beszéd szolgáltatás fogadja a tömörítetlen pulse kód modulációs hang. A szolgáltatás hang küldése [WAV](https://en.wikipedia.org/wiki/WAV) formájában, így az első hang darabos *kell* tartalmaznak egy érvényes [erőforrás Interchange fájlformátum](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format) (RIFF) fejléc. Ha az ügyfél kezdeményez egy előre egy hang adattömbök, amelyet a *nem* egy érvényes RIFF fejlécet tartalmaz, a szolgáltatás visszautasítja a kérelmet, és a WebSocket-kapcsolat befejeződik.

PCM hang *kell* 16 kHz mintát és egy csatorna / 16 bites kell lekérdező (*riff-16khz-16 bites-monó-pcm*). Beszéd szolgáltatás sztereó hang adatfolyam nem támogatja, és a megadott átviteli sebességet, mintavételi gyakoriság vagy csatornák száma nem használó hang adatfolyamok elutasítja.

##### <a name="opus"></a>Opus

Opus nyitva, díjmentes, magas sokoldalú hang kodek. Beszéd szolgáltatás Opus támogatja állandó átviteli sebességgel `32000` vagy `16000`. Csak a `OGG` Opus tárolója által megadott jelenleg támogatott a `audio/ogg` MIME-típus.

Az Opus használ, módosítsa a [JavaScript minta](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101) , és módosítsa a `RecognizerSetup` metódus való visszatéréshez.

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

#### <a name="detect-end-of-speech"></a>Beszéd végét észlelése

Az emberek explicit módon jelezni, amikor azok végzett beszélünk. Bármely alkalmazás, amely fogadja a beszédfelismerés bemeneti egy hangadatfolyam beszéd végén kezelésére két lehetősége van: szolgáltatás vége a beszédfelismerés észlelési és az ügyfél vége a beszédfelismerés észlelése. Ezek két választási lehetőség, a szolgáltatás vége a beszédfelismerés észlelési általában jobb felhasználói élményt biztosít.

##### <a name="service-end-of-speech-detection"></a>Szolgáltatás vége a beszédfelismerés észlelése

Az ideális beavatkozás nélküli beszéd élmény létrehozásához alkalmazások lehetővé a szolgáltatás észleli, ha a felhasználó befejezte, és beszéljen. Ügyfelek, a mikrofon hang küldeni *hang* adattömböket, amíg a szolgáltatás észleli a csend, és küld vissza a `speech.endDetected` üzenet.

##### <a name="client-end-of-speech-detection"></a>Ügyfél vége a beszédfelismerés észlelése

Ügyfélalkalmazások, amelyek lehetővé teszik a felhasználót, hogy jelezni valamilyen módon beszéd végén is biztosíthat a szolgáltatás, amely jel. Egy ügyfélalkalmazás Előfordulhat például, a "Stop" vagy "Némító" gombra, hogy a felhasználó nyomja le az. Jelezze end-az-beszéd átalakítás, ügyfélalkalmazások küldése egy *hang* adatrészlet üzenet nulla hosszúságú szervezethez. Beszéd szolgáltatás a bejövő hangadatfolyam végén üzenetként értelmezi.

### <a name="message-telemetry"></a>üzenet `telemetry`

Ügyfélalkalmazások *kell* minden kapcsolja végén tudomásul beszéd szolgáltatás telemetriai adatainak a kapcsolja elküldésével. Kapcsolja-a befejezési visszaigazolás lehetővé teszi a beszédfelismerés szolgáltatás győződjön meg arról, hogy minden szükséges a kérés- és annak megfelelően kapott üzenetet az ügyfél által. Kapcsolja-a befejezési visszaigazolás is lehetővé teszi a beszédfelismerés szolgáltatás győződjön meg arról, hogy az ügyfélalkalmazások számára elvárt módon működnek. Ezekkel az információkkal már hasznos információt, ha segítségre van szüksége a beszéd-kompatibilis alkalmazás hibaelhárítása.

Ügyfelek elküldésével kell megerősíti a kapcsolja végén egy `telemetry` üzenet fogadását követően a `turn.end` üzenet. Ügyfelek arányosan megerősíti a `turn.end` lehető legrövidebb időn belül. Ha egy ügyfél-alkalmazás nem kapcsolja vége tudomásul, a beszédfelismerés szolgáltatás előfordulhat, hogy megszakítja a kapcsolatot hiba. Az ügyfelek csak az egyiket kell küldenie `telemetry` üzenetet minden egyes kérés és válasz által azonosított a *X-kérelemazonosító* érték.

| Mező | Leírás |
| ------------- | ---------------- |
| WebSocket üzenet kódolása | Szöveg |
| Útvonal | `telemetry` |
| X-időbélyeg | Ügyfél UTC óra időbélyeg ISO 8601 formátumban |
| Content-Type | `application/json` |
| Törzs | A JSON struktúrában, amelynek a kapcsolja ügyfél adatait tartalmazza |

A séma törzséhez a `telemetry` üzenet van megadva a [Telemetriai séma](#telemetry-schema) szakasz.

#### <a name="telemetry-for-interrupted-connections"></a>Megszakított kapcsolatok telemetriai adat

Ha bármilyen okból egy kapcsolja során nem sikerül a hálózati kapcsolat és az ügyfél *nem* kap egy `turn.end` üzenet a szolgáltatásból, az ügyfél elküldi a `telemetry` üzenet. Ez az üzenet a következő alkalommal, az ügyfél a szolgáltatás kapcsolatot hoz létre a sikertelen kérelmek írja le. Ügyfeleknek nem kell kapcsolódni a küldése azonnal megkísérli a `telemetry` üzenet. Az üzenet előfordulhat, hogy az ügyfél pufferelt, és a jövőbeni felhasználó azt kérte kapcsolaton keresztül. A `telemetry` üzenet megadása a sikertelen kérelmek *kell* használja a *X-kérelemazonosító* a sikertelen kérelmek közötti értéket. Ez lehet küldeni a szolgáltatáshoz, amint létrejön a kapcsolat, váró elküldeni vagy fogadni a további üzenetek nélkül.

## <a name="service-originated-messages"></a>Szolgáltatás-szolgáltatásoktól üzenetek

Ez a szakasz ismerteti a beszédfelismerés szolgáltatás származnak, és az ügyfélnek küldött üzeneteket. Beszéd szolgáltatás megőrzi a beállításjegyzékbeli ügyfél képességeit, és állít elő, az üzenetek által igényelt minden ügyfél, ezért nem minden ügyfél az itt ismertetett üzenetek fogadására. Értékének által hivatkozott üzenetek kivonatosan mutatja a *elérési* fejléc. Például hivatkozunk WebSocket SMS-ben a *elérési* érték `speech.hypothesis` speech.hypothesis üzenetben.

### <a name="message-speechstartdetected"></a>üzenet `speech.startDetected`

A `speech.startDetected` az üzenet azt jelzi, hogy a beszédfelismerés szolgáltatás a hangadatfolyam beszéd észleli.

| Mező | Leírás |
| ------------- | ---------------- |
| WebSocket üzenet kódolása | Szöveg |
| Útvonal | `speech.startDetected` |
| Content-Type | az Application/json; charset = utf-8 |
| Törzs | A beszédfelismerés kezdetét észlelésekor feltételek kapcsolatos információkat tartalmazó JSON struktúrában. A *eltolás* Ez a struktúra mezője eltolását (100 nanoszekundumos egységekben) Ha beszéd észlelt a hangadatfolyam, az adatfolyam kezdete viszonyítva. |

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

Alatt beszédfelismerés beszéd szolgáltatás rendszeres időközönként hoz létre a szavakkal kapcsolatos feltételezéseket ismeri fel a szolgáltatás. Beszéd szolgáltatás elküldi ezeket feltételezéseket az ügyfél körülbelül minden 300 ezredmásodperc. A `speech.hypothesis` megfelelő *csak* a beszédfelismerés felhasználói élmény fokozása. A függőség tartalmát vagy a szöveg pontossága nem szükséges, ha ezek az üzenetek a.

 A `speech.hypothesis` üzenet ezeket az ügyfeleket, amelyek bizonyos szöveg megjelenítési funkció, és visszajelzést szeretne biztosítani közel valós idejű, a folyamatban lévő felismerés van, és beszéljen aki alkalmazandó.

| Mező | Leírás |
| ------------- | ---------------- |
| WebSocket üzenet kódolása | Szöveg |
| Útvonal | `speech.hypothesis` |
| X-kérelemazonosító | A "no-vonal" formátum UUID |
| Content-Type | application/json |
| Törzs | A beszédfelismerés alapul, JSON struktúrában |

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

A *eltolás* elem eltolását (100 nanoszekundumos egységekben) Ha a kifejezés ismerte fel, a hangadatfolyam kezdetét viszonyítva.

A *időtartam* elem a beszédfelismerés kifejezés (100 nanoszekundumos egységekben) időtartamát határozza meg.

Az ügyfelek nem tehetik a gyakoriság, időzítési vagy szöveg szerepel a beszédfelismerés feltevése vagy a konzisztencia bármely két beszéd feltételezéseket szöveg bármely feltételezéseket. A feltételezéseket szolgáltatási írjanak elő folyamatba csak pillanatképek. Nem jelentik a stabil felhalmozódásához írjanak elő. Például egy első beszéd alapul előfordulhat, hogy a "finom visszatöltött" szavak, valamint a második alapul előfordulhat, hogy tartalmazhatja szavak "vicces található." Beszéd szolgáltatás nem hajtható végre (például a kis-és nagybetűk, a írásjelek) bármely utófeldolgozás a szöveget a beszédfelismerés alapul.

### <a name="message-speechphrase"></a>üzenet `speech.phrase`

Ha beszéd szolgáltatás határozza meg, hogy arról, hogy rendelkezik-e elegendő információt, amely nem változik, a szolgáltatás előállított felismerési eredmény egy `speech.phrase` üzenet. Beszéd szolgáltatás ezekkel az eredményekkel hoz létre, azt észleli, hogy a felhasználó befejezte egy mondat vagy kifejezés után.

| Mező | Leírás |
| ------------- | ---------------- |
| WebSocket üzenet kódolása | Szöveg |
| Útvonal | `speech.phrase` |
| Content-Type | application/json |
| Törzs | A beszédfelismerés kifejezés JSON struktúrában |

A beszédfelismerés kifejezés JSON-séma a következő mezőket tartalmazza: `RecognitionStatus`, `DisplayText`, `Offset`, és `Duration`. Ezek a mezők kapcsolatos további információkért lásd: [írjanak elő válaszok](../concepts.md#transcription-responses).

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

A `speech.endDetected` üzenetet határozza meg, hogy az ügyfélalkalmazás álljon le, a szolgáltatás hang adatfolyam.

| Mező | Leírás |
| ------------- | ---------------- |
| WebSocket üzenet kódolása | Szöveg |
| Útvonal | `speech.endDetected` |
| Törzs | A beszédfelismerés végén észlelésekor eltolását tartalmazó JSON struktúrában. Az eltolás felismerés használt hang kezdetétől a 100 nanoszekundumos egységek eltolás jelzi. |
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

A *eltolás* elem eltolását (100 nanoszekundumos egységekben) Ha a kifejezés ismerte fel, a hangadatfolyam kezdetét viszonyítva.

### <a name="message-turnstart"></a>üzenet `turn.start`

A `turn.start` jelzi a egy kapcsolja a szolgáltatás szempontjából. A `turn.start` üzenet mindig az első válasz üzenet minden kérelemhez. Ha nem érkezik egy `turn.start` üzenet, azt feltételezik, hogy a szolgáltatás kapcsolati állapota érvénytelen.

| Mező | Leírás |
| ------------- | ---------------- |
| WebSocket üzenet kódolása | Szöveg |
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

A választörzs a `turn.start` üzenet elején a kapcsolja a környezet tartalmazó JSON struktúrában. A *környezetben* elem tartalmazza-e egy *serviceTag* tulajdonság. Ez a tulajdonság meghatározza a címke, amely a szolgáltatás a kapcsolja van társítva. Ez az érték a Microsoft által használható, ha segítségre van szüksége az alkalmazás hibáinak elhárítása.

### <a name="message-turnend"></a>üzenet `turn.end`

A `turn.end` egy kapcsolja végén jelzi a szolgáltatás szempontjából. A `turn.end` üzenet mindig a legutóbbi válaszüzenetet, megjelenik a kérelmet. Az ügyfelek az üzenet célba érkezése használható tisztítás tevékenységek és az inaktív állapotú való váltás jel. Ha nem érkezik egy `turn.end` üzenet, azt feltételezik, hogy a szolgáltatás kapcsolati állapota érvénytelen. Ezekben az esetekben zárja be a szolgáltatás a meglévő kapcsolatot, és csatlakozzon újra.

| Mező | Leírás |
| ------------- | ---------------- |
| WebSocket üzenet kódolása | Szöveg |
| Útvonal | `turn.end` |
| Törzs | None |

#### <a name="sample-message"></a>Mintaüzenet

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>Telemetria séma

A választörzs a *telemetriai* üzenet, amely egy kapcsolja vagy kapcsolódási kísérlet ügyfél adatait tartalmazza JSON struktúrában. A struktúra rögzíti, amikor az ügyfél események következnek be ügyfél időbélyegeket tevődik össze. Minden időbélyegzőjét "Fejléc X-időbélyeg." című szakaszban leírt módon ISO 8601 formátumban kell lennie *Telemetria* üzeneteket, amelyek nem adnak meg a kötelező mezőket a JSON-struktúrát, vagy a megfelelő stamp időformátum nem használó előfordulhat, hogy a szolgáltatás a kapcsolat az ügyfélnek. Ügyfelek *kell* adjon érvényes értéket az összes kötelező mezőt. Ügyfelek *kell* választható mezőket, amikor a megfelelő értéket ad meg. A mintában az itt látható értékek Szemléltetés céljából.

Telemetria séma a következő részre oszlik: fogadott üzenet időbélyegeket és metrikákat. A formátum és minden egyes részben használatát a következő szakaszokban van megadva.

### <a name="received-message-time-stamps"></a>A beérkezett üzenet időbélyegeket

Ügyfelek idő a fogadást értékek megérkezése után sikeresen csatlakoznak-e a szolgáltatás összes üzenetek tartalmaznia kell. Ezeket az értékeket kell rögzítenie az idő során az ügyfél *kapott* minden üzenetet a hálózatról. Az érték nem rögzíteni kell a más bármikor. Például az ügyfél nem rögzíteni kell a idő amikor azt *intézkedni* az üzenetben. A fogadott üzenet időbélyegeket megadott bájttömb *name: value* párokat. A pár nevét adja meg a *elérési* érték az üzenet. A pár értéke határozza meg az ügyfél ideje, ha az üzenet érkezett. Vagy, a megadott névvel egynél több üzenet érkezett, ha a két értéke azt jelzi, ha azokat az üzeneteket érkezett időbélyegeket tömbjét.

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

Ügyfelek *kell* azokat az üzeneteket az időbélyegek belefoglalja a JSON-törzsére a szolgáltatás által küldött összes üzenet átvételét. Ha egy ügyfél nem sikerült az üzenet célba érkezése megerősíti, a szolgáltatás előfordulhat, hogy megszakítja a kapcsolatot.

### <a name="metrics"></a>Mérőszámok

Ügyfelek tartalmaznia kell egy kérelmet élettartama során előforduló eseményeket adatokat. A következő mérőszámokat támogatottak: `Connection`, `Microphone`, és `ListeningTrigger`.

### <a name="metric-connection"></a>A metrika `Connection`

A `Connection` metrika határozza meg a kapcsolódási kísérletek által az ügyfél adatait. A metrika időbélyeggel kell tartalmazniuk a WebSocket-kapcsolat indított el és fejeződött be. A `Connection` kell adni a metrikát *csak az első kapcsolódási kapcsolja a*. Ennek a későbbi tartalmazza ezt az információt nem szükségesek. Ha egy ügyfél több kapcsolódási kísérletek kapcsolatot létesít, mielőtt információ *összes* a kapcsolódási kísérletek tartalmaznia kell. További információkért lásd: [kapcsolati hibák telemetriai adatainak](#connection-failure-telemetry).

| Mező | Leírás | Használat |
| ----- | ----------- | ----- |
| Name (Név) | `Connection` | Szükséges |
| Azonosító | A kapcsolat-értéket, amely már használta a *X-ConnectionId* a kapcsolódási kérelem fejléc | Szükséges |
| Indítás | Az idő, amikor az ügyfél küldött-e a kapcsolódási kérelem | Szükséges |
| Befejezés | Az idő az ügyfelet, hogy a kapcsolat sikeresen létrejött-e értesítés fogadásakor, hiba esetben elutasították, visszautasította vagy nem sikerült | Szükséges |
| Hiba | A következő hiba történt, ha van ilyen leírását. Ha a kapcsolódás sikeres volt, a ügyfelek kell hagyja ki ezt a mezőt. Ez a mező hossza legfeljebb 50 karakter hosszú lehet. | Hibaeseteknél, ellenkező esetben nincs megadva a szükséges |

A hiba leírása legfeljebb 50 karakter lehet, és ideális kell lennie a következő táblázatban szereplő érték. Ha a hibát kiváltó feltétel nem egyezik a következő értékek egyike, ügyfelek használhatják-e a hibát kiváltó feltétel tömör leírása használatával [CamelCasing](https://en.wikipedia.org/wiki/Camel_case) szóköz nélkül. Küldése egy *telemetriai* üzenet megköveteli, hogy a kapcsolat a szolgáltatással, ezért csak átmeneti vagy ideiglenes hibaállapotok is kerülnek be a *telemetriai* üzenet. A hibák *véglegesen* blokk a szolgáltatáshoz való kapcsolódás ügyfelet, hogy az ügyfél az összes üzenetet küld a szolgáltatást, beleértve a *telemetriai* üzenetek.

| Hiba | Használat |
| ----- | ----- |
| DNSfailure | Az ügyfél nem tudott csatlakozni a szolgáltatáshoz, a hálózati verem a DNS-hibája miatt. |
| NoNetwork | Az ügyfél megpróbált kapcsolatot, de a hálózati verem jelentette, hogy nincsenek fizikai hálózati volt elérhető. |
| NoAuthorization | Az ügyfél kapcsolata nem sikerült a kapcsolat egy engedélyezési jogkivonatot megszerzésére tett kísérlet során. |
| NoResources | Az ügyfél elfogyott néhány helyi erőforrás (például memóriája) kapcsolat létrehozása közben. |
| Tiltott | Az ügyfél nem tudott kapcsolódni a szolgáltatáshoz, mert a szolgáltatás által visszaadott HTTP `403 Forbidden` a WebSocket-frissítési kérelem az állapotkód. |
| Nem engedélyezett | Az ügyfél nem tudott kapcsolódni a szolgáltatáshoz, mert a szolgáltatás által visszaadott HTTP `401 Unauthorized` a WebSocket-frissítési kérelem az állapotkód. |
| Hibás kérés | Az ügyfél nem tudott kapcsolódni a szolgáltatáshoz, mert a szolgáltatás által visszaadott HTTP `400 Bad Request` a WebSocket-frissítési kérelem az állapotkód. |
| ServerUnavailable | Az ügyfél nem tudott kapcsolódni a szolgáltatáshoz, mert a szolgáltatás által visszaadott HTTP `503 Server Unavailable` a WebSocket-frissítési kérelem az állapotkód. |
| ServerError | Az ügyfél nem tudott kapcsolódni a szolgáltatáshoz, mert a szolgáltatás által visszaadott egy `HTTP 500` állapotkód belső hiba történt a WebSocket-frissítési kérelem. |
| Időtúllépés | Az ügyfél-kapcsolódási kérelem túllépte az időkorlátot a szolgáltatás válasza nélkül. A *End* mező tartalmazza az idő, amikor az ügyfél túllépte az időkorlátot, és várakozik a kapcsolatot. |
| ClientError | Az ügyfél néhány belső ügyfél hibája miatt megszakította a kapcsolatot. | 

### <a name="metric-microphone"></a>A metrika `Microphone`

A `Microphone` metrika összes beszéd viszont szükség. Ez a metrika az ügyfélen, amelyben hangbemenetet aktívan használja a beszédfelismerés kérelmek időt méri.

Az alábbi példák útmutatóul felvételhez használt *Start* time értékeinek a `Microphone` metrika az ügyfél alkalmazásban:

* Egy ügyfélalkalmazás megköveteli, hogy a felhasználó kell egy fizikai indítása gombra a mikrofon. A gomb megnyomása után az ügyfélalkalmazás a bemeneti beolvassa a mikrofon, és elküldi beszéd szolgáltatás. A *Start* értékét a `Microphone` metrika gomb megnyomása után idejét rögzíti, amikor a mikrofon inicializálva, és készen áll a információk megadása. A *End* értékét a `Microphone` metrika rögzíti az idő, amikor az ügyfélalkalmazás leállt, a szolgáltatás hang adatfolyam, azt a fogadását követően a `speech.endDetected` üzenetet a szolgáltatástól.

* Egy ügyfélalkalmazás olyan, mely a "mindig" kulcsszó spotter használja. Csak azt követően a kulcsszó spotter észleli szóbeli eseményindító kifejezés nem az ügyfélalkalmazás származó információk gyűjtésére, a a mikrofon és beszéd szolgáltatásnak. A *Start* értékét a `Microphone` metrika rögzíti az idő, amikor a kulcsszó spotter értesítést kap az ügyfél a mikrofon bemenetének elindítására. A *End* értékét a `Microphone` metrika rögzíti az idő, amikor az ügyfélalkalmazás leállt, a szolgáltatás hang adatfolyam, azt a fogadását követően a `speech.endDetected` üzenetet a szolgáltatástól.

* Egy ügyfélalkalmazás hozzáfér-e egy állandó hangadatfolyam és csend/beszéd észlelési végez az adott hangadatfolyam egy *beszéd feltételészlelési modul*. A *Start* értékét a `Microphone` metrika idejét rögzíti, amikor a *beszéd feltételészlelési modul* értesítést kap az ügyfél a hangadatfolyam bemeneti indíthatja. A *End* értékét a `Microphone` metrika rögzíti az idő, amikor az ügyfélalkalmazás leállt, a szolgáltatás hang adatfolyam, azt a fogadását követően a `speech.endDetected` üzenetet a szolgáltatástól.

* Egy ügyfélalkalmazás a második kapcsolja több kapcsolja kérés feldolgozása folyamatban van, és a mikrofon bemeneti összegyűjtéséhez a második kapcsolja bekapcsolása szolgáltatás válaszüzenetet arról tájékoztatja. A *Start* értékét a `Microphone` metrika rögzíti a idő, amikor az ügyfélalkalmazás lehetővé teszi, hogy a mikrofon, és elindítja a hang forrás bemeneti használatával. A *End* értékét a `Microphone` metrika rögzíti az idő, amikor az ügyfélalkalmazás leállt, a szolgáltatás hang adatfolyam, azt a fogadását követően a `speech.endDetected` üzenetet a szolgáltatástól.

A *End* idő értékét a `Microphone` metrika az idő, amikor az ügyfélalkalmazás leállt hangbemenetet streaming rögzíti. A legtöbb esetben ez az esemény érkezett az ügyfél hamarosan után kerül sor a `speech.endDetected` üzenetet a szolgáltatástól. Ügyfélalkalmazások előfordulhat, hogy ellenőrizze, hogy azok még megfelelően megfelelő a protokoll biztosításával, hogy a *End* idő értékét a `Microphone` metrika fogadását idő értéke legkésőbb akkor fordul elő a `speech.endDetected` üzenet. És mivel általában egy kapcsolja az és egy másik kapcsolja kezdetét késleltetés, az ügyfelek ellenőrzik előfordulhat, hogy protokoll megfelelési, biztosítva, hogy a *Start* az ideje a `Microphone` metrika bármely ezt követő Turn megfelelően idejét rögzíti, amikor az ügyfél *lépések* a mikrofon hang bemeneti adatfolyam a szolgáltatás használatával.

| Mező | Leírás | Használat |
| ----- | ----------- | ----- |
| Name (Név) | Mikrofon | Szükséges |
| Indítás | Az ügyfél a mikrofon vagy más hangadatfolyam hangbemenetet használatának és egy eseményindító kapott a kulcsszó spotter | Szükséges |
| Befejezés | Az idő, amikor az ügyfél leállt, a mikrofon, illetve a hangfelvételen hallható adatfolyam használata | Szükséges |
| Hiba | A következő hiba történt, ha van ilyen leírását. Ha a mikrofon műveleteket sikeres, az ügyfelek el kell hagynia ebben a mezőben. Ez a mező hossza legfeljebb 50 karakter hosszú lehet. | Hibaeseteknél, ellenkező esetben nincs megadva a szükséges |

### <a name="metric-listeningtrigger"></a>A metrika `ListeningTrigger`
A `ListeningTrigger` metrika méri az idő, amikor a felhasználó végrehajtja a bemeneti kezdeményezett művelet. A `ListeningTrigger` metrika nem kötelező, de ez a metrika biztosít az ügyfelek hosszúan ehhez.

Használja az alábbi példák útmutatóul a rögzítés *Start* és *End* time értékeinek a `ListeningTrigger` metrika az ügyfél-alkalmazásban.

* Egy ügyfélalkalmazás megköveteli, hogy a felhasználó kell egy fizikai indítása gombra a mikrofon. A *Start* érték, ez a mérőszám a gomb leküldéses rögzíti. A *End* érték rögzíti az idő, ha a gomb leküldéses befejeződik.

* Egy ügyfélalkalmazás olyan, mely a "mindig" kulcsszó spotter használja. A kulcsszó után spotter észleli szóbeli eseményindító kifejezés, az ügyfélalkalmazás a bemeneti beolvassa a mikrofon, és elküldi beszéd szolgáltatás. A *Start* érték, ez a mérőszám a időpontban, amikor a kulcsszó spotter az eseményindító kifejezést, majd észlelt hang rögzíti. A *End* érték amikor volt a megadott eseményindító-kifejezés az utolsó szó a felhasználó által felolvasott idejét rögzíti.

* Egy ügyfélalkalmazás hozzáfér-e egy állandó hangadatfolyam és csend/beszéd észlelési végez az adott hangadatfolyam egy *beszéd feltételészlelési modul*. A *Start* érték, ez a metrika idejét rögzíti, amelyek a *beszéd feltételészlelési modul* kapott, amely majd érzékelte beszéd hang. A *End* érték idejét rögzíti, amikor a *beszéd feltételészlelési modul* beszéd észlelte.

* Egy ügyfélalkalmazás a második kapcsolja több kapcsolja kérés feldolgozása folyamatban van, és a mikrofon bemeneti összegyűjtéséhez a második kapcsolja bekapcsolása szolgáltatás válaszüzenetet arról tájékoztatja. Az ügyfélalkalmazás kell *nem* közé tartozik egy `ListeningTrigger` a TURN metrika.

| Mező | Leírás | Használat |
| ----- | ----------- | ----- |
| Name (Név) | ListeningTrigger | Optional |
| Indítás | A indításakor mikor az ügyfél figyelő eseményindító | Szükséges |
| Befejezés | Az idő, amikor az ügyfél figyelő eseményindító befejeződött | Szükséges |
| Hiba | A következő hiba történt, ha van ilyen leírását. Ha az eseményindító művelet sikeres volt, az ügyfelek el kell hagynia ezt a mezőt. Ez a mező hossza legfeljebb 50 karakter hosszú lehet. | Hibaeseteknél, ellenkező esetben nincs megadva a szükséges |

#### <a name="sample-message"></a>Mintaüzenet

A következő példában ReceivedMessages és a metrikák alkotórészek telemetriai üzenet:

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

Ez a szakasz ismerteti a hibaüzeneteket és feltételeket, amelyek az alkalmazás kell kezelje típusú.

### <a name="http-status-codes"></a>HTTP-állapotkódok

A WebSocket-frissítési kérelem során beszéd szolgáltatás előfordulhat, hogy vissza bármelyik a szabványos HTTP-állapotkódok, például a `400 Bad Request`stb. Az alkalmazás megfelelően kell hibaüzenet feltételekben kezeli.

#### <a name="authorization-errors"></a>Engedélyezési hibák

Nem megfelelő engedély biztosítása a WebSocket frissítéskor beszéd szolgáltatás adja vissza egy HTTP esetén `403 Forbidden` állapotkódot. A feltételek, amelyek aktiválhatják a hibakóddal között a következők:

* Hiányzó *engedélyezési* fejléc

* Érvénytelen engedélyezési jogkivonat

* Lejárt engedélyezési jogkivonat

A `403 Forbidden` hibaüzenet nem beszéd szolgáltatás hibáját jelzik. Ez a hibaüzenet azt jelzi, hogy az ügyfélalkalmazás problémáját.

### <a name="protocol-violation-errors"></a>Protokoll megsértése hibák

Beszéd szolgáltatás bármely ügyféltől protokoll megsértése észlel, ha a szolgáltatás leáll-e a WebSocket-kapcsolat újracsatlakozást követően egy *állapotkód* és *OK* a záráshoz. Ügyfélalkalmazások ezen információk használatával elháríthatja és kijavíthatja a megsértése.

#### <a name="incorrect-message-format"></a>Helytelen üzenetformátuma

Ha egy ügyfél a szöveges vagy bináris üzenet küld a szolgáltatás nem ebben a specifikációban megadott helyes formátumban kódolt, a szolgáltatás bezárja a kapcsolatot a *1007 érvénytelen adattartalom* állapotkódot. 

A szolgáltatás ezzel az állapotkóddal számos okból, az ad vissza, a következő példákban látható módon:

* "Hibás üzenetek formátuma. Bináris üzenetnek érvénytelen fejléc mérete előtag." Az ügyfél, amely egy érvénytelen fejléc mérete előtagja bináris üzenetet küldött.

* "Hibás üzenetek formátuma. Bináris üzenetnek érvénytelen fejléc mérete." Az ügyfél, amely egy érvénytelen fejléc mérete a megadott bináris üzenetet küldött.

* "Hibás üzenetek formátuma. Az UTF-8 dekódolás bináris üzenetfejlécek nem sikerült." Az ügyfél egy bináris UTF-8 nem megfelelően kódolt fejlécek tartalmazó üzenetet küldött.

* "Hibás üzenetek formátuma. SMS-üzenet nem tartalmaz adatokat." Az ügyfél a szervezet adatot nem tartalmazó szöveges üzenetet küldtünk.

* "Hibás üzenetek formátuma. Szöveges üzenet az UTF-8 dekódolás sikertelen." Az ügyfél, amely nem megfelelően kódolt szöveges üzenetet küldtünk UTF-8.

* "Hibás üzenetek formátuma. Szöveges üzenet tartalmazza nincs fejléc elválasztó." Az ügyfél nem tartalmazott fejléc elválasztó, illetve a megfelelő fejléc elválasztó használható szöveges üzenetet küldtünk.

#### <a name="missing-or-empty-headers"></a>Hiányzik vagy üres fejlécek

Ha egy ügyfél egy üzenetet küld, amely nem rendelkezik a szükséges fejlécek *X-kérelemazonosító* vagy *elérési*, a szolgáltatás bezárja a kapcsolatot egy *1002 protokollhiba* állapotkódot. A következő: "fejléc hiányzik vagy üres. Fejléc a(z) {name}.

#### <a name="requestid-values"></a>Kérelemazonosító értékek

Ha egy ügyfél üzenetet küld, amely meghatározza egy *X-kérelemazonosító* fejléc a következő helytelen formátumú, a szolgáltatás megszünteti a kapcsolatot, és adja vissza egy *1002 protokollhiba* állapotát. A következő: "érvénytelen kérést. X-kérelemazonosító fejléc értéke nem volt megadva no-vonal UUID formátumban."

#### <a name="audio-encoding-errors"></a>Hang kódolási hibák

Ha egy ügyfél küld egy hang adatrészlet indít el egy kapcsolja és a hangformátum vagy kódolás nem felelnek meg a kötelező specifikációnak, a szolgáltatás megszünteti a kapcsolatot, és adja vissza egy *1007 érvénytelen adattartalom* állapotkódot. Az üzenet azt jelzi, hogy a kódolási hiba a forrás formátuma.

#### <a name="requestid-reuse"></a>Kérelemazonosító újbóli

Egy kapcsolja befejezése után Ha egy ügyfél küld egy üzenetet, amely a rendszer újból felhasználja az, hogy kapcsolja az azonosítóját, a szolgáltatás megszünteti a kapcsolatot, és adja vissza egy *1002 protokollhiba* állapotkódot. A következő: "érvénytelen kérést. A kérelem azonosítók újbóli nem megengedett."

## <a name="connection-failure-telemetry"></a>Kapcsolódási hibák telemetriai adatainak

A legjobb lehetséges felhasználói élmény biztosítása érdekében ügyfelek tájékoztatnia kell beszéd-szolgáltatását az időbélyegek belül kapcsolatot fontos ellenőrzőpontok használatával a *telemetriai* üzenet. Hasonlóan fontos, hogy az ügyfelek tájékoztatja a szolgáltatás kapcsolatot próbált, de nem sikerült.

Minden kapcsolódási kísérlet sikertelen, az ügyfelek hozzon létre egy *telemetriai* tartalmazó üzenet, amely egy egyedi *X-kérelemazonosító* állomásfejléc-érték. Az ügyfél nem tudott kapcsolatot, mert a *ReceivedMessages* a JSON-törzsére mezője kihagyható. Csak a `Connection` bejegyzést a *metrikák* mező szerepel. Ez a bejegyzés tartalmazza a kezdő és záró időbélyegeket, valamint a hibajelzést kiváltó körülmény történt.

### <a name="connection-retries-in-telemetry"></a>A telemetriai adatok kapcsolatot létesíteni

Ügyfelek kell megkülönböztetni *újrapróbálkozások* a *több kapcsolódási kísérletek* által a kapcsolódási kísérlet kiváltó eseményt. Kapcsolódási kísérletek elvégzett programozott módon bármely felhasználói beavatkozás nélkül újrapróbálkozások. Felhasználói bevitel válaszul hajtják végre több csatlakozási kísérletek több kapcsolódási kísérletek. Az ügyfelek minden egyes felhasználó által indított kapcsolódási kísérlet egy egyedi adjon *X-kérelemazonosító* és *telemetriai* üzenet. Ügyfelek felhasználhatja a *X-kérelemazonosító* programozott újrapróbálkozások számára. Ha több próbálkozás történt egyetlen a rendszer, egyes újrapróbálkozások alkalmazás része egy `Connection` bejegyzést a *telemetriai* üzenet.

Tegyük fel például, a felhasználók elindítsanak egy kapcsolatot az kulcsszó eseményindító beszél, és az első kapcsolódási kísérletre DNS-hibák miatt nem sikerül. Azonban az ügyfél által programozott módon készített próbálkozik sikeres lesz. Az ügyfél a kapcsolat a felhasználó további beavatkozást nem igénylő újra, mert az ügyfél használ-e egyetlen *telemetriai* tartalmazó üzenet, amely több `Connection` megadásával írhatja le a kapcsolati bejegyzések.

Másik példaként tegyük fel, hogy a felhasználók elindítsanak egy kapcsolatot az kulcsszó eseményindító beszél, és a csatlakozási kísérlet meghiúsul három próbálkozások után sem. Az ügyfél majd megkísérel csatlakozni a szolgáltatás leáll, biztosítja, és csupán tájékoztatja a felhasználót, hogy valamilyen probléma merült fel. A felhasználó majd beszél kulcsszó eseményindító újra. Megadott idő tegyük fel, hogy az ügyfél csatlakozik a szolgáltatáshoz. A csatlakozás után a kliensgép azonnal küld egy *telemetriai* az üzenethez, és a szolgáltatás, amely tartalmazza a három `Connection` bejegyzéseit, amelyek a csatlakozási hibák ismertetik. Fogadását követően a `turn.end` üzenetet, az ügyfél elküldi egy másik *telemetriai* a sikeres kapcsolat leíró üzenet.

## <a name="error-message-reference"></a>Hibaüzenet-referenciája

### <a name="http-status-codes"></a>HTTP-állapotkódok

| HTTP-állapotkód | Leírás | Hibaelhárítás |
| - | - | - |
| 400 Hibás kérés | Az ügyfél küldött WebSocket-kapcsolódási kérelem, amely helytelen volt. | Ellenőrizze, hogy a szükséges paramétereket és a HTTP-fejlécek adott-e meg, és, hogy helyesek-e az értékeket. |
| 401 nem engedélyezett | Az ügyfél nem tartalmazta a szükséges engedélyezési információkat. | Ellenőrizze, hogy továbbküldi a *engedélyezési* fejléc a következő a WebSocket-kapcsolat. |
| 403 Tiltott | Az ügyfél engedélyezési adatokat küldött, de érvénytelen volt. | Ellenőrizze, hogy nem küldünk egy lejárt vagy érvénytelen érték a *engedélyezési* fejléc. |
| 404 – Nem található | Az ügyfél megpróbált hozzáférni az egy URL-címe nem támogatott. | Ellenőrizze, hogy a WebSocket-kapcsolat a helyes URL-címet használ. |
| 500 Kiszolgálóhiba | A szolgáltatás belső hibába ütközött, és nem tudta teljesíteni a kérést. | A legtöbb esetben ez a hiba nem átmeneti. Próbálkozzon újra a kéréssel. |
| 503 A szolgáltatás nem érhető el | A szolgáltatás nem érhető el, a kérelem kezelésére. | A legtöbb esetben ez a hiba nem átmeneti. Próbálkozzon újra a kéréssel. |

### <a name="websocket-error-codes"></a>WebSocket hibakódok

| WebSocketsStatus kód | Leírás | Hibaelhárítás |
| - | - | - |
| 1000 normál megszüntetésre | A szolgáltatás lezárta a WebSocket-kapcsolat hiba nélkül. | Ha a WebSocket megszüntetésre volt váratlan, újraolvasásához a dokumentációt, amelyik megértse, hogyan és mikor történjen a szolgáltatás a WebSocket-kapcsolat is leáll. |
| 1002 hálózatiprotokoll-hiba | Az ügyfél nem tudott igazodnia kell a protokollal kapcsolatos követelményeket. | Győződjön meg arról, hogy tudomásul veszi a protokoll dokumentációját, és követelményeivel kapcsolatos egyértelmű. A hiba oka megjelenítéséhez, ha meg van a szabályt sértő protokoll követelmények előző dokumentációjában olvasható. |
| 1007 érvénytelen az adattartalom adatok | Az ügyfél a protokoll-üzenetben küldött adatok érvénytelenek. | Ellenőrizze a hibákat a szolgáltatáshoz az utolsó üzenetet. Hasznos hibákat a korábbi dokumentációjában olvasható. |
| 1011 kiszolgálóhiba | A szolgáltatás belső hibába ütközött, és nem tudta teljesíteni a kérést. | A legtöbb esetben ez a hiba nem átmeneti. Próbálkozzon újra a kéréssel. |

## <a name="related-topics"></a>Kapcsolódó témakörök

Tekintse meg a [JavaScript SDK](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) , amely a beszédfelismerés szolgáltatás WebSocket-alapú protokoll megvalósítását.
