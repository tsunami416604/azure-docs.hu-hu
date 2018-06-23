---
title: Szöveg-hang transzformációs API Microsoft beszéd szolgáltatás |} Microsoft Docs
description: A szöveg-hang transzformációs API használatával adja meg a különböző hangok és nyelvek valós idejű szöveg-beszéd átalakítás
services: cognitive-services
author: priyaravi20
manager: yanbo
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 03/16/2017
ms.author: priyar
ms.openlocfilehash: 4b633cefa37c11511a8171d5a7f61b03dfaa4466
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347630"
---
# <a name="bing-text-to-speech-api"></a>Bing szöveg-hang transzformációs API

## <a name="Introduction"></a>Bevezetés

A Bing szöveg-hang transzformációs API-t az alkalmazás küldhet HTTP-kérelmek felhő kiszolgálóra, ahol szöveg azonnal az emberi hangzó beszéd synthesized és hangfájl adott vissza. Ez az API számos különböző környezetekben használható arra, hogy számos különböző hangok és nyelvek a valós idejű szöveg-beszéd átalakítás.

## <a name="VoiceSynReq"></a>Hangalámondás összefoglaló kérelem

### <a name="Subscription"></a>Engedélyezési jogkivonat

Minden hang összefoglaló kérelem JSON webes jogkivonat (JWT) hozzáférési jogkivonat szükséges. A JWT jogkivonat áthalad a beszédfelismerés kérelemfejlécet. A token rendelkezik egy lejárati idő 10 perc. További információ az előfizetés, és érvényes JWT jogkivonatot beolvasni használt API-kulcsok beszerzéséhez: [kognitív szolgáltatások előfizetés](https://azure.microsoft.com/try/cognitive-services/).

Az API-kulcsot a jogkivonat-szolgáltatás lett átadva. Példa:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

A token hozzáféréshez szükséges fejléc-információ a következőképpen történik.

Name (Név)| Formátum | Leírás
----|----|----
Az OCP-Apim-előfizetés-kulcs | ASCII | Előfizetési kulcs

A jogkivonat-szolgáltatás adja vissza, a JWT jogkivonat `text/plain`. A JWT átadása majd egy `Base64 access_token` a beszédfelismerés végpontnak egy engedélyezési fejléc a karakterlánc a következő előtaggal `Bearer`. Példa:

`Authorization: Bearer [Base64 access_token]`

Az ügyfelek a szöveg-beszéd átalakítás szolgáltatás eléréséhez a következő végpont kell használnia:

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>Az előfizetés kulccsal szerezték olyan hozzáférési jogkivonatot, a fentebb leírt módon, amíg ez a hivatkozás állít elő egy `403 Forbidden` válasz hiba.

### <a name="Http"></a>HTTP-fejlécek

Az alábbi táblázat a hang összefoglaló kérelmek használt HTTP-fejléceket.

Fejléc |Érték |Megjegyzések
----|----|----
Content-Type | alkalmazás/ssml + xml | A bemeneti tartalomtípusa.
X-Microsoft-OutputFormat | **1.** ssml-16 khz-16 bites-monó-szöveg-beszéd átalakítás <br> **2.** nyers-16 khz-16 bites-monó-pcm <br>**3.** hang-16 khz-16 KB/s-monó-siren <br> **4.** riff-16 khz-16 KB/s-monó-siren <br> **5.** riff-16 khz-16 bites-monó-pcm <br> **6.** hang-16 khz-128kbitrate-monó-mp3 <br> **7.** hang-16 khz-64kbitrate-monó-mp3 <br> **8.** hang-16 khz-32kbitrate-monó-mp3 | A kimeneti hangformátum.
X-keresési-AppId | (Csak hexadecimális, nincs kötőjelek) GUID | Egy azonosító, amely egyedileg azonosítja az ügyfélalkalmazást. Ez a tár Azonosítóját. alkalmazások lehet. Nem érhető el, ha az azonosító felhasználók által létrehozott egy alkalmazás is lehet.
X-keresési-ClientID | (Csak hexadecimális, nincs kötőjelek) GUID | Egy azonosító, amely egyedileg azonosít egy alkalmazáspéldányt minden telepítéshez.
Felhasználói ügynök | Alkalmazásnév | Az alkalmazás neve szükség, és legfeljebb 255 karakterből állhat.
Engedélyezés | Engedélyezési jogkivonat |  Tekintse meg a <a href="#Subscription">engedélyezési jogkivonat</a> szakasz.

### <a name="InputParam"></a>A bemeneti paraméterek

Az a Bing szöveg-hang transzformációs API-kérelmek HTTP POST-hívásokkal. A fejlécek meg van adva az előző szakaszban. A szöveg kell synthesized jelölő beszéd összefoglaló Markup Language (SSML) bemeneti törzsében. A kód például a nyelvi beszéd szempontok és a beszélőre nemének vezérlő leírását lásd a [SSML W3C Specification](http://www.w3.org/TR/speech-synthesis/).

>[!NOTE]
>Az SSML-bemenet támogatott maximális mérete 1024 karaktert, köztük az összes címkék.

###  <a name="SampleVoiceOR"></a>Példa: hang kimeneti kérelem

Egy hang kimeneti kérelem például a következőképpen történik:

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

## <a name="VoiceOutResponse"></a>Hang kimeneti válasz

A Bing szöveg-hang transzformációs API HTTP POST használatával hang küldése az ügyfélnek. Az API-válasz tartalmazza a hangadatfolyam és a kodek, és a kért kimeneti formátuma megegyezik. A hangot, egy adott kérelem vissza nem lehet hosszabb 15 másodperc.

### <a name="SuccessfulRecResponse"></a>Példa: sikeres összefoglaló válasz

A következő kód egy JSON-NÁ válaszul sikeres hang összefoglaló példája. A megjegyzések és formázását a kód a jelen példában csak, és kimaradnak a tényleges válasz.

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>Példa: összefoglaló hiba

Az alábbi példakód mutatja egy JSON-válasz hang-összefoglaló lekérdezési hiba esetén:

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>Hibaüzenetek

Hiba | Leírás
----|----
Hibás kérés HTTP/400 | Egy kötelező paraméter hiányzik, üres vagy null, vagy átadott vagy egy kötelező vagy választható paraméter értéke érvénytelen. "Érvénytelen" válasz fogadása az egyik oka az, hogy a karakterlánc-értéke hosszabb a megengedettnél. Egy rövid leírást a problematikus paraméter szerepel.
HTTP/401-es nem engedélyezett | A kérelem nem engedélyezett.
A HTTP/413 RequestEntityTooLarge  | Az SSML-bemenet, nagyobb támogatott források és műveletek.
A HTTP/502 BadGateway | Nincs, a hálózattal kapcsolatos probléma vagy kiszolgálóoldali probléma okozza.

Egy hiba történt egy válasz például a következőképpen történik:

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>Hangkimenet keresztül SSML módosítása

A W3C Microsoft szöveg-beszéd átalakítás API támogatja az SSML 1.0 [beszéd összefoglaló Markup Language (SSML) 1.0-s verziója](http://www.w3.org/TR/2009/REC-speech-synthesis-20090303/). Ez a szakasz ismerteti a példák módosítása bizonyos, például a beszéd létrehozott hangkimenet jellemzői értékelje, kiejtés SSML-címkék használatával stb.

1. Skálamegszakítás hozzáadása

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)'> Welcome to use Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.</voice> </speak>
  ```

2. Nyelvű sebességének módosítása

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody rate="+30.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

3. Kiejtés

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'> <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme></voice> </speak>
  ```

4. Kötet módosítása

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody volume="+20.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

5. Módosítsa a térköz

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Welcome to use <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

6. Változás prosody eloszlási

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
  ```

> [!NOTE]
> Megjegyzés: a hangadatok-nak kell lennie a 8 KB-os vagy 16 KB-os wav bejegyezve a következő formátumban: **CRC-kód** (CRC-32): 4 bájt (DWORD) az érvényes tartomány 0x00000000 ~ 0xFFFFFFFF; **Hang formátumot jelző**: 4 bájt (DWORD) az érvényes tartomány 0x00000000 ~ 0xFFFFFFFF; **Minták száma**: 4 bájt (DWORD) az érvényes tartomány 0x00000000 ~ 0x7FFFFFFF; **Bináris méretének**: 4 bájt (DWORD) az érvényes tartomány 0x00000000 ~ 0x7FFFFFFF; **Bináris törzs**: n bájt.

## <a name="SampleApp"></a>Mintaalkalmazás

Megvalósítás részletei, lásd: a [Visual C# .NET szöveget beszéddé átalakító mintaalkalmazás](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs).

## <a name="SupLocales"></a>Támogatott területi beállításokat, és hang betűtípusok

A következő táblázat ismerteti a támogatott területi és a kapcsolódó hang betűtípusok.

Területi beállítás | Nem | Szolgáltatás felhasználónevek hozzárendelése
---------|--------|------------
ar EG * | Nő | "Microsoft Server beszéd szöveg-beszéd hang (ar – pl. Hoda)"
ar-SA | Férfi | "Microsoft Server beszéd szöveg beszéd hang (ar-SA, Naayf)"
bg-BG | Férfi | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (bg-BG, Ivan)"
CA-ES | Nő | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (ca-ES, HerenaRUS)"
cs-CZ | Férfi | "Microsoft Server beszéd szöveg-beszéd hang (cs-CZ, Jakub)"
da-DK | Nő | "Microsoft Server beszéd szöveg beszéd hang (da-DK, HelleRUS)"
de-AT | Férfi | "Microsoft Server beszéd szöveg beszéd hang (de-AT, Michael)"
de-CH | Férfi | "Microsoft Server beszéd szöveg beszéd hang (de-CH, Karsten)"
de-DE | Nő | "Microsoft Server beszéd szöveg beszéd hang (de-DE, Hedda)"
de-DE | Nő | "Microsoft Server beszéd szöveg beszéd hang (de-DE, HeddaRUS)"
de-DE | Férfi | "Microsoft Server beszéd szöveg beszéd hang (de-DE, lengyel, Apollo)"
el-GR | Férfi | "Microsoft Server beszéd szöveg beszéd hang (el-GR, Stefanos)"
en-AU | Nő | "Microsoft Server beszéd szöveg beszéd hang (en-AU, Catherine)"
en-AU | Nő | "Microsoft Server beszéd szöveg beszéd hang (en-AU, HayleyRUS)"
en-Kanada | Nő | "Microsoft Server beszéd szöveg beszéd hang (en-CA, Linda)"
en-Kanada | Nő | "Microsoft Server beszéd szöveg beszéd hang (en-CA, HeatherRUS)"
en-GB | Nő | "Microsoft Server beszéd szöveg beszéd hang (en GB-os, Susan, Apollo)"
en-GB | Nő | "Microsoft Server beszéd szöveg beszéd hang (en-GB, HazelRUS)"
en-GB | Férfi | "Microsoft Server beszéd szöveg beszéd hang (en GB-os, George, Apollo)"
en-IE | Férfi | "Microsoft Server beszéd szöveg beszéd hang (en-IE, András)"
en-IN | Nő | "Microsoft Server beszéd szöveg beszéd hang (en-IN, Heera, Apollo)"
en-IN | Nő | "Microsoft Server beszéd szöveg beszéd hang (en-IN, PriyaRUS)"
en-IN | Férfi | "Microsoft Server beszéd szöveg beszéd hang (en-IN, Ravi, Apollo)"
hu-HU | Nő | "Microsoft Server beszéd szöveg beszéd hang (en-US, ZiraRUS)"
hu-HU | Nő | "Microsoft Server beszéd szöveg beszéd hang (en-US, JessaRUS)"
hu-HU | Férfi | "Microsoft Server beszéd szöveg beszéd hang (en-US, BenjaminRUS)"
es-ES | Nő | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (es-ES, Laura, Apollo)"
es-ES | Nő | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (es-ES, HelenaRUS)"
es-ES | Férfi | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (es-ES, Pablo, Apollo)"
es-MX | Nő | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (es-MX, HildaRUS)"
es-MX | Férfi | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (es-MX, Raul, Apollo)"
fi-FI | Nő | "Microsoft Server beszéd szöveg beszéd hang (fi-FI, HeidiRUS)"
FR-Kanada | Nő | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (fr-CA, Caroline)"
FR-Kanada | Nő | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (fr-CA, HarmonieRUS)"
FR-CH | Férfi | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (fr-CH, Guillaume)"
FR-FR | Nő | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (fr-FR, Ágnes, Apollo)"
FR-FR | Nő | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (fr-FR, HortenseRUS)"
FR-FR | Férfi | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (fr-FR, Paul, Apollo)"
He-IL| Férfi| "Microsoft Server beszéd szöveg beszéd hang (he-IL, Asaf)"
üdv-IN | Nő | "Microsoft Server beszéd szöveg beszéd hang (hi-IN, Kalpana, Apollo)"
üdv-IN | Nő | "Microsoft Server beszéd szöveg beszéd hang (hi-IN, Kalpana)"
üdv-IN | Férfi | "Microsoft Server beszéd szöveg beszéd hang (hi-IN, Hemant)"
hr-HR | Férfi | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (hr-HR, Matej)"
hu-HU | Férfi | "Microsoft Server beszéd szöveg beszéd hang (hu-HU, Szabolcs)"
Azonosító | Férfi | "Microsoft Server beszéd szöveg beszéd hang (-azonosító, Andika)"
it-IT | Férfi | "Microsoft Server beszéd szöveg beszéd hang (it-IT, Cosimo, Apollo)"
ja-JP | Nő | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (ja-JP, Ayumi, Apollo)"
ja-JP | Férfi | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (ja-JP, Ichiro, Apollo)"
ja-JP | Nő | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (ja-JP, HarukaRUS)"
ja-JP | Nő | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (ja-JP, LuciaRUS)"
ja-JP | Férfi | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (ja-JP, EkaterinaRUS)"
ko-KR | Nő | "Microsoft Server beszéd szöveg beszéd hang (ko-KR, HeamiRUS)"
MS-saját | Férfi | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (ms-saját, Rizwan)"
nb-NO | Nő | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (nb-NO, HuldaRUS)"
NL-NL | Nő | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (nl-NL, HannaRUS)"
pl-PL | Nő | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (pl-PL, PaulinaRUS)"
pt-BR | Nő | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (pt-BR, HeloisaRUS)"
pt-BR | Férfi | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (pt-BR, Daniel, Apollo)"
PT-PT | Nő | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (pt-PT, HeliaRUS)"
ro-RO | Férfi | "Microsoft Server beszéd szöveg beszéd hang (ro-RO, Andrei)"
ru-RU | Nő | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (ru-RU, Irina, Apollo)"
ru-RU | Férfi | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (ru-RU, Pavel, Apollo)"
SK-SK | Férfi | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (sk-SK, Filip)"
SA-SI | Férfi | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (SA-SI, Lado)"
SV-SE | Nő | "Microsoft Server beszéd szöveg beszéd hang (sv-SE, HedvigRUS)"
TA-IN | Férfi | "Microsoft Server beszéd szöveg beszéd hang (ta-IN, Valluvar)"
cs-EDIK | Férfi | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (cs-EDIK, Pattara)"
tr-TR | Nő | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (tr-TR, SedaRUS)"
vi-VN | Férfi | "Microsoft Server beszéd szöveg-beszéd átalakítás hang (vi-VN, az)"
zh-CN | Nő | "Microsoft Server beszéd szöveg beszéd hang (zh-CN, HuihuiRUS)"
zh-CN | Nő | "Microsoft Server beszéd szöveg beszéd hang (zh-CN, Yaoyao, Apollo)"
zh-CN | Férfi | "Microsoft Server beszéd szöveg beszéd hang (zh-CN, Kangkang, Apollo)"
zh-HK | Nő | "Microsoft Server beszéd szöveg beszéd hang (zh-HK, Tracy, Apollo)"
zh-HK | Nő | "Microsoft Server beszéd szöveg beszéd hang (zh-HK, TracyRUS)"
zh-HK | Férfi | "Microsoft Server beszéd szöveg beszéd hang (zh-HK, Danny, Apollo)"
zh-TW | Nő | "Microsoft Server beszéd szöveg beszéd hang (zh-TW, Yating, Apollo)"
zh-TW | Nő | "Microsoft Server beszéd szöveg beszéd hang (zh-TW, HanHanRUS)"
zh-TW | Férfi | "Microsoft Server beszéd szöveg beszéd hang (zh-TW, Zhiwei, Apollo)"
 * ar – pl. támogatja Modern szabványos arab (msa-t).

> [!NOTE]
> Vegye figyelembe, hogy az előző szolgáltatásnevek **Microsoft Server beszéd szöveg-beszéd hang (cs-CZ, Vit)** és **Microsoft Server beszéd szöveg-beszéd hang (en-IE, Shaun)** elavulttá válik 3/31/2018 után a sorrend optimalizálása a Bing Diktálásfelismerési API-képességeit. Frissítse a kódot a frissített nevekkel.

## <a name="TrouNSupport"></a>Hibaelhárítás és támogatás

Minden kérdéseket és a problémák a [Bing Diktálásfelismerési szolgáltatás](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService) MSDN fórumon. Olyan, mint például a további információkat:

* Ilyen például a kérelem teljes karakterlánc.
* Ha van ilyen, a sikertelen kérelmet, amely tartalmazza a teljes kimeneti jelentkezzen azonosítók.
* A sikertelen kérelmek százalékos aránya
