---
title: A Microsoft Translator Diktálásfelismerési API-referencia |} Microsoft Docs
titleSuffix: Cognitive Services
description: A Microsoft Translator Diktálásfelismerési API referenciadokumentációt tartalmaz.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 05/18/2018
ms.author: v-jansko
ms.openlocfilehash: be8faddf56158de3399713c41638c0b913b4627e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349711"
---
# <a name="microsoft-translator-speech-api"></a>Microsoft Translator Speech API

Ez a szolgáltatás egy streamelési API átírni conversational beszédfelismerés egyik nyelvről egy másik nyelv szöveggé kínál. Az API-t is integrálható a lefordított szöveg felolvasása szöveget beszéddé átalakító képességeket. A Microsoft Translator Diktálásfelismerési API lehetővé teszi, hogy helyzetekben, például a valós idejű fordítása beszélgetések Skype fordító látható módon.

A Microsoft Translator Diktálásfelismerési API-val ügyfélalkalmazások adatfolyam beszéd hang a szolgáltatáshoz, és adatfolyamon vissza szöveges eredmények, amelyek a felismert szöveg szerepel az adatforrás nyelvi és a fordítás a célként megadott nyelven. Szöveges eredményt automatikus beszéd felismerési (ASR) a bejövő hangadatfolyam mély Neurális hálózatokat technológiával alkalmazása hozzák létre. Egy új technika, hogy jobban azok tükrözzék, szándékos felhasználói adat Igaz szöveg nevű van további javítja a nyers automatikus rendszer-Helyreállítás kimenete. Például Igaz szöveg eltávolítja disfluencies (a hmms és coughs) és a visszaállítási megfelelő absztrakt és a nagybetűk. Maszkolnia, vagy zárja ki profanities is megtalálható. A megjelenítés és fordítási motorok kifejezetten képzett conversational beszéd kezelésére. A beszédfelismerés fordítási szolgáltatás csend észlelési egy utterance végén meghatározására használja. Hang tevékenységet megelőző szünet a szolgáltatás fogja adatfolyam vissza a befejezett utterance a végső eredményt. A szolgáltatás is küldhet vissza részleges eredmények, amelyek köztes elfpgads és egy utterance fordításainak folyamatban van. A végső eredmény elérése érdekében a szolgáltatás teszi lehetővé a célként megadott nyelveken szóbeli szövegből (szöveg-beszéd) beszéd szintetizálásához. Szöveg-beszéd átalakítás hang létrejön az ügyfél által megadott formátumban. WAV és MP3 formátuma nem érhető el.

A Microsoft Translator Diktálásfelismerési API kihasználja az ügyfél és a kiszolgáló közötti kétirányú kommunikációs csatornát biztosítanak a WebSocket protokoll. Egy alkalmazás ezeket a lépéseket, a szolgáltatás használatára lesz szükség:

## <a name="1-getting-started"></a>1. Első lépések
Szüksége lesz a Microsoft Translator szöveg API eléréséhez [Regisztráljon a Microsoft Azure](translator-speech-how-to-signup.md).

## <a name="2-authentication"></a>2. Hitelesítés

Az Előfizetés kulcs segítségével hitelesíteni. A Microsoft Translator Diktálásfelismerési API hitelesítési két módot támogat:

* **Access token használatával:** az alkalmazás beszerzése egy jogkivonatot a token szolgáltatástól. A Microsoft Translator Diktálásfelismerési API-előfizetés kulcs segítségével olyan hozzáférési jogkivonatot szerezni az kognitív hitelesítési szolgáltatások. A hozzáférési jogkivonat érvénytelen 10 percig. Új hozzáférési jogkivonat beszerzése 10 percenként, és tovább használni a ugyanazokkal a hozzáférési jogkivonat ismételt kérelmek ezen 10 percen belül.

* **Közvetlenül egy előfizetés kulccsal:** az alkalmazás át az Előfizetés kulcs értékként `Ocp-Apim-Subscription-Key` fejléc.

Az Előfizetés kulcs és a hozzáférési jogkivonat tekinti titkokat nézetből el kell rejteni.

## <a name="3-query-languages"></a>3. Lekérdezés nyelvek
**A lekérdezés a nyelvek erőforrás aktuális készletében lévő támogatott nyelveket.** A [nyelvek erőforrás](languages-reference.md) mutatja meg a meghatározott nyelv és hangok beszédfelismerési, szöveges fordítási és szöveg-beszéd átalakítás. Minden nyelven vagy a hang kap egy azonosító, amely a Microsoft Translator Diktálásfelismerési API nyelv vagy a hang azonosítására használ.

## <a name="4-stream-audio"></a>4. Az adatfolyam hang
**Nyissa meg a kapcsolatot, és a szolgáltatás hang adatfolyam megkezdése.** A szolgáltatás URL-cím `wss://dev.microsofttranslator.com/speech/translate`. Paraméterek és a szolgáltatás által várt hang formátumok folyamata az alábbiakban olvasható, a rendszer a `/speech/translate` műveletet. A paraméterek egyike segítségével a hozzáférési jogkivonat továbbítja a 2. lépésben fent.

## <a name="5-process-the-results"></a>5. Az eredmény feldolgozása
**Az eredmény vissza a szolgáltatás a folyamatos átviteli feldolgozása.** A dokumentációban ismertetett részleges eredmények, a végső eredmények és a szöveg-beszéd átalakítás, illetve hang-szegmensek formátuma a `/speech/translate` az alábbi műveletet.

Kódminták, amely tartalmazza a Microsoft Translator Diktálásfelismerési API használata érhetők el a [Microsoft Translator Github hely](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések

Egy munkamenet beszéd fordításhoz Establishes /speech/translate beolvasása

### <a name="connecting"></a>Csatlakozás
A szolgáltatáshoz való csatlakozáskor előtt tekintse át a később az itt megadott paraméterek listáját. Egy példa egy kérelem a következő:

`GET wss://dev.microsofttranslator.com/speech/translate?from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa&api-version=1.0`
`Ocp-Apim-Subscription-Key: {subscription key}`
`X-ClientTraceId: {GUID}`

A kérelem határozza meg, hogy szóbeli angol részére a szolgáltatás és olasz lefordítva. Egyes végső felismerési eredmények hoz létre egy szöveget beszéddé átalakító hang válasz az Elsa nevű nőivarú használatának. Figyelje meg, hogy a kérelem tartalmazza a hitelesítő adatokat a `Ocp-Apim-Subscription-Key header`. A kérelem is a következő ajánlott úgy, hogy egy globálisan egyedi azonosítót a fejlécben `X-ClientTraceId`. Egy ügyfélalkalmazás naplózza a nyomkövetési azonosító, hogy a probléma megoldásához, azok bekövetkezésekor használható.

### <a name="sending-audio"></a>Hang küldése
Miután létrejött a kapcsolat, az ügyfél kezdődik, a szolgáltatás hang adatfolyam. Az ügyfél elküldi az adattömbök hang. Egyes adattömbök bináris típusú Websocket üzenetet küldeni.

A hanghullám hang formátumban van hangbemenetet (HULLÁM, vagy több gyakran nevezik WAV miatt a fájlnév kiterjesztése). Az ügyfélalkalmazás egy csatorna, aláírt 16 bites PCM hang 16 kHz lekérdező kell adatfolyam. Az ügyfél részére első készletét tartalmazza a WAV-fejléc. A 44 bájtos fejléc számára egy csatornán aláírt 16 bites PCM adatfolyam 16 kHz lekérdező van:

|Eltolás|Érték|
|:---|:---|
|0 – 3|"RIFF"|
|4 – 7|0|
|8 – 11|"WAVE"|
|12 – 15|"fmt"|
|16 - 19|16|
|20 - 21.|1|
|22 – 23|1|
|24 - 27|16000|
|28 - 31|32000|
|32 - 33|2|
|34 - 35|16|
|36 - 39|az "adatok"|
|40 - 43|0|

Figyelje meg, hogy a fájlok összesített mérete (bájt 4 – 7) és a "data" (bájt 40-43) mérete nulla értékre van beállítva. Ez az OK gombra a folyamatos átviteli forgatókönyvhöz, ahol a teljes mérete nem feltétlenül ismert előzetes megfizetése esetén.

A WAV (RIFF) fejléc elküldése, után az ügyfél elküldi a hang adattömböket írnak. Az ügyfél általában fog adatfolyam-jelző (pl. adatfolyama 100 MS egyszerre hang) rögzített időtartamú rögzített méretű adattömböket.

### <a name="final-result"></a>Végső eredményére
Végső Beszéd felismerés eredményeként jön létre egy utterance végén. A szolgáltatás egy eredményt a Text típusú WebSocket üzenet ügyfélre kerül továbbításra. Az üzenet tartalma a JSON-szerializálás egy objektum a következő tulajdonságokkal:

* `type`: A karakterlánc-konstansra azonosításához eredményének típusa. A végső eredmények végső értéke.
* `id`: A felismerési eredmények rendelt azonosító karakterlánc.
* `recognition`: Az adatforrás nyelvi felismert szöveget. A szöveg egy hamis felismerés esetén üres karakterlánc lehet.
* `translation`: A megadott nyelv felismert szöveg lefordítva.
* `audioTimeOffset`: A ticks felismerés elindítása idő eltolása (1 osztásjelek = 100 nanoszekundumban). Az eltolás értéke kezdete streaming viszonyítva.
* `audioTimeSize`: A felismerési száma (100 nanoszekundumban) időtartama.
* `audioStreamPosition`: A felismerési elindítása bájt eltolását. Az eltolás van, az adatfolyam kezdete viszonyítva.
* `audioSizeBytes`: Mérete a felismerési bájtban.

Vegye figyelembe, hogy a felismerés a hangadatfolyam elhelyezéséhez nem szerepel az eredményeket alapértelmezés szerint. A `TimingInfo` szolgáltatás meg kell adni az ügyfél (lásd: `features` paraméter).

Egy minta végeredményt a következőképpen történik:

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

### <a name="partial-result"></a>Részleges eredménye
Részleges vagy köztes beszéd felismerési eredmények nem átvitt az ügyfél alapértelmezés szerint. Az ügyfél a szolgáltatások lekérdezési paraméter segítségével igényelhetnek őket.

Részleges eredményt ügyfélre szöveg típusú WebSocket üzenetben kerül továbbításra a szolgáltatás. Az üzenet tartalma a JSON-szerializálás egy objektum a következő tulajdonságokkal:

* `type`: A karakterlánc-konstansra azonosításához eredményének típusa. A részleges eredmények részleges értéke.
* `id`: A felismerési eredmények rendelt azonosító karakterlánc.
* `recognition`: Az adatforrás nyelvi felismert szöveget.
* `translation`: A megadott nyelv felismert szöveg lefordítva.
* `audioTimeOffset`: A ticks felismerés elindítása idő eltolása (1 osztásjelek = 100 nanoszekundumban). Az eltolás értéke kezdete streaming viszonyítva.
* `audioTimeSize`: A felismerési száma (100 nanoszekundumban) időtartama.
* `audioStreamPosition`: A felismerési elindítása bájt eltolását. Az eltolás van, az adatfolyam kezdete viszonyítva.
* `audioSizeBytes`: Mérete a felismerési bájtban.

Vegye figyelembe, hogy a felismerés a hangadatfolyam elhelyezéséhez nem szerepel az eredményeket alapértelmezés szerint. A TimingInfo szolgáltatást meg kell adni az ügyfél (lásd a szolgáltatások paraméter).

Egy minta végeredményt a következőképpen történik:

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

### <a name="text-to-speech"></a>Szöveg-beszéd átalakítás
Ha a szöveg-beszéd átalakítás funkció engedélyezve van (lásd: `features` paraméterhez), a végeredményt a hang szóbeli lefordított szöveg követ. Hang adatok pedig és az ügyfélnek küldött a szolgáltatás bináris típusú Websocket-üzenetek sorozatot. Egy ügyfél az egyes üzenetek FIN bit ellenőrzésével észleli az adatfolyam végét. Az utolsó üzenet bináris fog rendelkezni a FIN bit beállítása egy, az adatfolyam végét jelzi. Az adatfolyam formátuma értékének függ a `format` paraméter.

### <a name="closing-the-connection"></a>A kapcsolat bezárása
Egy ügyfélalkalmazás hang adatfolyam befejeződött, és az utolsó végeredményt kapott, a kapcsolat által a WebSocket záró kézfogás kezdeményezése kell azonnal. Nincsenek feltételeket, amelyek hatására a kiszolgáló képes megszakítani a kapcsolatot. A következő WebSocket lezárt kódok előfordulhat, hogy fogadja az ügyfél:

* `1003 - Invalid Message Type`: A kiszolgáló megszakítja a kapcsolatot a kapott adattípus nem fogad el, mert. Ez általában akkor fordul elő, amikor bejövő hang nem indul el egy megfelelő fejlécet.
* `1000 - Normal closure`: A kapcsolat bezárta a kérés teljesítése után. A kiszolgáló a kapcsolat bezárul: fogadásakor nincsenek hang az ügyfélről egy hosszú ideig; Ha hosszabb ideig; csend továbbítja adatfolyamként Amikor a munkamenet eléri az engedélyezett maximális időtartamot (körülbelül 90 percig).
* `1001 - Endpoint Unavailable`: Azt jelzi, hogy a kiszolgáló nem lesz elérhető. Ügyfél alkalmazás megkísérli az újbóli próbálkozások számát korlátozást kapcsolódjon újra.
* `1011 - Internal Server Error`: A kapcsolat a kiszolgáló hibája miatt a kiszolgáló bezárul.

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|A paraméter típusa|Adattípus|
|:---|:---|:---|:---|:---|
|API-verzió|1.0|Az API-t, az ügyfél által kért verzióját. Két érték engedélyezett: `1.0`.|lekérdezés   |sztring|
|forrás:|(üres)   |Meghatározza a bejövő beszéd nyelvét. Az érték egy, a nyelvi azonosítót a `speech` hatókörben található a nyelvek API válaszát.|lekérdezés|sztring|
|erre:|(üres)|Meghatározza a transcribed szöveg lefordítani nyelvét. Az érték egy, a nyelvi azonosítót a `text` hatókörben található a nyelvek API válaszát.|lekérdezés|sztring|
|funkciókkal|(üres)   |Az ügyfél által kijelölt szolgáltatások vesszővel tagolt készlete. Rendelkezésre álló funkciók a következők:<ul><li>`TextToSpeech`: Megadja, hogy a szolgáltatás kell a végső lefordított mondat lefordított hang.</li><li>`Partial`: Adja meg, hogy a szolgáltatás köztes felismerési eredmények kell visszaadnia, amíg a hang adatfolyam-e a szolgáltatás.</li><li>`TimingInfo`: Adja meg, hogy a szolgáltatás minden egyes felismerés társított információk kell visszaadnia.</li></ul>Például egy ügyfél kellene megadnia `features=partial,texttospeech` hiányos eredményeket és a szöveg-beszéd átalakítás, de a időzítési adatokat fogadhat. Vegye figyelembe, hogy az ügyfél mindig átvitt végleges eredmények.|lekérdezés|sztring|
|hangalámondás|(üres)|Milyen hang használandó szöveget beszéddé átalakító megjelenítési a lefordított szöveg azonosítja. Az érték a nyelvek API a válaszban szereplő szöveg-beszéd átalakítás hatókörből hang azonosítók egyike. Egy hang nincs megadva a rendszer automatikusan az válasszon egy Ha a szöveg-beszéd átalakítás szolgáltatás engedélyezve van.|lekérdezés|sztring|
|Formátumban|(üres)|Megadja a szolgáltatás által visszaadott szöveg-beszéd átalakítás hangadatfolyam formátumát. Rendelkezésre álló lehetőségek közül választhat:<ul><li>`audio/wav`: Hullám hangadatfolyam. Ügyfél által használandó a WAV-fejléc a hangformátum megfelelően értelmezni. Szöveg-beszéd átalakítás WAV hang 16 bites, egyetlen csatorna PCM 24kHz vagy 16kHz mintavételi sebességet.</li><li>`audio/mp3`: MP3 hangadatfolyam.</li></ul>Az alapértelmezett szint a `audio/wav`.|lekérdezés|sztring|
|ProfanityAction    |(üres)    |Itt adhatja meg, hogyan kezelje a szolgáltatás a profanities a beszédfelismerés ismert. Érvényes műveletek a következők:<ul><li>`NoAction`: Profanities megmaradnak, mert a.</li><li>`Marked`: A mutatóhoz profanities helyére kerülnek. Lásd: `ProfanityMarker` paraméter.</li><li>`Deleted`: Profanities törlődnek. Például ha a word `"jackass"` Profanitás, a kódot a rendszer `"He is a jackass."` lesz `"He is a .".`</li></ul>Alapértelmezés szerint be van jelölve.|lekérdezés|sztring|
|ProfanityMarker|(üres)    |Megadja, hogyan észlelt profanities kezeli, mikor `ProfanityAction` értéke `Marked`. Érvényes lehetőségek közül választhat:<ul><li>`Asterisk`: A karakterláncot profanities váltják `***`. Például ha a word `"jackass"` Profanitás, a kódot a rendszer `"He is a jackass."` lesz `"He is a ***.".`</li><li>`Tag`: Egy XML-címke Profanitás Profanitás tette. Például ha a word `"jackass"` Profanitás, a kódot a rendszer `"He is a jackass."` lesz `"He is a <profanity>jackass</profanity>."`.</li></ul>Az alapértelmezett érték `Asterisk`.|lekérdezés|sztring|
|Engedélyezés|(üres)  |Meghatározza azt az értéket, az ügyfél tulajdonosi jogkivonat. Az előtag használata `Bearer` értékét követi a `access_token` a hitelesítési jogkivonat-szolgáltatás által visszaadott érték.|header   |sztring|
|Az OCP-Apim-előfizetés-kulcs|(üres)|Kötelező, ha a `Authorization` fejléc nem lett megadva.|header|sztring|
|access_token|(üres)   |Más módja felelt meg egy érvényes OAuth jogkivonatot. A tulajdonosi jogkivonattal által általában biztosított fejléc `Authorization`. Egyes websocket dokumentumtárak nem teszik lehetővé az Ügyfélkód fejlécek beállítása. Ilyen esetben az ügyfél által használt a `access_token` lekérdezésparaméter felelt meg egy érvényes tokent. Ha a hozzáférési token használatával hitelesítést végezni, ha `Authorization` fejléc nincs beállítva, majd `access_token` be kell állítani. Ha mind a fejlécet, és a lekérdezési paraméter van beállítva, a következő lekérdezésparaméter figyelmen kívül hagyja. Ügyfelek továbbítja a jogkivonatot egy metódus csak használja.|lekérdezés|sztring|
|Előfizetés-kulcs|(üres)   |Hozzáférési kulcs előfizetés másik módja. Egyes websocket dokumentumtárak nem teszik lehetővé az Ügyfélkód fejlécek beállítása. Ilyen esetben az ügyfél által használt a `subscription-key` lekérdezésparaméter felelt meg egy érvényes előfizetéssel kulcsot. Ha az Előfizetés kulcs használata a hitelesítéshez, ha `Ocp-Apim-Subscription-Key` fejléc nincs beállítva, akkor az Előfizetés-kulcs be kell állítani. Ha mind a fejlécet, és a lekérdezési paraméter van beállítva, a következő lekérdezésparaméter figyelmen kívül hagyja. Az ügyfelek csak használja egy metódus adja át a `subscription key`.|lekérdezés|sztring|
|X-ClientTraceId    |(üres)    |Ügyfél által létrehozott GUID használni egy kérelmet nyomon követésére. Problémák megfelelő hibaelhárítási, az ügyfelek kell minden egyes kérelemmel adjon meg új értéket, és jelentkezzen azt.<br/>Fejléc helyett függvénynek adható át ezt az értéket a következő lekérdezésparaméter `X-ClientTraceId`. Ha mind a fejlécet, és a lekérdezési paraméter van beállítva, a következő lekérdezésparaméter figyelmen kívül hagyja.|header|sztring|
|X-CorrelationId|(üres)    |Egy ügyfél által generált azonosítójának több csatornát a beszélgetés összefüggést. Több beszéd fordítási munkamenet engedélyezése a felhasználók között beszélgetések is létrehozható. Ilyen esetben az összes beszéd fordítási munkamenetek segítségével ugyanazt a korrelációs Azonosítót a csatornák alkalmazássá. Ez elősegíti a nyomkövetés és diagnosztika. A-azonosítót meg kell felelnie: `^[a-zA-Z0-9-_.]{1,64}$`<br/>Fejléc helyett függvénynek adható át ezt az értéket a következő lekérdezésparaméter `X-CorrelationId`. Ha mind a fejlécet, és a lekérdezési paraméter van beállítva, a következő lekérdezésparaméter figyelmen kívül hagyja.|header|sztring|
|X-ClientVersion|(üres)    |Azonosítja az ügyfél alkalmazás verziója. Példa: "2.1.0.123".<br/>Fejléc helyett függvénynek adható át ezt az értéket a következő lekérdezésparaméter `X-ClientVersion`. Ha mind a fejlécet, és a lekérdezési paraméter van beállítva, a következő lekérdezésparaméter figyelmen kívül hagyja.|header|sztring|
|X-OsPlatform|(üres)   |Azonosítja a nevét és az ügyfélalkalmazás futó operációs rendszer verziója. Példák: "Android 5.0-s", "iOs 8.1.3", "Windows 8.1".<br/>Fejléc helyett függvénynek adható át ezt az értéket a következő lekérdezésparaméter `X-OsPlatform`. Ha mind a fejlécet, és a lekérdezési paraméter van beállítva, a következő lekérdezésparaméter figyelmen kívül hagyja.|header|sztring|

### <a name="response-messages"></a>A válaszüzenetek

|HTTP-állapotkód|Ok|Válasz modell|Fejlécek|
|:--|:--|:--|:--|
|101    |WebSocket frissítését.|Modell Példaérték <br/> objektum {}|X-kérelemazonosító<br/>A kérelem hibaelhárítási célból azonosító érték.<br/>sztring|
|400    |Hibás kérés. Ellenőrizze, érvényes azok bemeneti paramétereket. A válasz objektum tartalmazza a hiba részletes leírását.|||
|401    |Nem engedélyezett. Győződjön meg arról, hogy hitelesítő adatok vannak beállítva, akkor érvényesek, és, hogy az Azure Data piaci előfizetés van, és egy elérhető egyenlegét jó állandó.|||
|500    |Hiba történt. Ha a probléma továbbra is fennáll, jelentse az ügyfél nyomkövetési azonosító (X-ClientTraceId), vagy a kérelem azonosítója (X-kérelemazonosító).|||
|503    |A kiszolgáló ideiglenesen nem érhető el. Próbálja megismételni a kérelmet. Ha a probléma továbbra is fennáll, jelentse az ügyfél nyomkövetési azonosító (X-ClientTraceId), vagy a kérelem azonosítója (X-kérelemazonosító).|||

    


    





    
    




    




    




    

            




        









