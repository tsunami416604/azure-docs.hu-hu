---
title: Kibocsátási megjegyzések – beszédszolgáltatások
titlesuffix: Azure Cognitive Services
description: Tekintse meg a futó naplózása a funkciók kiadása, fejlesztéseket, hibajavításokat és ismert problémák az Azure Speech Services.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 4/5/2019
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: d53019294a255e42c4cf66f59226c9234a5adf73
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59359742"
---
# <a name="release-notes"></a>Kibocsátási megjegyzések

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

Ez a kiadás csak a JavaScript. Nincsenek funkciók lettek hozzáadva. A következő javítások történtek:

* Megakadályozza, hogy a webpack használatával készült https proxyügynök betöltése.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0: A 2019-áprilisi kiadás

**Új funkciók** 

* Az SDK-val most már támogatja a szöveg-hang transzformációs szolgáltatás bétaverziójának. A Windows és Linux rendszerű asztali C++, a támogatott C#, és a Java használatával. További információkért tekintse a [szöveg-hang transzformációs áttekintése](text-to-speech.md#get-started-with-text-to-speech).
* Az SDK-val most stream bemeneti fájlok formájában támogatja az MP3- és Opus/Ogg hangfájlok. Ez a funkció csak a c++ segítségével Linux rendszeren érhető el, és C# és jelenleg bétaverzióban (További részletek [Itt](how-to-use-compressed-audio-input-streams.md)).
* A beszédfelismerés SDK a .NET core, Java, C++ és Objective-C macOS-támogatás révén. Az Objective-C támogatja a macOS jelenleg bétaverzióban.
* iOS: A beszédfelismerés SDK IOS (Objective-C) most is közzéteszi, egy CocoaPod.
* JavaScript: Nem alapértelmezett mikrofon beviteli eszközként támogatása.
* JavaScript: Proxy-támogatás a node.js-ben.

**Példák**

* A macOS rendszeren a Speech SDK Objective-C és c++ minták érhetők el.
* Az a szöveg-hang transzformációs szolgáltatás használatának bemutatásához minták érhetők el.

**Fejlesztések / módosítása**

* Python: További tulajdonságainak felismerési eredményeket most keresztül érhetők el a `properties` tulajdonság.
* A további fejlesztés és hibakeresés támogatása SDK naplózási és diagnosztikai információk egy naplófájlba átirányíthatja (További részletek [Itt](how-to-use-logging.md)).
* JavaScript: Hang feldolgozási teljesítmény javítása.

**Hibajavítások**

* Mac/iOS: Egy hiba, ha a Speech Service-kapcsolatot nem lehet létrehozni egy hosszú ideig várakozott vezetett a rendszerriasztások mechanizmusában.
* Python: javíthatja a Python-visszahívások argumentumok a hibakezelést.
* JavaScript: A beszédfelismerés reporting rögzített nem megfelelő állapotban RequestSession napon fejeződött be.

## <a name="speech-sdk-131-2019-february-refresh"></a>Beszédfelismerés SDK 1.3.1: A 2019-februári frissítés

Ez a hibajavítás kiadás, és csak ez hatással lenne a natív és felügyelt SDK-t. Ez nincs hatással az SDK-t a JavaScript-verzióját.

**Hibajavítás**

* Rögzített memóriavesztés, amikor a mikrofon bemeneti. Stream-alapú vagy a bemeneti fájl nincs hatással.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0: A 2019-februári kiadással

**Új funkciók**

* A beszédfelismerés SDK támogatja a bemeneti mikrofon AudioConfig osztály keresztül. Ez lehetővé teszi az audio adatok streamelése a Speech Services az alapértelmezettől eltérő mikrofon. További információkért lásd: a dokumentáció leíró [hangbemeneti eszköz kiválasztása](how-to-select-audio-input-devices.md). Ez még nem áll rendelkezésre a JavaScript.
* A beszédfelismerés SDK mostantól támogatja a Unity béta verziójában. Adja meg a probléma szakaszában keresztül visszajelzést a [GitHub-mintaadattárból](https://aka.ms/csspeech/samples). Ez a kiadás támogatja a Unity a x86 és x64 (asztalon vagy univerzális Windows-Platformos alkalmazások), Windows és Android (ARM32/64, x86). További információkat a [Unity rövid](quickstart-csharp-unity.md).
* A fájl `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (tartalmazza a szükséges a korábbi kiadásokban) többé nem szükséges. A funkció mostantól integrálva van a core SDK-t.


**Példák**

A következő új tartalom érhető el a [mintaadattár](https://aka.ms/csspeech/samples):

* További minták AudioConfig.FromMicrophoneInput.
* További Python minták szándékának felismerése és a fordítás.
* További minták az IOS-es a kapcsolat objektumot.
* További Java-minták hangkimeneti fordítását.
* Új minta használata a [Batch Beszédátírási REST API](batch-transcription.md).

**Fejlesztések / módosítása**

* Python
  * Továbbfejlesztett paraméter-ellenőrzés és SpeechConfig hibaüzenetek.
  * A kapcsolat objektumot támogatása.
  * A Python 32 bites Windows a (x86) támogatása.
  * A beszédfelismerés SDK Pythonhoz készült bétaverziós kívül esik.
* iOS
  * Az SDK-val most már az iOS SDK verziója 12.1 elleni épül.
  * Az SDK-t mostantól támogatja az IOS-es 9.2 és újabb verziók.
  * Segédanyagok javítása, és hárítsa el a tulajdonságnevek több.
* JavaScript
  * A kapcsolat objektumot támogatása.
  * Csomagolt JavaScript-típus definíciós fájlok hozzáadása
  * Kezdeti támogatás és kifejezés mutatók megvalósítása.
  * Visszatérési tulajdonsággyűjteményében elismerési JSON szolgáltatással
* Windows DLL-ek mostantól tartalmazza a verzió erőforrás.
* Ha létrehoz egy felismerő `FromEndpoint` adhat paramétereket közvetlenül a végpont URL-címe. Használatával `FromEndpoint` szabványos konfigurációs tulajdonságai a felismerő nem lehet konfigurálni.

**Hibajavítások**

* Üres proxykiszolgáló felhasználónév és a proxy jelszavát nem megfelelően kezelt. Ebben a kiadásban Ha proxy-felhasználónév és a proxy jelszavát egy üres karakterláncra, nem kerül sor a proxy való csatlakozáskor.
* Munkamenet-azonosító által létrehozott az SDK nem volt mindig valóban véletlenszerű néhány nyelv esetében&nbsp;/ környezetekben. Hozzáadott véletlenszerű generátor inicializálása a probléma megoldásához.
* Engedélyezési jogkivonat kezelésének javítása. Ha szeretné használni egy engedélyezési jogkivonatot, adja meg a SpeechConfig, és hagyja üresen az előfizetési kulcsot. Ezután hozzon létre a felismerő a szokásos módon.
* Bizonyos esetekben a kapcsolat objektum nem szabadul fel megfelelően. Ez megoldottuk.
* A JavaScript-minta a hangkimeneti támogatására is a Safari fordítási összefoglaló rendszerriasztások mechanizmusában.

## <a name="speech-sdk-121"></a>Beszéd 1.2.1-es SDK

Ez a kiadás csak a JavaScript. Nincsenek funkciók lettek hozzáadva. A következő javítások történtek:

* Adatfolyam turn.end, nem pedig speech.end aktiválódik.
* Javítva a hang szivattyú, hogy adott ütemezés nem ezután küldése, ha az aktuális a küldés nem sikerült.
* Javítsa ki a folyamatos felismerése a hitelesítési tokent.
* Hibajavítás a különböző felismerő / végpontok.
* Dokumentáció fejlesztései.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0: 2018. – December kiadás

**Új funkciók**

* Python
  * A bétaverzió Python-támogatás (3.5-ös vagy újabb) ebben a kiadásban érhető el. További információkért lásd: here](quickstart-python.md).
* JavaScript
  * A beszédfelismerés JavaScript SDK lett, nyílt forráskódú. A forráskódja elérhető a [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  * Mostantól támogatjuk a Node.js, a további információk találhatók [Itt](quickstart-js-node.md).
  * A hang munkamenetek hossza korlátozása el lett távolítva, újracsatlakozás a megjelöléssel automatikusan megtörténik.
* A kapcsolatobjektum
  * A felismerő egy kapcsolat objektumot érheti el. Ez az objektum teszi lehetővé explicit módon a szolgáltatás kapcsolatot kezdeményez, és feliratkozhat a csatlakoztatása és leválasztása események.
    (Ez még nem áll rendelkezésre a JavaScript és Python.)
* Ubuntu 18.04 támogatása.
* Android
  * Engedélyezett ProGuard támogatja az Alkalmazáscsomag létrehozása során.

**Fejlesztései**

* Továbbfejlődött a belső szál használat, a szálak, a zárolás, mutexek számának csökkentését.
* Továbbfejlesztett hibajelentés / információkat. Számos esetben hibaüzenetek nem ki lett érvényesülnek.
* Frissített fejlesztési függőségek naprakész modulok használata javascriptben.

**Hibajavítások**

* Rögzített méretű memória elveszít egy adattípus-eltérés a RecognizeAsync miatt.
* Bizonyos esetekben kivételek kiszivárgott is folyamatban van.
* Memóriavesztés értesítenünk fordítási esemény argumentumai.
* Rögzített zárolási problémát a reconnect a hosszan futó munkameneteket.
* Javítva lett egy probléma, hogy hiányzik a végső eredményt sikertelen fordítások vezethet.
* C#: Ha egy aszinkron művelet nem volt várni a fő szálát az, volt lehetséges a felismerő sikerült eldobva, mielőtt az aszinkron feladat befejeződött.
* Java: Kijavítva egy probléma, a Java virtuális gép összeomlás eredményez.
* Objective-C: Rögzített enumerálási leképezés; RecognizedIntent visszaadott RecognizingIntent helyett.
* JavaScript: Készlet alapértelmezett kimeneti formátum a "simple" SpeechConfig.
* JavaScript: A JavaScript a konfigurációs objektum tulajdonságainak és egyéb nyelvekhez közötti inkonzisztencia eltávolítása.

**Példák**

* Frissítve, és rögzített több mintát (például kimeneti beszédhangot fordítási stb.).
* A Node.js-minták hozzáadva a [mintaadattár](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**Új funkciók**

* Android x86/x64 támogatása.
* Proxy-támogatás: A SpeechConfig objektumban most meghívhat egy függvényt, amely a proxy adatainak (állomásnév, port, felhasználónév és jelszó) beállítása. Ez a funkció még nem áll rendelkezésre álló IOS-eszközökön.
* Továbbfejlesztett hibakód és üzenetek. Elismerés hibát adott vissza, ha ez már adta-e meg `Reason` (a törölt esemény) vagy `CancellationDetails` (a felismerés eredményét) való `Error`. A törölt esemény mostantól tartalmaz két további tagok, `ErrorCode` és `ErrorDetails`. A kiszolgáló további információ a hibáról az a jelzett hibát adott vissza, ha most lesz elérhető az új tagjait.

**Fejlesztései**

* További ellenőrzés hozzáadva a felismerő konfigurációs és további hozzáadott hibaüzenet jelenik meg.
* Hangfájl közepén régóta csend jobb kezelése.
* NuGet-csomagot: .NET-keretrendszer projektek esetén ez megakadályozza, hogy használhatja a AnyCPU konfiguráció.

**Hibajavítások**

* Rögzített felismerő található kivételek. Ezenkívül kivételek észlelt, és visszavonva esemény konvertálva.
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
* Objective-C: Rögzített lehetséges végzetes hiba okozza a NSString felülbírálása nevét.
* Objective-C: API beállított láthatóságát
* JavaScript: Rögzített eseményeket és azok is észleltünk adattartalmakat.
* Dokumentáció fejlesztései.

Az a [mintaadattár](https://aka.ms/csspeech/samples), egy új mintát, JavaScript hozzá lett adva.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0: 2018. szeptember kiadás

**Új funkciók**

* Objective-C támogatása IOS-eszközökön. Tekintse meg a [Objective-C rövid útmutató iOS-es](quickstart-objectivec-ios.md).
* A böngészőben JavaScript támogatása. Tekintse meg a [JavaScript rövid](quickstart-js-browser.md).

**Kompatibilitástörő változások**

* Ebben a kiadásban bevezetett kompatibilitástörő változások számos.
  Ellenőrizze a [ezt oldal](https://aka.ms/csspeech/breakingchanges_1_0_0) részleteiről.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: 2018 augusztus kiadás

**Új funkciók**

* UWP-alkalmazás most már a Speech SDK-val készített továbbíthatja a Windows App Certification Kit (WACK).
  Tekintse meg a [UWP rövid](quickstart-csharp-uwp.md).
* A .NET Standard 2.0 Linux (Ubuntu 16.04 x 64) támogatása.
* Kísérleti funkció: Támogatja a Java 8 (64 bites) Windows és Linux (Ubuntu 16.04 x 64).
  Tekintse meg a [Java Runtime Environment rövid](quickstart-java-jre.md).

**Funkcionális módosítása**

* Tegyen elérhetővé további részletes információ a hibáról a csatlakozási hibák.

**Kompatibilitástörő változások**

* Java (Android) a `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` függvény már nem csak egy elérésiút-paraméter. Az elérési út most már automatikusan észleli az összes támogatott platformon.
* A get-hozzáférő tulajdonság `EndpointUrl` Java-és C# el lett távolítva.

**Hibajavítások**

* A Java a fordítási felismerő hang összefoglaló eredménye van megvalósítva, mostantól.
* Kijavítva a hiba, inaktív a szálak és a egy nyílt és a fel nem használt sockets megnövelt számú okozhatja.
* Rögzített probléma, ahol egy hosszú ideig futó felismerése sikerült leállítani a közepén továbbítására.
* Rögzített versenyhelyzet felismerő leállítása.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: Július 2018-as kiadás

**Új funkciók**

* Támogatás Android platform (API 23: Android 6.0-s Marshmallow vagy újabb). Tekintse meg a [Android rövid](quickstart-java-android.md).
* A .NET Standard 2.0 támogatja a Windows. Tekintse meg a [.NET Core rövid](quickstart-csharp-dotnetcore-windows.md).
* Kísérleti funkció: Az UWP támogatja a Windows (1709-es vagy újabb verzió).
  * Tekintse meg a [UWP rövid](quickstart-csharp-uwp.md).
  * Megjegyzés: A beszédfelismerés SDK-val készített UWP-alkalmazás még nem adnak át a Windows App Certification Kit (WACK).
* Támogatja az automatikus újracsatlakozás hosszú ideig futó felismerése.

**Funkcionális változások**

* `StartContinuousRecognitionAsync()` támogatja a hosszú ideig futó felismerése.
* A felismerés eredményét további mezőket tartalmaz. Hang elején és időtartama (a órajel során végbemenő is) a felismert szöveget és további értékek, amelyek a felismerés állapotát, például tolva vagyunk `InitialSilenceTimeout` és `InitialBabbleTimeout`.
* Támogatási AuthorizationToken előállító példányok létrehozásához.

**Kompatibilitástörő változások**

* Elismerés események: NoMatch eseménytípus hibaesemény lett egyesítve.
* C# nyelven SpeechOutputFormat átnevezte OutputFormat igazított c++ marad.
* A visszatérési típus az egyes módszerek a `AudioInputStream` felület kis mértékben módosítani:
   * A Java a `read` metódus most visszatért `long` helyett `int`.
   * A C# a `Read` metódus most visszatért `uint` helyett `int`.
   * A C++ a `Read` és `GetFormat` most visszatérési módszerek `size_t` helyett `int`.
* C++: Most már a hang bemeneti streamekhez példányait csak argumentumként átadhatók egy `shared_ptr`.

**Hibajavítások**

* Az eredmény nem megfelelő visszatérési értékek rögzített amikor `RecognizeAsync()` túllépi az időkorlátot.
* A Windows media foundation kódtárak függőség el lett távolítva. Az SDK mostantól az alapvető hang API-k.
* Dokumentációs javítás: Hozzáadott egy [régiók](regions.md) a támogatott régiók leíró oldalon.

**Ismert probléma**

* A beszédfelismerés SDK for Android nem jelentést speech összefoglaló eredmények a fordítás. A probléma a következő kiadás javítja.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services beszéd SDK 0.4.0: 2018 június kiadás

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

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services beszéd SDK 0.2.12733: 2018-május kiadás

Ebben a kiadásban a Cognitive Services beszédfelismerő SDK első nyilvános előzetes verziója.
