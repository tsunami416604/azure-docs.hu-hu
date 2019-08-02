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
ms.date: 07/23/2019
ms.author: jhakulin
ms.custom: seodec18
ms.openlocfilehash: 12eecc4998153cbeedeb907ecad33c56141a50e6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559127"
---
# <a name="release-notes"></a>Kibocsátási megjegyzések

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0: 2019 – júniusi kiadás

**Példák**
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
    * Továbbfejlesztett hibajelentés: A hibát okozó metódusok két verzióban jelennek meg: Az egyik, amely egy `NSError` objektumot tesz elérhetővé a hibakezelés céljából, és egy kivételt okoz. Az előbbi a Swift számára elérhetővé válnak. Ehhez a változáshoz a meglévő Swift-kódnak való átalakításra van szükség.
    * Továbbfejlesztett események feldolgozása

**Hibajavítások**
*   Javítás a TTS-hez: Ha a SpeakTextAsync a jövőben nem várakozik, amíg a hang nem fejeződött be
*   A teljes nyelvi támogatás engedélyezéséhez C# javítsa a karakterláncok előkészítését
*   A .NET Core-alkalmazás problémáinak elhárítása az alapszintű függvénytár betöltéséhez a mintákban a net461 Target Framework használatával
*   Javítsa ki az esetenkénti problémákat, hogy natív kódtárakat helyezzen üzembe a minták kimeneti mappájába
*   Javítás a webes szoftvercsatorna megbízható bezárásakor
*   Javítsa a lehetséges összeomlást a Linuxon nagyon nagy terhelés alatt álló kapcsolatok megnyitásakor
*   Javítsa ki a hiányzó metaadatokat a macOS-keretrendszer csomagban
*   Problémák elhárítása Windows `pip install --user` rendszeren


## <a name="speech-sdk-151"></a>Speech SDK 1.5.1

Ez egy hibajavítási kiadás, amely csak a natív/felügyelt SDK-t érinti. Ez nem befolyásolja az SDK JavaScript-verzióját.

**Hibajavítások**

* Javítsa a FromSubscription, ha a beszélgetési átirattal együtt használja.
* Javítsa ki a hibát az első virtuális asszisztens számára a kulcsszó-bepecsételés során.


## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0: 2019 – májusi kiadás

**Új funkciók**

* A Windows és a Linux rendszerhez elérhető a Wake Word (kulcsszó-felpecsételés/KWS) funkció. A KWS funkció bármilyen mikrofonos típussal működhet, a hivatalos KWS-támogatás azonban jelenleg az Azure Kinect DK hardverben vagy a Speech Devices SDK-ban található mikrofon-tömbökre korlátozódik.
* A kifejezésre mutató funkció az SDK-n keresztül érhető el. További információ: [itt](how-to-phrase-lists.md).
* A társalgási átirat funkció az SDK-n keresztül érhető el. Lásd [itt](conversation-transcription-service.md).
* Adja hozzá a hang-első virtuális asszisztensek támogatását a Direct line Speech Channel használatával.

**Példák**

* További minták az SDK által támogatott új szolgáltatásokhoz vagy új szolgáltatásokhoz.

**Tökéletesítések/változások**

* Különböző felismerő tulajdonságok lettek hozzáadva a szolgáltatás viselkedésének vagy a szolgáltatás eredményeinek (például maszkolási káromkodás és mások) beállításához.
* Mostantól beállíthatja a felismerőt a szabványos konfigurációs tulajdonságok segítségével, még akkor is, ha létrehozta `FromEndpoint`a felismerőt.
* Objective-C: `OutputFormat` tulajdonság hozzáadva a SPXSpeechConfiguration-hez.
* Az SDK mostantól támogatja a Debian 9-es Linux-disztribúciót.

**Hibajavítások**

* Kijavított egy problémát, amelyben a beszélő erőforrás túl korán megsemmisült a szöveg-beszédben.
## <a name="speech-sdk-142"></a>Speech SDK 1.4.2

Ez egy hibajavítási kiadás, amely csak a natív/felügyelt SDK-t érinti. Ez nem befolyásolja az SDK JavaScript-verzióját.

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

Ez egy csak JavaScript kiadás. Nem lettek hozzáadva funkciók. A következő javítások történtek:

* A web Pack használatának megakadályozása a https-proxy-Agent betöltésében.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0: 2019 – április kiadás

**Új funkciók** 

* Az SDK mostantól támogatja a Text-to-speech szolgáltatást béta verzióként. A Windows és a Linux rendszerű asztali C++ számítógépeken C#támogatott. További információkért tekintse meg a [szöveg – beszéd áttekintést](text-to-speech.md#get-started-with-text-to-speech).
* Az SDK mostantól stream bemeneti fájlként támogatja az MP3 és az Opus/OGG hangfájlokat. Ez a funkció csak a (z) C++ és C# rendszerű Linux rendszeren érhető el (további részletek [itt](how-to-use-codec-compressed-audio-input-streams.md)).
* A Java, a .NET Core C++ és a Objective-C beszédfelismerési SDK-val MacOS-támogatást szerzett. A macOS-hez készült Objective-C-támogatás jelenleg béta verzióban érhető el.
* iOS: Az iOS-hez készült Speech SDK (Objective-C) mostantól CocoaPod is közzé lett téve.
* JavaScript Nem alapértelmezett mikrofon támogatása bemeneti eszközként.
* JavaScript Proxy-támogatás a Node. js-hez.

**Példák**

* A (z) C++ és a (z) és a (z) és a (z) és a MacOS rendszerhez készült Speech SDK használatát a
* A Text-to-speech szolgáltatás használatát bemutató minták lettek hozzáadva.

**Tökéletesítések/változások**

* Python: Az elismerési eredmények további tulajdonságai már elérhetők a `properties` tulajdonságon keresztül.
* A fejlesztéshez és a hibakereséshez további támogatást az SDK-naplózási és diagnosztikai információk naplófájlba való átirányítására (további részletek [itt](how-to-use-logging.md)talál).
* JavaScript Javítsa a hangfeldolgozási teljesítményt.

**Hibajavítások**

* Mac/iOS: Egy olyan hiba, amely hosszú várakozást eredményezett, ha a beszédfelismerési szolgáltatáshoz való kapcsolódás nem lett megállapítani.
* Python: javítsa a hibák kezelését a Python-visszahívásokban.
* JavaScript Nem megfelelő állapotú jelentéskészítés történt a RequestSession való beszéd befejezéséhez.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1: 2019 – február frissítés

Ez egy hibajavítási kiadás, amely csak a natív/felügyelt SDK-t érinti. Ez nem befolyásolja az SDK JavaScript-verzióját.

**Hibajavítás**

* Rögzített memória-szivárgás a mikrofon bemenetének használatakor. A stream-alapú vagy a fájl bemenete nincs hatással.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0: 2019 – február kiadás

**Új funkciók**

* A Speech SDK a AudioConfig osztályon keresztül támogatja a bemeneti mikrofon kijelölését. Ez lehetővé teszi a hangadatoknak a beszédfelismerési szolgáltatásokhoz való továbbítását egy nem alapértelmezett mikrofonból. További információkért tekintse meg a hangbeviteli [eszköz kijelölését](how-to-select-audio-input-devices.md)ismertető dokumentációt. Ez a funkció még nem érhető el a JavaScriptből.
* A Speech SDK mostantól támogatja az Unity használatát egy bétaverziós verzióban. Visszajelzés küldése a [GitHub-minta tárházának](https://aka.ms/csspeech/samples)probléma szakaszában. Ez a kiadás támogatja az Unity használatát a Windows x86 és x64 (asztali vagy Univerzális Windows-platform alkalmazások) és az Android (ARM32/64, x86) esetében. További információ az [Unity](quickstart-csharp-unity.md)gyors üzembe helyezési útmutatójában található.
* A ( `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` korábbi kiadásokban szállított) fájl már nem szükséges. A funkció mostantól integrálva van az alap SDK-ba.


**Példák**

A következő új tartalom érhető el a [példában](https://aka.ms/csspeech/samples)szereplő adattárban:

* További minták a AudioConfig. FromMicrophoneInput-hoz.
* További Python-minták a szándék-felismeréshez és a fordításhoz.
* További minták a kapcsolatok objektum iOS-ben való használatához.
* További Java-minták hang kimenettel való fordításhoz.
* Új minta a Batch-átírási [REST API](batch-transcription.md)használatához.

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
* Ha létrehoz egy felismerőt `FromEndpoint` , közvetlenül is hozzáadhat paramétereket a végpont URL-címéhez. A `FromEndpoint` nem tudja konfigurálni a felismerőt a szabványos konfigurációs tulajdonságok használatával.

**Hibajavítások**

* Az üres proxy felhasználóneve és a proxy jelszava helytelenül lett kezelve. Ebben a kiadásban, ha a proxy felhasználónevét és a proxy jelszavát üres karakterlánccá állítja be, a rendszer nem küldi el a proxyhoz való csatlakozáskor.
* Az SDK által létrehozott munkamenet-azonosítók nem mindig voltak igazán véletlenszerűek&nbsp;egyes nyelvekhez/környezetekhez. A probléma megoldásához hozzáadta a véletlenszerű generátor inicializálását.
* Az engedélyezési tokenek kezelését javítani kell. Ha engedélyezési jogkivonatot szeretne használni, akkor a SpeechConfig válassza ki a elemet, és hagyja üresen az előfizetési kulcsot. Ezután hozza létre a felismerőt a szokásos módon.
* Bizonyos esetekben a kapcsolattípus helytelenül lett közzétéve. Ez a probléma megoldódott.
* A JavaScript-minta úgy lett javítva, hogy támogassa a fordítási szintézis hangkimenetét a Safarion is.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Ez egy csak JavaScript kiadás. Nem lettek hozzáadva funkciók. A következő javítások történtek:

* A stream végén a turn. End, nem a Speech. End.
* Javítsa ki azt a hibát a hangszivattyúban, amely nem ütemezett következő küldést, ha az aktuális küldés sikertelen volt.
* Az Auth-jogkivonat folyamatos felismerésének javítása.
* Hibajavítás különböző felismerőhöz/végpontokhoz.
* Dokumentáció fejlesztései.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0: 2018 – decemberi kiadás

**Új funkciók**

* Python
  * A Python-támogatás bétaverziója (3,5 és újabb) ebben a kiadásban érhető el. További információ: itt] (rövid útmutató – python.md).
* JavaScript
  * A JavaScripthez készült Speech SDK nyílt forráskódú. A forráskód elérhető a githubon [](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
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
* Java: Probléma javítva a Java virtuális gép összeomlása miatt.
* Objective-C: Rögzített enumerálási leképezés; A RecognizingIntent helyett a RecognizedIntent lett visszaadva.
* JavaScript Állítsa az alapértelmezett kimeneti formátumot "Simple" értékre a SpeechConfig.
* JavaScript A konfigurációs objektum Tulajdonságok közötti inkonzisztencia eltávolítása a JavaScriptben és más nyelveken.

**Példák**

* Több minta frissítése és javítása (például kimeneti hangok a fordításhoz stb.).
* Node. js-minták lettek hozzáadva a [minta tárházban](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**Új funkciók**

* Android x86/x64 támogatása.
* Proxy támogatása: A SpeechConfig objektumban mostantól meghívhat egy függvényt a proxy információinak (állomásnév, port, Felhasználónév és jelszó) megadására. Ez a funkció még nem áll rendelkezésre álló IOS-eszközökön.
* Továbbfejlesztett hibakód és üzenetek. Elismerés hibát adott vissza, ha ez már adta-e meg `Reason` (a törölt esemény) vagy `CancellationDetails` (a felismerés eredményét) való `Error`. A törölt esemény mostantól tartalmaz két további tagok, `ErrorCode` és `ErrorDetails`. A kiszolgáló további információ a hibáról az a jelzett hibát adott vissza, ha most lesz elérhető az új tagjait.

**Fejlesztései**

* További ellenőrzés hozzáadva a felismerő konfigurációs és további hozzáadott hibaüzenet jelenik meg.
* Hangfájl közepén régóta csend jobb kezelése.
* NuGet-csomagot: .NET-keretrendszer projektek esetén ez megakadályozza, hogy használhatja a AnyCPU konfiguráció.

**Hibajavítások**

* Rögzített felismerő található kivételek. Emellett a kivételek bekerülnek és megszakított eseményre lesznek konvertálva.
* A tulajdonság felügyeleti memóriavesztés ki.
* Kijavítva a hiba, amelyben a bemeneti hangfájl összeomlási sikerült a felismerő.
* Kijavítva a hiba, ahol események tudta fogadott munkamenet leállítási esemény után.
* Rögzített threading néhány versenyhelyzet feltételeket.
* Rögzített egy IOS-es kompatibilitási probléma, amely egy összeomlási eredményezhet.
* Jobb stabilitás Android mikrofon támogatás.
* Kijavítva a hiba, ahol a JavaScript-felismerő lenne figyelmen kívül hagyja a beszédfelismerési nyelv.
* Kijavítva a hiba megakadályozza a EndpointId beállítása (bizonyos esetekben) a JavaScript.
* A JavaScript és a hozzáadott hiányzó AddIntent JavaScript-aláírás AddIntent sorrendi módosított paraméter.

**Példák**

* Hozzáadja a C++ és C# a lehívásos és a leküldéses stream használati samplea a [mintaadattár](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

Hibajavításokat tartalmaz, és megbízhatóság fejlesztései:

* Felismerő értékesítésére versenyhelyzet miatt rögzített lehetséges végzetes hiba
* Rögzített lehetséges végzetes hiba esetén az adott Orientation tulajdonságait.
* A hozzáadott további hiba és a paraméter ellenőrzése.
* Objective-C: A lehetséges végzetes hiba történt a név felülbírálása miatt a NSString-ben.
* Objective-C: Az API igazított láthatósága
* JavaScript Az eseményekre és azok hasznos adataira vonatkozó javítás.
* Dokumentáció fejlesztései.

Az a [mintaadattár](https://aka.ms/csspeech/samples), egy új mintát, JavaScript hozzá lett adva.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0: 2018 – szeptember kiadás

**Új funkciók**

* Objective-C támogatása IOS-eszközökön. Tekintse meg a [Objective-C rövid útmutató iOS-es](quickstart-objectivec-ios.md).
* A böngészőben JavaScript támogatása. Tekintse meg a [JavaScript rövid](quickstart-js-browser.md).

**Használhatatlanná tévő változásai**

* Ebben a kiadásban számos megszakított változást vezetünk be.
  További részletekért olvassa el [ezt a lapot](https://aka.ms/csspeech/breakingchanges_1_0_0) .

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: 2018 – augusztus kiadás

**Új funkciók**

* UWP-alkalmazás most már a Speech SDK-val készített továbbíthatja a Windows App Certification Kit (WACK).
  Tekintse meg a [UWP rövid](quickstart-csharp-uwp.md).
* A .NET Standard 2.0 Linux (Ubuntu 16.04 x 64) támogatása.
* Kísérleti A Java 8 támogatása Windows (64 bites) és Linux rendszeren (Ubuntu 16,04 x64).
  Tekintse meg a [Java Runtime Environment rövid](quickstart-java-jre.md).

**Funkcionális módosítása**

* Tegyen elérhetővé további részletes információ a hibáról a csatlakozási hibák.

**Használhatatlanná tévő változásai**

* Java (Android) a `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` függvény már nem csak egy elérésiút-paraméter. Az elérési út most már automatikusan észleli az összes támogatott platformon.
* A get-hozzáférő tulajdonság `EndpointUrl` Java-és C# el lett távolítva.

**Hibajavítások**

* A Java a fordítási felismerő hang összefoglaló eredménye van megvalósítva, mostantól.
* Kijavítva a hiba, inaktív a szálak és a egy nyílt és a fel nem használt sockets megnövelt számú okozhatja.
* Rögzített probléma, ahol egy hosszú ideig futó felismerése sikerült leállítani a közepén továbbítására.
* Rögzített versenyhelyzet felismerő leállítása.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: 2018 – júliusi kiadás

**Új funkciók**

* Android platform támogatása (23 API: Android 6,0 Marshmallow vagy újabb). Tekintse meg a [Android rövid](quickstart-java-android.md).
* A .NET Standard 2.0 támogatja a Windows. Tekintse meg a [.NET Core rövid](quickstart-csharp-dotnetcore-windows.md).
* Kísérleti A Windows UWP támogatása (1709-es vagy újabb verzió).
  * Tekintse meg a [UWP rövid](quickstart-csharp-uwp.md).
  * Megjegyzés: A Speech SDK-val készített UWP-alkalmazások még nem adják át a Windows-alkalmazás minősítési csomagját (WACK).
* Támogatja az automatikus újracsatlakozás hosszú ideig futó felismerése.

**Funkcionális változások**

* `StartContinuousRecognitionAsync()` támogatja a hosszú ideig futó felismerése.
* A felismerés eredményét további mezőket tartalmaz. Hang elején és időtartama (a órajel során végbemenő is) a felismert szöveget és további értékek, amelyek a felismerés állapotát, például tolva vagyunk `InitialSilenceTimeout` és `InitialBabbleTimeout`.
* Támogatási AuthorizationToken előállító példányok létrehozásához.

**Használhatatlanná tévő változásai**

* Felismerési események: Az esemény típusa nem egyezik a hiba eseményeivel.
* C# nyelven SpeechOutputFormat átnevezte OutputFormat igazított c++ marad.
* A visszatérési típus az egyes módszerek a `AudioInputStream` felület kis mértékben módosítani:
   * A Java a `read` metódus most visszatért `long` helyett `int`.
   * A C# a `Read` metódus most visszatért `uint` helyett `int`.
   * A C++ a `Read` és `GetFormat` most visszatérési módszerek `size_t` helyett `int`.
* C++: A hangbemeneti adatfolyamok példányai mostantól csak a `shared_ptr`-ként adhatók át.

**Hibajavítások**

* Az eredmény nem megfelelő visszatérési értékek rögzített amikor `RecognizeAsync()` túllépi az időkorlátot.
* A Windows media foundation kódtárak függőség el lett távolítva. Az SDK mostantól az alapvető hang API-k.
* Dokumentációs javítás: A támogatott régiók leírásához hozzáadott egy [régiók](regions.md) lapot.

**Ismert hiba**

* A beszédfelismerés SDK for Android nem jelentést speech összefoglaló eredmények a fordítás. A probléma a következő kiadás javítja.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0: 2018 – júniusi kiadás

**Funkcionális változások**

- AudioInputStream

  Egy felismerő most már felhasználhatja a stream hang forrásaként. További információkért tekintse meg a kapcsolódó [gyakorlati útmutató](how-to-use-audio-input-streams.md).

- Részletes kimeneti formátum

  Amikor létrehoz egy `SpeechRecognizer`, kérheti a `Detailed` vagy `Simple` kimeneti formátum. A `DetailedSpeechRecognitionResult` maszkolt cenzúrázása egy megbízhatósági pontszám, felismert szöveget, lexikai nyers formában, normalizált űrlap és normalizált űrlap tartalmazza.

**Kompatibilitástörő változás**

- A módosított `SpeechRecognitionResult.Text` a `SpeechRecognitionResult.RecognizedText` C#-ban.

**Hibajavítások**

- Kijavítottuk a lehetséges visszahívási a USP rétegben leállítás során.

- Egy felismerő felhasznált bemeneti hangfájl, ha azt volt üzemben szükséges hosszabb fájlleírót.

- Az üzenet szivattyú és a felismerő között számos holtpontok eltávolítva.

- Fire egy `NoMatch` vezethet, ha a szolgáltatás válasza túllépte az időkorlátot.

- A Windows media foundation könyvtáraiban betöltött késleltetés. Ez a kódtár mikrofon bemeneti csak szükség.

- A feltöltési sebességét hívásaiból sebessége kétszer az eredeti hang korlátozva.

- Windows, a C# nyelvet használó .NET-szerelvények most strong neve.

- Dokumentációs javítás: `Region` az információt kötelező megadni egy felismerő létrehozásához.

További példák lettek hozzáadva, és folyamatosan változik. A legújabb minták, lásd: a [beszéd SDK-minták GitHub-adattár](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733: 2018 – májusi kiadás

Ebben a kiadásban a Cognitive Services beszédfelismerő SDK első nyilvános előzetes verziója.
