---
title: Kibocsátási megjegyzések – Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: A Beszédszolgáltatás szolgáltatásának futó naplója, fejlesztések, hibajavítások és ismert problémák.
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: 7bab0a28ba2b75903b6bdf4708e6aa0a98bdc9e5
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607406"
---
# <a name="release-notes"></a>Kibocsátási megjegyzések
## <a name="speech-sdk-1110-2020-march-release"></a>Beszéd SDK 1.11.0: 2020-március kiadás

**Új funkciók**

- Linux: A Red Hat Enterprise Linux (RHEL)/CentOS 7 x64 támogatása a Speech SDK rendszerének konfigurálására [vonatkozó utasításokkal.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-rhel-centos-7)
- Linux: A .NET Core C# támogatása Linux ARM32 és ARM64 rendszeren. Bővebben [itt](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=linux). 
- C#, C++: `UtteranceId` `ConversationTranscriptionResult`Hozzáadva a, az összes köztes és végső beszédfelismerési eredmény egységes azonosítója. C# [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult?view=azure-dotnet), [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult)részletek.
- Python: Hozzáadott `Language ID`támogatás . Tekintse meg speech_sample.py a [GitHub-tárházban.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
- Windows: Hozzáadott tömörített audio bemeneti formátum támogatása a Windows platform on all the win32 konzol applications. Részletek [itt](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams). 
- JavaScript: Beszédszintézis támogatása (szövegfelolvasás) a NodeJS-ben. Tudjon meg többet [itt](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech). 
- JavaScript: Új API-k hozzáadása az összes küldési és fogadott üzenet ellenőrzésének engedélyezéséhez. Tudjon meg többet [itt](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript). 
        
**Hibajavítások**

- C#, C++: Kijavítottunk egy problémát, ezért `SendMessageAsync` most bináris üzenetet küld bináris típusként. C# [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_), [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection)részletek.
- C#, C++: Kijavítottunk `Connection MessageReceived` egy problémát, `Recognizer` amely miatt `Connection` az esemény használata összeomlást okozhat, ha az objektum előtt van elhelyezve. C# [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived?view=azure-dotnet), [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection#messagereceived)részletek.
- Android: A mikrofon ból származó hangpuffer mérete 800 ms-ról 100 ms-ra csökkent a késleltetés javítása érdekében.
- Android: Kijavítottuk az Android-emulátorral kapcsolatos [problémát](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) az Android Studio alkalmazásban.
- JavaScript: Az `fromSubscription` API-val a kínai régiók támogatása hozzáadva. Részletek [itt](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#fromsubscription-string--string-). 
- JavaScript: További hibainformációk hozzáadása a NodeJS csatlakozási hibáiról.
        
**Példák**

- Egység: Szándékfelismerés nyilvános minta rögzített, ahol a LUIS json import sikertelen volt. Részletek [itt](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369).
- Python: Minta `Language ID`hozzáadva a hoz. Részletek [itt](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py).
    
**Covid19 rövidített vizsgálat**

Mivel az elmúlt hetekben távolról dolgoztunk, nem tudtunk annyi kézi eszközellenőrző vizsgálatot végezni, mint általában. Erre példa a mikrofonbemenet és a hangszóró kimenetének tesztelése Linux, iOS és macOS rendszeren. Nem hajtottunk végre semmilyen változtatást, ami szerintünk bármit is megszakított volna ezeken a platformokon, és az automatizált tesztjeink mind átmentek. Abban a valószínűtlen esetben, ha valamit kihagytunk, kérjük, tudassa velünk a [GitHubon](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?page=2&q=is%3Aissue+is%3Aopen).<br> Köszönöm a folyamatos támogatást. Mint mindig, kérjük, tegye kérdések vagy visszajelzések [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?page=2&q=is%3Aissue+is%3Aopen) vagy [Stack túlcsordulás](https://stackoverflow.microsoft.com/questions/tagged/731).<br>
Maradjon egészséges!

## <a name="speech-sdk-1100-2020-february-release"></a>Beszéd SDK 1.10.0: 2020-február kiadás

**Új funkciók**

 - Python-csomagok at adott hozzá a Python új 3.8-as kiadásának támogatásához.
 - Red Hat Enterprise Linux (RHEL)/CentOS 8 x64 támogatás (C++, C#, Java, Python).
   > [!NOTE] 
   > Az ügyfeleknek az OpenSSL-t [a következő utasításoknak](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)megfelelően kell konfigurálniuk.
 - Linux ARM32 támogatás a Debianés az Ubuntu számára.
 - A DialogServiceConnector mostantól támogatja a BotFrameworkConfig opcionális "bot azonosító" paraméterét. Ez a paraméter lehetővé teszi több direct line beszédrobot használatát egyetlen Azure-beszéderőforrással. A megadott paraméter nélkül a rendszer az alapértelmezett robotot használja (a Direct Line Speech channel konfigurációs lapja szerint).
 - A DialogServiceConnector mostmár rendelkezik Egy SpeechActivityTemplate tulajdonsággal. Ennek a JSON-karakterláncnak a tartalmát a Direct Line Speech a támogatott mezők széles körének előzetes feltöltésére használja a direct line beszédrobotot elérő tevékenységekben, beleértve az olyan eseményekre adott automatikusan létrehozott tevékenységeket is, mint például a beszédfelismerés.
 - A TTS mostantól előfizetési kulcsot használ a hitelesítéshez, csökkentve az első szintézis eredményének első bájtkésességét a szintetizátor létrehozása után.
 - Frissítve beszédfelismerő modellek 19 területi beállításhoz, 18,6%-os átlagos szóhibaarány-csökkenéssel (es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN, zh-HK, nb-NO, fi-FL, ru-RU, pl-PL, ca-ES, zh-TW, TH-TH, pt-, TR-TR-). Az új modellek jelentős fejlesztéseket hoznak több tartományban, beleértve a Diktálást, a Call-Center átírást és a videoindexelési forgatókönyveket.

**Hibajavítások**

 - Javítottuk azt a hibát, amely szerint a Beszélgetés-átíró nem várt megfelelően a JAVA API-kban 
 - Android x86 emulátor javítás a Xamarin [GitHub problémához](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - Hiányzó hozzáadása (Get| Set)Tulajdonság módszerek AudioConfig
 - TTS hiba javítása, amelyben az audioDataStream nem állítható le, ha a kapcsolat sikertelen
 - Ha régió nélküli végpontot használ, az USP-hibákat okozna a beszélgetésfordítószámára
 - Az univerzális Windows-alkalmazások azonosítógenerálása mostantól megfelelően egyedi GUID algoritmust használ; ez korábban és akaratlanul nem teljesítette a csonka megvalósítást, amely gyakran ütközéseket okozott nagy kölcsönhatások során.
 
 **Példák**
 
 - Unityminta a Speech SDK [Unity mikrofonnal és leküldéses módú streameléssel](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone) való használatához

**További változások**

 - [Linuxra frissítve az OpenSSL konfigurációs dokumentációja](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)

## <a name="speech-sdk-190-2020-january-release"></a>Beszéd SDK 1.9.0: 2020-január kiadás

**Új funkciók**

- Többeszközes beszélgetés: több eszközt csatlakoztathat ugyanahhoz a beszédhez vagy szövegalapú beszélgetéshez, és igény szerint lefordíthatja a közöttük küldött üzeneteket. További információ ebben a [cikkben](multi-device-conversation.md). 
- Kulcsszófelismerés támogatása hozzá Android .aar csomagot, és hozzáadott támogatja az x86 és x64 ízek. 
- C célkitűzés: `SendMessage` `SetMessageProperty` és a `Connection` tárgyhoz hozzáadott módszerek. Lásd a [dokumentációt itt](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection).
- A TTS C++ `std::wstring` api mostantól támogatja a szintézis szövegbevitelt, így nincs szükség a wstring karakterláncra konvertálására, mielőtt átadná az SDK-nak. Lásd a részleteket [itt](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync). 
- C#: [A nyelvi azonosító](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) és a [forrásnyelvi konfiguráció](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp) már elérhető.
- JavaScript: Hozzáadott egy `Connection` olyan funkciót, amely ellenzi a beszédszolgáltatásból érkező egyéni üzenetek visszahívását. `receivedServiceMessage`
- JavaScript: További `FromHost API` támogatás a használat megkönnyítéséhez a prem tárolókkal és a szuverén felhőkkel. Lásd a [dokumentációt itt](speech-container-howto.md).
- JavaScript: Most `NODE_TLS_REJECT_UNAUTHORIZED` becsület köszönhetően hozzájárulása [orgads](https://github.com/orgads). Lásd a részleteket [itt](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75).

**Változások megbontása**

- `OpenSSL`az 1.1.1b verzióra frissítve lett, és statikusan kapcsolódik a Speech SDK linuxos központi könyvtárához. Ez törést okozhat, ha `OpenSSL` a beérkezett `/usr/lib/ssl` üzenetek mappája nincs telepítve a rendszer könyvtárába. Kérjük, olvassa el a beszédsdkó dokumentumok [dokumentációját](how-to-configure-openssl-linux.md) a probléma megkerüléséhez.
- A `WordLevelTimingResult.Offset` C# számára visszaadott adattípust úgy `int` módosítottuk, hogy `long` a beszédadatok hoz a 2 percnél hosszabb a hozzáféréshez. `WordLevelTimingResults`
- `PushAudioInputStream`és `PullAudioInputStream` most küldjön wav fejléc információkat `AudioStreamFormat`a beszédszolgáltatás alapján, opcionálisan meg van adva, amikor létrehozták őket. Az ügyfeleknek most a [támogatott hangbeviteli formátumot](how-to-use-audio-input-streams.md)kell használniuk. Bármely más formátum az optimálistól elmaradó felismerési eredményeket eredményez, vagy más problémákat okozhat. 

**Hibajavítások**

- Tekintse `OpenSSL` meg a fenti módosítások megbontása című témakört. Rögzítettük mind az időszakos összeomlást, mind a teljesítményproblémát (lock versengés nagy terhelés alatt) Linux és Java nyelven. 
- Java: Az objektumlezárás továbbfejlesztette a magas egyidejűségi forgatókönyvekben.
- Átalakítottuk a NuGet csomagot. Eltávolítottuk a lib `Microsoft.CognitiveServices.Speech.core.dll` `Microsoft.CognitiveServices.Speech.extension.kws.dll` mappák három példányát, így a NuGet csomag kisebb és gyorsabb lesz a letöltéshez, és hozzáadtuk a C++ natív alkalmazások összeállításához szükséges fejléceket.
- Javítottuk a gyorsindítási mintákat [itt](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp). Ezek voltak kilépés nélkül bemutatás " mikrofon nem alapít" kivétel -ra Linux, MacOS, Windows.
- Javítottuk az SDK összeomlását, hosszú beszédfelismerési eredményekkel bizonyos kódútvonalakon, mint [ez a minta.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp)
- Javítottuk az SDK telepítési hibáját az Azure Web App környezetben [az ügyfélprobléma megoldása](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396)érdekében.
- Javítottunk egy TTS-hibát, miközben több `<voice>` címkét vagy `<audio>` címkét használtunk az [ügyfélprobléma megoldására.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433) 
- Javítottunk egy TTS 401-es hibát, amikor az SDK-t helyreállt a felfüggesztés.
- JavaScript: Rögzített egy kör alakú import audio adatok köszönhetően hozzájárulása [euirim](https://github.com/euirim). 
- JavaScript: az 1.7-ben hozzáadott szolgáltatástulajdonságok beállításának támogatása.
- JavaScript: javítottunk egy hibát, amely miatt a csatlakozási hiba folyamatos, sikertelen websocket-újracsatlakozási kísérleteket eredményezhet.

**Példák**

- Hozzáadott kulcsszó elismerés minta Android [itt](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo).
- Hozzáadott TTS minta a szerver forgatókönyv [itt](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs).
- Hozzáadott többeszközes beszélgetés rövid útmutató a C # és C ++ [itt](quickstarts/multi-device-conversation.md).

**További változások**

- Optimalizált SDK-magkönyvtárméret Androidon.
- Az 1.9.0-s és az azt `int` futó `string` SDK támogatja a Beszélgetésátírás hangaláírás-verziómezőjének típusát.

## <a name="speech-sdk-180-2019-november-release"></a>Beszéd SDK 1.8.0: 2019-november kiadás

**Új funkciók**

- Hozzáadott `FromHost()` egy API-t, hogy megkönnyítse a használaton-ra-prem konténerek és szuverén felhők.
- Automatikus forrásnyelv-felismerés hozzáadva a beszédfelismeréshez (Java és C++)
- Hozzáadott `SourceLanguageConfig` objektum a beszédfelismeréshez, a várt forrásnyelvek megadására szolgál (Java és C++)
- Hozzáadott `KeywordRecognizer` támogatás a Windows (UWP), Android és iOS keresztül NuGet és Unity csomagok
- Távoli beszélgetés Java API-t adott hozzá a beszélgetés átírásához aszinkron kötegekben.

**Változások megbontása**

- A Beszélgetésátíró funkciói névtér `Microsoft.CognitiveServices.Speech.Transcription`alatt költöztek.
- A Beszélgetés-átíró metódusok egy `Conversation` része új osztályba kerül.
- A 32 bites (ARMv7 és x86) iOS támogatásának elvetett támogatása

**Hibajavítások**

- Összeomlás javítása, `KeywordRecognizer` ha a helyi rendszert érvényes beszédszolgáltatás-előfizetési kulcs nélkül használja

**Példák**

- Xamarin minta`KeywordRecognizer`
- Egységminta`KeywordRecognizer`
- C++ és Java minták automatikus forrásnyelv-felismeréshez.

## <a name="speech-sdk-170-2019-september-release"></a>Beszéd SDK 1.7.0: 2019-szeptember kiadás

**Új funkciók**

- Hozzáadott béta támogatása Xamarin az Univerzális Windows Platform (UWP), Android és iOS
- IOS-támogatás hozzáadva az Unity-hez
- Hozzáadott `Compressed` input támogatása ALaw, Mulaw, FLAC Android, iOS és Linux
- `SendMessageAsync` Hozzáadva `Connection` az osztályba az üzenet szolgáltatásba küldéséhez
- `SetMessageProperty` Hozzáadva `Connection` az osztályhoz az üzenet tulajdonságának beállításához
- A TTS java (Jre és Android), Python, Swift és Objective-C kötéseket adott hozzá
- A TTS a macOS, az iOS és az Android rendszer lejátszási támogatását adta hozzá.
- Hozzáadott "word határ" információ TTS.

**Hibajavítások**

- Rögzített IL2CPP build probléma Unity 2019 For Android
- Kijavítottuk a wav fájlbemenet hibásan formázott fejléceit, amelyek helytelenfeldolgozása volt
- Kijavítottuk azt a problémát, amely miatt az UUI-k nem voltak egyediek egyes kapcsolati tulajdonságokban
- Javítva néhány figyelmeztetés a Swift-kötések nullability megadóiról (kis kódmódosításokat igényelhet)
- Javítva egy hiba, amely miatt a websocket-kapcsolatok nem kecsesen zártak hálózati terhelés alatt
- Kijavítottunk egy problémát az Androidon, amely néha ismétlődő megjelenítésazonosítókat eredményezett, amelyeket a`DialogServiceConnector`
- A kapcsolatok stabilitásának javítása a többfordulatos interakciók során, `Canceled` valamint a hibák (eseményeken keresztül) jelentése, amikor azok`DialogServiceConnector`
- `DialogServiceConnector`a munkamenet mostantól megfelelően biztosítja `ListenOnceAsync()` az eseményeket, beleértve az aktív`StartKeywordRecognitionAsync()`
- A fogadott tevékenységekkel `DialogServiceConnector` kapcsolatos összeomlás kezelése

**Példák**

- Rövid útmutató a Xamarinhoz
- Frissített CPP rövid útmutató linuxos ARM64 információkkal
- A Unity frissítése az iOS-adatokkal

## <a name="speech-sdk-160-2019-june-release"></a>Beszéd SDK 1.6.0: 2019-június kiadás

**Példák**

- Gyorsindítási minták szövegfelolvasáshoz az UWP-n és az Egységen
- Gyorsútmutató a Swifthez iOS rendszeren
- Egységminták a beszédfelismeréshez & a szándékfelismeréshez és a fordításhoz
- Frissített rövid útmutató minták`DialogServiceConnector`

**Fejlesztések / változások**

- Párbeszédpanel névtere:
  - A `SpeechBotConnector` új nevet kapott: `DialogServiceConnector`
  - A `BotConfig` új nevet kapott: `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()`újralett rendelve`DialogServiceConfig::FromBotSecret()`
  - Az átnevezés után továbbra is minden meglévő direct line beszédügyfél támogatott
- TTS REST-adapter frissítése proxy, állandó kapcsolat támogatásához
- Érvénytelen terület átadása esetén hibaüzenet jelenik meg
- Swift/Objective-C:
  - Továbbfejlesztett hibajelentés: A hibát okozó metódusok most antól két verzióban `NSError` jelennek meg: Az egyik egy objektumot a hibakezeléshez, a másik kivételt tesz ki. Az előbbiek ki vannak téve Swiftnek. Ez a módosítás a meglévő Swift-kódhoz való alkalmazkodást igényel.
  - Továbbfejlesztett eseménykezelés

**Hibajavítások**

- Fix a TTS: ahol `SpeakTextAsync` a jövőben visszatért várakozás nélkül, amíg a hang befejeződött renderelés
- A C# átadási karakterláncok javítása a teljes nyelvi támogatás engedélyezéséhez
- Fix a .NET core app probléma betölteni mag könyvtár net461 cél keretrendszer minták
- Javítsa ki a natív könyvtárak minták kimeneti mappájába való központi telepítésével kapcsolatos alkalmi problémákat
- Fix web socket bezárása megbízhatóan
- Fix lehetséges összeomlik megnyitása közben a kapcsolat alatt nagyon nagy terhelés linuxos
- A hiányzó metaadatok javítása a macOS keretcsomagjában
- A Windows `pip install --user` rendszerrel kapcsolatos problémák megoldása

## <a name="speech-sdk-151"></a>Beszéd SDK 1.5.1

Ez egy hibajavítás-kiadás, amely csak a natív/felügyelt SDK-t érinti. Ez nem érinti az SDK JavaScript verzióját.

**Hibajavítások**

- Fix FromSubscription, ha használják beszélgetés átírása.
- Fix hiba kulcsszó pecsételő hangasszisztensek.

## <a name="speech-sdk-150-2019-may-release"></a>Beszéd SDK 1.5.0: 2019-május kiadás

**Új funkciók**

- Kulcsszó pecsételő (KWS) már elérhető a Windows és linux. A KWS-funkciók bármilyen mikrofontípussal működhetnek, a hivatalos KWS-támogatás azonban jelenleg az Azure Kinect DK hardverben vagy a beszédfelismerési eszközök SDK-ban található mikrofontömbökre korlátozódik.
- A kifejezéstipp funkció az SDK-n keresztül érhető el. További információ: [itt](how-to-phrase-lists.md).
- A beszélgetésátírási funkció az SDK-n keresztül érhető el. Lásd [itt](conversation-transcription-service.md).
- A közvetlen vonalbeszédcsatornával támogatást adhat a hangsegédekhez.

**Példák**

- Hozzáadott minták at új funkciók vagy új szolgáltatások által támogatott SDK.

**Fejlesztések / változások**

- Különböző felismerő tulajdonságokat adott hozzá a szolgáltatás viselkedésének vagy a szolgáltatás eredményeinek (például a káromkodás és mások maszkolásának beállításához).
- Most már konfigurálhatja a felismerőt a szabványos konfigurációs tulajdonságokon keresztül, még akkor is, ha létrehozta a felismerőt. `FromEndpoint`
- Objective-C: `OutputFormat` ingatlan hozzálett adva a hoz. `SPXSpeechConfiguration`
- Az SDK mostantól támogatja a Debian 9-et Linux disztribúcióként.

**Hibajavítások**

- Kijavítottuk azt a problémát, amely miatt a szónok forrása túl korán dekaszkultrálett a szövegfelolvasáson.

## <a name="speech-sdk-142"></a>Beszéd SDK 1.4.2

Ez egy hibajavítás-kiadás, amely csak a natív/felügyelt SDK-t érinti. Ez nem érinti az SDK JavaScript verzióját.

## <a name="speech-sdk-141"></a>Beszéd SDK 1.4.1

Ez egy csak JavaScript-kiadás. Nincs hozzáadódó funkció. A következő javítások történtek:

- Megakadályozza, hogy a webcsomag betöltse a https-proxy-ügynököt.

## <a name="speech-sdk-140-2019-april-release"></a>Beszéd SDK 1.4.0: 2019-április kiadás

**Új funkciók**

- Az SDK mostantól bétaverzióként támogatja a szövegfelolvasó szolgáltatást. C++ és C#-tól származó Windows és Linux Desktop esetén támogatott. További információt a [szövegfelolvasó áttekintése című témakörben talál.](text-to-speech.md#get-started)
- Az SDK mostantól támogatja az MP3 és az Opus/OGG hangfájlokat stream bemeneti fájlként. Ez a funkció csak Linuxon érhető el C++ és C# és jelenleg béta (további részletek [itt).](how-to-use-codec-compressed-audio-input-streams.md)
- A Java, .NET core, C++ és Objective-C beszédszoló SDK macOS-támogatást kapott. A macOS Objektív-C támogatása jelenleg béta verzióban érhető el.
- iOS: A speech SDK iOS (Objective-C) most is megjelent, mint a CocoaPod.
- JavaScript: A nem alapértelmezett mikrofon oka bemeneti eszközként való támogatás.
- JavaScript: Proxy támogatása Node.js.

**Példák**

- A beszédfelismerési SDK C++ és Objektív-C macOS rendszeren történő használatával rendelkező minták lettek hozzáadva.
- A szövegfelolvasó szolgáltatás használatát igazoló minták kerültek hozzáadásra.

**Fejlesztések / változások**

- Python: A felismerési eredmények további tulajdonságai `properties` most már elérhetővé a tulajdonságon keresztül.
- További fejlesztési és hibakeresési támogatás érdekében átirányíthatja az SDK naplózási és diagnosztikai adatait egy naplófájlba (további részletek [itt).](how-to-use-logging.md)
- JavaScript: A hangfeldolgozásteljesítményének javítása.

**Hibajavítások**

- Mac/iOS: Ki lett javítva egy olyan hiba, amely hosszú várakozáshoz vezetett, amikor nem lehetett kapcsolatot létesíteni a beszédfelismerési szolgáltatással.
- Python: javítsa az argumentumok hibakezelését a Python-visszahívásokban.
- JavaScript: Fix rossz állapotjelentés beszéd véget ért A RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Beszéd SDK 1.3.1: 2019-február frissítés

Ez egy hibajavítás-kiadás, amely csak a natív/felügyelt SDK-t érinti. Ez nem érinti az SDK JavaScript verzióját.

**Hibajavítás**

- Rögzített egy memóriavesztés, amikor a mikrofon bemenet. Az adatfolyam-alapú vagy a fájlbemenetet ez nem érinti.

## <a name="speech-sdk-130-2019-february-release"></a>Beszéd SDK 1.3.0: 2019-február release

**Új funkciók**

- A beszédstabk támogatja a bemeneti mikrofon kiválasztását az `AudioConfig` osztályon keresztül. Ez lehetővé teszi, hogy hangadatokat továbbítson a beszédfelismerési szolgáltatásba egy nem alapértelmezett mikrofonról. További információt a [hangbemeneti eszköz kiválasztását](how-to-select-audio-input-devices.md)leíró dokumentációban talál. Ez a funkció még nem érhető el javascriptből.
- A Speech SDK mostantól támogatja a Unity-t egy bétaverzióban. Visszajelzés küldése a [GitHub mintatárban](https://aka.ms/csspeech/samples)található problémaszakaszon keresztül. Ez a kiadás támogatja a Unity t Windows x86 és x64 (asztali vagy univerzális Windows Platform alkalmazások) és az Android (ARM32/64, x86) rendszeren. További információ a [Unity rövid útmutató.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity)
- A `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` fájlra (amelyet a korábbi kiadásokban szállítottak) már nincs szükség. The functionality is now integrated into the core SDK.

**Példák**

A következő új tartalom érhető el [a mintatárban:](https://aka.ms/csspeech/samples)

- További minták `AudioConfig.FromMicrophoneInput`a hoz.
- További Python-minták a szándék felismeréséhez és a fordításhoz.
- További minták az `Connection` objektum iOS-ben való használatához.
- További Java minták fordításaudio kimenettel.
- Új minta a [Batch Transcription REST API használatához.](batch-transcription.md)

**Fejlesztések / változások**

- Python
  - Továbbfejlesztett paraméter-ellenőrzés és `SpeechConfig`hibaüzenetek a területen.
  - Az objektum `Connection` támogatásának hozzáadása.
  - 32 bites Python (x86) támogatása Windows rendszeren.
  - A Python beszédsdk-je nincs béta.
- iOS
  - Az SDK most már az iOS SDK 12.1-es verziójához készült.
  - Az SDK mostantól támogatja az iOS 9.2-es és újabb verzióit.
  - Javítsa a referenciadokumentációt, és javítson ki több tulajdonságnevet.
- JavaScript
  - Az objektum `Connection` támogatásának hozzáadása.
  - Típusdefiníciós fájlok hozzáadása a csomagban lévő JavaScripthez
  - A kifejezéstippek kezdeti támogatása és megvalósítása.
  - Visszaad tulajdonságok gyűjteménye szolgáltatás JSON felismerés
- A Windows DL-ek most már tartalmaznak verzió-erőforrást.
- Ha létrehoz egy `FromEndpoint` felismerőt, közvetlenül a végpont URL-címéhez adhat paramétereket. A `FromEndpoint` használata nem konfigurálhatja a felismerő a szabványos konfigurációs tulajdonságokon keresztül.

**Hibajavítások**

- Az üres proxyfelhasználó-név és a proxyjelszó kezelése nem volt megfelelő. Ebben a kiadásban, ha a proxy felhasználónevet és a proxyjelszót egy üres karakterlánchoz állítja be, azok nem lesznek elküldve a proxyhoz való csatlakozáskor.
- SessionId által létrehozott SDK nem mindig volt igazán véletlenszerű egyes nyelvek /&nbsp;környezetek. Hozzáadott véletlen generátor inicializálása, hogy megoldja ezt a problémát.
- Az engedélyezési jogkivonat kezelésének javítása. Ha engedélyezési jogkivonatot szeretne használni, `SpeechConfig` adja meg a, és hagyja üresen az előfizetési kulcsot. Ezután hozza létre a felismerőt a szokásos módon.
- Bizonyos esetekben `Connection` az objektum nem megfelelően lett kiadva. A probléma ki lett javítva.
- A JavaScript-mintát úgy rögzítették, hogy támogassa a fordítási szintézis hangkimenetét a Safari-n is.

## <a name="speech-sdk-121"></a>Beszéd SDK 1.2.1

Ez egy csak JavaScript-kiadás. Nincs hozzáadódó funkció. A következő javítások történtek:

- Tűz végén patak turn.end, nem speech.end.
- Javítsa ki az audioszivattyú hibáját, amely nem ütemezte a következő küldést, ha az aktuális küldés nem sikerült.
- A folyamatos felismerés javítása hitelesítési tokentel.
- Hibajavítás a különböző felismerő / végpontok.
- Dokumentációs fejlesztések.

## <a name="speech-sdk-120-2018-december-release"></a>Beszéd SDK 1.2.0: 2018-december kiadás

**Új funkciók**

- Python
  - A Python-támogatás bétaverziója (3.5-ös vagy újabb verzió) érhető el ebben a kiadásban. További információ: itt](quickstart-python.md).
- JavaScript
  - A Beszéd SDK JavaScript nyílt forráskódú. A forráskód elérhető a [GitHubon.](https://github.com/Microsoft/cognitive-services-speech-sdk-js)
  - Most már támogatja Node.js, további információ megtalálható [itt](quickstart-js-node.md).
  - A hangmunkamenetek hosszkorlátozását eltávolították, az újracsatlakozás automatikusan megtörténik a fedél alatt.
- `Connection`Objektum
  - A `Recognizer`ból a , `Connection` elérheti az objektumot. Ez az objektum lehetővé teszi a szolgáltatáskapcsolat kifejezett elindítását, valamint az események összekapcsolására és leválasztására való előfizetést.
    (Ez a funkció még nem érhető el a JavaScript és a Python.)
- Az Ubuntu 18.04 támogatása.
- Android
  - Engedélyezett ProGuard támogatás az APK-generáció során.

**Fejlesztések**

- Fejlesztések a belső szál használat, számának csökkentése szálak, zárak, mutexes.
- Továbbfejlesztett hibajelentés / információ. A hibaüzenetek több esetben nem lettek teljesen propagálva.
- Frissített fejlesztési függőségek JavaScript-ben a naprakész modulok használatához.

**Hibajavítások**

- Rögzített memóriavesztés a típus eltérése `RecognizeAsync`miatt a ban.
- Egyes esetekben kivételek szivárogtak ki.
- Memóriavesztés javítása a fordítási esemény argumentumaiban.
- Kijavítottuk a hosszú ideig futó munkamenetek újracsatlakozásával kapcsolatos zárolási problémát.
- Kijavítottunk egy problémát, amely a sikertelen fordítások eredményének hiányához vezethetett.
- C#: Ha `async` egy művelet nem várt a fő szálban, lehetséges, hogy a felismerőt az aszinkron feladat befejezése előtt ártalmatlanítani lehet.
- Java: Kijavítottuk azt a problémát, amely a Java virtuális gép összeomlását eredményezte.
- Célkitűzés-C: Rögzített felsorítás feltérképezése; A (RecognizedIntent) `RecognizingIntent`helyett a (RecognizedIntent) értéket adta vissza a (.
- JavaScript: Állítsa az alapértelmezett kimeneti `SpeechConfig`formátumot "egyszerű" értékre a ban.
- JavaScript: A javascriptben és más nyelveken lévő konfigurációs objektum tulajdonságai közötti inkonzisztencia eltávolítása.

**Példák**

- Több minta frissítése és rögzítése (például fordítási hangok stb.).
- Hozzáadott Node.js mintákat a [mintatárban](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Beszéd SDK 1.1.0

**Új funkciók**

- Támogatás az Android x86/x64.
- Proxy támogatás: `SpeechConfig` Az objektumban most már meghívhat egy függvényt a proxyadatok (állomásnév, port, felhasználónév és jelszó) beállításához. Ez a funkció még nem érhető el iOS rendszeren.
- Továbbfejlesztett hibakód és üzenetek. Ha a felismerés hibát adott vissza, akkor ez már beállította `Reason` (a visszavont eseményesetén) vagy `CancellationDetails` (a felismerés eredményében) a értékre. `Error` A törölt esemény mostantól két `ErrorCode` `ErrorDetails`további tagot tartalmaz, és a . Ha a kiszolgáló további hibaadatokat adott vissza a jelentett hibával kapcsolatban, akkor az elérhető lesz az új tagoknál.

**Fejlesztések**

- További ellenőrzést adott hozzá a felismerő konfigurációjához, és további hibaüzenetet adott hozzá.
- A hosszú ideig tartó csend jobb kezelése egy hangfájl közepén.
- NuGet csomag: a .

**Hibajavítások**

- Javítva, hogy több kivétel is megtalálható a felismerőkben. Ezenkívül a kivételek kivételeket `Canceled` elkapják, és eseménysé alakítják.
- Memóriavesztés javítása a tulajdonságkezelésben.
- Javítva az a hiba, amelyben egy hangbemeneti fájl összeomlik a felismerővel.
- Kijavítottunk egy hibát, amely miatt események érkeztek egy munkamenet-leállítási esemény után.
- Javítva néhány versenykörülmények menet.
- Kijavítottunk egy iOS-kompatibilitási problémát, amely összeomlást okozhatott.
- Stabilitási fejlesztések az Android mikrofontámogatáshoz.
- Javítva egy hiba, amely szerint a JavaScript-ben egy felismerő figyelmen kívül hagyta a felismerés nyelvét.
- Javítva egy hiba, amely megakadályozta a `EndpointId` (bizonyos esetekben) beállítását a JavaScript-ben.
- Megváltozott a paramétersorrend az AddIntent-ben `AddIntent` JavaScript-ben, és hozzáadta a hiányzó JavaScript-aláírást.

**Példák**

- C++ és C# mintákat adott hozzá a [mintatárban](https://aka.ms/csspeech/samples)a lekéréses és leküldéses adatfolyam-használathoz.

## <a name="speech-sdk-101"></a>Beszéd SDK 1.0.1

Megbízhatósági fejlesztések és hibajavítások:

- Javítottuk a lehetséges végzetes hibát a versenyállapot miatt a felismerő
- Javítottuk a lehetséges végzetes hibát a nem beállított tulajdonságok esetén.
- További hiba- és paraméterellenőrzés hozzáadva.
- Objective-C: Javítva az NSString névfelülbírálása által okozott lehetséges végzetes hiba.
- Célkitűzés-C: Az API korrigált láthatósága
- JavaScript: Rögzített kapcsolatos események és a hasznos teher.
- Dokumentációs fejlesztések.

A [mi minta tárház](https://aka.ms/csspeech/samples), egy új mintát a JavaScript került.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0: 2018-szeptemberi kiadás

**Új funkciók**

- Az Objective-C támogatása iOS rendszeren. Tekintse meg [az Objective-C rövid útmutatót az iOS rendszerhez.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md)
- JavaScript támogatása a böngészőben. Tekintse meg [javascript-gyorsútmutatónkat.](quickstart-js-browser.md)

**Változások megbontása**

- Ezzel a kiadással számos törési módosítás vezet be.
  Ellenőrizze [ezt az oldalt](https://aka.ms/csspeech/breakingchanges_1_0_0) a részletekért.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: 2018-augusztus kiadás

**Új funkciók**

- A speech SDK-val készült UWP-alkalmazások most már megfelelnek a Windows alkalmazásminősítő készlet (WACK) számára.
  Tekintse meg az [UWP rövid útmutatót.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp)
- A .NET Standard 2.0 támogatása Linux rendszeren (Ubuntu 16.04 x64).
- Kísérleti: Támogatja a Java 8 Windows (64 bites) és Linux (Ubuntu 16.04 x64).
  Tekintse meg a [Java Runtime Environment rövid útmutatót.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre)

**Funkcionális változás**

- A csatlakozási hibákkal kapcsolatos további hibarészletek megjelenítése.

**Változások megbontása**

- Java (Android) `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` rendszeren a függvénynek már nincs szüksége elérési út paraméterre. Most az elérési út automatikusan észlelhető az összes támogatott platformon.
- A java és c# `EndpointUrl` típusú tulajdonság get-accessor-ja eltávolításra került.

**Hibajavítások**

- Java-ban a fordítási felismerő hangszintézisének eredménye most valósul meg.
- Javítva egy hiba, amely inaktív szálakat és megnövekedett számú nyitott és nem használt aljzatot okozhat.
- Kijavítottunk egy problémát, amely miatt egy hosszú ideig futó felismerés az átvitel közepén megszakadt.
- Javítottunk egy versenyállapotot a felismerő leállításakor.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: 2018-júliusi kiadás

**Új funkciók**

- Támogatja az Android platform (API 23: Android 6.0 Marshmallow vagy újabb). Nézze meg az [Android rövid útmutató](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android).
- Támogatja a .NET Standard 2.0-t windows rendszeren. Nézze meg a [.NET Core rövid útmutatót.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)
- Kísérleti: Az UWP támogatása Windows rendszeren (1709-es vagy újabb verzió).
  - Tekintse meg az [UWP rövid útmutatót.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)
  - Megjegyzés: A beszédfelismerési SDK-val készített UWP-alkalmazások még nem adják át a Windows alkalmazásminősítő készletet (WACK).
- Támogatja a hosszú ideig futó felismerést az automatikus újracsatlakozással.

**Funkcionális változások**

- `StartContinuousRecognitionAsync()`támogatja a hosszú távú elismerést.
- A felismerés eredménye több mezőt tartalmaz. A program eltolja őket a felismert szöveg és a felismerési állapotot képviselő további értékek `InitialSilenceTimeout` `InitialBabbleTimeout`hangkezdetétől és időtartamától (mind a kullancsok ban), például a .
- Támogatás AuthorizationToken gyári példányok létrehozásához.

**Változások megbontása**

- Felismerési `NoMatch` események: az eseménytípus `Error` összeolvadt az eseménybe.
- A C#-ban lévő SpeechOutputFormat nevet `OutputFormat` átnevezte, hogy a C++-hoz igazodjon.
- A `AudioInputStream` felület egyes metódusainak visszatérési típusa kissé megváltozott:
  - Java-ban `read` a metódus `long` most `int`a helyett a értéket adja vissza.
  - A C#-ban `Read` a `uint` metódus `int`most a helyett a értéket adja vissza.
  - A C++ `Read` nyelvben a `GetFormat` és a metódusok most a helyett a visszaadják `size_t` a lehetőséget. `int`
- C++: A hangbemeneti adatfolyamok példányai `shared_ptr`most már csak a rendszerként adhatók át.

**Hibajavítások**

- Rögzített helytelen visszatérési értékek `RecognizeAsync()` az eredményben, amikor az idő ki.
- A Windows médiaalaptáraitól való függőség megszűnt. Az SDK mostantól Core Audio API-kat használ.
- Dokumentációs javítás: Hozzáadott egy [régiók](regions.md) lapot a támogatott régiók leírására.

**Ismert probléma**

- Az Android-alapú beszédsdka nem jelenti a beszédszintézis eredményeit a fordításhoz. Ezt a problémát a következő kiadásban kijavítjuk.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0: 2018-júniusi kiadás

**Funkcionális változások**

- AudioInputStream

  A felismerő most már felhasználhatja az adatfolyamot hangforrásként. További információt a kapcsolódó [útmutatóban](how-to-use-audio-input-streams.md)talál.

- Részletes kimeneti formátum

  Amikor létrehoz `SpeechRecognizer`egy , `Detailed` kérheti `Simple` vagy kimeneti formátumot kérhet. A `DetailedSpeechRecognitionResult` tartalmaz egy megbízhatósági pontszámot, felismert szöveget, nyers lexikális formát, normalizált űrlapot és maszkolt káromkodással normalizált formát.

**A változás megtörése**

- A C#-ban megadott számra `SpeechRecognitionResult.Text` `SpeechRecognitionResult.RecognizedText` változott.

**Hibajavítások**

- Kijavítottunk egy lehetséges visszahívási problémát az USP-rétegben a leállítás során.
- Ha egy felismerő a szükségesnél hosszabb ideig tartotta a fájlleírót, akkor a fájlleírót tartotta meg.
- Eltávolított több holtpont között az üzenet szivattyú és a felismerő.
- Az `NoMatch` eredmény akkor következik be, amikor a szolgáltatásból érkező válasz időhöz vezet.
- A Windows médiaalaptárai késleltetett betöltésűek. Ez a tár csak a mikrofonbemenethez szükséges.
- A feltöltési sebesség audio adatok korlátozódik körülbelül kétszerese az eredeti hangsebesség.
- Windows rendszerben a C# .NET szerelvények most már erős nevű.
- Dokumentációjavítás: `Region` a felismerő létrehozásához szükséges információ.

További minták at adtak hozzá, és folyamatosan frissítik. A legújabb minták, tekintse meg a [Speech SDK minták GitHub-tárház.](https://aka.ms/csspeech/samples)

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733: 2018-Május kiadás

Ez a kiadás a Cognitive Services speech SDK első nyilvános előzetes kiadása.
