---
title: Kibocsátási megjegyzések – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A Speech Service egy futó naplója, amely kiadásokat, javításokat, hibajavításokat és ismert problémákat tartalmaz.
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: ac4b0c59cfad3d435858e094cbcb8c9f855a0041
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185315"
---
# <a name="speech-service-release-notes"></a>Beszédfelismerési szolgáltatás kibocsátási megjegyzései

## <a name="new-speech-to-text-locales-2020-august-release"></a>Új beszéd-szöveg területi beállítások: 2020 – augusztus kiadás
Az augusztusi beszéd-szöveg megjelent 26 új területi beállítás augusztusban: 2 európai nyelv CS-CZ és hu-HU, 5 angol területi beállítás és 19 spanyol területi beállítás, amely a legtöbb Dél-amerikai országot fedi le. Az alábbi lista az új területi beállítások listáját tartalmazza. A teljes nyelvi listát [itt](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support)találja.

| Területi beállítás  | Nyelv                          |
|---------|-----------------------------------|
| `cs-CZ` | Cseh (Cseh Köztársaság)            | 
| `en-HK` | Angol (Hongkong)               | 
| `en-IE` | Angol (Írország)                 | 
| `en-PH` | Angol (Fülöp-szigetek)             | 
| `en-SG` | Angol (Szingapúr)               | 
| `en-ZA` | Angol (Dél-Afrika)            | 
| `es-AR` | Spanyol (Argentína)               | 
| `es-BO` | Spanyol (Bolívia)                 | 
| `es-CL` | Spanyol (Chile)                   | 
| `es-CO` | Spanyol (Kolumbia)                | 
| `es-CR` | Spanyol (Costa Rica)              | 
| `es-CU` | Spanyol (Kuba)                    | 
| `es-DO` | Spanyol (Dominikai Köztársaság)      | 
| `es-EC` | Spanyol (Ecuador)                 | 
| `es-GT` | Spanyol (Guatemala)               | 
| `es-HN` | Spanyol (Honduras)                | 
| `es-NI` | Spanyol (Nicaragua)               | 
| `es-PA` | Spanyol (Panama)                  | 
| `es-PE` | Spanyol (perui)                    | 
| `es-PR` | Spanyol (Puerto Rico)             | 
| `es-PY` | Spanyol (Paraguay)                | 
| `es-SV` | Spanyol (Salvador)             | 
| `es-US` | Spanyol (USA)                     | 
| `es-UY` | Spanyol (Uruguay)                 | 
| `es-VE` | Spanyol (Venezuela)               | 
| `hu-HU` | Magyar (Magyarország)               | 


## <a name="speech-sdk-1130-2020-july-release"></a>Speech SDK 1.13.0:2020 – júliusi kiadás

**Megjegyzés**: a Windowson futó Speech SDK a visual Studio 2015, 2017 és 2019 rendszerhez készült Microsoft Visual C++ terjeszthető változattól függ. Töltse le és telepítse innen [.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

**Új funkciók**
- **C#**: az aszinkron beszélgetések átírásának támogatása. [Itt](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-async-conversation-transcription)találja a dokumentációt.  
- **JavaScript**: a [böngésző](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/speaker-recognition) és a [node.js](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/speaker-recognition)Speaker Recognition támogatását is felvettük.
- **JavaScript**: az automatikus nyelvfelismerés/nyelvi azonosító támogatása. [Itt](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-javascript)találja a dokumentációt.
- **Objective-C**: a [többeszközes beszélgetések](https://docs.microsoft.com/azure/cognitive-services/speech-service/multi-device-conversation) és a [beszélgetés átírásának](https://docs.microsoft.com/azure/cognitive-services/speech-service/conversation-transcription)támogatása. 
- **Python**: bővített hangtámogatás a Pythonhoz Windows és Linux rendszeren. [Itt](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams)találja a dokumentációt. 

**Hibajavítások**
- **Összes**: kijavított egy olyan problémát, amely miatt a KeywordRecognizer nem mozdult el a streamek továbbítása után.
- **Összes**: kijavított egy hibát, amely a KeywordRecognitionResult beszerzett adatfolyamot okozta, hogy ne tartalmazza a kulcsszót.
- **Összes**: kijavított egy hibát, amely szerint a SendMessageAsync nem küldi el az üzenetet a dróton keresztül, miután a felhasználók befejeztek a várakozást.
- **Összes**: javítva Speaker Recognition API-k összeomlása, amikor a felhasználók többször is meghívja a VoiceProfileClient:: SpeakerRecEnrollProfileAsync metódust, és nem várja meg a hívások befejezését.
- **Összes**: a VoiceProfileClient és a SpeakerRecognizer osztályokban a fájlok naplózásának engedélyezése rögzített.
- **JavaScript**: [probléma](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/74) javítva a szabályozással a böngésző méretének csökkentése érdekében.
- **JavaScript**: [probléma](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/78) javítva a streamek memóriavesztés esetén.
- **JavaScript**: a NodeJS-ből származó OCSP-válaszok gyorsítótárazása hozzáadva.
- **Java**: kijavított egy problémát, amely BigInteger-mezőket eredményezett, így mindig 0 értéket ad vissza.
- **iOS**: [probléma](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/702) javítva a Speech SDK-alapú alkalmazások iOS App Store-ban való közzétételekor.

**Példák**
- **C++** [: Speaker Recognitionhoz](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console/samples/speaker_recognition_samples.cpp)hozzáadott mintakód.

**COVID – 19 rövidített tesztelés:** Az elmúlt néhány hét során távolról végzett munka miatt nem sikerült a lehető legkevesebb kézi ellenőrzési tesztet végrehajtani, mint általában. Nem történt semmilyen olyan változás, amelyet úgy gondolunk, hogy bármi megszakadt, és az automatizált tesztek mindegyike sikeres volt. Ha nem valószínű, hogy kihagytak valamit, kérjük, tudassa velünk a [githubon](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Egészségesek maradjanak!

## <a name="text-to-speech-2020-july-release"></a>Szöveg-beszéd 2020 – júliusi kiadás

### <a name="new-features"></a>Új funkciók

* **NEURÁLIS TTS, 15 új neurális hang**: a neurális TTS-portfólióhoz hozzáadott új hangok az `ar-EG` Arab (Egyiptom), a Zariyah `ar-SA` Arab (Szaúd-Arábiai), a `ca-ES` katalán (spanyolországi), a dán ( `da-DK` dániai) Orsi, angol nyelven `es-IN` (India a Noora `fi-FI` finn (finnországi), Swara `hi-IN` hindi (India), Colette `nl-NL` holland (Hollandia), Zofia `pl-PL` lengyel (Lengyelország), Fernanda `pt-PT` portugál (portugáliai), Dariya `ru-RU` orosz (Oroszország), Hillevi `sv-SE` svéd (svédországi), Achara in `th-TH` thai (Thaiföld), HiuGaai `zh-HK` kínai (kantoni, hagyományos) és HsiaoYu `zh-TW` kínai (tajvani mandarin). Az összes [támogatott nyelv](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices)ellenõrzése.  

* **Egyéni hang-és hangvezérelt hangalapú tesztelés a felhasználói élmény egyszerűbbé tétele érdekében**: az új tesztelési funkcióval az egyes nyelvekre optimalizált, előre definiált, az általános és a hangsegéd-forgatókönyveket lefedő tesztelési készlettel automatikusan teszteli a rendszer. Ezeket a teszteket a rendszer gondosan kijelöli és teszteli, hogy tartalmazza a jellemző használati eseteket és a fonémák a nyelven. Emellett a felhasználók továbbra is kiválaszthatják saját tesztelési parancsfájljaik feltöltését a modellek betanításakor.

* **Hangtartalom létrehozása: új funkciók állnak rendelkezésére, amelyek nagyobb teljesítményű hanghangolási és hangkezelési képességeket tesznek lehetővé**

    * `Pitch`a, a `rate` és a `volume` továbbfejlesztett, hogy a hangolást előre meghatározott értékkel támogassa, például lassú, közepes és gyors. Most már egyértelmű, hogy a felhasználók "konstans" értéket válasszanak a hangszerkesztéshez.

    ![Hang hangolás](media/release-notes/audio-tuning.png)

    * A felhasználók most már áttekinthetik a `Audio history` munkahelyi fájljaikat. Ezzel a funkcióval a felhasználók könnyedén követhetik a munkafájlhoz kapcsolódó összes generált hangot. Megtekinthetik az előzmények verzióját, és összehasonlítják a minőséget a hangolással egyidejűleg. 

    ![Hangelőzmények](media/release-notes/audio-history.png)

    * A `Clear` funkció mostantól rugalmasabb. A felhasználók törölhetnek egy adott hangolási paramétert a kiválasztott tartalomhoz elérhető egyéb paraméterek megőrzése mellett.  

    * A kezdőlapon egy oktatóanyag-videó lett hozzáadva [, amellyel a](https://speech.microsoft.com/audiocontentcreation) felhasználók gyorsan megkezdhetik a TTS hanghangolási és hangkezelési szolgáltatásait. 

### <a name="general-tts-voice-quality-improvements"></a>Általános TTS hang minőségének fejlesztése

* Továbbfejlesztett TTS-vocoder a jobb hűség és az alacsonyabb késés érdekében.

    * Az Elsa- `it-IT` t egy olyan új vocoder frissítette, amely a + 0,464 CMOS-t (összehasonlító átlagot), a hangminőséget, az 40%-ot gyorsabb szintézisben és 30%-kal csökkenti az első bájt késését. 
    * A Xiaoxiao frissítve lett az `zh-CN` új vocoder, az általános tartomány + 0,348-es verziójával, valamint a lírai stílushoz + 0,195 a + 0148 CMOS-nyereséggel. 

* Frissített `de-DE` és `ja-JP` hangmodellek, hogy a TTS kimenete természetesebb legyen.
    
    * Frissített Katja a `de-DE` legújabb prosody modellezési módszerrel, a Mos (mean vélemények pontszáma) nyereség + 0,13. 
    * Frissítve a Nanami `ja-JP` egy új pitch Accent prosody-modellel, a Mos (mean vélemények pontszáma) nyereség + 0,19;  

* Jobb Word szintű kiejtési pontosság 5 nyelven.

    | Nyelv | Kiejtési hiba csökkentése |
    |---|---|
    | `en-GB` | 51% |
    | `ko-KR` | 17 |
    | `pt-BR` | 39% |
    | `pt-PT` | 77% |
    | `id-ID` | 46% |

### <a name="bug-fixes"></a>Hibajavítások

* Pénznem olvasása
    * Kijavítottuk a és a pénznem beolvasásával kapcsolatos problémát `es-ES``es-MX`
     
    | Nyelv | Bevitel | Fejlesztés utáni kiolvasás |
    |---|---|---|
    | `es-MX` | $1,58 | un peso cincuenta y Ocho centavos |
    | `es-ES` | $1,58 | un dólar cincuenta y Ocho centavos |

    * A negatív pénznem támogatása (például "-€325") a következő területi beállításokban:,,,, `en-US` `en-GB` `fr-FR` `it-IT` `en-AU` , `en-CA` .

* Továbbfejlesztett címek beolvasása `pt-PT` .
* A " `en-AU` `en-UK` for" és a "Four" szó fix Natasha () és a (z) "a" kiejtéssel kapcsolatos problémái.  
* Rögzített hibák a hangtartalom-létrehozási eszközön
    * A második bekezdés után megjelenő további és váratlan szüneteltetés.  
    * A "No break" funkció vissza lett adva egy regressziós hibából. 
    * A Speech Studio véletlenszerű frissítési hibája kijavítva.  

### <a name="samplessdk"></a>Minták/SDK

* JavaScript: megjavítja a Firefox lejátszási problémáját, valamint a Safarit macOS és iOS rendszeren. 

## <a name="speech-sdk-1121-2020-june-release"></a>Speech SDK 1.12.1:2020 – júniusi kiadás
**Speech CLI (más néven SPX)**
-   Megjelent a CLI súgó Keresési funkciói:
    -   `spx help find --text TEXT`
    -   `spx help find --topic NAME`
-   Frissítve, hogy működjön az újonnan telepített v 3.0 batch és Custom Speech API-kkal:
    -   `spx help batch examples`
    -   `spx help csr examples`

**Új funkciók**
-   **C \# , C++**: Speaker Recognition előzetes verzió: Ez a funkció lehetővé teszi a beszélők azonosítását (ki beszél?) és a hangszórók ellenőrzését (ez az, aki a következőt igényli?). Először [tekintse át az áttekintést](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/speaker-recognition-overview), olvassa el a [Speaker Recognition alapjairól szóló cikket](https://docs.microsoft.com/azure/cognitive-services/speech-service/speaker-recognition-basics), vagy az API- [referenciák dokumentációját](https://docs.microsoft.com/rest/api/speakerrecognition/).

**Hibajavítások**
-   **C \# , C++**: a rögzített mikrofon rögzítése nem működik a 1,12-ben a hangszórók felismerése során.
-   **JavaScript**: javítások a Firefox szöveg-beszédéhez és a Safari MacOS és iOS rendszeren.
-   Javítás a Windows-alkalmazás-ellenőrző hozzáférés-megsértésének összeomlása a beszélgetés átírásakor a 8 csatornás adatfolyam használata esetén.
-   Javítsa ki a Windows-alkalmazás-ellenőrző hozzáférés-megsértésének összeomlását a többeszközes beszélgetések fordításakor.

**Példák**
-   **C#**: [kód minta](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition) a hangszórók felismeréséhez.
-   **C++**: [kód minta](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition) a hangszórók felismeréséhez.
-   **Java**: [mintakód a szándék](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/intent-recognition) -felismeréshez Androidon. 

**COVID – 19 rövidített tesztelés:** Az elmúlt néhány hét során távolról végzett munka miatt nem sikerült a lehető legkevesebb kézi ellenőrzési tesztet végrehajtani, mint általában. Nem történt semmilyen olyan változás, amelyet úgy gondolunk, hogy bármi megszakadt, és az automatizált tesztek mindegyike sikeres volt. Ha nem valószínű, hogy kihagytak valamit, kérjük, tudassa velünk a [githubon](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Egészségesek maradjanak!


## <a name="speech-sdk-1120-2020-may-release"></a>Speech SDK 1.12.0:2020 – májusi kiadás
**Speech CLI (más néven SPX-ként is ismert)**
- Az **SPX** egy új parancssori eszköz, amellyel elvégezhető a parancssorból való felismerés, szintézis, fordítás, kötegelt átírás és egyéni beszédfelismerés. A segítségével tesztelheti a beszédfelismerési szolgáltatást, vagy parancsfájlban megadhatja a végrehajtani kívánt beszédfelismerési szolgáltatás feladatait. Töltse le az eszközt, és olvassa el [itt](https://docs.microsoft.com/azure/cognitive-services/speech-service/spx-overview)a dokumentációt.

**Új funkciók**
- **Go**: új go nyelvi támogatás a [beszédfelismeréshez](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/speech-to-text-from-microphone?pivots=programming-language-go) és az [Egyéni hangsegédekhez](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/voice-assistants?pivots=programming-language-go). [Itt](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-go)állíthatja be a fejlesztői környezetet. A mintakód esetében lásd a minták szakaszt alább. 
- **JavaScript**: a böngésző támogatja a szöveg és a beszéd használatát. [Itt](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/text-to-speech-audio-file?pivots=programming-language-JavaScript)találja a dokumentációt.
- **C++, C#, Java**: új `KeywordRecognizer` objektum és API-k támogatottak Windows, Android, Linux & iOS platformokon. Olvassa el [itt](https://docs.microsoft.com/azure/cognitive-services/speech-service/custom-keyword-overview)a dokumentációt. A mintakód esetében lásd a minták szakaszt alább. 
- **Java**: többeszközes beszélgetés hozzáadva a fordítási támogatással. Tekintse meg a dokumentációt [itt](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.transcription).

**& optimalizálások fejlesztése**
- **JavaScript**: optimalizált böngésző-mikrofon implementálása a beszédfelismerés pontosságának növelésével.
- **Java**: a VEDEL nélküli közvetlen JNI implementációt használó kötések újrabontása. Ezzel a megoldással a Windows, Android, Linux és Mac rendszerekhez használt összes Java-csomag esetében 10x-re csökkenti a kötések méretét, és megkönnyíti a beszédfelismerési SDK Java-implementációjának további fejlesztését.
- **Linux**: frissített támogatási [dokumentáció](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=linux) a legújabb RHEL 7-re vonatkozó megjegyzésekkel.
- Továbbfejlesztett kapcsolati logika, amely szolgáltatás-és hálózati hibák esetén többször is csatlakoztatja a kapcsolódási kísérleteket.
- Frissítettük a [Portal.Azure.com](https://portal.azure.com) Speech rövid útmutatót, amely segít a fejlesztőknek az Azure Speech utazás következő lépésében.

**Hibajavítások**
- **C#, Java**: javítva a [probléma](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/587) az SDK-kódtárak betöltésével a Linux ARM-on (32 és 64 bit).
- **C#**: az TranslationRecognizer, a IntentRecognizer és a kapcsolatok objektumaihoz tartozó natív fogópontok explicit ártalmatlanítása.
- **C#**: rögzített hangbemeneti életciklus-kezelés a ConversationTranscriber objektumhoz.
- Kijavított egy hibát `IntentRecognizer` , amelyben az eredmény oka nem volt megfelelően beállítva az egyszerű kifejezésekből származó leképezések felismerése során.
- Kijavított egy hibát, amelyben az `SpeechRecognitionEventArgs` eredmény eltolása helytelenül lett beállítva.
- Rögzített egy versenyhelyzet, amelyben az SDK hálózati üzenetet próbált elküldeni a WebSocket-kapcsolat megnyitása előtt. Reprodukálható a `TranslationRecognizer` résztvevők hozzáadásakor.
- Rögzített memória-szivárgás a kulcsszó-felismerő motorban.

**Példák**
- **Go**: a [beszédfelismeréshez](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/speech-to-text-from-microphone?pivots=programming-language-go) és az [Egyéni hangsegédhez](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/voice-assistants?pivots=programming-language-go)hozzáadott gyors útmutatók. [Itt](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples)megtalálhatja a mintakód kódját. 
- **JavaScript**: gyors útmutató a [szöveg-beszéd](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/text-to-speech?pivots=programming-language-javascript), a [fordítás](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/translate-speech-to-text?pivots=programming-language-javascript)és a [Szándékfelismeréshoz](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/intent-recognition?pivots=programming-language-javascript).
- Kulcsszavas felismerési minták a [C \# ](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer) és a [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer) (Android) rendszerhez.  

**COVID – 19 rövidített tesztelés:** Az elmúlt néhány hét során távolról végzett munka miatt nem sikerült a lehető legkevesebb kézi ellenőrzési tesztet végrehajtani, mint általában. Nem történt semmilyen olyan változás, amelyet úgy gondolunk, hogy bármi megszakadt, és az automatizált tesztek mindegyike sikeres volt. Ha nem valószínű, hogy kihagytak valamit, kérjük, tudassa velünk a [githubon](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Egészségesek maradjanak!

## <a name="speech-sdk-1110-2020-march-release"></a>Speech SDK 1.11.0:2020 – márciusi kiadás
**Új funkciók**
- Linux: a Red Hat Enterprise Linux (RHEL)/CentOS 7 x64 támogatása a System for Speech SDK konfigurálására [vonatkozó utasításokkal](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-rhel-centos-7) .
- Linux: a .NET Core C# támogatása a Linux ARM32 és a ARM64. További tudnivalók [itt](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=linux). 
- C#, C++: a `UtteranceId` (z) `ConversationTranscriptionResult` rendszerhez hozzáadva egy egységes azonosítót az összes köztes és a végső beszédfelismerési eredményben. [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult?view=azure-dotnet), [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult)– részletek.
- Python: támogatás hozzáadva a rendszerhez `Language ID` . Tekintse meg a speech_sample. a. a [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)-tárházat.
- Windows: a Windows platformon az összes Win32 Console-alkalmazáshoz hozzáadott tömörített hangbemeneti formátum támogatása. Részletek [itt](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams). 
- JavaScript: a NodeJS-ben támogatja a beszédfelismerést (szöveg – beszéd). További információ [itt](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech). 
- JavaScript: új API-k hozzáadása az összes küldési és fogadási üzenet ellenőrzésének engedélyezéséhez. További információ [itt](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript). 
        
**Hibajavítások**
- C#, C++: javítva a probléma, hogy `SendMessageAsync` most bináris típusúként küld bináris üzenetet. [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_), [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection)– részletek.
- C#, C++: javítva a probléma, hogy az `Connection MessageReceived` esemény használata összeomlást okozhat `Recognizer` , ha az objektumot az objektum előtt dobja el a rendszer `Connection` . [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived?view=azure-dotnet), [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection#messagereceived)– részletek.
- Android: a mikrofon hangpufferének mérete a 800ms-ról a 100ms-re csökkent a késés javítása érdekében.
- Android: kijavított egy [problémát](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) az x86-os Android-emulátorban Android Studioban.
- JavaScript: támogatás a kínai régiókban az API-val `fromSubscription` . Részletek [itt](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#fromsubscription-string--string-). 
- JavaScript: további hibaüzenetek hozzáadása a NodeJS-től érkező sikertelen kapcsolatok esetén.
        
**Példák**
- Unity: a szándék-felismerés nyilvános mintája rögzített, ahol a LUIS JSON-importálás sikertelen volt. Részletek [itt](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369).
- Python: minta hozzáadva a következőhöz: `Language ID` . Részletek [itt](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py).
    
**Covid19 rövidített tesztelése:** Az elmúlt néhány hét során távolról végzett munka miatt nem sikerült a lehető legtöbb kézi eszköz-ellenőrzési tesztet végrehajtani, mint általában. Erre példa a mikrofon bemenetének és a hangszórók kimenetének tesztelése Linux, iOS és macOS rendszereken. Nem történt semmilyen olyan változás, amelyet úgy gondolunk, hogy bármi megszakadt ezen a platformon, és az automatizált tesztek mindegyike sikeres volt. Ha nem valószínű, hogy kihagytak valamit, kérjük, tudassa velünk a [githubon](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br> Köszönjük a folyamatos támogatást. Ahogy mindig, tegye fel kérdéseit vagy visszajelzéseit a [githubon](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) vagy a [stack Overflowon](https://stackoverflow.microsoft.com/questions/tagged/731).<br>
Egészségesek maradjanak!

## <a name="speech-sdk-1100-2020-february-release"></a>Speech SDK 1.10.0:2020 – februári kiadás

**Új funkciók**

 - Python-csomagok hozzáadása a Python új 3,8-es kiadásának támogatásához.
 - Red Hat Enterprise Linux (RHEL)/CentOS 8 x64 támogatás (C++, C#, Java, Python).
   > [!NOTE] 
   > A felhasználóknak az [alábbi utasítások](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)szerint kell konfigurálniuk az OpenSSL-t.
 - Linux ARM32-támogatás Debian és Ubuntu rendszerekhez.
 - A DialogServiceConnector mostantól támogatja az opcionális "bot ID" paramétert a BotFrameworkConfig. Ez a paraméter lehetővé teszi, hogy egyetlen Azure Speech-erőforrással több közvetlen vonalas beszédfelismerési robotot használjon. A megadott paraméter nélkül a rendszer a következő alapértelmezett robotot használja: (a Direct line Speech Channel konfigurációs lapja határozza meg).
 - A DialogServiceConnector most már rendelkezik egy SpeechActivityTemplate tulajdonsággal. A JSON-karakterlánc tartalmát a közvetlen vonalas beszéd fogja használni, hogy előre töltse fel a támogatott mezők széles körét az összes olyan tevékenységben, amelyek a közvetlen sortörési robothoz jutnak, beleértve a beszédfelismerésre adott eseményekre adott válaszként automatikusan generált tevékenységeket is.
 - A TTS mostantól előfizetési kulcsot használ a hitelesítéshez, így csökkentve az első szintézis eredményének első bájtos késését a szintetizátor létrehozása után.
 - Frissített beszédfelismerési modellek 19 területi beállításhoz az átlagos Word-hibák 18,6%-os csökkenéséhez (es-ES, es-MX, fr-CA, fr-FR, IT-IT, ja-JP, ko-KR, PT-BR, zh-CN, ZH-HK, NB-NO, fi-FL, ru-RU, pl-PL, CA-ES, zh-TW, th-TH, PT-PT, TR-TR). Az új modellek jelentős mértékben javítják a különböző tartományokat, beleértve a diktálást, a Call-Center átírást és a videó indexelési forgatókönyveit.

**Hibajavítások**

 - Kijavítva a hiba, ahol a beszélgetési átiratok nem várnak megfelelően a JAVA API-kon 
 - Android x86-emulátor javítás a Xamarin [GitHub-probléma](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363) esetén
 - Hiányzó hozzáadása (get | Set) tulajdonság-metódusok AudioConfig
 - Olyan TTS-hiba javítása, amelyben a audioDataStream nem állítható le, ha a csatlakozás meghiúsul
 - Ha olyan végpontot használ, amelynek nincs régiója, az USP-hibákat okoz a beszélgetési fordítónál
 - Az univerzális Windows-alkalmazásokban az azonosító generálás mostantól egy megfelelően egyedi GUID algoritmust használ; korábban egy stubbed-implementációban, amely gyakran ütközéseket okozott nagy mennyiségű interakció során.
 
 **Példák**
 
 - Unity-minta a Speech SDK [és az Unity mikrofon és a leküldéses mód streaming](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone) használatával való használatához

**További változások**

 - [Az OpenSSL konfigurációs dokumentációjának frissítése Linuxra](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)

## <a name="speech-sdk-190-2020-january-release"></a>Speech SDK 1.9.0:2020 – januári kiadás

**Új funkciók**

- Többeszközes beszélgetés: csatlakoztasson több eszközt ugyanahhoz a beszéd-vagy szöveges beszélgetéshez, és igény szerint fordítsa le a közöttük küldött üzeneteket. További információt [ebben a cikkben](multi-device-conversation.md)talál. 
- A kulcsszó-felismerési támogatás hozzáadva az Android. éves kiadási csomaghoz, valamint az x86-és x64-alapú ízek támogatása. 
- Objective-C: `SendMessage` és `SetMessageProperty` az objektumhoz hozzáadott metódusok `Connection` . [Itt](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection)találja a dokumentációt.
- A TTS C++ API mostantól támogatja a `std::wstring` szintézis szövegként való bevitelt, így nincs szükség a wstring karakterlánccá konvertálására, mielőtt átadná azt az SDK-nak. Tekintse meg [a részleteket.](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync) 
- C#: mostantól elérhető a [nyelvi azonosító](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) és a [forrás nyelvi konfigurációja](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp) .
- JavaScript: egy olyan szolgáltatás hozzáadása az `Connection` objektumhoz, amely a beszédfelismerési szolgáltatás egyéni üzenetein keresztül továbbítja a visszahívást `receivedServiceMessage` .
- JavaScript: további támogatás a `FromHost API` helyszíni tárolók és a szuverén felhők használatának megkönnyítéséhez. [Itt](speech-container-howto.md)találja a dokumentációt.
- JavaScript: `NODE_TLS_REJECT_UNAUTHORIZED` Köszönjük, hogy hozzájárult a [orgads](https://github.com/orgads). Tekintse meg [a részleteket.](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75)

**Változtatások megszakítása**

- `OpenSSL`frissítve lett a b 1.1.1-es verzióra, és statikusan kapcsolódik a Linux rendszerhez készült Speech SDK Core-könyvtárhoz. Ez akkor fordulhat elő, ha a beérkezett fájlok `OpenSSL` nem lettek telepítve a `/usr/lib/ssl` rendszeren lévő könyvtárba. A probléma megoldásához tekintse meg a Speech SDK [dokumentációját](how-to-configure-openssl-linux.md) .
- Módosítottuk a C#-tól a-ig visszaadott adattípust, `WordLevelTimingResult.Offset` `int` `long` hogy engedélyezzék a hozzáférést, `WordLevelTimingResults` Ha a beszédfelismerési adatok 2 percnél hosszabbak.
- `PushAudioInputStream`és `PullAudioInputStream` most a WAV-fejléc információit a beszédfelismerési szolgáltatásnak küldi a (z) alapján `AudioStreamFormat` , opcionálisan megadhatja őket a létrehozásuk után. Az ügyfeleknek most a [támogatott hangbemenet formátumot](how-to-use-audio-input-streams.md)kell használniuk. Bármilyen más formátum esetén a rendszer az optimálisnál rosszabb eredményeket kap, vagy más problémákat okozhat. 

**Hibajavítások**

- Tekintse meg a `OpenSSL` frissítést a fenti módosítások feltörése alatt. A Linux és a Java esetében is rögzítettünk egy időszakos összeomlást és egy teljesítménnyel kapcsolatos problémát (nagy terhelés melletti zárolást). 
- Java: az objektumok bezárásának tökéletesítése magas egyidejűségi helyzetekben.
- Átalakítottuk a NuGet-csomagot. A rendszer eltávolította a három példányát a `Microsoft.CognitiveServices.Speech.core.dll` `Microsoft.CognitiveServices.Speech.extension.kws.dll` lib-mappák közül, és a letöltéshez a NuGet-csomagot kisebb és gyorsabbra teszi, és hozzáadta a C++ natív alkalmazások fordításához szükséges fejléceket.
- [Itt talál](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp)rögzített rövid útmutatót. Ezek a "mikrofon nem található" kivétel nélkül lettek kizárva a Linux, macOS és Windows rendszereken.
- Rögzített SDK-összeomlás a hosszú beszédfelismerés eredményeként bizonyos kódok elérési útjain, például [a](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp)mintában.
- Az Azure webalkalmazás-környezet rögzített SDK-telepítési hibája a [probléma](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396)megoldásához.
- A probléma megoldásához több címke vagy címke használatakor hiba történt `<voice>` `<audio>` . [this customer issue](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433) 
- A rendszer felfüggesztette a TTS 401 hibát, ha az SDK-t felfüggesztették a felfüggesztésből.
- JavaScript: rögzített hangadatok körkörös importálása a [euirim](https://github.com/euirim)hozzájárulásának köszönhetően. 
- JavaScript: a szolgáltatás tulajdonságainak beállításához hozzáadott támogatás a 1,7-as verzióban.
- JavaScript: kijavított egy hibát, amikor egy kapcsolati hiba folyamatos, sikertelenül működő WebSocket-újracsatlakozási kísérleteket eredményezhet.

**Példák**

- Kulcsszó-felismerési minta hozzáadva az Androidhoz [itt](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo).
- A [kiszolgáló forgatókönyvéhez](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs)hozzáadott TTS-minta.
- A C# és a C++ nyelvhez készült többeszközes beszélgetéseket [itt](quickstarts/multi-device-conversation.md)is hozzáadhatja.

**További változások**

- Optimalizált SDK-Alapkönyvtár mérete Androidon.
- Az SDK a 1.9.0-ben és a-ben egyaránt támogatja `int` `string` a beszélgetési átiratok hangaláírási verzió mezőjében a és a típust is.

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0:2019 – november kiadás

**Új funkciók**

- Hozzáadott egy `FromHost()` API-t, amellyel könnyedén használhatja a helyszíni tárolókat és a szuverén felhőket.
- A beszédfelismeréshez hozzáadott automatikus forrás Nyelvfelismerés (Java és C++ nyelven)
- `SourceLanguageConfig`A beszédfelismeréshez hozzáadott objektum, amely a várt forrás nyelvek (Java és C++ nyelven) megadására szolgál.
- `KeywordRecognizer`A Windows (UWP), az Android és az iOS támogatása a NuGet-és Unity-csomagokon keresztül
- Távoli beszélgetési Java API hozzáadva a beszélgetések átírásához aszinkron kötegekben.

**Változtatások megszakítása**

- A beszélgetési átirat funkciói a névtér alatt lettek áthelyezve `Microsoft.CognitiveServices.Speech.Transcription` .
- A beszélgetési átirat módszereinek egy része új `Conversation` osztályba kerül.
- Eldobott támogatás a 32 bites (ARMv7 és x86) iOS-hez

**Hibajavítások**

- Az összeomlás javítása, ha `KeywordRecognizer` a helyi használata érvényes Speech Service-előfizetési kulcs nélkül történik

**Példák**

- Xamarin minta a következőhöz:`KeywordRecognizer`
- Unity minta a következőhöz:`KeywordRecognizer`
- C++ és Java-minták az automatikus forrás Nyelvfelismerés.

## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0:2019 – szeptemberi kiadás

**Új funkciók**

- Bétaverziós támogatás hozzáadva a Xamarin Univerzális Windows-platform (UWP), Android és iOS rendszerhez
- IOS-támogatás hozzáadva az Unity-hoz
- `Compressed`A atörvény, a mulaw, a FLAC és az Android, az iOS és a Linux rendszerhez készült bemeneti támogatás hozzáadva
- Az osztályban lett hozzáadva a `SendMessageAsync` `Connection` szolgáltatásnak küldött üzenetekhez
- Osztály hozzáadva az `SetMessageProperty` `Connection` üzenet beállítása tulajdonságához
- A Java (JRE és Android), a Python, a Swift és a Objective-C kötések hozzáadva
- A macOS, iOS és Android rendszerekhez készült lejátszási támogatás hozzáadva.
- "Word határ" információ hozzáadva a TTS-hez.

**Hibajavítások**

- Rögzített IL2CPP-Build probléma az 2019-es Unity Android rendszerhez
- Helytelenül formázott fejléceket tartalmazó hiba kijavítva a WAV-fájlban megadott bemeneten
- Az UUID-mel kapcsolatos kijavított probléma bizonyos kapcsolatok tulajdonságaiban nem egyedi.
- Néhány figyelmeztetés a Swift-kötésekben a nullák megadásával kapcsolatban (kis kód megváltoztatására lehet szükség)
- Kijavítva egy olyan hibát, amely miatt a WebSocket-kapcsolatok nem megfelelően vannak lezárva a hálózati terhelés alatt
- Kijavított egy problémát az Androidon, amely időnként a következő által használt duplikált benyomási azonosítókat eredményezi:`DialogServiceConnector`
- A kapcsolatok stabilitásának fejlesztése több fordulatos interakciók és a hibák jelentése ( `Canceled` eseményeken keresztül)`DialogServiceConnector`
- `DialogServiceConnector`a munkamenet-indítás mostantól megfelelően biztosítja az eseményeket, beleértve `ListenOnceAsync()` az aktív`StartKeywordRecognitionAsync()`
- A kapott tevékenységekhez kapcsolódó összeomlást kezelte `DialogServiceConnector`

**Példák**

- Gyors útmutató a Xamarin
- Frissített CPP-gyors útmutató Linux ARM64-információkkal
- Frissített Unity rövid útmutató iOS-adatokkal

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0:2019 – júniusi kiadás

**Példák**

- Gyors példák a UWP és az Unity szövegének beszédére
- Gyors üzembe helyezési minta iOS rendszeren
- A Speech & Szándékfelismerés és a fordítás Unity mintái
- Frissített gyors üzembe helyezési minták a következőhöz:`DialogServiceConnector`

**Tökéletesítések/változások**

- Párbeszédpanel névtere:
  - A `SpeechBotConnector` új nevet kapott: `DialogServiceConnector`
  - A `BotConfig` új nevet kapott: `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()`újra leképezve a következőre`DialogServiceConfig::FromBotSecret()`
  - Az Átnevezés után az összes meglévő közvetlen beszédfelismerési ügyfél továbbra is támogatott.
- Az TTS REST-adapter frissítése a proxy és az állandó kapcsolatok támogatásához
- Javítsa a hibaüzenetet, ha egy érvénytelen régiót ad át
- Swift/Objective-C:
  - Továbbfejlesztett hibajelentés: az olyan metódusok, amelyek egy hibát okozhatnak, két verzióban jelennek meg: az egyik, amely egy objektumot tesz elérhetővé `NSError` a hibakezelés céljából, és egy kivételt felvet. Az előbbi a Swift számára elérhetővé válnak. Ehhez a változáshoz a meglévő Swift-kódnak való átalakításra van szükség.
  - Továbbfejlesztett események feldolgozása

**Hibajavítások**

- Javítás a TTS-hez: a rendszer a `SpeakTextAsync` várakozások nélkül tért vissza, amíg a hang befejezte a renderelést
- Javítsa a karakterláncok átadását a C# nyelvben a teljes nyelvi támogatás engedélyezéséhez
- A .NET Core-alkalmazás problémáinak elhárítása az alapszintű függvénytár betöltéséhez a mintákban a net461 Target Framework használatával
- Javítsa ki az esetenkénti problémákat, hogy natív kódtárakat helyezzen üzembe a minták kimeneti mappájába
- Javítás a webes szoftvercsatorna megbízható bezárásakor
- Javítsa a lehetséges összeomlást a Linuxon nagyon nagy terhelés alatt álló kapcsolatok megnyitásakor
- Javítsa ki a hiányzó metaadatokat a macOS-keretrendszer csomagban
- Problémák elhárítása `pip install --user` Windows rendszeren

## <a name="speech-sdk-151"></a>Speech SDK 1.5.1

Ez egy hibajavítási kiadás, amely csak a natív/felügyelt SDK-t érinti. Ez nem befolyásolja az SDK JavaScript-verzióját.

**Hibajavítások**

- Javítsa a FromSubscription, ha a beszélgetési átirattal együtt használja.
- Javítsa ki a kulcsszót a hangsegédek esetében.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0:2019 – májusi kiadás

**Új funkciók**

- Mostantól elérhető a Windows és a Linux rendszerhez készült kulcsszó-észrevétel (KWS). A KWS funkció bármilyen mikrofonos típussal működhet, a hivatalos KWS-támogatás azonban jelenleg az Azure Kinect DK hardverben vagy a Speech Devices SDK-ban található mikrofon-tömbökre korlátozódik.
- A kifejezésre mutató funkció az SDK-n keresztül érhető el. További információ: [itt](how-to-phrase-lists.md).
- A társalgási átirat funkció az SDK-n keresztül érhető el. Lásd [itt](conversation-transcription-service.md).
- Adja hozzá a hangsegédek támogatását a közvetlen vonalas beszéd csatornán keresztül.

**Példák**

- További minták az SDK által támogatott új szolgáltatásokhoz vagy új szolgáltatásokhoz.

**Tökéletesítések/változások**

- Különböző felismerő tulajdonságok lettek hozzáadva a szolgáltatás viselkedésének vagy a szolgáltatás eredményeinek (például maszkolási káromkodás és mások) beállításához.
- Mostantól beállíthatja a felismerőt a szabványos konfigurációs tulajdonságok segítségével, még akkor is, ha létrehozta a felismerőt `FromEndpoint` .
- Objective-C: `OutputFormat` a tulajdonság hozzá lett adva `SPXSpeechConfiguration` .
- Az SDK mostantól támogatja a Debian 9-es Linux-disztribúciót.

**Hibajavítások**

- Kijavított egy problémát, amelyben a beszélő erőforrás túl korán megsemmisült a szöveg-beszédben.

## <a name="speech-sdk-142"></a>Speech SDK 1.4.2

Ez egy hibajavítási kiadás, amely csak a natív/felügyelt SDK-t érinti. Ez nem befolyásolja az SDK JavaScript-verzióját.

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

Ez egy csak JavaScript kiadás. Nem lettek hozzáadva funkciók. A következő javítások történtek:

- A web Pack használatának megakadályozása a https-proxy-Agent betöltésében.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0:2019 – április kiadás

**Új funkciók**

- Az SDK mostantól támogatja a Text-to-speech szolgáltatást béta verzióként. A Windows és a Linux rendszerű asztali számítógépeken támogatott a C++ és a C#. További információkért tekintse meg a [szöveg – beszéd áttekintést](text-to-speech.md#get-started).
- Az SDK mostantól stream bemeneti fájlként támogatja az MP3 és az Opus/OGG hangfájlokat. Ez a funkció csak a Linux rendszerű C++ és C# nyelvről érhető el, és jelenleg Beta (további részletek [itt](how-to-use-codec-compressed-audio-input-streams.md)).
- A Java, a .NET Core, a C++ és a Objective-C beszédfelismerési SDK-t a macOS-támogatás nyerte. A macOS-hez készült Objective-C-támogatás jelenleg béta verzióban érhető el.
- iOS: az iOS-hez készült Speech SDK (Objective-C) mostantól CocoaPod is közzé lett téve.
- JavaScript: nem alapértelmezett mikrofon bemeneti eszközként való támogatása.
- JavaScript: Node.js proxy támogatása.

**Példák**

- Minták a Speech SDK és a C++ nyelv használatához, valamint a macOS-hez készült Objective-C használatával.
- A Text-to-speech szolgáltatás használatát bemutató minták lettek hozzáadva.

**Tökéletesítések/változások**

- Python: az elismerési eredmények további tulajdonságai már elérhetők a `properties` tulajdonságon keresztül.
- A fejlesztéshez és a hibakereséshez további támogatást az SDK-naplózási és diagnosztikai információk naplófájlba való átirányítására (további részletek [itt](how-to-use-logging.md)talál).
- JavaScript: a hangfeldolgozási teljesítmény javítása.

**Hibajavítások**

- Mac/iOS: olyan hiba, amely hosszú várakozást eredményezett, ha a beszédfelismerési szolgáltatáshoz való kapcsolódás nem lett meghatározva.
- Python: javítsa a hibák kezelését a Python-visszahívásokban.
- JavaScript: helytelen állapotú jelentéskészítés a beszédfelismeréshez a RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1:2019 – február frissítés

Ez egy hibajavítási kiadás, amely csak a natív/felügyelt SDK-t érinti. Ez nem befolyásolja az SDK JavaScript-verzióját.

**Hibajavítás**

- Rögzített memória-szivárgás a mikrofon bemenetének használatakor. A stream-alapú vagy a fájl bemenete nincs hatással.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0:2019 – februári kiadás

**Új funkciók**

- A Speech SDK az osztályban keresztül támogatja a bemeneti mikrofon kijelölését `AudioConfig` . Ez lehetővé teszi a hangadatoknak a beszédfelismerési szolgáltatásba való továbbítását egy nem alapértelmezett mikrofonból. További információkért tekintse meg a [hangbeviteli eszköz kijelölését](how-to-select-audio-input-devices.md)ismertető dokumentációt. Ez a funkció még nem érhető el a JavaScriptből.
- A Speech SDK mostantól támogatja az Unity használatát egy bétaverziós verzióban. Visszajelzés küldése a [GitHub-minta tárházának](https://aka.ms/csspeech/samples)probléma szakaszában. Ez a kiadás támogatja az Unity használatát a Windows x86 és x64 (asztali vagy Univerzális Windows-platform alkalmazások) és az Android (ARM32/64, x86) esetében. További információ az [Unity](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity)gyors üzembe helyezési útmutatójában található.
- A `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (korábbi kiadásokban szállított) fájl már nem szükséges. A funkció mostantól integrálva van az alap SDK-ba.

**Példák**

A következő új tartalom érhető el a [példában szereplő adattárban](https://aka.ms/csspeech/samples):

- További minták a következőhöz: `AudioConfig.FromMicrophoneInput` .
- További Python-minták a szándék-felismeréshez és a fordításhoz.
- További minták az `Connection` objektum iOS-ben való használatához.
- További Java-minták hang kimenettel való fordításhoz.
- Új minta a [Batch-Átírási REST API](batch-transcription.md)használatához.

**Tökéletesítések/változások**

- Python
  - Javított paraméterek ellenőrzése és hibaüzenetek a-ben `SpeechConfig` .
  - Adja hozzá az objektum támogatását `Connection` .
  - A 32-bites Python (x86) támogatása Windows rendszeren.
  - A Pythonhoz készült Speech SDK a bétaverzión kívül van.
- iOS
  - Az SDK mostantól az iOS SDK 12,1-es verziójára épül.
  - Az SDK mostantól támogatja az iOS 9,2-es és újabb verzióit.
  - Javítsa a hivatkozási dokumentációt, és javítsa ki a tulajdonságok nevét.
- JavaScript
  - Adja hozzá az objektum támogatását `Connection` .
  - Type Definition Files hozzáadása a kötegelt JavaScripthez
  - Kezdeti támogatás és megvalósítás a kifejezésekre vonatkozó útmutatókhoz.
  - Visszatérési tulajdonságok gyűjteménye a szolgáltatás JSON-vel való felismeréséhez
- A Windows-DLL-ek mostantól egy verzió-erőforrást tartalmaznak.
- Ha létrehoz egy felismerőt `FromEndpoint` , közvetlenül is hozzáadhat paramétereket a végpont URL-címéhez. `FromEndpoint`A nem tudja konfigurálni a felismerőt a szabványos konfigurációs tulajdonságok használatával.

**Hibajavítások**

- Az üres proxy felhasználóneve és a proxy jelszava helytelenül lett kezelve. Ebben a kiadásban, ha a proxy felhasználónevét és a proxy jelszavát üres karakterlánccá állítja be, a rendszer nem küldi el a proxyhoz való csatlakozáskor.
- Az SDK által létrehozott munkamenet-azonosítók nem mindig voltak igazán véletlenszerűek egyes nyelvekhez &nbsp; /környezetekhez. A probléma megoldásához hozzáadta a véletlenszerű generátor inicializálását.
- Az engedélyezési tokenek kezelését javítani kell. Ha engedélyezési jogkivonatot szeretne használni, akkor a és az `SpeechConfig` előfizetés kulcsának üresen kell maradnia. Ezután hozza létre a felismerőt a szokásos módon.
- Bizonyos esetekben az `Connection` objektum helytelenül lett közzétéve. A probléma kijavítva.
- A JavaScript-minta úgy lett javítva, hogy támogassa a fordítási szintézis hangkimenetét a Safarion is.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Ez egy csak JavaScript kiadás. Nem lettek hozzáadva funkciók. A következő javítások történtek:

- A stream végén a turn. End, nem a Speech. End.
- Javítsa ki azt a hibát a hangszivattyúban, amely nem ütemezett következő küldést, ha az aktuális küldés sikertelen volt.
- Az Auth-jogkivonat folyamatos felismerésének javítása.
- Hibajavítás különböző felismerőhöz/végpontokhoz.
- Dokumentációs újítások.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0:2018 – decemberi kiadás

**Új funkciók**

- Python
  - A Python-támogatás bétaverziója (3,5 és újabb) ebben a kiadásban érhető el. További információ: itt] (rövid útmutató – python.md).
- JavaScript
  - A JavaScripthez készült Speech SDK nyílt forráskódú. A forráskód elérhető a [githubon](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - Most már támogatjuk a Node.jst, további információt [itt](quickstart-js-node.md)találhat.
  - A hangmunkamenetek hosszának korlátozása el lett távolítva, az újrakapcsolódás automatikusan megtörténik a fedél alatt.
- `Connection`objektum
  - A-ben elérheti az `Recognizer` `Connection` objektumokat. Ezzel az objektummal explicit módon kezdeményezheti a szolgáltatás kapcsolatát, és előfizethet az események csatlakoztatására és leválasztására.
    (Ez a funkció még nem érhető el a JavaScriptből és a Pythonból.)
- Az Ubuntu 18,04 támogatása.
- Android
  - Engedélyezve van a kisegítő képesség az APK-generációban.

**Fejlesztései**

- A belső szál használatának fejlesztése, a szálak, a zárolások és a mutexek számának csökkentése.
- Továbbfejlesztett hibajelentés/információk. Számos esetben a hibaüzenetek nem lettek propagálva az összes kiút.
- Frissített fejlesztői függőségek a JavaScriptben, hogy naprakész modulokat használjanak.

**Hibajavítások**

- Rögzített memória-szivárgások, mert a típus nem egyezik a ben `RecognizeAsync` .
- Bizonyos esetekben a kivételek kiszivárgása történt.
- Memóriavesztés kijavítása a fordítási események argumentumai között.
- Kijavított egy zárolási problémát a hosszú ideig futó munkamenetekben való újrakapcsolódáskor.
- Kijavított egy problémát, amely a sikertelen fordítások végső eredményét eredményezheti.
- C#: Ha egy `async` művelet nem volt várt a fő szálban, lehetséges, hogy a felismerő el lett távolítva az aszinkron feladat befejezése előtt.
- Java: javítva a probléma a Java virtuális gép összeomlása miatt.
- Objective-C: rögzített enumerálási leképezés; A RecognizedIntent visszatért a következő helyett: `RecognizingIntent` .
- JavaScript: alapértelmezett kimeneti formátum beállítása "Simple" értékre a alkalmazásban `SpeechConfig` .
- JavaScript: a konfigurációs objektum Tulajdonságok közötti inkonzisztencia eltávolítása a JavaScriptben és más nyelveken.

**Példák**

- Több minta frissítése és javítása (például kimeneti hangok a fordításhoz stb.).
- Node.js mintákat vett fel a [minta adattárba](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**Új funkciók**

- Támogatás Android x86/x64 rendszerhez.
- Proxy-támogatás: az `SpeechConfig` objektumban mostantól meghívhat egy függvényt a proxy információk (állomásnév, port, Felhasználónév és jelszó) beállítására. Ez a funkció még nem érhető el az iOS rendszeren.
- Javított hibakódok és üzenetek. Ha egy felismerés hibát adott vissza, akkor ez már be lett állítva `Reason` (megszakított eseménynél) vagy `CancellationDetails` (az elismerés eredményében) `Error` . A megszakított esemény most két további tagot tartalmaz, `ErrorCode` és `ErrorDetails` . Ha a kiszolgáló további hibaüzeneteket adott vissza a jelentett hibával, mostantól elérhető lesz az új tagokban.

**Fejlesztései**

- További ellenőrzés történt a felismerő konfigurációjában, és további hibaüzenetet adott hozzá.
- Hatékonyabban kezelhető a hosszú idő a hangfájlok közepén.
- NuGet csomag: a .NET-keretrendszer projektjeihez a AnyCPU-konfigurációval való kiépítés megakadályozása.

**Hibajavítások**

- Rögzített több kivétel található a felismerők között. Emellett a kivételek bekerülnek, és az `Canceled` eseményre konvertálódnak.
- Memóriavesztés kijavítása a Property Management szolgáltatásban.
- Kijavítva a hiba, amelyben egy hangbemeneti fájl összeomlhat a felismerővel.
- Kijavítva egy hiba, amelyben az események egy munkamenet-leállítási esemény után fogadhatók.
- Rögzített bizonyos versenyhelyzet-feltételek a Threading-ben.
- Javítva lett egy iOS-kompatibilitási probléma, amely összeomlást eredményezhet.
- Az androidos mikrofonok támogatásának stabilitási fejlesztése.
- Kijavítva a hiba, ha egy felismerő a JavaScriptben figyelmen kívül hagyja az elismerés nyelvét.
- Kijavítva egy hiba, amely megakadályozza a JavaScript beállítását `EndpointId` (bizonyos esetekben).
- A AddIntent megváltoztatott a JavaScriptben, és hozzáadta a hiányzó `AddIntent` JavaScript-aláírást.

**Példák**

- C++ és C# minták lettek hozzáadva a lekéréses és leküldéses adatfolyam-használathoz a [minta tárházban](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

Megbízhatósági javítások és hibajavítások:

- Rögzített potenciális végzetes hiba történt a versenyhelyzet ártalmatlanítását kiváltó feltétele miatt
- Kijavított lehetséges végzetes hiba a nem beállított tulajdonságok esetében.
- További hibák és paraméterek ellenőrzése hozzáadva.
- Objective-C: a lehetséges végzetes hiba történt a NSString-ben a név felülbírálása miatt.
- Objective-C: az API igazított láthatósága
- JavaScript: az eseményekkel és azok hasznos adataival kapcsolatban rögzített.
- Dokumentációs újítások.

A [minta adattárában](https://aka.ms/csspeech/samples)egy új minta lett hozzáadva a javascripthez.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0:2018 – szeptemberi kiadás

**Új funkciók**

- Az Objective-C támogatása iOS rendszeren. Tekintse meg [az iOS-hez készült Objective-C](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md)rövid útmutatót.
- JavaScript-támogatás a böngészőben. Tekintse meg a [JavaScript](quickstart-js-browser.md)rövid útmutatót.

**Változtatások megszakítása**

- Ebben a kiadásban számos megszakított változást vezetünk be.
  További részletekért olvassa el [ezt a lapot](https://aka.ms/csspeech/breakingchanges_1_0_0) .

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0:2018 – augusztus kiadás

**Új funkciók**

- A Speech SDK-val létrehozott UWP-alkalmazások mostantól átadhatják a Windows-alkalmazás minősítési csomagját (WACK).
  Tekintse meg a [UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp)rövid útmutatót.
- .NET Standard 2,0-támogatás Linux rendszeren (Ubuntu 16,04 x64).
- Kísérleti: a Java 8 támogatása Windows (64 bites) és Linux rendszeren (Ubuntu 16,04 x64).
  Tekintse meg a [Java Runtime Environment](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre)rövid útmutatót.

**Funkcionális változás**

- További hibák részletes információinak közzététele a csatlakoztatási hibákról.

**Változtatások megszakítása**

- Java (Android) esetén a `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` függvénynek már nincs szüksége elérésiút-paraméterre. A rendszer mostantól automatikusan észleli az elérési utat az összes támogatott platformon.
- A Java-és C#-beli tulajdonság get-accesser `EndpointUrl` eltávolítása megtörtént.

**Hibajavítások**

- A Java-ban a fordítási felismerő hangszintézisének eredménye most már megvalósítva van.
- Kijavítva egy olyan hibát, amely inaktív szálakat okozhat, és nagyobb számú nyitott és nem használt szoftvercsatorna van.
- Javítva a probléma, ahol a hosszan futó felismerés megszakadhat az átvitel közepén.
- Rögzített versenyhelyzet a felismerő leállításakor.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0:2018 – júliusi kiadás

**Új funkciók**

- Támogatás Android platform (API 23: Android 6,0 Marshmallow vagy újabb). Tekintse meg az [androidos](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android)rövid útmutatót.
- A .NET Standard 2,0 támogatása Windows rendszeren. Tekintse meg a [.net Core](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)rövid útmutatót.
- Kísérleti: a UWP támogatása Windows rendszeren (1709-es vagy újabb verzió).
  - Tekintse meg a [UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)rövid útmutatót.
  - Megjegyzés: a Speech SDK-val készített UWP-alkalmazások még nem adják át a Windows-alkalmazás minősítési csomagját (WACK).
- A hosszan futó felismerés támogatása automatikus újracsatlakoztatással.

**Funkcionális változások**

- `StartContinuousRecognitionAsync()`támogatja a hosszan futó felismerést.
- A felismerési eredmény több mezőt tartalmaz. Ezek a felismert szöveg és a felismerési állapotot jelképező további értékek (például a kullancsok) és az azokhoz tartozó további értékek (például: és) eltolása `InitialSilenceTimeout` `InitialBabbleTimeout` .
- Támogatási AuthorizationToken a gyári példányok létrehozásához.

**Változtatások megszakítása**

- Felismerési események: `NoMatch` az esemény típusa egyesítve lett az `Error` eseménybe.
- A C# nyelvben lévő SpeechOutputFormat átnevezték, `OutputFormat` hogy a C++ nyelvre legyen igazítva.
- A csatoló egyes módszereinek visszatérési típusa `AudioInputStream` némileg megváltozott:
  - A Java-ban a `read` metódus most a helyett a értéket adja vissza `long` `int` .
  - A C# nyelvben a `Read` metódus most a helyett a értéket adja vissza `uint` `int` .
  - A C++ nyelvben a `Read` és a `GetFormat` metódusok mostantól a helyett a értéket adják vissza `size_t` `int` .
- C++: a hangbemeneti adatfolyamok példányai mostantól csak a-ként adhatók át `shared_ptr` .

**Hibajavítások**

- Időtúllépés esetén az eredményben rögzített helytelen visszatérési értékek vannak megadva `RecognizeAsync()` .
- A Windows Media Foundation-kódtárainak függősége el lett távolítva. Az SDK mostantól a Core audio API-kat használja.
- Dokumentációs javítás: hozzáadott egy [régiókat](regions.md) tartalmazó lapot a támogatott régiók leírásához.

**Ismert probléma**

- Az Androidhoz készült Speech SDK nem jelenti a fordításhoz tartozó beszédfelismerési eredmények jelentését. Ezt a problémát a következő kiadásban rögzíti a rendszer.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0:2018 – júniusi kiadás

**Funkcionális változások**

- AudioInputStream

  A felismerő mostantól hangforrásként használhatja a streamet. További információ: [a kapcsolódó útmutató](how-to-use-audio-input-streams.md).

- Részletes kimeneti formátum

  A létrehozásakor `SpeechRecognizer` kérheti vagy megadhatja a kívánt `Detailed` `Simple` formátumot. A `DetailedSpeechRecognitionResult` tartalmazza a megbízhatósági pontszámot, a felismert szöveget, a nyers lexikális űrlapot, a normalizált formát és a normalizált formát maszkolásos káromkodással.

**Változás megszakítása**

- Módosítva a `SpeechRecognitionResult.Text` következőre `SpeechRecognitionResult.RecognizedText` : C#.

**Hibajavítások**

- Egy lehetséges visszahívási probléma javítva az USP-rétegben a leállítás során.
- Ha a felismerő hangbemeneti fájlt használt, a fájl a szükségesnél hosszabb ideig tartott.
- Az üzenet-szivattyú és a felismerő között több holtpont is megszűnt.
- `NoMatch`Ha a szolgáltatás válasza időtúllépés miatt megtörténik, a folyamat tüzet eredményez.
- A Windows Media Foundation kódtárai betöltődik. Ez a függvénytár csak a mikrofon bemenetéhez szükséges.
- A hangadatok feltöltési sebessége az eredeti hangsebesség körülbelül kétszeresére korlátozódik.
- Windows rendszeren a C# .NET-szerelvények már erős névvel rendelkeznek.
- Dokumentációs javítás: a `Region` felismerő létrehozásához szükséges információ.

További minták lettek hozzáadva, és folyamatosan frissülnek. A legújabb mintákhoz lásd a [SPEECH SDK Samples GitHub-tárházát](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733:2018 – májusi kiadás

Ez a kiadás a Cognitive Services Speech SDK első nyilvános előzetes kiadása.
