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
ms.date: 02/25/2020
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: c7caa6c2b329d7d24d6c9a3008d884b396fc99ce
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83584909"
---
# <a name="release-notes"></a>Kibocsátási megjegyzések
## <a name="speech-sdk-1120-2020-may-release"></a>Speech SDK 1.12.0:2020 – májusi kiadás

**SPX Azure Speech Services parancssori konzol**
- Az **SPX** egy új parancssori eszköz, amellyel elvégezhető a parancssorból való felismerés, szintézis, fordítás, kötegelt átírás és egyéni beszédfelismerés. Ezzel tesztelheti az Azure Speech Service-t, vagy elvégezheti a szükséges Speech Service-feladatok futtatását. Töltse le az eszközt, és olvassa el [itt](https://docs.microsoft.com/azure/cognitive-services/speech-service/spx-overview)a dokumentációt.

**Új funkciók**

- **Go**: új go nyelvi támogatás a [beszédfelismeréshez](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/speech-to-text-from-microphone?pivots=programming-language-go) és az [Egyéni hangsegédekhez](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/voice-assistants?pivots=programming-language-go). [Itt](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-go)állíthatja be a fejlesztői környezetet. A mintakód esetében lásd a minták szakaszt alább. 
- **JavaScript**: a böngésző támogatja a szöveg és a beszéd használatát. [Itt](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/text-to-speech-audio-file?pivots=programming-language-JavaScript)találja a dokumentációt.
- **C++, C#, Java**: új KeywordRecognizer objektum és API-k támogatott Windows, Android, Linux & iOS platformokon. [Itt](https://docs.microsoft.com/azure/cognitive-services/speech-service/custom-keyword-overview)olvashatja el a dokumentációt. A mintakód esetében lásd a minták szakaszt alább. 
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
- Kijavított egy hibát, amelyben az IntentRecognizer eredményének oka nem volt megfelelően beállítva az egyszerű kifejezésekből származó leképezések felismerése során.
- Kijavított egy hibát, amelyben a SpeechRecognitionEventArgs-eredmény eltolása helytelenül lett beállítva.
- Rögzített egy versenyhelyzet, amelyben az SDK hálózati üzenetet próbált elküldeni a WebSocket-kapcsolat megnyitása előtt. Megismételhető a TranslationRecognizer a résztvevők hozzáadásakor.
- Rögzített memória-szivárgás a kulcsszó-felismerő motorban.

**Példák**

- **Go**: a [beszédfelismeréshez](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/speech-to-text-from-microphone?pivots=programming-language-go) és az [Egyéni hangsegédhez](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/voice-assistants?pivots=programming-language-go)hozzáadott gyors útmutatók. [Itt](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples)megtalálhatja a mintakód kódját. 
- **JavaScript**: gyors útmutató a [szöveg-beszéd](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/text-to-speech?pivots=programming-language-javascript), a [fordítás](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/translate-speech-to-text?pivots=programming-language-javascript)és a [Szándékfelismeréshoz](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/intent-recognition?pivots=programming-language-javascript).
- Kulcsszavas felismerési minták a [C \# ](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer) és a [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer) (Android) rendszerhez.  

**COVID – 19 rövidített tesztelés:**

Az elmúlt néhány hét során távolról végzett munka miatt nem sikerült a lehető legkevesebb kézi ellenőrzési tesztet végrehajtani, mint általában. Erre példa a mikrofon bemenetének és a hangszórók kimenetének tesztelése Linux, iOS és macOS rendszereken. Nem történt semmilyen olyan változás, amelyet úgy gondolunk, hogy bármi megszakadt ezen a platformon, és az automatizált tesztek mindegyike sikeres volt. Ha nem valószínű, hogy kihagytak valamit, kérjük, tudassa velünk a [githubon](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?page=2&q=is%3Aissue+is%3Aopen).<br>
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
    
**Covid19 – rövidített tesztelés**

Az elmúlt néhány hét során távolról végzett munka miatt nem sikerült a lehető legtöbb kézi eszköz-ellenőrzési tesztet végrehajtani, mint általában. Erre példa a mikrofon bemenetének és a hangszórók kimenetének tesztelése Linux, iOS és macOS rendszereken. Nem történt semmilyen olyan változás, amelyet úgy gondolunk, hogy bármi megszakadt ezen a platformon, és az automatizált tesztek mindegyike sikeres volt. Ha nem valószínű, hogy kihagytak valamit, kérjük, tudassa velünk a [githubon](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?page=2&q=is%3Aissue+is%3Aopen).<br> Köszönjük a folyamatos támogatást. Ahogy mindig, tegye fel kérdéseit vagy visszajelzéseit a [githubon](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?page=2&q=is%3Aissue+is%3Aopen) vagy a [stack Overflowon](https://stackoverflow.microsoft.com/questions/tagged/731).<br>
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

**Kompatibilitástörő változások**

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

**Kompatibilitástörő változások**

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
- JavaScript: proxy-támogatás a Node. js-hez.

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
  - Mostantól a Node. js-t is támogatja, további információ [itt](quickstart-js-node.md)található.
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
- Node. js-minták lettek hozzáadva a [minta tárházban](https://aka.ms/csspeech/samples).

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

**Kompatibilitástörő változások**

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

**Kompatibilitástörő változások**

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

**Kompatibilitástörő változások**

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
