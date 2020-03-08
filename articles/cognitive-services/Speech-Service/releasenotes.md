---
title: Kibocsátási megjegyzések – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A Speech Service egy futó naplója, amely kiadásokat, javításokat, hibajavításokat és ismert problémákat tartalmaz.
services: cognitive-services
author: brianem
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: brianem
ms.custom: seodec18
ms.openlocfilehash: cbe9c7cbd0f402e38d1163050d77b055f89948ba
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394780"
---
# <a name="release-notes"></a>Kibocsátási megjegyzések
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
 
 **Minták**
 - Unity-minta a Speech SDK [és az Unity mikrofon és a leküldéses mód streaming](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone) használatával való használatához

**Egyéb változások**
 - [Az OpenSSL konfigurációs dokumentációjának frissítése Linuxra](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)

## <a name="speech-sdk-190-2020-january-release"></a>Speech SDK 1.9.0:2020 – januári kiadás

**Új funkciók**

- Többeszközes beszélgetés: csatlakoztasson több eszközt ugyanahhoz a beszéd-vagy szöveges beszélgetéshez, és igény szerint fordítsa le a közöttük küldött üzeneteket. További információt [ebben a cikkben](multi-device-conversation.md)talál. 
- A kulcsszó-felismerési támogatás hozzáadva az Android. éves kiadási csomaghoz, valamint az x86-és x64-alapú ízek támogatása. 
- Objective-C: az `Connection` objektumhoz hozzáadott `SendMessage` és `SetMessageProperty` metódusok. [Itt](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection)találja a dokumentációt.
- A C++ TTS API mostantól támogatja a `std::wstring`t a szintézis szövegeként, így nem kell konvertálnia a wstring a karakterláncra, mielőtt átadná azt az SDK-nak. Tekintse meg [a részleteket.](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync) 
- C#: Mostantól elérhető a [nyelvi azonosító](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) és a [forrás nyelvi konfigurációja](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp) .
- JavaScript: hozzáadta a szolgáltatást `Connection` objektumhoz, hogy átadja a beszédfelismerési szolgáltatás egyéni üzeneteit a visszahívási `receivedServiceMessage`.
- JavaScript: a `FromHost API` támogatása a helyszíni tárolókkal és a szuverén Felhőkkel való egyszerű használat érdekében. [Itt](speech-container-howto.md)találja a dokumentációt.
- JavaScript: most megtiszteltetés `NODE_TLS_REJECT_UNAUTHORIZED` a [orgads](https://github.com/orgads)hozzájárulásának köszönhetően. Tekintse meg [a részleteket.](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75)


**Változtatások megszakítása**

- a `OpenSSL` a 1.1.1-es verzióra frissült, és statikusan kapcsolódik a Linux rendszerhez készült Speech SDK Core-könyvtárhoz. Ez akkor fordulhat elő, ha a beérkezett fájlok mappájában `OpenSSL` nem lett telepítve a rendszer `/usr/lib/ssl` könyvtárába. A probléma megoldásához tekintse meg a Speech SDK [dokumentációját](how-to-configure-openssl-linux.md) .
- Módosítottuk a C# `WordLevelTimingResult.Offset`hoz visszaadott adattípust `int`ról `long`re, hogy lehetővé tegyék `WordLevelTimingResults` elérését, ha a beszédfelismerési adatok 2 percnél hosszabbak.
- `PushAudioInputStream` és `PullAudioInputStream` a WAV-fejlécek adatait `AudioStreamFormat`alapján is elküldheti a beszédfelismerési szolgáltatásnak, opcionálisan megadhatja őket a létrehozásuk során. Az ügyfeleknek most a [támogatott hangbemenet formátumot](how-to-use-audio-input-streams.md)kell használniuk. Bármilyen más formátum esetén a rendszer az optimálisnál rosszabb eredményeket kap, vagy más problémákat okozhat. 


**Hibajavítások**

- Tekintse meg a `OpenSSL` frissítést a fenti módosítások feltörése alatt. A Linux és a Java esetében is rögzítettünk egy időszakos összeomlást és egy teljesítménnyel kapcsolatos problémát (nagy terhelés melletti zárolást). 
- Java: az objektumok bezárásának tökéletesítése magas egyidejűségi helyzetekben.
- Átalakítottuk a NuGet-csomagot. Eltávolította a `Microsoft.CognitiveServices.Speech.core.dll` három példányát, és `Microsoft.CognitiveServices.Speech.extension.kws.dll` a lib-mappák alatt, így a NuGet-csomag kisebb és gyorsabb lesz, és felvettük a C++ natív alkalmazások fordításához szükséges fejléceket.
- [Itt talál](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp)rögzített rövid útmutatót. Ezek a "mikrofon nem található" kivétel nélkül lettek kizárva a Linux, MacOS és Windows rendszereken.
- Rögzített SDK-összeomlás a hosszú beszédfelismerés eredményeként bizonyos kódok elérési útjain, például [a](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp)mintában.
- Az Azure webalkalmazás-környezet rögzített SDK-telepítési hibája a [probléma](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396)megoldásához.
- A [probléma](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433)megoldásához több `<voice>` címke vagy `<audio>` címke használatakor hiba történt. 
- A rendszer felfüggesztette a TTS 401 hibát, ha az SDK-t felfüggesztették a felfüggesztésből.
- JavaScript: rögzített hangadatok körkörös importálása a [euirim](https://github.com/euirim)hozzájárulásának köszönhetően. 
- JavaScript: a szolgáltatás tulajdonságainak beállításához hozzáadott támogatás a 1,7-as verzióban.
- JavaScript: kijavított egy hibát, amikor egy kapcsolati hiba folyamatos, sikertelenül működő WebSocket-újracsatlakozási kísérleteket eredményezhet.


**Minták**

- Kulcsszó-felismerési minta hozzáadva az Androidhoz [itt](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo).
- A [kiszolgáló forgatókönyvéhez](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs)hozzáadott TTS-minta.
- Többeszközes beszélgetésekkel kapcsolatos C# C++ rövid [útmutatók.](quickstarts/multi-device-conversation.md)


**Egyéb változások**

- Optimalizált SDK-Alapkönyvtár mérete Androidon.
- Az SDK a 1.9.0-ben és a-ben egyaránt támogatja a `int` és a `string` típust is a beszélgetési átiratok hangaláírási verzió mezőjében.

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0:2019 – november kiadás

**Új funkciók**

- Egy `FromHost()` API-t adott hozzá, amely megkönnyíti a helyszíni tárolók és a szuverén felhők használatát.
- A beszédfelismeréshez hozzáadott automatikus forrás Nyelvfelismerés (Java és C++)
- Felvette `SourceLanguageConfig` objektumot a beszédfelismeréshez, amely a várt forrás nyelvek megadására szolgál C++(Java és)
- A Windows (UWP), az Android és az iOS `KeywordRecognizer` támogatásának hozzáadása a NuGet és az Unity csomagon keresztül
- Távoli beszélgetési Java API hozzáadva a beszélgetések átírásához aszinkron kötegekben.

**Változtatások megszakítása**

- A beszélgetési átirat funkciói a névtér `Microsoft.CognitiveServices.Speech.Transcription`alatt lettek áthelyezve.
- A beszélgetési átirat módszereinek egy része új `Conversation` osztályba kerül.
- Eldobott támogatás a 32 bites (ARMv7 és x86) iOS-hez

**Hibajavítások**

- Az összeomlás javítása, ha a helyi `KeywordRecognizer` érvényes Speech Service-előfizetési kulcs nélkül használja

**Minták**

- `KeywordRecognizer` Xamarin mintája
- Unity minta `KeywordRecognizer`
- C++és Java-mintákat az automatikus forrás Nyelvfelismerés.

## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0:2019 – szeptemberi kiadás

**Új funkciók**

- Bétaverziós támogatás hozzáadva a Xamarin Univerzális Windows-platform (UWP), Android és iOS rendszerhez
- IOS-támogatás hozzáadva az Unity-hoz
- A atörvény, a mulaw, a FLAC és az Android, az iOS és a Linux rendszerhez hozzáadott `Compressed` bemeneti támogatás
- `SendMessageAsync` hozzáadva a `Connection` osztályban, hogy üzenetet küldjön a szolgáltatásnak
- `SetMessageProperty` hozzáadva a `Connection` osztályban egy üzenet beállítása tulajdonságához
- A Java (JRE és Android), a Python, a Swift és a Objective-C kötések hozzáadva
- A macOS, iOS és Android rendszerekhez készült lejátszási támogatás hozzáadva.
- "Word határ" információ hozzáadva a TTS-hez.

**Hibajavítások**

- Rögzített IL2CPP-Build probléma az 2019-es Unity Android rendszerhez
- Helytelenül formázott fejléceket tartalmazó hiba kijavítva a WAV-fájlban megadott bemeneten
- Az UUID-mel kapcsolatos kijavított probléma bizonyos kapcsolatok tulajdonságaiban nem egyedi.
- Néhány figyelmeztetés a Swift-kötésekben a nullák megadásával kapcsolatban (kis kód megváltoztatására lehet szükség)
- Kijavítva egy olyan hibát, amely miatt a WebSocket-kapcsolatok nem megfelelően vannak lezárva a hálózati terhelés alatt
- Kijavított egy problémát az Androidon, amely időnként az `DialogServiceConnector` által használt duplikált benyomási azonosítókat eredményezi
- A kapcsolatok stabilitásának fejlesztése több fordulatos interakciók és a hibák jelentése (`Canceled` eseményeken keresztül), ha `DialogServiceConnector`
- `DialogServiceConnector` munkamenet indításakor mostantól megfelelő események is megadhatók, beleértve az aktív `StartKeywordRecognitionAsync()` során `ListenOnceAsync()` meghívásakor.
- A kapott `DialogServiceConnector` tevékenységekhez kapcsolódó összeomlást észlelt

**Minták**

- Gyors útmutató a Xamarin
- Frissített CPP-gyors útmutató Linux ARM64-információkkal
- Frissített Unity rövid útmutató iOS-adatokkal

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0:2019 – júniusi kiadás

**Minták**

- Gyors példák a UWP és az Unity szövegének beszédére
- Gyors üzembe helyezési minta iOS rendszeren
- A speech & Szándékfelismerés és a fordítás Unity mintái
- Frissített gyors példák a `DialogServiceConnector`

**Tökéletesítések/változások**

- Párbeszédpanel névtere:
  - A `SpeechBotConnector` új nevet kapott: `DialogServiceConnector`
  - A `BotConfig` új nevet kapott: `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()` újra le lett képezve `DialogServiceConfig::FromBotSecret()`
  - Az Átnevezés után az összes meglévő közvetlen beszédfelismerési ügyfél továbbra is támogatott.
- Az TTS REST-adapter frissítése a proxy és az állandó kapcsolatok támogatásához
- Javítsa a hibaüzenetet, ha egy érvénytelen régiót ad át
- Swift/Objective-C:
  - Továbbfejlesztett hibajelentés: az olyan metódusok, amelyek egy hibát okozhatnak, két verzióban jelennek meg: az egyik, amely egy `NSError` objektumot tesz elérhetővé a hibák kezelésére, és egy kivételt felvet. Az előbbi a Swift számára elérhetővé válnak. Ehhez a változáshoz a meglévő Swift-kódnak való átalakításra van szükség.
  - Továbbfejlesztett események feldolgozása

**Hibajavítások**

- Javítás a TTS-hez: Ha a `SpeakTextAsync` a jövőben nem várakozik, amíg a hang befejezte a renderelést
- A teljes nyelvi támogatás engedélyezéséhez C# javítsa a karakterláncok előkészítését
- A .NET Core-alkalmazás problémáinak elhárítása az alapszintű függvénytár betöltéséhez a mintákban a net461 Target Framework használatával
- Javítsa ki az esetenkénti problémákat, hogy natív kódtárakat helyezzen üzembe a minták kimeneti mappájába
- Javítás a webes szoftvercsatorna megbízható bezárásakor
- Javítsa a lehetséges összeomlást a Linuxon nagyon nagy terhelés alatt álló kapcsolatok megnyitásakor
- Javítsa ki a hiányzó metaadatokat a macOS-keretrendszer csomagban
- A Windows `pip install --user` kapcsolatos problémák elhárítása

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

**Minták**

- További minták az SDK által támogatott új szolgáltatásokhoz vagy új szolgáltatásokhoz.

**Tökéletesítések/változások**

- Különböző felismerő tulajdonságok lettek hozzáadva a szolgáltatás viselkedésének vagy a szolgáltatás eredményeinek (például maszkolási káromkodás és mások) beállításához.
- Mostantól konfigurálhatja a felismerőt a szabványos konfigurációs tulajdonságok segítségével, még akkor is, ha létrehozta a felismerő `FromEndpoint`.
- Objective-C: `OutputFormat` tulajdonság hozzáadva a `SPXSpeechConfiguration`hoz.
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

- Az SDK mostantól támogatja a Text-to-speech szolgáltatást béta verzióként. A Windows és a Linux rendszerű asztali C++ számítógépeken C#támogatott. További információkért tekintse meg a [szöveg – beszéd áttekintést](text-to-speech.md#get-started).
- Az SDK mostantól stream bemeneti fájlként támogatja az MP3 és az Opus/OGG hangfájlokat. Ez a funkció csak a (z) C++ és C# rendszerű Linux rendszeren érhető el (további részletek [itt](how-to-use-codec-compressed-audio-input-streams.md)).
- A Java, a .NET Core C++ és a Objective-C beszédfelismerési SDK-val MacOS-támogatást szerzett. A macOS-hez készült Objective-C-támogatás jelenleg béta verzióban érhető el.
- iOS: az iOS-hez készült Speech SDK (Objective-C) mostantól CocoaPod is közzé lett téve.
- JavaScript: nem alapértelmezett mikrofon bemeneti eszközként való támogatása.
- JavaScript: proxy-támogatás a Node. js-hez.

**Minták**

- A (z) C++ és a (z) és a (z) és a (z) és a MacOS rendszerhez készült Speech SDK használatát a
- A Text-to-speech szolgáltatás használatát bemutató minták lettek hozzáadva.

**Tökéletesítések/változások**

- Python: a felismerési eredmények további tulajdonságai már elérhetők a `properties` tulajdonságon keresztül.
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

- A Speech SDK a `AudioConfig` osztályon keresztül támogatja a bemeneti mikrofon kijelölését. Ez lehetővé teszi a hangadatoknak a beszédfelismerési szolgáltatásba való továbbítását egy nem alapértelmezett mikrofonból. További információkért tekintse meg a [hangbeviteli eszköz kijelölését](how-to-select-audio-input-devices.md)ismertető dokumentációt. Ez a funkció még nem érhető el a JavaScriptből.
- A Speech SDK mostantól támogatja az Unity használatát egy bétaverziós verzióban. Visszajelzés küldése a [GitHub-minta tárházának](https://aka.ms/csspeech/samples)probléma szakaszában. Ez a kiadás támogatja az Unity használatát a Windows x86 és x64 (asztali vagy Univerzális Windows-platform alkalmazások) és az Android (ARM32/64, x86) esetében. További információ az [Unity](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity)gyors üzembe helyezési útmutatójában található.
- A (korábbi kiadásokban szállított) `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` fájl már nem szükséges. A funkció mostantól integrálva van az alap SDK-ba.

**Minták**

A következő új tartalom érhető el a [példában szereplő adattárban](https://aka.ms/csspeech/samples):

- További minták a `AudioConfig.FromMicrophoneInput`hoz.
- További Python-minták a szándék-felismeréshez és a fordításhoz.
- További minták a `Connection` objektum iOS-ben való használatához.
- További Java-minták hang kimenettel való fordításhoz.
- Új minta a [Batch-Átírási REST API](batch-transcription.md)használatához.

**Tökéletesítések/változások**

- Python
  - Továbbfejlesztett paraméterek ellenőrzése és hibaüzenetek `SpeechConfig`ban.
  - Adja hozzá a `Connection` objektum támogatását.
  - A 32-bites Python (x86) támogatása Windows rendszeren.
  - A Pythonhoz készült Speech SDK a bétaverzión kívül van.
- iOS
  - Az SDK mostantól az iOS SDK 12,1-es verziójára épül.
  - Az SDK mostantól támogatja az iOS 9,2-es és újabb verzióit.
  - Javítsa a hivatkozási dokumentációt, és javítsa ki a tulajdonságok nevét.
- JavaScript
  - Adja hozzá a `Connection` objektum támogatását.
  - Type Definition Files hozzáadása a kötegelt JavaScripthez
  - Kezdeti támogatás és megvalósítás a kifejezésekre vonatkozó útmutatókhoz.
  - Visszatérési tulajdonságok gyűjteménye a szolgáltatás JSON-vel való felismeréséhez
- A Windows-DLL-ek mostantól egy verzió-erőforrást tartalmaznak.
- Ha létrehoz egy felismerőt `FromEndpoint` hozzáadhat paramétereket közvetlenül a végpont URL-címéhez. A `FromEndpoint` használata nem tudja konfigurálni a felismerőt a szabványos konfigurációs tulajdonságok használatával.

**Hibajavítások**

- Az üres proxy felhasználóneve és a proxy jelszava helytelenül lett kezelve. Ebben a kiadásban, ha a proxy felhasználónevét és a proxy jelszavát üres karakterlánccá állítja be, a rendszer nem küldi el a proxyhoz való csatlakozáskor.
- Az SDK által létrehozott munkamenet-azonosítók nem mindig voltak igazán véletlenszerűek egyes nyelveken&nbsp;/környezetekben. A probléma megoldásához hozzáadta a véletlenszerű generátor inicializálását.
- Az engedélyezési tokenek kezelését javítani kell. Ha engedélyezési jogkivonatot szeretne használni, akkor a `SpeechConfig`ban válassza az előfizetési kulcsot, és hagyja üresen. Ezután hozza létre a felismerőt a szokásos módon.
- Bizonyos esetekben a `Connection` objektum helytelenül lett közzétéve. Ez a probléma megoldódott.
- A JavaScript-minta úgy lett javítva, hogy támogassa a fordítási szintézis hangkimenetét a Safarion is.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Ez egy csak JavaScript kiadás. Nem lettek hozzáadva funkciók. A következő javítások történtek:

- A stream végén a turn. End, nem a Speech. End.
- Javítsa ki azt a hibát a hangszivattyúban, amely nem ütemezett következő küldést, ha az aktuális küldés sikertelen volt.
- Az Auth-jogkivonat folyamatos felismerésének javítása.
- Hibajavítás különböző felismerőhöz/végpontokhoz.
- Dokumentáció fejlesztései.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0:2018 – decemberi kiadás

**Új funkciók**

- Python
  - A Python-támogatás bétaverziója (3,5 és újabb) ebben a kiadásban érhető el. További információ: itt] (rövid útmutató – python.md).
- JavaScript
  - A JavaScripthez készült Speech SDK nyílt forráskódú. A forráskód elérhető a [githubon](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - Mostantól a Node. js-t is támogatja, további információ [itt](quickstart-js-node.md)található.
  - A hangmunkamenetek hosszának korlátozása el lett távolítva, az újrakapcsolódás automatikusan megtörténik a fedél alatt.
- `Connection` objektum
  - A `Recognizer`egy `Connection` objektumhoz férhet hozzá. Ezzel az objektummal explicit módon kezdeményezheti a szolgáltatás kapcsolatát, és előfizethet az események csatlakoztatására és leválasztására.
    (Ez a funkció még nem érhető el a JavaScriptből és a Pythonból.)
- Az Ubuntu 18,04 támogatása.
- Android
  - Engedélyezve van a kisegítő képesség az APK-generációban.

**Fejlesztései**

- A belső szál használatának fejlesztése, a szálak, a zárolások és a mutexek számának csökkentése.
- Továbbfejlesztett hibajelentés/információk. Számos esetben a hibaüzenetek nem lettek propagálva az összes kiút.
- Frissített fejlesztői függőségek a JavaScriptben, hogy naprakész modulokat használjanak.

**Hibajavítások**

- A `RecognizeAsync`ban nem egyező típusú rögzített memória-szivárgások.
- Bizonyos esetekben a kivételek kiszivárgása történt.
- Memóriavesztés kijavítása a fordítási események argumentumai között.
- Kijavított egy zárolási problémát a hosszú ideig futó munkamenetekben való újrakapcsolódáskor.
- Kijavított egy problémát, amely a sikertelen fordítások végső eredményét eredményezheti.
- C#: Ha a fő szálban nem várt `async` művelet, lehetséges, hogy a felismerő el lett távolítva az aszinkron feladat befejezése előtt.
- Java: javítva a probléma a Java virtuális gép összeomlása miatt.
- Objective-C: rögzített enumerálási leképezés; `RecognizingIntent`helyett a RecognizedIntent lett visszaadva.
- JavaScript: alapértelmezett kimeneti formátum beállítása "Simple" értékre `SpeechConfig`ban.
- JavaScript: a konfigurációs objektum Tulajdonságok közötti inkonzisztencia eltávolítása a JavaScriptben és más nyelveken.

**Minták**

- Több minta frissítése és javítása (például kimeneti hangok a fordításhoz stb.).
- Node. js-minták lettek hozzáadva a [minta tárházban](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**Új funkciók**

- Android x86/x64 támogatása.
- Proxy-támogatás: a `SpeechConfig` objektumban meghívhat egy függvényt a proxy információk (állomásnév, port, Felhasználónév és jelszó) beállításához. Ez a funkció még nem áll rendelkezésre álló IOS-eszközökön.
- Továbbfejlesztett hibakód és üzenetek. Ha egy felismerés hibát adott vissza, akkor a `Reason` (a megszakított eseménynél), vagy `CancellationDetails` (az elismerés eredménye) `Error`. A megszakított esemény most két további tagot tartalmaz, `ErrorCode` és `ErrorDetails`. A kiszolgáló további információ a hibáról az a jelzett hibát adott vissza, ha most lesz elérhető az új tagjait.

**Fejlesztései**

- További ellenőrzés hozzáadva a felismerő konfigurációs és további hozzáadott hibaüzenet jelenik meg.
- Hangfájl közepén régóta csend jobb kezelése.
- NuGet-csomagot: .NET-keretrendszer projektek esetén ez megakadályozza, hogy használhatja a AnyCPU konfiguráció.

**Hibajavítások**

- Rögzített felismerő található kivételek. Emellett a kivételek bekerülnek és `Canceled` eseményre konvertálódnak.
- A tulajdonság felügyeleti memóriavesztés ki.
- Kijavítva a hiba, amelyben a bemeneti hangfájl összeomlási sikerült a felismerő.
- Kijavítva a hiba, ahol események tudta fogadott munkamenet leállítási esemény után.
- Rögzített threading néhány versenyhelyzet feltételeket.
- Rögzített egy IOS-es kompatibilitási probléma, amely egy összeomlási eredményezhet.
- Jobb stabilitás Android mikrofon támogatás.
- Kijavítva a hiba, ahol a JavaScript-felismerő lenne figyelmen kívül hagyja a beszédfelismerési nyelv.
- Kijavítva egy hiba, amely megakadályozza a `EndpointId` (bizonyos esetekben) a JavaScriptben.
- A AddIntent-ben megváltozott a paraméterek sorrendje a JavaScriptben, és a rendszer hiányzó `AddIntent` JavaScript-aláírást adott hozzá.

**Minták**

- A C++ rendszer C# hozzáadta és mintákat ad a lekéréses és leküldéses adatfolyam-használathoz a [minta tárházban](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

Hibajavításokat tartalmaz, és megbízhatóság fejlesztései:

- Felismerő értékesítésére versenyhelyzet miatt rögzített lehetséges végzetes hiba
- Rögzített lehetséges végzetes hiba esetén az adott Orientation tulajdonságait.
- A hozzáadott további hiba és a paraméter ellenőrzése.
- Objective-c rögzített lehetséges végzetes hiba okozza a NSString felülbírálása neve.
- Objective-c igazítva látható-e API
- JavaScript: Rögzített eseményeket és azok is észleltünk adattartalmakat.
- Dokumentáció fejlesztései.

A [minta adattárában](https://aka.ms/csspeech/samples)egy új minta lett hozzáadva a javascripthez.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services beszédfelismerő SDK 1.0.0-s: 2018. szeptember kiadás

**Új funkciók**

- Objective-C támogatása IOS-eszközökön. Tekintse meg [az iOS-hez készült Objective-C](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md)rövid útmutatót.
- A böngészőben JavaScript támogatása. Tekintse meg a [JavaScript](quickstart-js-browser.md)rövid útmutatót.

**Változtatások megszakítása**

- Ebben a kiadásban számos megszakított változást vezetünk be.
  További részletekért olvassa el [ezt a lapot](https://aka.ms/csspeech/breakingchanges_1_0_0) .

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services beszédfelismerő SDK 0.6.0: 2018 augusztus kiadás

**Új funkciók**

- UWP-alkalmazás most már a Speech SDK-val készített továbbíthatja a Windows App Certification Kit (WACK).
  Tekintse meg a [UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp)rövid útmutatót.
- A .NET Standard 2.0 Linux (Ubuntu 16.04 x 64) támogatása.
- Kísérleti funkció: Támogatja a Java 8 (64 bites) Windows és Linux (Ubuntu 16.04 x 64).
  Tekintse meg a [Java Runtime Environment](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre)rövid útmutatót.

**Funkcionális változás**

- Tegyen elérhetővé további részletes információ a hibáról a csatlakozási hibák.

**Változtatások megszakítása**

- Java (Android) esetén a `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` függvényhez már nem szükséges elérésiút-paraméter. Az elérési út most már automatikusan észleli az összes támogatott platformon.
- A (`EndpointUrl` Java- C# ban található tulajdonság get-accesser objektuma el lett távolítva.

**Hibajavítások**

- A Java a fordítási felismerő hang összefoglaló eredménye van megvalósítva, mostantól.
- Kijavítva a hiba, inaktív a szálak és a egy nyílt és a fel nem használt sockets megnövelt számú okozhatja.
- Rögzített probléma, ahol egy hosszú ideig futó felismerése sikerült leállítani a közepén továbbítására.
- Rögzített versenyhelyzet felismerő leállítása.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services beszédfelismerő SDK 0.5.0-s: 2018 július kiadás

**Új funkciók**

- Támogatás Android platform (API 23: Android 6.0-s Marshmallow vagy újabb). Tekintse meg az [androidos](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android)rövid útmutatót.
- A .NET Standard 2.0 támogatja a Windows. Tekintse meg a [.net Core](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)rövid útmutatót.
- Kísérleti: Támogatás Windows (1709-es vagy újabb verzió) az UWP.
  - Tekintse meg a [UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)rövid útmutatót.
  - Megjegyzés: A Speech SDK-val készített UWP-alkalmazás nem még meg a Windows App Certification Kit (WACK).
- Támogatja az automatikus újracsatlakozás hosszú ideig futó felismerése.

**Funkcionális változások**

- `StartContinuousRecognitionAsync()` támogatja a hosszan futó felismerést.
- A felismerés eredményét további mezőket tartalmaz. Ezek a felismert szöveg és a felismerési állapotot jelképező további értékek (a kullancsok esetében is) és az elszámolási állapotot jelző, például a `InitialSilenceTimeout` és az `InitialBabbleTimeout`.
- Támogatási AuthorizationToken előállító példányok létrehozásához.

**Változtatások megszakítása**

- Felismerési események: `NoMatch` eseménytípus be lett fésülve a `Error` eseménybe.
- A SpeechOutputFormat C# átnevezve lett `OutputFormat`ra, hogy a C++következővel legyen összehangolva:.
- A `AudioInputStream` csatoló egyes módszereinek visszatérési típusa némileg megváltozott:
  - A Java-ban a `read` metódus most a `int`helyett `long` ad vissza.
  - A C#-ben a `Read` metódus most a `int`helyett `uint` ad vissza.
  - A C++-ben a `Read` és `GetFormat` metódusok mostantól a `int`helyett `size_t` adnak vissza.
- C++: A hangbemeneti streamek példányai már csak `shared_ptr`ként adhatók át.

**Hibajavítások**

- Az eredményben rögzített helytelen visszatérési értékek `RecognizeAsync()` időtúllépés miatt.
- A Windows media foundation kódtárak függőség el lett távolítva. Az SDK mostantól az alapvető hang API-k.
- Dokumentációs javítás: hozzáadott egy [régiókat](regions.md) tartalmazó lapot a támogatott régiók leírásához.

**Ismert probléma**

- A beszédfelismerés SDK for Android nem jelentést speech összefoglaló eredmények a fordítás. A probléma a következő kiadás javítja.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services beszédfelismerő SDK 0.4.0: 2018 június kiadás

**Funkcionális változások**

- AudioInputStream

  Egy felismerő most már felhasználhatja a stream hang forrásaként. További információ: [a kapcsolódó útmutató](how-to-use-audio-input-streams.md).

- Részletes kimeneti formátum

  `SpeechRecognizer`létrehozásakor `Detailed` vagy `Simple` kimeneti formátumot kérhet le. A `DetailedSpeechRecognitionResult` tartalmaz egy megbízhatósági pontszámot, a felismert szöveget, a nyers lexikális űrlapot, a normalizált formát és a normalizált formát maszkolásos káromkodással.

**Változás megszakítása**

- `SpeechRecognitionResult.Text`ra módosult, `SpeechRecognitionResult.RecognizedText` C#a alkalmazásban.

**Hibajavítások**

- Kijavítottuk a lehetséges visszahívási a USP rétegben leállítás során.
- Egy felismerő felhasznált bemeneti hangfájl, ha azt volt üzemben szükséges hosszabb fájlleírót.
- Az üzenet szivattyú és a felismerő között számos holtpontok eltávolítva.
- Ha a szolgáltatás válasza időtúllépés miatt megtörténik, akkor a `NoMatch`.
- A Windows media foundation könyvtáraiban betöltött késleltetés. Ez a kódtár mikrofon bemeneti csak szükség.
- A feltöltési sebességét hívásaiból sebessége kétszer az eredeti hang korlátozva.
- Windows, a C# nyelvet használó .NET-szerelvények most strong neve.
- Dokumentációs javítás: `Region` a felismerő létrehozásához szükséges információk.

További példák lettek hozzáadva, és folyamatosan változik. A legújabb mintákhoz lásd a [SPEECH SDK Samples GitHub-tárházát](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services beszédfelismerő SDK 0.2.12733: 2018 – május kiadás

Ebben a kiadásban a Cognitive Services beszédfelismerő SDK első nyilvános előzetes verziója.
