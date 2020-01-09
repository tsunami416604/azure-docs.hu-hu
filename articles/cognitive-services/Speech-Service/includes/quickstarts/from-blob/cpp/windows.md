---
title: 'Gyors útmutató: a blob Storage C++ -ban tárolt beszéd felismerése – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: b7ce80baa090ed87722397384b8e75c134eb26da
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468635"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [Forrásfájl feltöltése Azure-blobba](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [A fejlesztési környezet beállítása](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Üres minta projekt létrehozása](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>A projekt megnyitása a Visual Studióban

Első lépésként győződjön meg arról, hogy a projekt meg van nyitva a Visual Studióban.

1. Indítsa el a Visual Studio 2019-es kiadását.
2. Töltse be a projektet, és nyissa meg `helloworld.cpp`.

## <a name="add-a-references"></a>Hivatkozások hozzáadása

A kód fejlesztésének felgyorsításához a következő két külső összetevőt fogjuk használni:
* [CPP Rest SDK](https://github.com/microsoft/cpprestsdk) Egy ügyféloldali kódtár, amely REST-hívásokat tesz a REST-szolgáltatásnak.
* [nlohmann/JSON](https://github.com/nlohmann/json) Hasznos JSON-elemzési/szerializálási/deszerializálási függvénytár.

Mindkettő telepíthető a [vcpkg](https://github.com/Microsoft/vcpkg/)használatával.

```
vcpkg install cpprestsdk cpprestsdk:x64-windows
vcpkg install nlohmann-json
```

## <a name="start-with-some-boilerplate-code"></a>Kezdés néhány szabványos kóddal

Vegyünk fel egy olyan kódot, amely csontvázként működik a projekthez.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-32,187-190,300-309)]
(`YourSubscriptionKey`, `YourServiceRegion`és `YourFileUrl` értékeit a saját értékeire kell cserélnie.)

## <a name="json-wrappers"></a>JSON-burkolók

Mivel a REST API JSON formátumban fogadja a kérelmeket, és a JSON eredményeit is visszaküldi, csak karakterláncok használatával kezelhetjük őket, de ez nem ajánlott.
Annak érdekében, hogy a kérések és válaszok könnyebben kezelhetők legyenek, deklaráljuk néhány osztályt a JSON szerializálásához/deszerializálásához, valamint néhány olyan módszerhez, amely segítséget nyújt a nlohmann/JSON használatához.

`recognizeSpeech` előtt helyezze el a bejelentéseit.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=33-185)]

## <a name="create-and-configure-an-http-client"></a>Http-ügyfél létrehozása és konfigurálása
Első lépésként egy olyan http-ügyfélre van szükségünk, amely megfelelő alap URL-címmel és hitelesítési készlettel rendelkezik.
A kód beillesztése `recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=191-197)]

## <a name="generate-a-transcription-request"></a>Átírási kérelem létrehozása
Ezután létrehozjuk az átírási kérelmet. Kód hozzáadása `recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=199-203)]

## <a name="send-the-request-and-check-its-status"></a>Küldje el a kérést, és keresse meg az állapotát
Most közzétesszük a kérést a beszédfelismerési szolgáltatásba, és megvizsgáljuk a kezdeti válasz kódját. A válasz kódja egyszerűen azt jelzi, hogy a szolgáltatás megkapta-e a kérelmet. A szolgáltatás egy URL-címet ad vissza a válasz fejlécekben, amely az a hely, ahol az átírási állapotot tárolni fogja.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=204-216)]

## <a name="wait-for-the-transcription-to-complete"></a>Várja meg, amíg az átírás befejeződik
Mivel a szolgáltatás aszinkron módon dolgozza fel az átírást, minden olyan esetben le kell kérdezni annak állapotát. 5 másodpercenként megvizsgáljuk.

Az állapotot úgy is megtekintheti, hogy beolvassa a tartalmat a kérelem elküldésekor kapott URL-címen. Ha visszavesszük a tartalmat, deszerializáljuk az egyik segítő osztályba, hogy könnyebb legyen az együttműködés.

A sikeres befejezést követően az állapotot megjelenítő lekérdezési kód a következő lesz.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=222-245,285-299)]

## <a name="display-the-transcription-results"></a>Az átírás eredményeinek megjelenítése
Ha a szolgáltatás sikeresen befejezte az átírást, a rendszer egy másik URL-címen tárolja az eredményeket, amelyet az állapot válasza alapján kaphat.

Letöltjük az URL-cím tartalmát, deszerializáljuk a JSON-t, majd az eredményeken keresztül kinyomtatjuk a megjelenített szöveget.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=246-284)]

## <a name="check-your-code"></a>A kód megkeresése
Ezen a ponton a kódnak így kell kinéznie: (adtunk hozzá néhány megjegyzést ehhez a verzióhoz)

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-308)]

## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

Most már készen áll az alkalmazás felépítésére és a beszédfelismerési szolgáltatás használatával történő tesztelésre.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [footer](./footer.md)]
