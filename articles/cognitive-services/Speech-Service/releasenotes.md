---
title: Kibocsátási megjegyzések – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Tekintse meg a szolgáltatás kiadásait futtató naplót, a javításokat, a hibajavításokat és a beszédfelismerési szolgáltatás ismert problémáit.
services: cognitive-services
author: BrianMouncer
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: brianem
ms.custom: seodec18
ms.openlocfilehash: b40df5878d08b222d145531bfdad1e30b2fe989d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647405"
---
# <a name="release-notes"></a>Kibocsátási megjegyzések

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0:2019 – november kiadás

**Új funkciók**
*   Hozzáadott egy FromHost () API-t, amely megkönnyíti a helyszíni tárolók és a szuverén felhők használatát.
*   A beszédfelismeréshez hozzáadott automatikus forrás Nyelvfelismerés (Java és C++)
*   SourceLanguageConfig-objektum hozzáadva a beszédfelismeréshez, amely a várt forrás nyelvek megadására C++szolgál (Java és)
*   KeywordRecognizer-támogatás hozzáadva a Windows (UWP), az Android és az iOS rendszerhez a Nuget és az Unity csomagok segítségével
*   Távoli beszélgetési Java API hozzáadva a beszélgetések átírásához aszinkron kötegekben.

**Változtatások megszakítása**
*   A beszélgetési átirat funkciói a Microsoft. CognitiveServices. Speech. átíró névtér alatt lettek áthelyezve.
*   A beszélgetési átirat módszereinek egy részét áthelyezi az új beszélgetési osztályba.
*   Eldobott támogatás a 32 bites (ARMv7 és x86) iOS-hez 

**Hibajavítások**
*   Az összeomlás javítása, ha a helyi KeywordRecognizer érvényes Speech Service-előfizetési kulcs nélkül használja

**Minták**
*   Xamarin minta a KeywordRecognizer
*   Unity minta a KeywordRecognizer
*   C++és Java-mintákat az automatikus forrás Nyelvfelismerés.


## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0:2019 – szeptemberi kiadás

**Új funkciók**
*   Bétaverziós támogatás hozzáadva a Xamarin Univerzális Windows-platform (UWP), Android és iOS rendszerhez
*   IOS-támogatás hozzáadva az Unity-hoz
*   Tömörített bemeneti támogatás hozzáadva a atörvény, a mulaw, a FLAC és az Android, az iOS és a Linux rendszerekhez
*   SendMessageAsync hozzáadva a szolgáltatáshoz üzenet küldéséhez a kapcsolódási osztályban
*   SetMessageProperty hozzáadva a kapcsolási osztályban az üzenet beállítása tulajdonságához
*   A Java (JRE és Android), a Python, a Swift és a Objective-C kötések hozzáadva
*   A macOS, iOS és Android rendszerekhez készült lejátszási támogatás hozzáadva.
*   "Word határ" információ hozzáadva a TTS-hez.

**Hibajavítások**
*   Rögzített IL2CPP-Build probléma az 2019-es Unity Android rendszerhez
*   Helytelenül formázott fejléceket tartalmazó hiba kijavítva a WAV-fájlban megadott bemeneten
*   Az UUID-mel kapcsolatos kijavított probléma bizonyos kapcsolatok tulajdonságaiban nem egyedi.
*   Néhány figyelmeztetés a Swift-kötésekben a nullák megadásával kapcsolatban (kis kód megváltoztatására lehet szükség)
*   Kijavítva egy olyan hibát, amely miatt a WebSocket-kapcsolatok nem megfelelően vannak lezárva a hálózati terhelés alatt
*   Kijavított egy problémát az Androidon, amely időnként a DialogServiceConnector által használt duplikált benyomási azonosítókat eredményez
*   A kapcsolatok stabilitásának fejlesztése több fordulatos interakciók és a hibák jelentése (a megszakított események használatával), amikor a DialogServiceConnector
*   A DialogServiceConnector-munkamenet megkezdése mostantól megfelelően biztosítja az eseményeket, beleértve a ListenOnceAsync () hívásakor az aktív StartKeywordRecognitionAsync ()
*   A kapott DialogServiceConnector-tevékenységekhez kapcsolódó összeomlást kezelte 

**Minták**
*   Gyors útmutató a Xamarin
*   Frissített CPP-gyors útmutató Linux ARM64-információkkal
*   Frissített Unity rövid útmutató iOS-adatokkal

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0:2019 – júniusi kiadás

**Minták**
*   Gyors példák a UWP és az Unity szövegének beszédére
*   Gyors üzembe helyezési minta iOS rendszeren
*   A speech & Szándékfelismerés és a fordítás Unity mintái
*   Frissített gyors üzembe helyezési minták a DialogServiceConnector

**Tökéletesítések/változások**
* Párbeszédpanel névtere:
    * A SpeechBotConnector átnevezve lett a DialogServiceConnector
    * A BotConfig átnevezve lett a DialogServiceConfig
    * BotConfig:: a FromChannelSecret () leképezése újra lett rendelve a DialogServiceConfig:: FromBotSecret ()
    * Az Átnevezés után az összes meglévő közvetlen beszédfelismerési ügyfél továbbra is támogatott.
* Az TTS REST-adapter frissítése a proxy és az állandó kapcsolatok támogatásához
* Javítsa a hibaüzenetet, ha egy érvénytelen régiót ad át
* Swift/Objective-C:
    * Továbbfejlesztett hibajelentés: az olyan metódusok, amelyek egy hibát okozhatnak, két verzióban jelennek meg: az egyik, amely egy `NSError` objektumot tesz elérhetővé a hibák kezelésére, és egy kivételt felvet. Az előbbi a Swift számára elérhetővé válnak. Ehhez a változáshoz a meglévő Swift-kódnak való átalakításra van szükség.
    * Továbbfejlesztett események feldolgozása

**Hibajavítások**
*   Javítás a TTS-hez: Ha a SpeakTextAsync a jövőben nem várakozik, amíg a hang nem fejeződött be
*   A teljes nyelvi támogatás engedélyezéséhez C# javítsa a karakterláncok előkészítését
*   A .NET Core-alkalmazás problémáinak elhárítása az alapszintű függvénytár betöltéséhez a mintákban a net461 Target Framework használatával
*   Javítsa ki az esetenkénti problémákat, hogy natív kódtárakat helyezzen üzembe a minták kimeneti mappájába
*   Javítás a webes szoftvercsatorna megbízható bezárásakor
*   Javítsa a lehetséges összeomlást a Linuxon nagyon nagy terhelés alatt álló kapcsolatok megnyitásakor
*   Javítsa ki a hiányzó metaadatokat a macOS-keretrendszer csomagban
*   A Windows `pip install --user` kapcsolatos problémák elhárítása


## <a name="speech-sdk-151"></a>Speech SDK 1.5.1

Ez egy hibajavítási kiadás, amely csak a natív/felügyelt SDK-t érinti. Ez nem befolyásolja az SDK JavaScript-verzióját.

**Hibajavítások**

* Javítsa a FromSubscription, ha a beszélgetési átirattal együtt használja.
* Javítsa ki a kulcsszót a hangsegédek esetében.


## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0:2019 – májusi kiadás

**Új funkciók**

* Mostantól elérhető a Windows és a Linux rendszerhez készült kulcsszó-észrevétel (KWS). A KWS funkció bármilyen mikrofonos típussal működhet, a hivatalos KWS-támogatás azonban jelenleg az Azure Kinect DK hardverben vagy a Speech Devices SDK-ban található mikrofon-tömbökre korlátozódik.
* A kifejezésre mutató funkció az SDK-n keresztül érhető el. További információ: [itt](how-to-phrase-lists.md).
* A társalgási átirat funkció az SDK-n keresztül érhető el. Lásd [itt](conversation-transcription-service.md).
* Adja hozzá a hangsegédek támogatását a közvetlen vonalas beszéd csatornán keresztül.

**Minták**

* További minták az SDK által támogatott új szolgáltatásokhoz vagy új szolgáltatásokhoz.

**Tökéletesítések/változások**

* Különböző felismerő tulajdonságok lettek hozzáadva a szolgáltatás viselkedésének vagy a szolgáltatás eredményeinek (például maszkolási káromkodás és mások) beállításához.
* Mostantól konfigurálhatja a felismerőt a szabványos konfigurációs tulajdonságok segítségével, még akkor is, ha létrehozta a felismerő `FromEndpoint`.
* Objective-C: `OutputFormat` tulajdonság hozzáadva a SPXSpeechConfiguration-hez.
* Az SDK mostantól támogatja a Debian 9-es Linux-disztribúciót.

**Hibajavítások**

* Kijavított egy problémát, amelyben a beszélő erőforrás túl korán megsemmisült a szöveg-beszédben.
## <a name="speech-sdk-142"></a>Speech SDK 1.4.2

Ez egy hibajavítási kiadás, amely csak a natív/felügyelt SDK-t érinti. Ez nem befolyásolja az SDK JavaScript-verzióját.

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

Ez egy csak JavaScript kiadás. Nem lettek hozzáadva funkciók. A következő javítások történtek:

* A web Pack használatának megakadályozása a https-proxy-Agent betöltésében.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0:2019 – április kiadás

**Új funkciók** 

* Az SDK mostantól támogatja a Text-to-speech szolgáltatást béta verzióként. A Windows és a Linux rendszerű asztali C++ számítógépeken C#támogatott. További információkért tekintse meg a [szöveg – beszéd áttekintést](text-to-speech.md#get-started-with-text-to-speech).
* Az SDK mostantól stream bemeneti fájlként támogatja az MP3 és az Opus/OGG hangfájlokat. Ez a funkció csak a (z) C++ és C# rendszerű Linux rendszeren érhető el (további részletek [itt](how-to-use-codec-compressed-audio-input-streams.md)).
* A Java, a .NET Core C++ és a Objective-C beszédfelismerési SDK-val MacOS-támogatást szerzett. A macOS-hez készült Objective-C-támogatás jelenleg béta verzióban érhető el.
* iOS: az iOS-hez készült Speech SDK (Objective-C) mostantól CocoaPod is közzé lett téve.
* JavaScript: nem alapértelmezett mikrofon bemeneti eszközként való támogatása.
* JavaScript: proxy-támogatás a Node. js-hez.

**Minták**

* A (z) C++ és a (z) és a (z) és a (z) és a MacOS rendszerhez készült Speech SDK használatát a
* A Text-to-speech szolgáltatás használatát bemutató minták lettek hozzáadva.

**Tökéletesítések/változások**

* Python: a felismerési eredmények további tulajdonságai már elérhetők a `properties` tulajdonságon keresztül.
* A fejlesztéshez és a hibakereséshez további támogatást az SDK-naplózási és diagnosztikai információk naplófájlba való átirányítására (további részletek [itt](how-to-use-logging.md)talál).
* JavaScript: a hangfeldolgozási teljesítmény javítása.

**Hibajavítások**

* Mac/iOS: olyan hiba, amely hosszú várakozást eredményezett, ha a beszédfelismerési szolgáltatáshoz való kapcsolódás nem lett meghatározva.
* Python: javítsa a hibák kezelését a Python-visszahívásokban.
* JavaScript: helytelen állapotú jelentéskészítés a beszédfelismeréshez a RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1:2019 – február frissítés

Ez egy hibajavítási kiadás, amely csak a natív/felügyelt SDK-t érinti. Ez nem befolyásolja az SDK JavaScript-verzióját.

**Hibajavítás**

* Rögzített memória-szivárgás a mikrofon bemenetének használatakor. A stream-alapú vagy a fájl bemenete nincs hatással.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0:2019 – februári kiadás

**Új funkciók**

* A Speech SDK a AudioConfig osztályon keresztül támogatja a bemeneti mikrofon kijelölését. Ez lehetővé teszi a hangadatoknak a beszédfelismerési szolgáltatásokhoz való továbbítását egy nem alapértelmezett mikrofonból. További információkért tekintse meg a [hangbeviteli eszköz kijelölését](how-to-select-audio-input-devices.md)ismertető dokumentációt. Ez a funkció még nem érhető el a JavaScriptből.
* A Speech SDK mostantól támogatja az Unity használatát egy bétaverziós verzióban. Visszajelzés küldése a [GitHub-minta tárházának](https://aka.ms/csspeech/samples)probléma szakaszában. Ez a kiadás támogatja az Unity használatát a Windows x86 és x64 (asztali vagy Univerzális Windows-platform alkalmazások) és az Android (ARM32/64, x86) esetében. További információ az [Unity](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity)gyors üzembe helyezési útmutatójában található.
* A (korábbi kiadásokban szállított) `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` fájl már nem szükséges. A funkció mostantól integrálva van az alap SDK-ba.


**Minták**

A következő új tartalom érhető el a [példában szereplő adattárban](https://aka.ms/csspeech/samples):

* További minták a AudioConfig. FromMicrophoneInput-hoz.
* További Python-minták a szándék-felismeréshez és a fordításhoz.
* További minták a kapcsolatok objektum iOS-ben való használatához.
* További Java-minták hang kimenettel való fordításhoz.
* Új minta a [Batch-Átírási REST API](batch-transcription.md)használatához.

**Tökéletesítések/változások**

* Python
  * Javított paraméterek ellenőrzése és hibaüzenetek a SpeechConfig.
  * Adja meg a kapcsolatok objektum támogatását.
  * A 32-bites Python (x86) támogatása Windows rendszeren.
  * A Pythonhoz készült Speech SDK a bétaverzión kívül van.
* iOS
  * Az SDK mostantól az iOS SDK 12,1-es verziójára épül.
  * Az SDK mostantól támogatja az iOS 9,2-es és újabb verzióit.
  * Javítsa a hivatkozási dokumentációt, és javítsa ki a tulajdonságok nevét.
* JavaScript
  * Adja meg a kapcsolatok objektum támogatását.
  * Type Definition Files hozzáadása a kötegelt JavaScripthez
  * Kezdeti támogatás és megvalósítás a kifejezésekre vonatkozó útmutatókhoz.
  * Visszatérési tulajdonságok gyűjteménye a szolgáltatás JSON-vel való felismeréséhez
* A Windows-DLL-ek mostantól egy verzió-erőforrást tartalmaznak.
* Ha létrehoz egy felismerőt `FromEndpoint` hozzáadhat paramétereket közvetlenül a végpont URL-címéhez. A `FromEndpoint` használata nem tudja konfigurálni a felismerőt a szabványos konfigurációs tulajdonságok használatával.

**Hibajavítások**

* Az üres proxy felhasználóneve és a proxy jelszava helytelenül lett kezelve. Ebben a kiadásban, ha a proxy felhasználónevét és a proxy jelszavát üres karakterlánccá állítja be, a rendszer nem küldi el a proxyhoz való csatlakozáskor.
* Az SDK által létrehozott munkamenet-azonosítók nem mindig voltak igazán véletlenszerűek egyes nyelveken&nbsp;/környezetekben. A probléma megoldásához hozzáadta a véletlenszerű generátor inicializálását.
* Az engedélyezési tokenek kezelését javítani kell. Ha engedélyezési jogkivonatot szeretne használni, akkor a SpeechConfig válassza ki a elemet, és hagyja üresen az előfizetési kulcsot. Ezután hozza létre a felismerőt a szokásos módon.
* Bizonyos esetekben a kapcsolattípus helytelenül lett közzétéve. A probléma kijavítva.
* A JavaScript-minta úgy lett javítva, hogy támogassa a fordítási szintézis hangkimenetét a Safarion is.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Ez egy csak JavaScript kiadás. Nem lettek hozzáadva funkciók. A következő javítások történtek:

* A stream végén a turn. End, nem a Speech. End.
* Javítsa ki azt a hibát a hangszivattyúban, amely nem ütemezett következő küldést, ha az aktuális küldés sikertelen volt.
* Az Auth-jogkivonat folyamatos felismerésének javítása.
* Hibajavítás különböző felismerőhöz/végpontokhoz.
* Dokumentációs újítások.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0:2018 – decemberi kiadás

**Új funkciók**

* Python
  * A Python-támogatás bétaverziója (3,5 és újabb) ebben a kiadásban érhető el. További információ: itt] (rövid útmutató – python.md).
* JavaScript
  * A JavaScripthez készült Speech SDK nyílt forráskódú. A forráskód elérhető a [githubon](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  * Mostantól a Node. js-t is támogatja, további információ [itt](quickstart-js-node.md)található.
  * A hangmunkamenetek hosszának korlátozása el lett távolítva, az újrakapcsolódás automatikusan megtörténik a fedél alatt.
* Kapcsolatok objektum
  * A Felismerőtől elérheti a kapcsolati objektumot. Ezzel az objektummal explicit módon kezdeményezheti a szolgáltatás kapcsolatát, és előfizethet az események csatlakoztatására és leválasztására.
    (Ez a funkció még nem érhető el a JavaScriptből és a Pythonból.)
* Az Ubuntu 18,04 támogatása.
* Android
  * Engedélyezve van a kisegítő képesség az APK-generációban.

**Fejlesztései**

* A belső szál használatának fejlesztése, a szálak, a zárolások és a mutexek számának csökkentése.
* Továbbfejlesztett hibajelentés/információk. Számos esetben a hibaüzenetek nem lettek propagálva az összes kiút.
* Frissített fejlesztői függőségek a JavaScriptben, hogy naprakész modulokat használjanak.

**Hibajavítások**

* Rögzített memória-szivárgások, amelyek típusa nem egyezik a RecognizeAsync.
* Bizonyos esetekben a kivételek kiszivárgása történt.
* Memóriavesztés kijavítása a fordítási események argumentumai között.
* Kijavított egy zárolási problémát a hosszú ideig futó munkamenetekben való újrakapcsolódáskor.
* Kijavított egy problémát, amely a sikertelen fordítások végső eredményét eredményezheti.
* C#: Ha egy aszinkron művelet nem volt várt a fő szálban, lehetséges, hogy a felismerőt az aszinkron feladat befejezése előtt el lehetett végezni.
* Java: javítva a probléma a Java virtuális gép összeomlása miatt.
* Objective-C: rögzített enumerálási leképezés; A RecognizingIntent helyett a RecognizedIntent lett visszaadva.
* JavaScript: alapértelmezett kimeneti formátum beállítása "Simple" értékre a SpeechConfig-ben.
* JavaScript: a konfigurációs objektum Tulajdonságok közötti inkonzisztencia eltávolítása a JavaScriptben és más nyelveken.

**Minták**

* Több minta frissítése és javítása (például kimeneti hangok a fordításhoz stb.).
* Node. js-minták lettek hozzáadva a [minta tárházban](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**Új funkciók**

* Támogatás Android x86/x64 rendszerhez.
* Proxy támogatása: a SpeechConfig objektumban mostantól meghívhat egy függvényt a proxy információk (állomásnév, port, Felhasználónév és jelszó) beállítására. Ez a funkció még nem érhető el az iOS rendszeren.
* Javított hibakódok és üzenetek. Ha egy felismerés hibát adott vissza, akkor a `Reason` (a megszakított eseménynél), vagy `CancellationDetails` (az elismerés eredménye) `Error`. A megszakított esemény most két további tagot tartalmaz, `ErrorCode` és `ErrorDetails`. Ha a kiszolgáló további hibaüzeneteket adott vissza a jelentett hibával, mostantól elérhető lesz az új tagokban.

**Fejlesztései**

* További ellenőrzés történt a felismerő konfigurációjában, és további hibaüzenetet adott hozzá.
* Hatékonyabban kezelhető a hosszú idő a hangfájlok közepén.
* NuGet csomag: a .NET-keretrendszer projektjeihez a AnyCPU-konfigurációval való kiépítés megakadályozása.

**Hibajavítások**

* Rögzített több kivétel található a felismerők között. Emellett a kivételek bekerülnek és megszakított eseményre lesznek konvertálva.
* Memóriavesztés kijavítása a Property Management szolgáltatásban.
* Kijavítva a hiba, amelyben egy hangbemeneti fájl összeomlhat a felismerővel.
* Kijavítva egy hiba, amelyben az események egy munkamenet-leállítási esemény után fogadhatók.
* Rögzített bizonyos versenyhelyzet-feltételek a Threading-ben.
* Javítva lett egy iOS-kompatibilitási probléma, amely összeomlást eredményezhet.
* Az androidos mikrofonok támogatásának stabilitási fejlesztése.
* Kijavítva a hiba, ha egy felismerő a JavaScriptben figyelmen kívül hagyja az elismerés nyelvét.
* Kijavítva egy hiba, amely megakadályozza a EndpointId (bizonyos esetekben) a JavaScriptben való beállítását.
* A AddIntent megváltoztatott a JavaScriptben, és hozzáadta a hiányzó AddIntent JavaScript-aláírást.

**Minták**

* A C++ rendszer C# hozzáadta a és a mintát a lekéréses és leküldéses adatfolyam-használathoz a [minta tárházban](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

Megbízhatósági javítások és hibajavítások:

* Rögzített potenciális végzetes hiba történt a versenyhelyzet ártalmatlanítását kiváltó feltétele miatt
* Kijavított lehetséges végzetes hiba a nem beállított tulajdonságok esetében.
* További hibák és paraméterek ellenőrzése hozzáadva.
* Objective-C: a lehetséges végzetes hiba történt a NSString-ben a név felülbírálása miatt.
* Objective-C: az API igazított láthatósága
* JavaScript: az eseményekkel és azok hasznos adataival kapcsolatban rögzített.
* Dokumentációs újítások.

A [minta adattárában](https://aka.ms/csspeech/samples)egy új minta lett hozzáadva a javascripthez.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0:2018 – szeptemberi kiadás

**Új funkciók**

* Az Objective-C támogatása iOS rendszeren. Tekintse meg [az iOS-hez készült Objective-C](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md)rövid útmutatót.
* JavaScript-támogatás a böngészőben. Tekintse meg a [JavaScript](quickstart-js-browser.md)rövid útmutatót.

**Változtatások megszakítása**

* Ebben a kiadásban számos megszakított változást vezetünk be.
  További részletekért olvassa el [ezt a lapot](https://aka.ms/csspeech/breakingchanges_1_0_0) .

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0:2018 – augusztus kiadás

**Új funkciók**

* A Speech SDK-val létrehozott UWP-alkalmazások mostantól átadhatják a Windows-alkalmazás minősítési csomagját (WACK).
  Tekintse meg a [UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp)rövid útmutatót.
* .NET Standard 2,0-támogatás Linux rendszeren (Ubuntu 16,04 x64).
* Kísérleti: a Java 8 támogatása Windows (64 bites) és Linux rendszeren (Ubuntu 16,04 x64).
  Tekintse meg a [Java Runtime Environment](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre)rövid útmutatót.

**Funkcionális változás**

* További hibák részletes információinak közzététele a csatlakoztatási hibákról.

**Változtatások megszakítása**

* Java (Android) esetén a `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` függvényhez már nem szükséges elérésiút-paraméter. A rendszer mostantól automatikusan észleli az elérési utat az összes támogatott platformon.
* A (`EndpointUrl` Java- C# ban található tulajdonság get-accesser objektuma el lett távolítva.

**Hibajavítások**

* A Java-ban a fordítási felismerő hangszintézisének eredménye most már megvalósítva van.
* Kijavítva egy olyan hibát, amely inaktív szálakat okozhat, és nagyobb számú nyitott és nem használt szoftvercsatorna van.
* Javítva a probléma, ahol a hosszan futó felismerés megszakadhat az átvitel közepén.
* Rögzített versenyhelyzet a felismerő leállításakor.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0:2018 – júliusi kiadás

**Új funkciók**

* Támogatás Android platform (API 23: Android 6,0 Marshmallow vagy újabb). Tekintse meg az [androidos](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android)rövid útmutatót.
* A .NET Standard 2,0 támogatása Windows rendszeren. Tekintse meg a [.net Core](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)rövid útmutatót.
* Kísérleti: a UWP támogatása Windows rendszeren (1709-es vagy újabb verzió).
  * Tekintse meg a [UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)rövid útmutatót.
  * Megjegyzés: a Speech SDK-val készített UWP-alkalmazások még nem adják át a Windows-alkalmazás minősítési csomagját (WACK).
* A hosszan futó felismerés támogatása automatikus újracsatlakoztatással.

**Funkcionális változások**

* `StartContinuousRecognitionAsync()` támogatja a hosszan futó felismerést.
* A felismerési eredmény több mezőt tartalmaz. Ezek a felismert szöveg és a felismerési állapotot jelképező további értékek (a kullancsok esetében is) és az elszámolási állapotot jelző, például a `InitialSilenceTimeout` és az `InitialBabbleTimeout`.
* Támogatási AuthorizationToken a gyári példányok létrehozásához.

**Változtatások megszakítása**

* Felismerési események: nem egyező eseménytípus lett egyesítve a hiba eseményében.
* A SpeechOutputFormat C# a (z) OutputFormat névre lett átnevezve, hogy C++megmaradjon az igazítása.
* A `AudioInputStream` csatoló egyes módszereinek visszatérési típusa némileg megváltozott:
   * A Java-ban a `read` metódus most a `int`helyett `long` ad vissza.
   * A C#-ben a `Read` metódus most a `int`helyett `uint` ad vissza.
   * A C++-ben a `Read` és `GetFormat` metódusok mostantól a `int`helyett `size_t` adnak vissza.
* C++: A hangbemeneti streamek példányai már csak `shared_ptr`ként adhatók át.

**Hibajavítások**

* Az eredményben rögzített helytelen visszatérési értékek `RecognizeAsync()` időtúllépés miatt.
* A Windows Media Foundation-kódtárainak függősége el lett távolítva. Az SDK mostantól a Core audio API-kat használja.
* Dokumentációs javítás: hozzáadott egy [régiókat](regions.md) tartalmazó lapot a támogatott régiók leírásához.

**Ismert probléma**

* Az Androidhoz készült Speech SDK nem jelenti a fordításhoz tartozó beszédfelismerési eredmények jelentését. Ezt a problémát a következő kiadásban rögzíti a rendszer.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0:2018 – júniusi kiadás

**Funkcionális változások**

- AudioInputStream

  A felismerő mostantól hangforrásként használhatja a streamet. További információ: [a kapcsolódó útmutató](how-to-use-audio-input-streams.md).

- Részletes kimeneti formátum

  `SpeechRecognizer`létrehozásakor `Detailed` vagy `Simple` kimeneti formátumot kérhet le. A `DetailedSpeechRecognitionResult` tartalmaz egy megbízhatósági pontszámot, a felismert szöveget, a nyers lexikális űrlapot, a normalizált formát és a normalizált formát maszkolásos káromkodással.

**Változás megszakítása**

- `SpeechRecognitionResult.Text`ra módosult, `SpeechRecognitionResult.RecognizedText` C#a alkalmazásban.

**Hibajavítások**

- Egy lehetséges visszahívási probléma javítva az USP-rétegben a leállítás során.

- Ha a felismerő hangbemeneti fájlt használt, a fájl a szükségesnél hosszabb ideig tartott.

- Az üzenet-szivattyú és a felismerő között több holtpont is megszűnt.

- Ha a szolgáltatás válasza időtúllépés miatt megtörténik, akkor a `NoMatch`.

- A Windows Media Foundation kódtárai betöltődik. Ez a függvénytár csak a mikrofon bemenetéhez szükséges.

- A hangadatok feltöltési sebessége az eredeti hangsebesség körülbelül kétszeresére korlátozódik.

- Windows rendszeren a C# .net-szerelvények már erős névvel rendelkeznek.

- Dokumentációs javítás: `Region` a felismerő létrehozásához szükséges információk.

További minták lettek hozzáadva, és folyamatosan frissülnek. A legújabb mintákhoz lásd a [SPEECH SDK Samples GitHub-tárházát](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733:2018 – májusi kiadás

Ez a kiadás a Cognitive Services Speech SDK első nyilvános előzetes kiadása.
