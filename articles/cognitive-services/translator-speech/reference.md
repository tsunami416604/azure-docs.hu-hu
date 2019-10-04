---
title: Translator Speech API referenciája
titleSuffix: Azure Cognitive Services
description: A Translator Speech API dokumentációja.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: reference
ms.date: 05/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9d2f78d05de6b966dd872e0b57a90d1c8e890975
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965376"
---
# <a name="translator-speech-api"></a>Beszédfordító API

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Ez a szolgáltatás egy streaming API-t biztosít a társalgási beszédek az egyik nyelvről egy másik nyelv szöveggé való átírásához. Az API emellett a szöveg-beszéd funkciókat is integrálja a lefordított szöveg visszaírásához. A Translator Speech API olyan forgatókönyveket tesz lehetővé, mint a Skype Translatorben látható beszélgetések valós idejű fordítása.

A Translator Speech API segítségével az ügyfélalkalmazások streamet továbbítanak a szolgáltatásnak, és szöveges eredményekből álló streamet kapnak, amely tartalmazza a forrás nyelvén felismert szöveget, valamint a fordítást a célnyelv nyelvén. A szövegeredmények a mély neurális hálózatok által működtetett automatikus beszédfelismerés (ASR) a bejövő audiostreamre való alkalmazásával jönnek létre. A nyers ASR kimenetét tovább fejleszti egy TrueText nevű új technikával, hogy jobban tükrözze a felhasználói szándékot. Például a TrueText eltávolítja a disfluencies (a hmms és a köhögést), és helyreállítja a megfelelő írásjeleket és a nagybetűket. Lehetőség van a profanitás kitakarására vagy eltávolítására is. A felismerő- és fordítómotorok kifejezetten a beszélgetések kezelésére lettek kifejlesztve. A beszédfelismerési szolgáltatás a csendek észlelésével határozza meg a lemondás végét. A beszédhangokban beállt szünet esetén a szolgáltatás visszastreameli a végleges eredményt a kész kimondott szöveghez. A szolgáltatás visszaküldhet részleges eredményeket is, amelyek köztes felismerést és fordítást biztosítanak a folyamatban lévő kimondott szöveghez. A végső eredményekhez a szolgáltatás lehetővé teszi a beszéd (szöveg – beszéd) szövegének szintetizálása a megcélzott nyelveken lévő szóbeli szöveg alapján. A szövegfelolvasás audioanyaga az ügyfél által megadott hangformátumban jön létre. A WAV és az MP3 formátum érhető el.

Translator Speech API kihasználja a WebSocket protokollt, hogy teljes kétirányú kommunikációs csatornát biztosítson az ügyfél és a kiszolgáló között. Egy alkalmazásnak a következő lépéseket kell használnia a szolgáltatás használatához:

## <a name="1-getting-started"></a>1. Első lépések
A Translator Text APIhoz való hozzáféréshez [regisztrálnia](translator-speech-how-to-signup.md)kell a Microsoft Azure.

## <a name="2-authentication"></a>2. Authentication

A hitelesítéshez használja az előfizetés kulcsát. A Translator Speech API két hitelesítési módot támogat:

* **Hozzáférési jogkivonat használata:** Az alkalmazásban szerezze be a jogkivonat-szolgáltatás hozzáférési jogkivonatát. A Translator Speech API előfizetési kulcsával szerezzen be hozzáférési jogkivonatot az Azure Cognitive Services Authentication szolgáltatásból. A hozzáférési jogkivonat 10 percig érvényes. 10 percenként szerezzen be egy új hozzáférési jogkivonatot, és továbbra is ugyanazt a hozzáférési jogkivonatot használja az ismételt kérelmekhez ezen a 10 percen belül.

* **Előfizetés-kulcs közvetlen használata:** Az alkalmazásban adja át az előfizetési kulcsot a fejlécben `Ocp-Apim-Subscription-Key` szereplő értékként.

Kezelje az előfizetési kulcsot és a hozzáférési jogkivonatot olyan titokként, amelyeknek el kell rejteniük a nézetet.

## <a name="3-query-languages"></a>3. Lekérdezési nyelvek
**A nyelvek erőforrás lekérdezése a támogatott nyelvek aktuális készletéhez.** A [nyelvek erőforrás](languages-reference.md) a beszédfelismeréshez rendelkezésre álló nyelveket és hangokat teszi elérhetővé szöveg-fordításhoz és szöveg-beszéd megjelenítéshez. Minden nyelv vagy hang olyan azonosítóval rendelkezik, amelyet a Translator Speech API az adott nyelv vagy hang azonosítására használ.

## <a name="4-stream-audio"></a>4. Stream hang
**Nyisson meg egy kapcsolódást, és kezdje el közvetíteni a hangot a szolgáltatásnak.** A szolgáltatás URL- `wss://dev.microsofttranslator.com/speech/translate`címe:. A szolgáltatás által várt paramétereket és hangformátumokat lásd alább, a `/speech/translate` műveletben. A paraméterek egyike a fenti 2. lépésben megadott hozzáférési jogkivonat átadására szolgál.

## <a name="5-process-the-results"></a>5. Az eredmények feldolgozása
**Dolgozza fel a szolgáltatásból továbbított eredményeket.** A részleges eredmények, a végső eredmények és a szöveg-hang típusú hangszegmensek formátumát az alábbi `/speech/translate` művelet dokumentációja ismerteti.

A Translator Speech API használatát bemutató mintakód a [Microsoft Translator GitHub webhelyéről](https://github.com/MicrosoftTranslator)érhető el.

## <a name="implementation-notes"></a>Implementációs megjegyzések

/Speech/Translate beolvasása – munkamenet létrehozása a beszédfelismerési fordításhoz

### <a name="connecting"></a>Csatlakozás
A szolgáltatáshoz való csatlakozás előtt tekintse át a szakasz későbbi részében megadott paraméterek listáját. Példa a kérelemre:

`GET wss://dev.microsofttranslator.com/speech/translate?from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa&api-version=1.0`
`Ocp-Apim-Subscription-Key: {subscription key}`
`X-ClientTraceId: {GUID}`

A kérelem azt adja meg, hogy a beszélt angol nyelv továbbítva lesz a szolgáltatásnak, és az olasz nyelvre lesz lefordítva. Minden végső felismerési eredmény egy szöveges – beszéd hangválaszt fog eredményezni az Elsa nevű női hangon. Figyelje meg, hogy a kérelem tartalmazza a `Ocp-Apim-Subscription-Key header`hitelesítő adatokat. A kérelem a globálisan egyedi azonosító a fejlécben `X-ClientTraceId`való beállításával is az ajánlott eljárást követi. Az ügyfélalkalmazás naplózni fogja a nyomkövetési azonosítót, hogy a rendszer felhasználja a problémák megoldására.

### <a name="sending-audio"></a>Hang küldése
A kapcsolódás után az ügyfél megkezdi a hang folyamatos átvitelét a szolgáltatásba. Az ügyfél hangtömbökbe küldi a hangot. A rendszer minden adatrészletet bináris típusú WebSocket-üzenet használatával továbbít.

A hangbemenet a hullámforma hangfájl-formátumában (WAVE vagy ismertebb nevén WAV, a fájlnév-kiterjesztés miatt). Az ügyfélalkalmazás egyetlen csatornán, a 16 kHz-es aláírt 16bit PCM-hangmintán keresztül kell továbbítania. Az ügyfél által továbbított bájtok első készlete tartalmazni fogja a WAV-fejlécet. A 16 kHz-es 16 bites PCM-adatfolyam 44 bájtos fejléce a következő:

|Offset|Value|
|:---|:---|
|0 - 3|RIFF|
|4 - 7|0|
|8 - 11|WAVE|
|12 - 15|FMT Package|
|16 - 19|16|
|20 - 21|1|
|22 - 23|1|
|24 - 27|16000|
|28 - 31|32000|
|32 – 33|2|
|34 – 35|16|
|36 – 39|adatok|
|40 - 43|0|

Figyelje meg, hogy a teljes fájlméret (4-7 bájt) és az "adat" (40-43 bájt) mérete nulla. Ez az adatfolyam-forgatókönyv esetében OK, ahol a teljes méret nem feltétlenül ismert.

A WAV (RIFF) fejléc elküldése után az ügyfél elküldi a hangadatok részleteit. Az ügyfél általában rögzített méretű adattömböket továbbít (például a stream 100ms egyszerre).

### <a name="signal-the-end-of-the-utterance"></a>A Kimondás végének jelzése
A Translator Speech API visszaadja a hangadatfolyam átiratát és fordítását, ahogy a hangot küldi. A végső átiratot, a végső fordítást és a lefordított hangot a rendszer csak a teljes összeg lejárta után adja vissza. Bizonyos esetekben előfordulhat, hogy kényszeríteni szeretné a lemondás végét. Kérjük, küldjön 2,5 másodperces csendet, hogy kikényszerítse a lemondás végét.

### <a name="final-result"></a>Végső eredmény
A végleges beszédfelismerés eredményét a rendszer a teljes összeg végén hozza létre. A szolgáltatás egy szöveg típusú WebSocket-üzenettel továbbítja a szolgáltatást az ügyfélnek. Az üzenet tartalma egy objektum JSON-szerializálása a következő tulajdonságokkal:

* `type`: Karakterlánc-konstans az eredmény típusának azonosításához. Az érték végleges a végső eredményekhez.
* `id`: A felismerési eredményhez rendelt karakterlánc-azonosító.
* `recognition`: Felismert szöveg a forrás nyelvén. Hamis felismerés esetén a szöveg üres karakterlánc lehet.
* `translation`: A felismert szöveg lefordítása a célként megadott nyelven.
* `audioTimeOffset`: Az elismerés kezdetének időbeli eltolása a kullancsokban (1 Tick = 100 nanoszekundumban). Az eltolás a streaming kezdetéhez képest relatív.
* `audioTimeSize`: Az elismerés időtartamát jelölő (100 nanoszekundumban).
* `audioStreamPosition`: Az elismerés kezdetének bájtban megadott eltolása. Az eltolás a stream elejéhez viszonyítva.
* `audioSizeBytes`: Az elismerés bájtban kifejezett mérete.

Vegye figyelembe, hogy a rendszer alapértelmezés szerint nem tartalmazza az elismerést az audio streamben. A `TimingInfo` szolgáltatást az ügyfélnek kell kiválasztania ( `features` lásd a paramétert).

A minta végső eredménye a következő:

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

### <a name="partial-result"></a>Részleges eredmény
A részleges vagy köztes beszédfelismerési eredményeket a rendszer alapértelmezés szerint nem továbbítja az ügyfélnek. Az ügyfél a szolgáltatások lekérdezési paraméter használatával kérheti le őket.

A részleges eredmény továbbítása a szolgáltatásból az ügyfélnek egy szöveg típusú WebSocket-üzenet használatával történik. Az üzenet tartalma egy objektum JSON-szerializálása a következő tulajdonságokkal:

* `type`: Karakterlánc-konstans az eredmény típusának azonosításához. Az érték részleges a részleges eredményekhez.
* `id`: A felismerési eredményhez rendelt karakterlánc-azonosító.
* `recognition`: Felismert szöveg a forrás nyelvén.
* `translation`: A felismert szöveg lefordítása a célként megadott nyelven.
* `audioTimeOffset`: Az elismerés kezdetének időbeli eltolása a kullancsokban (1 Tick = 100 nanoszekundumban). Az eltolás a streaming kezdetéhez képest relatív.
* `audioTimeSize`: Az elismerés időtartamát jelölő (100 nanoszekundumban).
* `audioStreamPosition`: Az elismerés kezdetének bájtban megadott eltolása. Az eltolás a stream elejéhez viszonyítva.
* `audioSizeBytes`: Az elismerés bájtban kifejezett mérete.

Vegye figyelembe, hogy a rendszer alapértelmezés szerint nem tartalmazza az elismerést az audio streamben. Az TimingInfo szolgáltatást az ügyfélnek kell kiválasztania (lásd a szolgáltatások paramétert).

A minta végső eredménye a következő:

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

### <a name="text-to-speech"></a>Szövegfelolvasás
Ha a szöveg-beszéd funkció engedélyezve van (lásd `features` az alábbi paramétert), a végeredményt a beszélt szöveg hangja követi. A hangadatokat a rendszer a bináris típusú WebSocket-üzenetek sorrendjében tömöríti és elküldi a szolgáltatástól az ügyfélnek. Az ügyfelek az egyes üzenetek FIN-bit ellenőrzésével azonosítják a stream végét. Az utolsó bináris üzenetben a FIN bit egy értékre lesz állítva, hogy jelezze az adatfolyam végét. Az adatfolyam formátuma a `format` paraméter értékétől függ.

### <a name="closing-the-connection"></a>A kapcsolatok bezárása
Ha egy ügyfélalkalmazás befejezte a hangátvitelt, és az utolsó végeredményt kapta, a WebSocket záró kézfogásának elindításával zárja be a kapcsolódást. Vannak olyan feltételek, amelyek hatására a kiszolgáló leállítja a kapcsolódást. Az ügyfél a következő WebSocket zárt kódokat is fogadhatja:

* `1003 - Invalid Message Type`: A kiszolgáló megszakítja a kapcsolatokat, mert nem tudja fogadni a kapott adattípust. Ez általában akkor fordul elő, ha a bejövő hang nem megfelelő fejléccel kezdődik.
* `1000 - Normal closure`: A kérés teljesítése után a kapcsolatok bezárultak. A kiszolgáló lezárta a csatlakozást: ha hosszabb ideig nem érkezik hang az ügyféltől; Ha a csendet hosszabb ideig továbbítják, Ha egy munkamenet eléri a maximálisan engedélyezett időtartamot (körülbelül 90 perc).
* `1001 - Endpoint Unavailable`: Azt jelzi, hogy a kiszolgáló elérhetetlenné válik. Az ügyfélalkalmazás megkísérelheti az újrakapcsolódást az újrapróbálkozások számának korlátozásával.
* `1011 - Internal Server Error`: A kiszolgáló a kiszolgáló hibája miatt bezárta a kapcsolatokat.

### <a name="parameters"></a>Paraméterek

|Paraméter|Value|Leírás|Paraméter típusa|Adattípus|
|:---|:---|:---|:---|:---|
|api-version|1.0|Az ügyfél által kért API-verzió. Az engedélyezett értékek a `1.0`következők:.|query   |Karakterlánc|
|from|üres   |Megadja a bejövő beszéd nyelvét. Az érték a nyelvek API válaszában található `speech` hatókörből származó nyelvi azonosítók egyike.|query|Karakterlánc|
|to|üres|Meghatározza az átirat szövegének fordítási nyelvét. Az érték a nyelvek API válaszában található `text` hatókörből származó nyelvi azonosítók egyike.|query|Karakterlánc|
|funkciókkal|üres   |Az ügyfél által kiválasztott funkciók vesszővel tagolt készlete. Az elérhető szolgáltatások a következők:<ul><li>`TextToSpeech`: Megadja, hogy a szolgáltatásnak vissza kell adnia a lefordított utolsó mondat lefordított hangját.</li><li>`Partial`: azt adja meg, hogy a szolgáltatásnak közbenső felismerési eredményeket kell visszaadnia, amikor a hang a szolgáltatás felé áramlik.</li><li>`TimingInfo`: Megadja, hogy a szolgáltatásnak az egyes felismerésekhez kapcsolódó időzítési információkat kell visszaadnia.</li></ul>Az ügyfél például úgy határozhat meg `features=partial,texttospeech` , hogy a részleges eredményeket és a szöveg és a beszéd közötti időt kapja, de nincs időzítési információ. Vegye figyelembe, hogy a végleges eredményeket a rendszer mindig továbbítja az ügyfélnek.|query|Karakterlánc|
|Hang|üres|A lefordított szöveg szöveg-beszéd megjelenítéséhez használandó hang azonosítása. Az érték a languages API válaszában a TTS hatókörből származó hangazonosítók egyike. Ha nincs megadva hang, a rendszer automatikusan kiválasztja az egyiket, ha a szöveg-beszéd funkció engedélyezve van.|query|Karakterlánc|
|format|üres|Megadja a szolgáltatás által visszaadott szöveg-beszéd típusú hangadatfolyam formátumát. Az elérhető lehetőségek:<ul><li>`audio/wav`: Hullámforma-adatfolyam. Az ügyfélnek a WAV-fejlécet kell használnia a hangformátum megfelelő értelmezéséhez. A szöveg és a beszéd közötti WAV hang 16 bites, egycsatornás PCM, 24kHz vagy 16kHz mintavételi sebességgel.</li><li>`audio/mp3`: MP3 audio stream.</li></ul>Az alapértelmezett szint a `audio/wav`.|query|Karakterlánc|
|ProfanityAction    |üres    |Meghatározza, hogy a szolgáltatás hogyan kezelje a beszédben felismert trágár szavakat. Az érvényes műveletek a következők:<ul><li>`NoAction`: A káromkodások a következőképpen maradnak.</li><li>`Marked`: A káromkodásokat jelölővel kell helyettesíteni. Lásd `ProfanityMarker` : paraméter.</li><li>`Deleted`: A káromkodások törlődnek. Ha például a szó `"jackass"` káromkodásként van kezelve, a kifejezés `"He is a jackass."` a következő lesz:`"He is a .".`</li></ul>Az alapértelmezett érték van megjelölve.|query|Karakterlánc|
|ProfanityMarker|üres    |Meghatározza, `ProfanityAction` `Marked`hogy az észlelt káromkodások hogyan legyenek kezelve, amikor a be van állítva. Az érvényes beállítások a következők:<ul><li>`Asterisk`: A káromkodások helyébe a karakterlánc `***`kerül. Ha például a szó `"jackass"` káromkodásként van kezelve, a kifejezés `"He is a jackass."` a következő lesz:`"He is a ***.".`</li><li>`Tag`: A káromkodást egy trágár XML-címke veszi körül. Ha például a szó `"jackass"` káromkodásként van kezelve, a kifejezés `"He is a jackass."` lesz `"He is a <profanity>jackass</profanity>."`.</li></ul>A mező alapértelmezett értéke: `Asterisk`.|query|Karakterlánc|
|Authorization|üres  |Az ügyfél tulajdonosi jogkivonatának értékét adja meg. Használja az előtagot `Bearer` `access_token` , majd a hitelesítési jogkivonat szolgáltatás által visszaadott érték értékét.|header   |Karakterlánc|
|OCP-Apim-Subscription-Key|üres|Kötelező, ha `Authorization` nincs megadva a fejléc.|header|Karakterlánc|
|access_token|üres   |Alternatív módszer egy érvényes OAuth hozzáférési jogkivonat átadására. A tulajdonosi jogkivonat általában fejléctel `Authorization`van ellátva. Egyes WebSocket-kódtárak nem engedélyezik az ügyfélalkalmazások számára a fejlécek beállítását. Ilyen esetben az ügyfél használhatja a `access_token` lekérdezési paramétert egy érvényes jogkivonat átadására. Ha hozzáférési tokent használ a hitelesítéshez, ha `Authorization` a fejléc nincs beállítva, akkor `access_token` be kell állítani. Ha a fejléc és a lekérdezési paraméter is be van állítva, a rendszer figyelmen kívül hagyja a lekérdezési paramétert. Az ügyfeleknek csak egyetlen módszert kell használniuk a jogkivonat átadásához.|query|Karakterlánc|
|előfizetés – kulcs|üres   |Alternatív módszer az előfizetési kulcs továbbítására. Egyes WebSocket-kódtárak nem engedélyezik az ügyfélalkalmazások számára a fejlécek beállítását. Ilyen esetben az ügyfél használhatja a `subscription-key` lekérdezési paramétert, hogy érvényes előfizetési kulcsot adjon át. Ha előfizetés-kulcsot használ a hitelesítéshez, ha `Ocp-Apim-Subscription-Key` a fejléc nincs beállítva, akkor az előfizetés-kulcsot be kell állítani. Ha a fejléc és a lekérdezési paraméter is be van állítva, a rendszer figyelmen kívül hagyja a lekérdezési paramétert. Az ügyfeleknek csak egyetlen módszert kell használniuk a `subscription key`továbbítására.|query|Karakterlánc|
|X-ClientTraceId    |üres    |Egy ügyfél által generált GUID, amely egy kérés nyomkövetésére szolgál. A problémák megfelelő hibaelhárítása érdekében az ügyfeleknek minden kéréssel új értéket kell megadniuk, és be kell jelentkezniük.<br/>Fejléc használata helyett ezt az értéket a lekérdezési paraméterrel `X-ClientTraceId`lehet átadni. Ha a fejléc és a lekérdezési paraméter is be van állítva, a rendszer figyelmen kívül hagyja a lekérdezési paramétert.|header|Karakterlánc|
|X-CorrelationId|üres    |Ügyfél által generált azonosító, amely egy beszélgetés több csatornájának összekapcsolására szolgál. Több beszéd fordítási munkamenet hozható létre a felhasználók közötti beszélgetések engedélyezéséhez. Ilyen esetben az összes beszéd-fordítási munkamenet ugyanazt a korrelációs azonosítót használja a csatornák összekapcsolásához. Ez megkönnyíti a nyomkövetést és a diagnosztikát. Az azonosítónak a következőknek kell megfelelnie:`^[a-zA-Z0-9-_.]{1,64}$`<br/>Fejléc használata helyett ezt az értéket a lekérdezési paraméterrel `X-CorrelationId`lehet átadni. Ha a fejléc és a lekérdezési paraméter is be van állítva, a rendszer figyelmen kívül hagyja a lekérdezési paramétert.|header|Karakterlánc|
|X – ClientVersion|üres    |Az ügyfélalkalmazás verzióját azonosítja. Példa: "2.1.0.123".<br/>Fejléc használata helyett ezt az értéket a lekérdezési paraméterrel `X-ClientVersion`lehet átadni. Ha a fejléc és a lekérdezési paraméter is be van állítva, a rendszer figyelmen kívül hagyja a lekérdezési paramétert.|header|Karakterlánc|
|X – OsPlatform|üres   |Annak az operációs rendszernek a nevét és verziószámát azonosítja, amelyen az ügyfélalkalmazás fut. Példák: "Android 5,0", "iOs 8.1.3", "Windows 8,1".<br/>Fejléc használata helyett ezt az értéket a lekérdezési paraméterrel `X-OsPlatform`lehet átadni. Ha a fejléc és a lekérdezési paraméter is be van állítva, a rendszer figyelmen kívül hagyja a lekérdezési paramétert.|header|Karakterlánc|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Reason|Válasz modell|Fejlécek|
|:--|:--|:--|:--|
|101    |WebSocket-frissítés.|Modell-példa értéke <br/> Objektum{}|X-RequestId<br/>A kérést a hibaelhárítási céllal azonosító érték.<br/>Karakterlánc|
|400    |Hibás kérelem. Ellenőrizze a bemeneti paramétereket, hogy azok érvényesek legyenek. A válasz objektum részletesebb leírást tartalmaz a hibáról.|||
|401    |Jogosulatlan. Győződjön meg arról, hogy a hitelesítő adatok meg vannak határozva, és hogy érvényesek-e, és hogy az Azure-beli adatpiaci előfizetése elérhető egyenleggel rendelkezik.|||
|500    |Hiba történt. Ha a hiba továbbra is fennáll, jelentse az ügyfél nyomkövetési azonosítóját (X-ClientTraceId) vagy a kérelem azonosítóját (X-kérelemazonosító).|||
|503    |A kiszolgáló átmenetileg nem érhető el. Próbálkozzon újra a kéréssel. Ha a hiba továbbra is fennáll, jelentse az ügyfél nyomkövetési azonosítóját (X-ClientTraceId) vagy a kérelem azonosítóját (X-kérelemazonosító).|||
