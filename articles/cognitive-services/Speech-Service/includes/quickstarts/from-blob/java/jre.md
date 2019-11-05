---
title: 'Rövid útmutató: a blob Storage-ban tárolt beszédfelismerés felismerése, Java-Speech Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: c5fa12621d689287db0d91885fc0a35edb772294
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503940"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [Forrásfájl feltöltése Azure-blobba](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [A fejlesztési környezet beállítása](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Üres minta projekt létrehozása](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-eclipse"></a>A projekt megnyitása az Eclipse-ben

Első lépésként győződjön meg arról, hogy a projekt az Eclipse-ben nyitva van.

1. Az Eclipse indítása
2. Töltse be a projektet, és nyissa meg `Main.java`.

## <a name="add-a-reference-to-gson"></a>Gson mutató hivatkozás hozzáadása
Ebben a rövid útmutatóban egy külső JSON-szerializáló/deszerializáló fogunk használni. A Java általunk választott [Gson](https://github.com/google/gson).

Nyissa meg a Pom. xml fájlt, és adja hozzá a következő hivatkozást: [!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/pom.xml?range=19-25)]

## <a name="start-with-some-boilerplate-code"></a>Kezdés néhány szabványos kóddal

Vegyünk fel egy olyan kódot, amely csontvázként működik a projekthez.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=1-13,95-105,206-207)]
(`YourSubscriptionKey`, `YourServiceRegion`és `YourFileUrl` értékeit a saját értékeire kell cserélnie.)

## <a name="json-wrappers"></a>JSON-burkolók

Mivel a REST API JSON formátumban fogadja a kérelmeket, és a JSON eredményeit is visszaküldi, csak karakterláncok használatával kezelhetjük őket, de ez nem ajánlott.
Annak érdekében, hogy a kérések és válaszok könnyebben kezelhetők legyenek, deklaráljuk néhány osztályt a JSON szerializálásához/deszerializálásához.

`Main`előtt helyezze el a bejelentéseit.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=15-93)]

## <a name="create-and-configure-an-http-client"></a>Http-ügyfél létrehozása és konfigurálása
Első lépésként egy olyan http-ügyfélre van szükségünk, amely megfelelő alap URL-címmel és hitelesítési készlettel rendelkezik.
A kód beillesztése `Main` [!code-javaba [](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=106-113)]

## <a name="generate-a-transcription-request"></a>Átírási kérelem létrehozása
Ezután létrehozjuk az átírási kérelmet. A kód hozzáadása a `Main` [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=115-116)]

## <a name="send-the-request-and-check-its-status"></a>Küldje el a kérést, és keresse meg az állapotát
Most közzétesszük a kérést a beszédfelismerési szolgáltatásba, és megvizsgáljuk a kezdeti válasz kódját. A válasz kódja egyszerűen azt jelzi, hogy a szolgáltatás megkapta-e a kérelmet. A szolgáltatás egy URL-címet ad vissza a válasz fejlécekben, amely az a hely, ahol az átírási állapotot tárolni fogja.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=118-129)]

## <a name="wait-for-the-transcription-to-complete"></a>Várja meg, amíg az átírás befejeződik
Mivel a szolgáltatás aszinkron módon dolgozza fel az átírást, minden olyan esetben le kell kérdezni annak állapotát. 5 másodpercenként megvizsgáljuk.

Az állapotot úgy is megtekintheti, hogy beolvassa a tartalmat a kérelem elküldésekor kapott URL-címen. Ha visszavesszük a tartalmat, deszerializáljuk az egyik segítő osztályba, hogy könnyebb legyen az együttműködés.

A sikeres befejezést követően az állapotot megjelenítő lekérdezési kód a következő lesz.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=131-159,192-204)]

## <a name="display-the-transcription-results"></a>Az átírás eredményeinek megjelenítése
Ha a szolgáltatás sikeresen befejezte az átírást, a rendszer egy másik URL-címen tárolja az eredményeket, amelyet az állapot válasza alapján kaphat.

Letöltjük az URL-cím tartalmát, deszerializáljuk a JSON-t, majd az eredményeken keresztül kinyomtatjuk a megjelenített szöveget.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=6-160-190)]

## <a name="check-your-code"></a>A kód megkeresése
Ezen a ponton a kódnak így kell kinéznie: (adtunk hozzá néhány megjegyzést ehhez a verzióhoz) [! code-Java [] (~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java]

## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

Most már készen áll az alkalmazás felépítésére és a beszédfelismerési szolgáltatás használatával történő tesztelésre.

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]
