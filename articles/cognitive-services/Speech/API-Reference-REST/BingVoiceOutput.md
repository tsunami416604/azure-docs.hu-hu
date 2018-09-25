---
title: Text to Speech API, Microsoft Speech Service |} A Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Adja meg a valós idejű szöveg-beszéd átalakítás beszédhangot és nyelvek széles a szöveg-beszéd átalakítás API használatával
services: cognitive-services
author: priyaravi20
manager: yanbo
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: priyar
ROBOTS: NOINDEX
ms.openlocfilehash: 664b696d5323177eaac2e7ce7b80a7c81ce601f1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949400"
---
# <a name="bing-text-to-speech-api"></a>A Bing szöveg-beszéd átalakítás API

## <a name="Introduction"></a>Bevezetés

A Bing szöveg-beszéd átalakítás API-t az alkalmazás küldhet HTTP-kérelmekre a felhőbeli kiszolgálón, ahol szöveg azonnal emberi hangzó beszéddé, és adja vissza a hangfájl. Az API-t biztosít különböző beszédhangot és nyelvek széles valós idejű szöveg-beszéd átalakítás számos különböző környezetekben is használható.

## <a name="VoiceSynReq"></a>Hangalapú összefoglaló kérelem

### <a name="Subscription"></a>Engedélyezési jogkivonat

Minden egyes hangalapú összefoglaló kérés JSON webes jogkivonat (JWT) hozzáférési jogkivonat szükséges. A JWT jogkivonat átadott révén a beszéd-kérelem fejléce. A jogkivonat-lejárati idő 10 perc rendelkezik. Előfizetés és érvénytelen JWT-hozzáférési jogkivonatok használt API-kulcsok beszerzésével kapcsolatban lásd: [Cognitive Services-előfizetés](https://azure.microsoft.com/try/cognitive-services/).

Az API-kulcsot a jogkivonat-szolgáltatás kerülnek. Példa:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

A szükséges fejlécadatokat hozzáférési token a következő.

Name (Név)| Formátum | Leírás
----|----|----
OCP-Apim-Subscription-Key | ASCII | Előfizetési kulcs

A jogkivonat-szolgáltatás adja vissza, mint a JWT jogkivonat `text/plain`. A JWT átadott majd egy `Base64 access_token` engedélyeztetési fejléc előtaggal van ellátva a karakterlánc, a beszéd végpontra `Bearer`. Példa:

`Authorization: Bearer [Base64 access_token]`

Az ügyfelek a szöveg-hang transzformációs szolgáltatás eléréséhez a következő végpontot kell használnia:

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>Beszerzett egy hozzáférési jogkivonatot az előfizetési kulccsal végzett korábban leírtaknak megfelelően, amíg ezt a hivatkozást hoz létre egy `403 Forbidden` válaszhoz tartozó hiba.

### <a name="Http"></a>HTTP-fejlécek

Az alábbi táblázat az összefoglaló hangutasítások használt HTTP-fejléceket.

Fejléc |Érték |Megjegyzések
----|----|----
Content-Type | alkalmazás/ssml + xml | A bemeneti tartalom típusa.
X-Microsoft-OutputFormat | **1.** ssml – 16 khz – 16 bites-mono-Szövegfelolvasás <br> **2.** nyers – 16 khz – 16 bites-mono-pcm <br>**3.** hang-16 khz – 16 KB/s-mono-siren <br> **4.** riff – 16 khz – 16 KB/s-mono-siren <br> **5.** riff – 16 khz – 16 bites-mono-pcm <br> **6.** hang-16 khz-128kbitrate-mono-mp3 <br> **7.** hang-16 khz-64kbitrate-mono-mp3 <br> **8.** hang-16 khz-32kbitrate-mono-mp3 | A kimeneti audio formátum.
X-Search-alkalmazásazonosító alapján | Egy GUID Azonosítót (csak hexadecimális, nincs szaggatott vonal) | Egy azonosító, amely egyedileg azonosítja az ügyfélalkalmazás. Ez lehet az alkalmazások a tároló azonosítója. Egy nem áll rendelkezésre, ha az azonosító lehet felhasználó által az alkalmazáshoz.
X-Search-ClientID | Egy GUID Azonosítót (csak hexadecimális, nincs szaggatott vonal) | Egy azonosító, amely egyedileg azonosítja az alkalmazáspéldány minden telepítésnél.
Felhasználói ügynök | Alkalmazásnév | Az alkalmazásnév megadása kötelező, és legfeljebb 255 karakterből állhat.
Engedélyezés | Engedélyezési jogkivonat |  Tekintse meg a <a href="#Subscription">engedélyezési jogkivonat</a> szakaszban.

### <a name="InputParam"></a>Bemeneti paraméterek

A Bing szöveg-beszéd átalakítás API a kérések HTTP POST-hívásokkal. A fejlécek az előző szakaszban vannak megadva. A szervezet Speech összefoglaló Markup Language (SSML) bemeneti szöveg lehet synthesized képviselő tartalmazza. Például a nyelvi beszédfelismerési jellemzőit és nem a beszélő vezérelhetők jelölőnyelvi ismertetését lásd: a [SSML W3C specifikáció](http://www.w3.org/TR/speech-synthesis/).

>[!NOTE]
>A SSML bemenet támogatott maximális mérete 1024 karakter hosszúságú, beleértve az összes címke.

###  <a name="SampleVoiceOR"></a>Példa): hangalapú kimeneti kérelem (

Hangalapú kimeneti kérelem például a következőképpen történik:

```HTTP
POST /synthesize
HTTP/1.1
Host: speech.platform.bing.com

X-Microsoft-OutputFormat: riff-8khz-8bit-mono-mulaw
Content-Type: application/ssml+xml
Host: speech.platform.bing.com
Content-Length: 197
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>Microsoft Bing Voice Output API</voice></speak>
```

## <a name="VoiceOutResponse"></a>Hangalapú kimeneti válasz

A Bing szöveg-beszéd átalakítás API HTTP POST használatával az ügyfélnek hang küldése. Az API-válasz tartalmazza az audio-adatfolyamot, és a kodeket, és a kulcs megegyezik-e a kért kimeneti formátum. A hanganyag adja vissza egy adott kérés esetében nem lehet hosszabb 15 másodperc.

### <a name="SuccessfulRecResponse"></a>Példa: a sikeres összefoglaló válasz

A következő kódot egy JSON-válasz sikeres hangalapú összefoglaló kérelemre példája. A megjegyzéseket, és a kód formázása csak ebben a példában a célokat szolgálnak, és kimaradnak a tényleges válasz.

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>Példa: összefoglaló hiba

Az alábbi példakód bemutatja, egy JSON-válasz voice-összefoglaló lekérdezés sikertelen:

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>Hibaválaszok

Hiba | Leírás
----|----
HTTP/400 – Hibás kérés | Egy kötelező paraméter hiányzik, üres vagy null, vagy átadott vagy egy kötelező vagy választható paraméter értéke érvénytelen. "Érvénytelen" válasz fogadása az egyik oka az, ha egy karakterláncérték, amely hosszabb a megengedettnél továbbítja. A problémás paraméter rövid leírása megtalálható.
HTTP/401 – jogosulatlan hibaüzenetet | A kérelem nem engedélyezett.
HTTP/413 RequestEntityTooLarge  | A SSML bemenet mérete nagyobb, mint a támogatott műveleteket ismerteti.
HTTP/502-es BadGateway | A hálózattal kapcsolatos probléma vagy kiszolgálóoldali probléma van.

Egy példa egy hibaválasz a következőképpen történik:

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>SSML keresztül hangkimenet módosítása

A Microsoft szöveg-hang transzformációs API támogatja SSML 1.0 a W3C [Speech összefoglaló Markup Language (SSML) 1.0-s verzió](http://www.w3.org/TR/2009/REC-speech-synthesis-20090303/). Ez a szakasz példát módosítása bizonyos létrehozott hangkimenet, például a különféle jellemzőit értékelje, írásmódja SSML címkék használatával stb.

1. Szünet hozzáadása

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)'> Welcome to use Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.</voice> </speak>
  ```

2. Nyelvű sebességének módosítása

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody rate="+30.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

3. Kiejtése

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'> <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme></voice> </speak>
  ```

4. Kötet módosítása

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody volume="+20.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

5. Térköz módosítása

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Welcome to use <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

6. Változás prosody eloszlás

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
  ```

> [!NOTE]
> Megjegyzés: a hívásaiból azt kell 8k vagy 16 k wav mezőjében a következő formátumban: **CRC-kód** (CRC-32): 4 bájt (DWORD) az érvényes tartomány 0x00000000 ~ 0xFFFFFFFF; **Hang formátumot jelző**: 4 bájt (DWORD) az érvényes tartomány 0x00000000 ~ 0xFFFFFFFF; **Minták száma**: 4 bájt (DWORD) az érvényes tartomány 0x00000000 ~ 0x7FFFFFFF; **Bináris terjesztett méretének**: 4 bájt (DWORD) az érvényes tartomány 0x00000000 ~ 0x7FFFFFFF; **Bináris törzs**: n bájt.

## <a name="SampleApp"></a>Mintaalkalmazás

A megvalósítás részleteit lásd: a [Visual C# .NET szöveg-hang transzformációs mintaalkalmazás](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs).

## <a name="SupLocales"></a>Támogatott nyelv és hangtípust

A következő táblázat ismerteti az egyes támogatott nyelv és kapcsolódó hangtípusokkal.

Területi beállítás | Nem | A felhasználónév-leképezés
---------|--------|------------
ar Például * | Nő | "A Microsoft Server hang-szöveg, beszédfelismerés, beszédfelismerési (ar-működtek az Adatbázisok, Hoda)"
ar-SA | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (ar-SA, Naayf)"
bg-BG | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (bg-BG, Ivan)"
CA-ES | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ca-ES, HerenaRUS)"
cs-CZ | Férfi | "A Microsoft Server hang-szöveg, beszédfelismerés, beszédfelismerési (cs-CZ, Jakub)"
da-DK | Nő | "A Microsoft Server beszéd szöveg Speech Voice (da-DK, HelleRUS)"
Németország-AT | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (Németország-AT, Michael)"
Németország – CH | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (Németország-CH, Karsten)"
de-DE | Nő | "A Microsoft Server beszéd szöveg Speech Voice (de-DE, Hedda)"
de-DE | Nő | "A Microsoft Server beszéd szöveg Speech Voice (de-DE, HeddaRUS)"
de-DE | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (de-DE, lengyel, Apollo)"
el-GR | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (el-GR, Stefanos)"
en-Ausztrália | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-Ausztrália, Catherine)"
en-Ausztrália | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-Ausztrália, HayleyRUS)"
en-hitelesítésszolgáltató | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-CA, Linda)"
en-hitelesítésszolgáltató | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-CA, HeatherRUS)"
en-GB | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-GB, Susan, Apollo)"
en-GB | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-GB, HazelRUS)"
en-GB | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (en-GB, George, Apollo)"
en-IE | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (en-IE, Sean)"
en-IN | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-IN, Heera, Apollo)"
en-IN | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-IN, PriyaRUS)"
en-IN | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (en-IN, Ravi, Apollo)"
hu-HU | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-US, ZiraRUS)"
hu-HU | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-US, JessaRUS)"
hu-HU | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (en-US, BenjaminRUS)"
es-ES | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (es-ES, Laura, Apollo)"
es-ES | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (es-ES, HelenaRUS)"
es-ES | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (es-ES, Pablo, Apollo)"
es-MX | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (es – MX, HildaRUS)"
es-MX | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (es – MX, Raul, Apollo)"
fi-FI | Nő | "A Microsoft Server beszéd szöveg Speech Voice (fi-FI, HeidiRUS)"
FR-hitelesítésszolgáltató | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-CA, Caroline)"
FR-hitelesítésszolgáltató | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-CA, HarmonieRUS)"
FR-CH | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-CH, Guillaume)"
FR-FR | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-FR, Ágnes, Apollo)"
FR-FR | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-FR, HortenseRUS)"
FR-FR | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-FR, Paul, Apollo)"
He-IL| Férfi| "A Microsoft Server beszéd szöveg Speech Voice (he-IL, Asaf)"
Üdvözöljük – India | Nő | "A Microsoft Server beszéd szöveg Speech Voice (hi-IN, Kalpana, Apollo)"
Üdvözöljük – India | Nő | "A Microsoft Server beszéd szöveg Speech Voice (Üdvözlöm-IN, Kalpana)"
Üdvözöljük – India | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (Üdvözlöm-IN, Hemant)"
hr-HR | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (hr-HR, Matej)"
hu-HU | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (hu-HU, Szabolcs)"
ID-Azonosítóját | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (id-azonosító, Andika)"
it-IT | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (it-IT, Cosimo, Apollo)"
it-IT | Nő | "A Microsoft Server beszéd szöveg Speech Voice (it-IT, LuciaRUS)"
ja-JP | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ja-JP, Ayumi, Apollo)"
ja-JP | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ja-JP, Ichiro, Apollo)"
ja-JP | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ja-JP, HarukaRUS)"
ko-KR | Nő | "A Microsoft Server beszéd szöveg Speech Voice (ko-KR, HeamiRUS)"
MS-saját | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ms-saját, Rizwan)"
nb-NO | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (nb-NO, HuldaRUS)"
NL-NL | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (nl-NL, HannaRUS)"
pl-PL | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (pl-PL, PaulinaRUS)"
pt-BR | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (pt-BR, HeloisaRUS)"
pt-BR | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (pt-BR, Daniel, Apollo)"
PT-PT | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (pt-PT, HeliaRUS)"
ro-RO | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (ro-RO, Andrei)"
ru-RU | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ru-RU, Irina, Apollo)"
ru-RU | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ru-RU, Pavel, Apollo)"
ru-RU | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ru-RU, EkaterinaRUS)"
SK-SK | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (sk-SK, Filip)"
sl SI | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (sl SI, Lado)"
SV-SE | Nő | "A Microsoft Server beszéd szöveg Speech Voice (sv-SE, HedvigRUS)"
TA-India | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (ta-IN, Valluvar)"
cs-EDIK | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (cs-EDIK, Pattara)"
tr-TR | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (tr-TR, SedaRUS)"
vi-VN | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (vi-VN, egy)"
zh-CN | Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-CN, HuihuiRUS)"
zh-CN | Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-CN, Yaoyao, Apollo)"
zh-CN | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (zh-CN, Kangkang, Apollo)"
zh-HK | Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-HK Tracy, Apollo)"
zh-HK | Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-HK TracyRUS)"
zh-HK | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (zh-HK Danny, Apollo)"
zh-TW | Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-TW, Yating, Apollo)"
zh-TW | Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-TW, HanHanRUS)"
zh-TW | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (zh-TW, Zhiwei, Apollo)"
 * ar – Például támogatja a Modern Standard arab (MSA).

> [!NOTE]
> Vegye figyelembe, hogy az előző szolgáltatásnevek **Microsoft Server hang-szöveg, beszédfelismerés, beszédfelismerési (cs-CZ, Vit)** és **Microsoft Server hang-szöveg Speech szövegfelolvasás (en-IE, Shaun)** később elavulttá fog után 3/31/2018. sorrend optimalizálása, a Bing Speech API-funkciókat. Frissítse a kódot a frissített nevekkel.

## <a name="TrouNSupport"></a>Hibaelhárítás és támogatás

Az összes kérdéseket és problémákat a [Bing – beszédszolgáltatás](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService) MSDN-fórum. További információkat tartalmaznak, mint például:

* Egy példa látható a kérelem teljes karakterláncra.
* Ha szükséges, egy sikertelen kéréssel, amely tartalmazza a teljes kimenete jelentkezzen azonosítók.
* Sikertelen kérelmek aránya.
