---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: trbye
ms.openlocfilehash: 804cc2213379364002a093509f2393e375c3971b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400964"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene, győződjön meg arról, hogy:

> [!div class="checklist"]
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programmming-language-java)
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [Forrásfájl feltöltése Azure-blobba](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)


## <a name="open-your-project-in-eclipse"></a>A projekt megnyitása az Eclipse programban

Az első lépés annak biztosítása, hogy a projekt meg legyen nyitva az Eclipse programban.

1. Az Eclipse indítása
2. Töltse be a `Main.java`projektet, és nyissa meg a programot.

## <a name="add-a-reference-to-gson"></a>Hivatkozás hozzáadása a Gsonra
Ebben a rövid útmutatóban egy külső JSON-szerializálót / deszerializálót fogunk használni. Java-hoz a [Gson-t](https://github.com/google/gson)választottuk.

Nyissa meg a pom.xml fájlt, és adja hozzá a következő hivatkozást.

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/pom.xml?range=19-25)]

## <a name="start-with-some-boilerplate-code"></a>Kezdje néhány sablonkóddal.

Adjunk hozzá néhány kódot, ami csontvázként működik a projektünkhöz.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=1-13,95-105,206-207)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>JSON csomagolóanyagok

Mivel a REST API json formátumban fogad el kéréseket, és a JSON-ban is visszaadja az eredményeket, csak karakterláncok használatával kommunikálhatunk velük, de ez nem ajánlott.
Annak érdekében, hogy a kérelmek és válaszok könnyebben kezelhető, akkor állapítsa meg néhány osztályt használni szerializálása / deszerializálása a JSON.

Gyerünk, és tegye `Main`a nyilatkozatok előtt .
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=15-93)]

## <a name="create-and-configure-an-http-client"></a>Http-ügyfél létrehozása és konfigurálása
Az első dolog, amire szükségünk lesz egy Http Client, amely a megfelelő alap URL-t és hitelesítési készlet.
A kód `Main` beszúrása a következőbe:[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=106-113)]

## <a name="generate-a-transcription-request"></a>Átírási kérelem létrehozása
Ezután létrehozunk egy átírási kérelmet. A kód `Main` hozzáadása a következőhöz:[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=115-116)]

## <a name="send-the-request-and-check-its-status"></a>Küldje el a kérelmet, és ellenőrizze annak állapotát
Most közzétesszük a kérést a beszédszolgáltatásnak, és ellenőrizzük a kezdeti válaszkódot. Ez a válaszkód egyszerűen jelzi, hogy a szolgáltatás megkapta-e a kérést. A szolgáltatás egy URL-címet ad vissza a válaszfejlécekben, amely az a hely, ahol az átírásállapotát tárolja.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=118-129)]

## <a name="wait-for-the-transcription-to-complete"></a>Várja meg, amíg az átírás befejeződik
Mivel a szolgáltatás aszinkron módon dolgozza fel az átírást, minden alkalommal le kell közvéleményítenünk az állapotát. 5 másodpercenként ellenőrizzük.

Az állapotot úgy ellenőrizhetjük, hogy lehívhatjuk a tartalmat az URL-en, amelyet a kérés kiírásakor kaptunk. Amikor visszakapjuk a tartalmat, deszerializáljuk az egyik segítő osztályunkba, hogy megkönnyítsük a kommunikációt.

Itt van a lekérdezési kódot állapot kijelző mindent, kivéve a sikeres befejezését, akkor ezt a következő.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=131-159,192-204)]

## <a name="display-the-transcription-results"></a>Az átírási eredmények megjelenítése
Miután a szolgáltatás sikeresen befejezte az átírást, az eredmények egy másik URL-ben tárolódnak, amelyet az állapotválaszból kaphatunk.

Letöltjük az URL tartalmát, deszeriáljuk a JSON-t, és menet közben végigmegyünk az eredményeken, kinyomtatjuk a megjelenített szöveget.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=6-160-190)]

## <a name="check-your-code"></a>A kód ellenőrzése
Ezen a ponton, a kódot kell kinéznie: (Már hozzá néhány megjegyzést, hogy ez a verzió) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java]

## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

Most már készen áll arra, hogy létrehozza az alkalmazást, és tesztelje a beszédfelismerést a Beszédszolgáltatás használatával.

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]
