---
title: Translator Speech API-referencia
titleSuffix: Azure Cognitive Services
description: A Translator Speech API dokumentációja.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: reference
ms.date: 05/18/2018
ms.author: v-jansko
ms.openlocfilehash: 1fc48687141ea8a7e8cb30d3438d81e8f1088e4f
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340443"
---
# <a name="translator-speech-api"></a>Translator Speech API

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Ezt a szolgáltatást kínál egy olyan streamelési API a alapuló átírás természetes nyelvi beszédfelismerési egyik nyelvről egy másik nyelv szöveggé. Az API-t a lefordított szöveg felolvasása szöveg-hang transzformációs képességeket is integrálható. A Translator Speech API lehetővé teszi például a valós idejű fordítását beszélgetések, a Skype fordítóban látható módon.

A Translator Speech API-val ügyfélalkalmazások adatfolyam speech hangot a szolgáltatáshoz, és vissza adatfolyamban kaphatja kézhez az eredmények szöveges többek között a felismert szöveget a forrás nyelvét, és a fordítás a célként megadott nyelven. A szövegeredmények a mély neurális hálózatok által működtetett automatikus beszédfelismerés (ASR) a bejövő audiostreamre való alkalmazásával jönnek létre. Nyers ASR kimeneti további továbbfejlesztett egy új technikát TrueText nevű annak érdekében, hogy jobban tükrözzék a felhasználói szándékot. Ha például TrueText eltávolítja disfluencies (hmms és coughs) és a visszaállítás megfelelő írásjelek és a nagybetűk. Lehetőség van a profanitás kitakarására vagy eltávolítására is. A felismerő- és fordítómotorok kifejezetten a beszélgetések kezelésére lettek kifejlesztve. A beszédalapú fordítási szolgáltatás csend észlelési használja az utterance (kifejezés) végén meghatározásához. A beszédhangokban beállt szünet esetén a szolgáltatás visszastreameli a végleges eredményt a kész kimondott szöveghez. A szolgáltatás visszaküldhet részleges eredményeket is, amelyek köztes felismerést és fordítást biztosítanak a folyamatban lévő kimondott szöveghez. A végső eredmények érdekében a szolgáltatás lehetőséget kínál a szintetizálásához speech (szöveg-hang transzformációs) a célként megadott nyelveken a kimondott szöveg. A szövegfelolvasás audioanyaga az ügyfél által megadott hangformátumban jön létre. A WAV és az MP3 formátum érhető el.

Translator Speech API, az ügyfél és a kiszolgáló közötti kétirányú kommunikációs csatornát biztosítanak a WebSocket protokoll használ. Egy alkalmazás ezeket a lépéseket, a szolgáltatás használatára lesz szükség:

## <a name="1-getting-started"></a>1. Első lépések
A Translator Text API kell eléréséhez [Regisztráljon a Microsoft Azure](translator-speech-how-to-signup.md).

## <a name="2-authentication"></a>2. Hitelesítés

Az előfizetési kulcs használatával hitelesíteni. A Translator Speech API-hitelesítés két módot támogat:

* **Hozzáférési token használatával:** az alkalmazás szerzi be egy hozzáférési jogkivonatot a jogkivonat-szolgáltatás. A Translator Speech API-előfizetési kulcs használatával hozzáférési jogkivonat beszerzése az Azure Cognitive Services hitelesítési szolgáltatástól. A hozzáférési jogkivonatot a 10 percig érvényes. Új hozzáférési jogkivonat beszerzése 10 percenként, és így továbbra is használhatja ugyanazt a hozzáférést token ismétlődő kérelmek ezen 10 percen belül.

* **Közvetlenül egy előfizetési kulcsot használva:** az alkalmazásban adja át az előfizetési kulcs értékként `Ocp-Apim-Subscription-Key` fejléc.

Kezelje az előfizetési kulcs és a hozzáférési jogkivonat titkos kódok, amelyek nézetből el kell rejteni.

## <a name="3-query-languages"></a>3. Lekérdezési nyelvet
**A lekérdezés a nyelvek erőforrás által támogatott nyelvekről az aktuális készletét.** A [nyelvek erőforrás](languages-reference.md) tesz elérhetővé a nyelvek készletét, és beszédhangot, beszédfelismerés, szövegfordítás és szöveg-hang transzformációs elérhető. Minden egyes nyelvi vagy szóbeli kap egy azonosító, amely a Translator Speech API nyelv vagy a hangalapú azonosítására használ.

## <a name="4-stream-audio"></a>4. Stream hang
**Nyisson meg egy kapcsolatot, és elkezdeni a streamelést a hangot a szolgáltatáshoz.** A szolgáltatás URL-cím `wss://dev.microsofttranslator.com/speech/translate`. Paraméterek és a szolgáltatás által várt hangformátumok alább találhatók, az a `/speech/translate` műveletet. A paraméterek egyike segítségével adja át a hozzáférési jogkivonatot a fenti 2. lépés.

## <a name="5-process-the-results"></a>5. Az eredmények feldolgozásához
**Az eredmények vissza a szolgáltatásból streamelt adatok feldolgozásához.** A dokumentációban ismertetett részleges eredményeket, a végső eredmények és a szöveg-hang transzformációs hang szegmensek formátuma a `/speech/translate` az alábbi műveletet.

A Translator Speech API használatát bemutató kódminta érhetők el a [a Microsoft Translator Github-webhelyről](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések

Egy munkamenet a beszédalapú fordítási Establishes /speech/translate beolvasása

### <a name="connecting"></a>Csatlakozás
A szolgáltatáshoz való csatlakozás előtt tekintse át a jelen szakaszban később megadott paraméterek listáját. A rendszer egy kérelem (példa):

`GET wss://dev.microsofttranslator.com/speech/translate?from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa&api-version=1.0`
`Ocp-Apim-Subscription-Key: {subscription key}`
`X-ClientTraceId: {GUID}`

A kérelem Megadja, hogy használja a beszélt angol adatfolyamként történő a szolgáltatás és olasz fordítja. Minden egyes végső felismerés eredményét a Elsa nevű női hang-szöveg-hang transzformációs hang választ hoz létre. Figyelje meg, hogy a kérelem tartalmazza-e a hitelesítő adatokat a `Ocp-Apim-Subscription-Key header`. A kérelem is követi a legjobb globálisan egyedi azonosító a fejléc beállításával `X-ClientTraceId`. Egy ügyfélalkalmazás naplózni kell a nyomkövetési Azonosítót az, hogy a felmerülő hibák elhárításához használható.

### <a name="sending-audio"></a>Hang küldése
Ha a kapcsolat létrejött, az ügyfél kezdődik, hang, a szolgáltatás folyamatos. Az ügyfél elküldi az adattömbök hang. Minden egyes adattömbbel bináris típusú Websocket üzenetet küldeni.

Hangbemenet hullám hang formátumban van (HULLÁM, vagy több gyakran nevezik WAV miatt a fájlnév kiterjesztése). Az ügyfélalkalmazás egy csatorna, aláírt 16 bites PCM hang: 16 kHz mintát kell adatfolyam. Az ügyfél részére első készlete a WAV-fejléc tartalmazza. A 44 bájtos fejléc esetében egy csatornán aláírt 16 bites PCM stream, 16 kHz mintát a következő:

|Eltolás|Érték|
|:---|:---|
|0 – 3|"RIFF"|
|4 – 7|0|
|8 – 11|"SZAKASZ"|
|12 – 15|"fmt"|
|16 - 19|16|
|20 - 21|1|
|22 - 23|1|
|24 - 27|16000|
|28 – 31.|32000|
|32 - 33|2|
|34 – 35|16|
|36 - 39|"adatok"|
|40 - 43|0|

Figyelje meg, hogy a teljes fájlméret (bájt 4-7) és a "data" (40-43 bájt) mérete értéke nulla. Ez az OK gombra a streamelési forgatókönyv, ahol a teljes mérete nem feltétlenül ismert előre.

A WAV (RIFF) fejléc elküldése, után az ügyfél elküldi az audio adatok adattömböket. Az ügyfél általában fog adatfolyam egy meghatározott időtartamra (például stream 100 MS egyszerre hanganyagra) jelölő rögzített méretű adattömbökre.

### <a name="final-result"></a>Végső eredményt
Végső speech recognition eredményeképpen jön létre az utterance (kifejezés) végén. Egy eredményt a szolgáltatásból átkerülnek a szöveg típusú WebSocket üzenetben az ügyfelet. Az üzenet tartalma a következő tulajdonságokkal rendelkező objektum JSON-szerializálás:

* `type`: Az eredmény típusának azonosításához karakterlánc-konstans. A végső eredmények végső értéke.
* `id`: A felismerés eredményét hozzárendelt azonosító karakterlánc.
* `recognition`: A Forrásnyelv felismert szöveg. A szöveg hamis felismerés esetén üres karakterlánc lehet.
* `translation`: A felismert szöveget a lefordított cél nyelven.
* `audioTimeOffset`: Az az órajelben-felismerés kezdete idő eltolása (1 osztásjelek = 100 nanoszekundumban). Az eltolás van folyamatos átvitel kezdete viszonyítva.
* `audioTimeSize`: Az órajelben (100 nanoszekundumban), a felismerés időtartama.
* `audioStreamPosition`: A beszédfelismerést kezdete bájt eltolását. Az eltolás van, a stream elején viszonyítva.
* `audioSizeBytes`: Mérete (bájt) a felismerése.

Vegye figyelembe, hogy a hang Stream elismerését elhelyezéséhez nem szerepel az eredményeket alapértelmezés szerint. A `TimingInfo` szolgáltatást ki kell választani az ügyfél (lásd: `features` paraméter).

A minta végeredmény a következőképpen történik:

```
{
  type: "final"
  id: "23",
  recognition: "what was said", 
  translation: "translation of what was said",
  audioStreamPosition: 319680,
  audioSizeBytes: 35840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 21900000
}
```

### <a name="partial-result"></a>Részleges eredményt
Részleges vagy köztes speech recognition eredmények adatfolyamként történő küldése az ügyfélnek alapértelmezés szerint. Az ügyfél a szolgáltatások lekérdezési paraméter használatával tanúsítványkérelmeket.

Részleges eredményt átkerülnek a szolgáltatásból a szöveg típusú WebSocket üzenetben az ügyfelet. Az üzenet tartalma a következő tulajdonságokkal rendelkező objektum JSON-szerializálás:

* `type`: Az eredmény típusának azonosításához karakterlánc-konstans. A részleges eredményeket részleges érték.
* `id`: A felismerés eredményét hozzárendelt azonosító karakterlánc.
* `recognition`: A Forrásnyelv felismert szöveg.
* `translation`: A felismert szöveget a lefordított cél nyelven.
* `audioTimeOffset`: Az az órajelben-felismerés kezdete idő eltolása (1 osztásjelek = 100 nanoszekundumban). Az eltolás van folyamatos átvitel kezdete viszonyítva.
* `audioTimeSize`: Az órajelben (100 nanoszekundumban), a felismerés időtartama.
* `audioStreamPosition`: A beszédfelismerést kezdete bájt eltolását. Az eltolás van, a stream elején viszonyítva.
* `audioSizeBytes`: Mérete (bájt) a felismerése.

Vegye figyelembe, hogy a hang Stream elismerését elhelyezéséhez nem szerepel az eredményeket alapértelmezés szerint. A TimingInfo szolgáltatást ki kell választani az ügyfél (lásd a szolgáltatások paraméter).

A minta végeredmény a következőképpen történik:

```
{
  type: "partial"
  id: "23.2",
  recognition: "what was", 
  translation: "translation of what was",
  audioStreamPosition: 319680,
  audioSizeBytes: 25840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 11900000
}
```

### <a name="text-to-speech"></a>Szöveg-hang transzformációs
Ha a szöveg-hang transzformációs funkció engedélyezve van (lásd: `features` paraméterhez), a végső eredményt a hanganyag a kimondott lefordított szöveg követ. Hang adatok adattömbökre osztotta és az ügyfélnek küldött a szolgáltatás bináris típusú Websocket üzenetek sorozataként. Egy ügyfél az egyes üzenetek PÉNZÜGY bit ellenőrzésével képes észlelni a konce datového proudu. Az utolsó üzenet bináris fog rendelkezni a PÉNZÜGY bit beállítása egy, az adatfolyam végén jelzi. Az adatfolyam formátuma értékét függ a `format` paraméter.

### <a name="closing-the-connection"></a>A kapcsolat bezárása
Amikor egy ügyfélalkalmazás hang streamelés befejeződött, és az utolsó végső eredményt kapott, a WebSocket záró kézfogás kezdeményezése azt kell bezárásához a kapcsolatot. Ebben az esetben feltételt, amely újraindítja a kiszolgálót, hogy állítsa le a kapcsolatot. Az ügyfél által a következő WebSocket lezárt kódok előfordulhat, hogy kapott:

* `1003 - Invalid Message Type`: A kapott adatok típusa nem fogad el, mert a kiszolgáló megszakítja a kapcsolatot. Ez gyakran történik, ha a bejövő hang kezdete nem a megfelelő fejlécet.
* `1000 - Normal closure`: A kapcsolat be van zárva, a kérés teljesítése után. A kiszolgáló bontja a kapcsolatot: amikor nincs hang érkezik az ügyfél hosszabb idő; Ha hosszabb ideig; csend adatfolyamként Ha a munkamenet elérte a megengedett maximális időtartamot (körülbelül 90 percet).
* `1001 - Endpoint Unavailable`: Azt jelzi, hogy a kiszolgáló nem lesz elérhető. Ügyfélalkalmazás előfordulhat, hogy megkísérel ismét kapcsolódni a próbálkozások számának korlátját.
* `1011 - Internal Server Error`: A kiszolgálón egy hiba miatt a kapcsolat lezárul a kiszolgáló által.

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:---|:---|:---|:---|:---|
|API-verzió|1.0|Az ügyfél által kért API-verzió. Engedélyezett értékek a következők: `1.0`.|lekérdezés   |sztring|
|forrás:|(üres)   |Meghatározza a bejövő beszéd nyelvét. Az érték a nyelv azonosítók egyike a `speech` hatókörhöz. a válasz a nyelvek API.|lekérdezés|sztring|
|erre:|(üres)|Meghatározza az átírt szöveget a lefordítandó nyelvét. Az érték a nyelv azonosítók egyike a `text` hatókörhöz. a válasz a nyelvek API.|lekérdezés|sztring|
|funkciókkal|(üres)   |Az ügyfél által kiválasztott funkciók vesszővel elválasztott készletét. Elérhető funkciók:<ul><li>`TextToSpeech`: Megadja, hogy a szolgáltatás kell a lefordított hang, a végső lefordított mondatra.</li><li>`Partial`: Itt adhatja meg, hogy a szolgáltatás köztes felismerési eredményeket kell visszaadnia, míg a hanganyag adatfolyam-e a szolgáltatáshoz.</li><li>`TimingInfo`: Itt adhatja meg, hogy a szolgáltatás a társított minden egyes felismerés időzítési információkkal kell visszaadnia.</li></ul>Például egy ügyfél kellene megadnia `features=partial,texttospeech` részleges eredményeket, és a szöveg-hang transzformációs, de nincs időadatokat fogadásához. Vegye figyelembe, hogy a végső eredmények mindig továbbított az ügyfélnek.|lekérdezés|sztring|
|Hang|(üres)|Milyen hangalapú használatához a fordítást, szöveg-hang transzformációs rendereléshez azonosítja. Értéke a válaszban a nyelvek API szövegfelolvasás hatókörből hangalapú azonosítók közül. Ha egy hang nincs megadva a rendszer automatikusan az válasszon egy Ha a szöveg-hang transzformációs szolgáltatás engedélyezve van.|lekérdezés|sztring|
|Formátum|(üres)|Itt adhatja meg a szolgáltatás által visszaadott szöveg-hang transzformációs audio-adatfolyam formátuma. Az elérhető lehetőségek:<ul><li>`audio/wav`: Hang hullám az adatfolyam. Ügyfél használja a WAV-fejléc hangformátum megfelelően értelmezni. WAV hang-szöveg-hang transzformációs a 16 bites, egyetlen csatorna PCM 24kHz vagy 16kHz mintavételi arány.</li><li>`audio/mp3`: Hang MP3-adatfolyam.</li></ul>Az alapértelmezett szint a `audio/wav`.|lekérdezés|sztring|
|ProfanityAction    |(üres)    |Itt adhatja meg, hogyan kezelje a szolgáltatás a profanities ismeri el a speech. Érvényes műveletek a következők:<ul><li>`NoAction`: Mivel profanities marad.</li><li>`Marked`: A jelölő profanities helyén. Lásd: `ProfanityMarker` paramétert.</li><li>`Deleted`: Profanities törlődnek. Például ha a szó `"jackass"` úgy viselkedik, mint a vulgáris, a kifejezés `"He is a jackass."` lesz `"He is a .".`</li></ul>Alapértelmezés szerint meg van jelölve.|lekérdezés|sztring|
|ProfanityMarker|(üres)    |Megadja, hogy észlelt profanities mikor kezeli `ProfanityAction` értékre van állítva `Marked`. Az érvényes beállítások a következők:<ul><li>`Asterisk`: A karakterlánc profanities helyén `***`. Például ha a szó `"jackass"` úgy viselkedik, mint a vulgáris, a kifejezés `"He is a jackass."` lesz `"He is a ***.".`</li><li>`Tag`: Egy XML-címke cenzúrázása cenzúrázása zárják közre. Például ha a szó `"jackass"` úgy viselkedik, mint a vulgáris, a kifejezés `"He is a jackass."` lesz `"He is a <profanity>jackass</profanity>."`.</li></ul>Az alapértelmezett érték `Asterisk`.|lekérdezés|sztring|
|Engedélyezés|(üres)  |Az ügyfél tulajdonosi jogkivonat értékét adja meg. Használja az előtag `Bearer` értékét követi a `access_token` a hitelesítési jogkivonat-szolgáltatás által visszaadott értéket.|header   |sztring|
|OCP-Apim-Subscription-Key|(üres)|Kötelező, ha a `Authorization` fejléc nincs megadva.|header|sztring|
|access_token|(üres)   |Alternatív módja átadni egy érvényes OAuth-jogkivonatot. A tulajdonosi jogkivonatot általában biztosított fejléccel `Authorization`. Websocket könyvtárak Ügyfélkód fejlécek beállítása nem teszik lehetővé. Ebben az esetben az ügyfél használhatja a `access_token` lekérdezési paraméter érvényes token át. Ha a hozzáférési token használatával hitelesíteni, ha `Authorization` fejléc nincs beállítva, majd `access_token` be kell állítani. Ha vannak beállítva, fejléc és a lekérdezési paraméter, a lekérdezési paraméter figyelmen kívül hagyja. Az ügyfelek csak egy módszert használja a jogkivonat.|lekérdezés|sztring|
|Előfizetés-kulcs|(üres)   |Az előfizetői át másik módszere. Websocket könyvtárak Ügyfélkód fejlécek beállítása nem teszik lehetővé. Ebben az esetben az ügyfél használhatja a `subscription-key` lekérdezési paraméter, át kell adnia egy érvényes előfizetési kulcsot. Ha egy előfizetési kulcsot használja a hitelesítéshez, ha `Ocp-Apim-Subscription-Key` fejléc nincs beállítva, akkor az előfizetői be kell állítani. Ha vannak beállítva, fejléc és a lekérdezési paraméter, a lekérdezési paraméter figyelmen kívül hagyja. Az ügyfelek csak használja a több módszert adja át a `subscription key`.|lekérdezés|sztring|
|X-ClientTraceId    |(üres)    |Egy ügyfél által létrehozott GUID egy kérelmet nyomon követéséhez használható. Megfelelő hibaelhárítási problémák, ügyfelek kell minden egyes kérelemmel adjon meg új értéket, és azt.<br/>Fejléc helyett ez az érték lekérdezési paraméterrel átadott `X-ClientTraceId`. Ha vannak beállítva, fejléc és a lekérdezési paraméter, a lekérdezési paraméter figyelmen kívül hagyja.|header|sztring|
|X-korrelációs azonosító|(üres)    |Egy ügyfél által generált azonosítójának korrelációját, ha több csatornán beszélgetés során használt. Beszéd fordítási több munkamenet engedélyezése a felhasználók közötti beszélgetések hozható létre. Ilyen esetben az összes speech fordítási munkamenetek segítségével az azonos korrelációs Azonosítót a csatornák összekapcsolása. Ez elősegíti a nyomkövetés és diagnosztika. Az azonosítót meg kell felelnie: `^[a-zA-Z0-9-_.]{1,64}$`<br/>Fejléc helyett ez az érték lekérdezési paraméterrel átadott `X-CorrelationId`. Ha vannak beállítva, fejléc és a lekérdezési paraméter, a lekérdezési paraméter figyelmen kívül hagyja.|header|sztring|
|X-ClientVersion|(üres)    |Az ügyfélalkalmazás verzióját azonosítja. Példa: "2.1.0.123".<br/>Fejléc helyett ez az érték lekérdezési paraméterrel átadott `X-ClientVersion`. Ha vannak beállítva, fejléc és a lekérdezési paraméter, a lekérdezési paraméter figyelmen kívül hagyja.|header|sztring|
|X-OsPlatform|(üres)   |A nevét és az ügyfélalkalmazás fut, az operációs rendszer verzióját azonosítja. Példák: "Android 5.0-s", "iOs 8.1.3", "a Windows 8.1".<br/>Fejléc helyett ez az érték lekérdezési paraméterrel átadott `X-OsPlatform`. Ha vannak beállítva, fejléc és a lekérdezési paraméter, a lekérdezési paraméter figyelmen kívül hagyja.|header|sztring|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Ok|Válaszmodellje|Fejlécek|
|:--|:--|:--|:--|
|101    |WebSocket-frissítés.|Modell Példaérték <br/> Objektum {}|X-RequestId:<br/>Hibaelhárítás céljából a kérelem azonosító érték.<br/>sztring|
|400    |Hibás kérés. Ellenőrizze, hogy biztosítsa azok érvényes bemeneti paramétereket. A válasz objektum tartalmazza a hiba részletes leírását.|||
|401    |Nem engedélyezett. Győződjön meg arról, hogy hitelesítő adatok vannak beállítva, akkor érvényesek, és, hogy van-e az Azure Data piaci előfizetés egy elérhető egyenlegű jó minősítésének.|||
|500    |Hiba történt. Ha a hiba továbbra is fennáll, jelentse az ügyfél nyomkövetési azonosító (X-ClientTraceId), vagy kérelemazonosító (X-kérelemazonosító).|||
|503    |A kiszolgáló átmenetileg nem érhető el. Ismételje meg a kérelmet. Ha a hiba továbbra is fennáll, jelentse az ügyfél nyomkövetési azonosító (X-ClientTraceId), vagy kérelemazonosító (X-kérelemazonosító).|||

    


    





    
    




    




    




    

            




        









