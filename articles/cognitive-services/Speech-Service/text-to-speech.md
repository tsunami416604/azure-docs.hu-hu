---
title: Szöveges – beszéd – beszéd szolgáltatás
titleSuffix: Azure Cognitive Services
description: A Speech Service szöveg-beszéd funkciója egy olyan szolgáltatás, amely lehetővé teszi, hogy az alkalmazások, eszközök vagy eszközök szövegét természetes, emberi – például szintetizált beszédre alakítsa át. Válasszon a standard és a neurális hangok közül, vagy hozzon létre saját egyéni hangját a termék vagy a márka számára. a 75 + standard hangok több mint 45 nyelven és területi beállításban érhetők el, és az 5 neurális hang 4 nyelven és területi beállításban érhető el.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: erhopf
ms.openlocfilehash: d12b952d298b41c8d06f0fcac141a45749de9051
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71799957"
---
# <a name="what-is-text-to-speech"></a>Mi az a szöveg – beszéd?

Az Azure Speech Services egy olyan szolgáltatás, amely lehetővé teszi, hogy az alkalmazások, eszközök vagy eszközök szöveggé alakítsanak természetes, emberi – például szintetizált beszédbe. Válasszon a standard és a neurális hangok közül, vagy hozzon létre saját egyéni hangját a termék vagy a márka számára. a 75 + standard hangok több mint 45 nyelven és területi beállításban érhetők el, és az 5 neurális hang 4 nyelven és területi beállításban érhető el. A teljes listát lásd: [támogatott nyelvek](language-support.md#text-to-speech).

A szövegről beszédre irányuló technológia lehetővé teszi, hogy a Content Creator különböző módokon kommunikáljon a felhasználóival. A szöveg és a beszéd közötti kommunikáció javíthatja a hozzáférést azáltal, hogy a felhasználók számára lehetővé teszi a tartalmak hallható módon történő használatát. Azt jelzi, hogy a felhasználó rendelkezik-e a vizualizációs fogyatékossággal, a tanulási fogyatékossággal vagy a navigációs információk megadásával, miközben a szöveg és a beszéd is javíthatja a meglévő felhasználói élményt. A szöveg és a beszéd is értékes kiegészítő funkció a Voice bots és a virtuális asszisztensek számára.


A Speech szintézis Markup Language (SSML) használatával egy XML-alapú Markup Language, a szöveg-beszéd szolgáltatást használó fejlesztők megadhatják, hogy a bemeneti szöveg hogyan legyen konvertálva a szintetizált beszédbe. A SSML a Pitch, a kiejtés, a beszéd arány, a kötet és más beállítások állíthatók be. További információ: [SSML](#speech-synthesis-markup-language-ssml).

### <a name="standard-voices"></a>Standard hangok

A standard hangok statisztikai számszerű szintézis és/vagy összefűzési módszerek használatával jönnek létre. Ezek a hangok jól érthetőek és természetesek. Egyszerűen engedélyezheti, hogy alkalmazásai több mint 45 nyelven beszéljenek, számos hanglehetőséggel. Ezek a hangok magas kiejtési pontosságot biztosítanak, beleértve a rövidítések támogatását, a rövidítések, a dátum/idő értelmezését, a telefonokat és egyebeket. Az alkalmazások és szolgáltatások kisegítő lehetőségeinek kihasználása érdekében a normál hang használatával a felhasználók a tartalmat hallható módon kezelhetik.

### <a name="neural-voices"></a>Neurális hangok

A neurális hangok mély neurális hálózatokkal használják fel a hagyományos szöveg-beszéd rendszerek korlátait a stressz és a hanglejtés mintázatának, valamint a hangvételi egységeknek a számítógépes hangra való beépítésével. A standard szöveg – beszéd típusú bontás a prosody különböző nyelvi elemzési és akusztikai előrejelzési lépésekre bontja fel, amelyek független modellekre vonatkoznak, ami tompa hangszintézist eredményezhet. A neurális képességgel párhuzamosan prosody az előrejelzés és a hangszintézis, ami több folyadékot és természetes hangú hangot eredményez.

A neurális hangokat felhasználhatja az csevegőrobotok-és virtuális asszisztensekkel folytatott interakcióra, így természetesebb és vonzó lehet a digitális szövegek, például az e-könyvek konvertálása az hangoskönyvekre, és hatékonyabbá teheti az autós navigációs rendszereket. Az emberi jellegű természetes prosody és a szavak egyértelmű megfogalmazásával a neurális hangok jelentősen csökkentik a figyelés fáradtságot, amikor az AI-rendszerekkel kommunikál.

A neurális hangok különböző stílusokat támogatnak, mint például a semleges és a vidám. Például a Jessa (en-US) hangja vidáman tud beszélni, ami meleg, boldog beszélgetésre van optimalizálva. A hangkimenetek, például a hang, a szurok és a sebesség a [beszédfelismerési leíró nyelv](speech-synthesis-markup.md)használatával állíthatók be. Az elérhető hangok teljes listájáért lásd: [támogatott nyelvek](language-support.md#text-to-speech).

Ha többet szeretne megtudni a neurális hangok előnyeiről, tekintse meg a [Microsoft új neurális szöveg-beszéd szolgáltatását, amellyel a gépek hasonló személyeket beszélnek](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/).

### <a name="custom-voices"></a>Egyéni hangok

A hangtestreszabás lehetővé teszi a márka felismerhető, egyfajta hangjának létrehozását. Egyéni hangbetűkészlet létrehozásához készítse el a studiót, és töltse fel a társított parancsfájlokat a betanítási adatokba. A szolgáltatás ezután létrehoz egy egyedi hangmodellt a rögzítéshez. Ezt az egyéni hangbetűtípust használhatja a beszéd szintetizálása érdekében. További információ: [egyéni hangok](how-to-customize-voice-font.md).

## <a name="speech-synthesis-markup-language-ssml"></a>Beszédszintézis-jelölőnyelv (SSML)

A Speech szintézis Markup Language (SSML) egy XML-alapú Markup Language, amely lehetővé teszi a fejlesztők számára, hogy a szöveg-beszéd szolgáltatás segítségével hogyan konvertálja a bemeneti szöveget a szintetizált beszédbe. Az egyszerű szöveghez képest a SSML lehetővé teszi a fejlesztők számára a hangfelvétel, a kiejtés, a beszéd arány, a kötet és még több szöveg-beszéd típusú kimenet finomhangolását. Normál írásjelek, például egy időszak utáni felfüggesztés vagy a megfelelő hanglejtés használata, ha a mondatot kérdőjeltel végződik, automatikusan kezeli a rendszer.

A szöveg-beszéd szolgáltatásnak eljuttatott összes szöveges bemenetet SSML-ként kell felépíteni. További információ: [Speech szintézis Markup Language](speech-synthesis-markup.md).

### <a name="pricing-note"></a>Díjszabási Megjegyzés

A szöveg-beszéd típusú szolgáltatás használatakor a rendszer minden, a beszédre konvertált karakter után számláz, beleértve a központozást is. Míg maga a SSML-dokumentum nem számlázható, a szöveg átalakításának módjára, például a fonémák és a Pitch formátumára szolgáló opcionális elemek számlázandó karakternek számítanak. Az alábbi lista tartalmazza a számlázható tartalmakat:

* A kérelem SSML törzsében a szöveg-beszéd szolgáltatásnak átadott szöveg
* A kérelem törzsének összes jelölése a SSML formátumban, kivéve a `<speak>` és `<voice>` címkéket
* Betűk, írásjelek, szóközök, tabulátorok, jelölések és minden fehér szóköz
* Minden Unicode-ban definiált kód pont

Részletes információkat a [díjszabásban](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)talál.

> [!IMPORTANT]
> Minden kínai, Japán és koreai nyelvi karakter két karakternek számít a számlázáshoz.

## <a name="core-features"></a>Alapvető funkciók

Ez a táblázat a szöveg és a beszéd alapvető funkcióit sorolja fel:

| Használati eset | SDK | REST |
|----------|-----|------|
| Szöveg konvertálása beszédre | Igen | Igen |
| Adatkészletek feltöltése a hangalapú átalakításhoz. | Nem | Igen @ no__t – 0 |
| Hang betűkészlet-modelljeinek létrehozása és kezelése. | Nem | Igen @ no__t – 0 |
| Hangbetűkészletek központi telepítésének létrehozása és kezelése. | Nem | Igen @ no__t – 0 |
| Hangos betűkészlet-tesztek létrehozása és kezelése. | Nem | Igen @ no__t – 0 |
| Előfizetések kezelése. | Nem | Igen @ no__t – 0 |

\* @no__t – a 1These-szolgáltatások az cris.ai végpont használatával érhetők el. Lásd a [hencegés referenciáját](https://westus.cris.ai/swagger/ui/index). Ezek az egyéni hangképzési és felügyeleti API-k olyan szabályozást valósítanak meg, amely 5 másodpercenként 25 másodpercenként korlátozza a kérelmeket, míg a Speech szintézis API maga valósítja meg a szabályozást, amely lehetővé teszi a maximális 200-kérelmek másodpercenkénti számát. A szabályozás során a rendszer értesítést küld az üzenetek fejlécén keresztül. *

## <a name="get-started-with-text-to-speech"></a>Ismerkedés a szöveg és a beszéd között

Olyan gyors útmutatókat is kínálunk, amelyek kevesebb, mint 10 perc alatt futtatják a kódot. Ez a tábla nyelv alapján rendezett szöveg-beszéd típusú rövid útmutatók listáját tartalmazza.

### <a name="sdk-quickstarts"></a>SDK-gyors útmutatók

| Gyors útmutató (SDK) | Platform | API-referencia |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-text-to-speech-dotnetcore.md) | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| [C#, .NET-keretrendszer](quickstart-text-to-speech-dotnet-windows.md) | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-text-to-speech-csharp-uwp.md) | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| [C#, Egység](quickstart-text-to-speech-csharp-unity.md) | Windows, Android | [Tallózás](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) | Windows | [Tallózás](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Tallózás](https://aka.ms/csspeech/cppref) |
| [Java](quickstart-text-to-speech-java-jre.md) | Windows, Linux, macOS | [Tallózás](https://aka.ms/csspeech/javaref) |
| [Java](quickstart-text-to-speech-java-android.md) | Android | [Tallózás](https://aka.ms/csspeech/javaref) |
| [Objective-C](quickstart-text-to-speech-objectivec-macos.md) | macOS | [Tallózás](https://aka.ms/csspeech/objectivecref) |
| [Objective-C](quickstart-text-to-speech-objectivec-ios.md) | iOS | [Tallózás](https://aka.ms/csspeech/objectivecref) |
| [Swift](quickstart-text-to-speech-swift-macos.md) | macOS | [Tallózás](https://aka.ms/csspeech/objectivecref) |
| [Swift](quickstart-text-to-speech-swift-ios.md) | iOS | [Tallózás](https://aka.ms/csspeech/objectivecref) |
| [Python](quickstart-text-to-speech-python.md) | Ablak, Linux, macOS | [Tallózás](https://aka.ms/csspeech/pythonref) |

### <a name="rest-quickstarts"></a>REST-útmutató

| Gyors útmutató (REST) | Platform | API-referencia |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows, macOS, Linux | [Tallózás](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Ablak, macOS, Linux | [Tallózás](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Ablak, macOS, Linux | [Tallózás](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>Mintakód

A szöveg és a beszéd mintája a GitHubon érhető el. Ezek a minták a legnépszerűbb programozási nyelvek szöveg-beszéd átalakítását fedik le.

* [Szöveg – beszéd minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Szöveg – beszéd minták (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Segédanyagok

* [Beszéd SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Szöveg – beszéd](rest-text-to-speech.md)
* [REST API: Kötegelt átírás és testreszabás](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>További lépések

* [Ingyenes Speech Services-előfizetés beszerzése](get-started.md)
* [Hozzon létre egyéni hangtípust](how-to-customize-voice-font.md)
