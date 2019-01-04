---
title: Kibocsátási megjegyzések – beszédszolgáltatások
titlesuffix: Azure Cognitive Services
description: Tekintse meg a futó naplózása a funkciók kiadása, fejlesztéseket, hibajavításokat és ismert problémák az Azure Speech Services.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 7485ca1e4b1143ed46c9b3bef9ca66af0638b4f8
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53599416"
---
# <a name="release-notes"></a>Kibocsátási megjegyzések

## <a name="speech-sdk-120-2018-december-release"></a>Beszédfelismerés SDK 1.2.0-s vagy annál újabb: 2018. – December kiadás

**Új funkciók**

* Python
  * A bétaverzió Python-támogatás (3.5-ös vagy újabb) ebben a kiadásban érhető el. További részletekért [Itt](quickstart-python.md).
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
* Kijavítva egy probléma, amely a végső eredményt sikertelen fordítások hiányzó vezethet.
* C#: Ha egy aszinkron művelet nem volt várni a fő szálát az, volt lehetséges a felismerő sikerült eldobva, mielőtt az aszinkron feladat befejeződött.
* Java: Kijavítva egy probléma, a Java virtuális gép összeomlás eredményez.
* Objective-c Rögzített enumerálási leképezés; RecognizedIntent visszaadott RecognizingIntent helyett.
* JavaScript: Készlet alapértelmezett kimeneti formátum a "simple" SpeechConfig.
* JavaScript: A JavaScript a konfigurációs objektum tulajdonságainak és egyéb nyelvekhez közötti inkonzisztencia eltávolítása.

**Példák**

* Frissítve, és rögzített több mintát (például kimeneti beszédhangot fordítási stb).
* A Node.js-minták hozzáadva a [mintaadattár](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Beszéd 1.1.0-s SDK

**Új funkciók**

* Android x86/x64 támogatása.
* Proxy-támogatás: A SpeechConfig objektumban most meghívhat egy függvényt, amely a proxy adatainak (állomásnév, port, felhasználónév és jelszó) beállítása. Ez a funkció még nem áll rendelkezésre álló IOS-eszközökön.
* Továbbfejlesztett hibakód és üzenetek. Elismerés hibát adott vissza, ha ez már adta-e meg `Reason` (a törölt esemény) vagy `CancellationDetails` (a felismerés eredményét) való `Error`. A törölt esemény mostantól tartalmaz két további tagok, `ErrorCode` és `ErrorDetails`. A kiszolgáló további információ a hibáról az a jelzett hibát adott vissza, ha most lesz elérhető az új tagjait.

**Fejlesztései**

* További ellenőrzés hozzáadva a felismerő konfigurációs és további hozzáadott hibaüzenet jelenik meg.
* Hangfájl közepén régóta csend jobb kezelése.
* NuGet-csomagot: .NET-keretrendszer projektek esetén ez megakadályozza, hogy használhatja a AnyCPU konfiguráció.

**Hibajavítások**

* Rögzített felismerő található kivételek. Ezenkívül kivételek lépett fel, és visszavonva esemény konvertálva.
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

## <a name="speech-sdk-101"></a>Beszéd 1.0.1 SDK

Hibajavításokat tartalmaz, és megbízhatóság fejlesztései:

* Felismerő értékesítésére versenyhelyzet miatt rögzített lehetséges végzetes hiba
* Rögzített lehetséges végzetes hiba esetén az adott Orientation tulajdonságait.
* A hozzáadott további hiba és a paraméter ellenőrzése.
* Objective-c Rögzített lehetséges végzetes hiba okozza a NSString felülbírálása nevét.
* Objective-c API beállított láthatóságát
* JavaScript: Rögzített eseményeket és azok is észleltünk adattartalmakat.
* Dokumentáció fejlesztései.

Az a [mintaadattár](https://aka.ms/csspeech/samples), egy új mintát, JavaScript hozzá lett adva.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services beszéd SDK 1.0.0-s: 2018. szeptember kiadás

**Új funkciók**

* Objective-C támogatása IOS-eszközökön. Tekintse meg a [Objective-C rövid útmutató iOS-es](quickstart-objectivec-ios.md).
* A böngészőben JavaScript támogatása. Tekintse meg a [JavaScript rövid](quickstart-js-browser.md).

**Használhatatlanná tévő változásai**

* Ebben a kiadásban bevezetett kompatibilitástörő változások számos.
  Ellenőrizze a [ezt oldal](https://aka.ms/csspeech/breakingchanges_1_0_0) részleteiről.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services beszéd SDK 0.6.0: 2018 augusztus kiadás

**Új funkciók**

* UWP-alkalmazás most már a Speech SDK-val készített továbbíthatja a Windows App Certification Kit (WACK).
  Tekintse meg a [UWP rövid](quickstart-csharp-uwp.md).
* A .NET Standard 2.0 Linux (Ubuntu 16.04 x 64) támogatása.
* Kísérleti funkció: Támogatja a Java 8 (64 bites) Windows és Linux (Ubuntu 16.04 x 64).
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

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services beszéd SDK 0.5.0-s: Július 2018-as kiadás

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

**Használhatatlanná tévő változásai**

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

**Ismert hiba**

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
