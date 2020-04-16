---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: trbye
ms.openlocfilehash: 629fc5e3bc41377fe852a1648680d77b22395d02
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400969"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene, győződjön meg arról, hogy:

> [!div class="checklist"]
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](../../../../quickstarts/setup-platform.md?tabs=linux&pivots=programmming-language-cpp)
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [Forrásfájl feltöltése Azure-blobba](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="open-your-project-in-visual-studio"></a>A projekt megnyitása a Visual Studióban

Az első lépés annak biztosítása, hogy a projekt meg legyen nyitva a Visual Studióban.

1. Indítsa el a Visual Studio 2019-et.
2. Töltse be a `helloworld.cpp`projektet, és nyissa meg a programot.

## <a name="add-a-references"></a>Hivatkozások hozzáadása

A kódfejlesztés felgyorsítása érdekében néhány külső összetevőt fogunk használni:
* [CPP pihenés SDK](https://github.com/microsoft/cpprestsdk) REST-hívások at kezdeményező ügyféltár.
* [nlohmann/json](https://github.com/nlohmann/json) Praktikus JSON elemzés / szerializálás / deszerializálás könyvtár.

Mindkettő lehet beiktatott használ [vcpkg](https://github.com/Microsoft/vcpkg/).

```
vcpkg install cpprestsdk cpprestsdk:x64-windows
vcpkg install nlohmann-json
```

## <a name="start-with-some-boilerplate-code"></a>Kezdje néhány sablonkóddal.

Adjunk hozzá néhány kódot, ami csontvázként működik a projektünkhöz.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-32,187-190,300-309)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>JSON csomagolóanyagok

Mivel a REST API json formátumban fogad el kéréseket, és a JSON-ban is visszaadja az eredményeket, csak karakterláncok használatával kommunikálhatunk velük, de ez nem ajánlott.
Annak érdekében, hogy a kérelmek és válaszok könnyebben kezelhető, akkor állapítsa meg néhány osztályt használni szerializálása / deszerializálása a JSON és néhány módszert, hogy segítse nlohmann / json.

Gyerünk, és tegye `recognizeSpeech` a nyilatkozatok előtt .
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=33-185)]

## <a name="create-and-configure-an-http-client"></a>Http-ügyfél létrehozása és konfigurálása
Az első dolog, amire szükségünk lesz egy Http Client, amely a megfelelő alap URL-t és hitelesítési készlet.
A kód beszúrása a következőbe:`recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=191-197)]

## <a name="generate-a-transcription-request"></a>Átírási kérelem létrehozása
Ezután létrehozunk egy átírási kérelmet. A kód hozzáadása a következőhöz:`recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=199-203)]

## <a name="send-the-request-and-check-its-status"></a>Küldje el a kérelmet, és ellenőrizze annak állapotát
Most közzétesszük a kérést a beszédszolgáltatásnak, és ellenőrizzük a kezdeti válaszkódot. Ez a válaszkód egyszerűen jelzi, hogy a szolgáltatás megkapta-e a kérést. A szolgáltatás egy URL-címet ad vissza a válaszfejlécekben, amely az a hely, ahol az átírásállapotát tárolja.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=204-216)]

## <a name="wait-for-the-transcription-to-complete"></a>Várja meg, amíg az átírás befejeződik
Mivel a szolgáltatás aszinkron módon dolgozza fel az átírást, minden alkalommal le kell közvéleményítenünk az állapotát. 5 másodpercenként ellenőrizzük.

Az állapotot úgy ellenőrizhetjük, hogy lehívhatjuk a tartalmat az URL-en, amelyet a kérés kiírásakor kaptunk. Amikor visszakapjuk a tartalmat, deszerializáljuk az egyik segítő osztályunkba, hogy megkönnyítsük a kommunikációt.

Itt van a lekérdezési kódot állapot kijelző mindent, kivéve a sikeres befejezését, akkor ezt a következő.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=222-245,285-299)]

## <a name="display-the-transcription-results"></a>Az átírási eredmények megjelenítése
Miután a szolgáltatás sikeresen befejezte az átírást, az eredmények egy másik URL-ben tárolódnak, amelyet az állapotválaszból kaphatunk.

Letöltjük az URL tartalmát, deszeriáljuk a JSON-t, és menet közben végigmegyünk az eredményeken, kinyomtatjuk a megjelenített szöveget.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=246-284)]

## <a name="check-your-code"></a>A kód ellenőrzése
Ezen a ponton, a kódot kell kinéznie: (Már hozzá néhány megjegyzést, hogy ez a verzió)

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-308)]

## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

Most már készen áll arra, hogy létrehozza az alkalmazást, és tesztelje a beszédfelismerést a Beszédszolgáltatás használatával.

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]
