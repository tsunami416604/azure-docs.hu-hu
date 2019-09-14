---
title: A Microsoft Speech Service Text to Speech APIja | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: A szöveg-beszéd API használatával valós idejű szöveg-beszéd átalakítást biztosíthat különböző hangokon és nyelveken
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ee9b0b47fb88cba948bc06db6eb83fe9c076fe40
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966874"
---
# <a name="bing-text-to-speech-api"></a>A Bing szövege a Speech API-hoz

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="Introduction"></a>Bevezetés

A Bing Text for Speech API használatával az alkalmazás HTTP-kéréseket küldhet egy felhőalapú kiszolgálónak, ahol a szöveg azonnal emberi hangzású beszédbe kerül, és hangfájlként lesz visszaadva. Ez az API számos különböző kontextusban használható, hogy valós idejű szöveg-beszéd átalakítást biztosítson számos különböző hangon és nyelven.

## <a name="VoiceSynReq"></a>Hangszintézisi kérelem

### <a name="Subscription"></a>Engedélyezési jogkivonat

Minden hangszintézisi kérelemhez JSON Web Token (JWT) hozzáférési token szükséges. A JWT hozzáférési token át lett adva a Speech Request fejlécben. A jogkivonat lejárati ideje 10 perc. Az érvényes JWT hozzáférési tokenek lekéréséhez használt API-kulcsok [előfizetésével](https://azure.microsoft.com/try/cognitive-services/)és beszerzésével kapcsolatos információkért lásd: Cognitive Services előfizetés.

Az API-kulcsot a rendszer átadja a jogkivonat-szolgáltatásnak. Példa:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

A jogkivonat-hozzáféréshez szükséges fejléc-információ a következő.

Name (Név)| Formátum | Leírás
----|----|----
OCP-Apim-Subscription-Key | ASCII | Előfizetési kulcs

A jogkivonat-szolgáltatás a JWT hozzáférési tokent `text/plain`adja vissza. Ezt követően a rendszer a JWT adja `Base64 access_token` át a Speech végpontnak, mint a sztringtel `Bearer`előrögzített engedélyezési fejlécet. Példa:

`Authorization: Bearer [Base64 access_token]`

Az ügyfeleknek a következő végpontot kell használniuk a szöveg-beszéd szolgáltatás eléréséhez:

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>Amíg a korábban leírtak szerint nem kapott hozzáférési jogkivonatot az előfizetési kulccsal, a `403 Forbidden` hivatkozás hibát generál.

### <a name="Http"></a>HTTP-fejlécek

A következő táblázat a hangszintézisi kérelmekhez használt HTTP-fejléceket mutatja be.

Fejléc |Value |Megjegyzések
----|----|----
Content-Type | Application/ssml + XML | A bemeneti tartalom típusa
X-Microsoft-OutputFormat | **1.** ssml-16khz-16bit-mono-TTS <br> **2.** nyers-16khz-16bit-mono-PCM <br>**3.** hang-16khz-16kbps-monó-sziréna <br> **4.** riff-16khz-16kbps-mono-Szirén <br> **5.** riff-16khz-16bit-mono-PCM <br> **6.** hang-16khz-128kbitrate-mono-MP3 <br> **7.** hang-16khz-64kbitrate-mono-MP3 <br> **8.** hang-16khz-32kbitrate-mono-MP3 | A kimeneti hang formátuma.
X-Search-AppId | GUID (csak hexadecimális, nincs kötőjel) | Az ügyfélalkalmazás egyedi azonosítására szolgáló azonosító. Ez lehet az alkalmazások áruház-azonosítója. Ha az egyik nem érhető el, az azonosító lehet a felhasználó által generált alkalmazás.
X-Search-ClientID | GUID (csak hexadecimális, nincs kötőjel) | Egy olyan azonosító, amely egyedileg azonosít egy alkalmazás-példányt az egyes telepítésekhez.
Felhasználói ügynök | Alkalmazás neve | Az alkalmazás nevének megadása kötelező, és 255 karakternél rövidebbnek kell lennie.
Authorization | Engedélyezési jogkivonat |  Lásd: <a href="#Subscription">engedélyezési jogkivonat</a> szakasz.

### <a name="InputParam"></a>Bemeneti paraméterek

A Bing-szöveg és a beszédfelismerési API felé irányuló kérések HTTP POST-hívások használatával történnek. A fejlécek az előző szakaszban vannak megadva. A törzs tartalmaz egy Speech szintézis Markup Language (SSML) bemenetet, amely a szintetizálni kívánt szöveget jelöli. A beszéd szempontjainak, például a beszélő nyelvének és nemre vonatkozó szabályozásához használt jelölés leírását a [SSML W3C-specifikációban](https://www.w3.org/TR/speech-synthesis/)találhatja meg.

>[!NOTE]
>A támogatott SSML-bemenet maximális mérete 1 024 karakter, beleértve az összes címkét is.

###  <a name="SampleVoiceOR"></a>Példa: hangkimeneti kérelem

A hangkimeneti kérelem példája a következő:

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

## <a name="VoiceOutResponse"></a>Hangkimeneti válasz

A Bing szöveg-beszéd API a HTTP POST használatával küldi vissza a hangot az ügyfélnek. Az API-válasz tartalmazza az audio streamet és a kodeket, és megfelel a kért kimeneti formátumnak. Egy adott kérelemhez megadott hang nem haladhatja meg a 15 másodpercet.

### <a name="SuccessfulRecResponse"></a>Példa: sikeres szintézis-válasz

A következő kód egy példát mutat be egy, a sikeres hangszintézisre irányuló kérelemre vonatkozó JSON-válaszra. A kód megjegyzései és formázása kizárólag ebben a példában szerepelnek, és kimaradnak a tényleges választól.

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>Példa: szintézisi hiba

A következő példa egy olyan JSON-választ mutat be, amely egy hang-szintézis lekérdezési hibára mutat:

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>Hibaüzenetek

Hiba | Leírás
----|----
HTTP/400 hibás kérelem | Egy kötelező paraméter hiányzik, üres vagy NULL értékű, vagy a kötelező vagy választható paraméternek átadott érték érvénytelen. Az "Érvénytelen" válasz beolvasásának egyik oka, hogy a karakterlánc értéke hosszabb, mint a megengedett hossz. A rendszer a problémás paraméter rövid leírását tartalmazza.
HTTP/401 jogosulatlan | A kérelem nem engedélyezett.
HTTP/413 RequestEntityTooLarge  | A SSML bemeneti értéke nagyobb a támogatottnál.
HTTP/502 BadGateway | A hálózattal kapcsolatos problémák vagy kiszolgálóoldali problémák merültek fel.

A következő hibaüzenetet választhatja:

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>Hangkimenet módosítása SSML-n keresztül

A Microsoft Text-to-Speech API a W3C [Speech szintézis Markup Language (SSML) 1,0-es verziójában](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/)definiált SSML 1,0-et támogatja. Ez a szakasz példákat mutat be a generált hangkimenetek, például a felszólalási arány, a kiejtés és a SSML címkék használatával történő módosítására.

1. Töréspont hozzáadása

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)'> Welcome to use Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.</voice> </speak>
   ```

2. Nyelvű sebességének módosítása

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody rate="+30.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

3. Kiejtése

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'> <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme></voice> </speak>
   ```

4. Kötet módosítása

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody volume="+20.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

5. Térköz módosítása

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Welcome to use <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

6. Prosody-kontúr módosítása

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
   ```

> [!NOTE]
> Vegye figyelembe, hogy a hangadatoknak a következő formátumban kell 8k vagy 16k WAV-t kell benyújtaniuk: **CRC-kód** (CRC-32): 4 bájt (DWORD) érvényes tartomány 0x00000000 ~ 0xFFFFFFFF; **Hangformátum jelzője**: 4 bájt (DWORD) érvényes tartomány 0x00000000 ~ 0xFFFFFFFF; **Minták száma**: 4 bájt (DWORD) érvényes tartomány 0x00000000 ~ 0x7FFFFFFF; **Bináris törzs mérete**: 4 bájt (DWORD) érvényes tartomány 0x00000000 ~ 0x7FFFFFFF; **Bináris törzs**: n bájt.

## <a name="SampleApp"></a>Minta alkalmazás

A megvalósítás részleteiért tekintse meg a [ C#Visual .net szöveg-beszéd minta alkalmazását](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs).

## <a name="SupLocales"></a>Támogatott területi beállítások és hangbetűkészletek

A következő táblázat a támogatott területi beállításokat és a kapcsolódó hangbetűkészleteket ismerteti.

Területi beállítás | Nem | A felhasználónév-leképezés
---------|--------|------------
AR-EG * | Nő | "A Microsoft Server hang-szöveg, beszédfelismerés, beszédfelismerési (ar-működtek az Adatbázisok, Hoda)"
ar-SA | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (ar-SA, Naayf)"
bg-BG | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (bg-BG, Ivan)"
CA-ES | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ca-ES, HerenaRUS)"
cs-CZ | Férfi | "A Microsoft Server hang-szöveg, beszédfelismerés, beszédfelismerési (cs-CZ, Jakub)"
da-DK | Nő | "A Microsoft Server beszéd szöveg Speech Voice (da-DK, HelleRUS)"
Németország-AT | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (Németország-AT, Michael)"
Németország – CH | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (Németország-CH, Karsten)"
de-DE | Nő | "Microsoft Server Speech Text to Speech Voice (de-DE, Hedda)"
de-DE | Nő | "A Microsoft Server beszéd szöveg Speech Voice (de-DE, HeddaRUS)"
de-DE | Férfi | "Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)"
el-GR | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (el-GR, Stefanos)"
en-Ausztrália | Nő | "Microsoft Server Speech Text to Speech hang (en-AU, Catherine)"
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
en-US | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-US, ZiraRUS)"
en-US | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-US, JessaRUS)"
en-US | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (en-US, BenjaminRUS)"
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

 \* AR-EG a modern szabványos Arab (MSA) szabványokat támogatja.

> [!NOTE]
> Vegye figyelembe, hogy a korábbi szolgáltatásnév a **Microsoft Server speech Text to speech Voice (cs-cz, Vit)** és a **microsoft Server Speech Text to speech hang (en-IE, Shaun)** a 3/31/2018 után elavult, hogy optimalizálja a Bing Speech API képességek. Frissítse a kódot a frissített nevekkel.

## <a name="TrouNSupport"></a>Hibaelhárítás és támogatás

Tegye fel az összes kérdést és problémát a [Bing Speech szolgáltatás](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService) MSDN fórumára. Adja meg a teljes részleteket, például:

* Példa a teljes kérelem sztringre.
* Ha alkalmazható, a sikertelen kérelem teljes kimenete, amely tartalmazza a napló-azonosítókat.
* A sikertelen kérelmek százalékos aránya.
