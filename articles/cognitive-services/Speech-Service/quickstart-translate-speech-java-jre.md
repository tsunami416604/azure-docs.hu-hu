---
title: 'Gyors útmutató: Beszéd fordítása, Java (Windows, Linux) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban egy egyszerű Java-alkalmazást fog létrehozni a felhasználói beszéd rögzítéséhez, lefordítani egy másik nyelvre, és kiírja a szöveget a parancssorba. Ez az útmutató Windows-és Linux-felhasználók számára készült.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 402bf198981641db74a86bbc420fab83124f0b47
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802021"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-java"></a>Gyors útmutató: Beszédfelismerés, beszédfelismerési SDK for Java fordítása

A gyors üzembe helyezési lehetőség a [beszéd-szöveg](quickstart-java-jre.md), a [szöveg-beszéd](quickstart-text-to-speech-java-jre.md)és a [hang-első virtuális asszisztens](quickstart-virtual-assistant-java-jre.md)számára is elérhető.

Ebben a rövid útmutatóban egy egyszerű Java-alkalmazást fog létrehozni, amely a felhasználó beszédét rögzíti a számítógép mikrofonjában, lefordítja a beszédet, és valós időben írja át a lefordított szöveget a parancssorba. Az alkalmazás úgy lett kialakítva, hogy 64 bites Windows vagy 64 bites Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9) vagy macOS 10,13 vagy újabb rendszeren fusson. A szolgáltatás a Speech SDK Maven-csomaggal és az Eclipse Java IDE készült.

A beszédfelismeréshez elérhető nyelvek teljes listáját a [nyelvi támogatás](language-support.md)című témakörben tekintheti meg.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* Operációs rendszer: 64 bites Windows, 64 bites Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9) vagy macOS 10,13 vagy újabb verzió
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) vagy [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Egy Azure-előfizetési kulcs a beszédfelismerési szolgáltatáshoz. [Szerezze be az egyiket ingyenesen](get-started.md).

Ha Linux rendszert futtat, győződjön meg arról, hogy ezek a függőségek telepítve vannak az Eclipse elindítása előtt.

 * On Ubuntu:

   ```sh
   sudo apt-get update
   sudo apt-get install libssl1.0.0 libasound2
   ```

 * Debian 9 rendszeren:

   ```sh
   sudo apt-get update
   sudo apt-get install libssl1.0.2 libasound2
   ```

> [!NOTE]
> A Speech Devices SDK-ról és a Roobo eszközről lásd: [Speech Devices SDK](speech-devices-sdk.md).

## <a name="create-and-configure-project"></a>Projekt létrehozása és konfigurálása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Ha új, üres osztályt szeretne hozzáadni a Java-projekthez, válassza a **File** > **New** > **Class** (Fájl, Új, Osztály) lehetőséget.

1. A **New Java Class** (Új Java-osztály) ablakban írja be a **speechsdk.quickstart** kifejezést a **Package** (Csomag), a **Main** (Fő) kifejezést pedig a **Név** mezőbe.

   ![A New Java Class varázsló képernyőképe](media/sdk/qs-java-jre-06-create-main-java.png)

1. Cserélje le a `Main.java` összes kódját az alábbi kódrészletre:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. Mentse a projekt módosításait.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

Nyomja le az F11 billentyűt, vagy válassza a **Run** > **Debug** (Futtatás, Hibakeresés) lehetőséget.

A mikrofonból bevitt beszéd beírása a konzol ablakában történik. A beszéd rögzítésének leállításához nyomja le az "Enter" billentyűt.

![Képernyőkép a konzolról a sikeres felismerést követően](media/sdk/qs-translate-java-jre-output.png)

## <a name="next-steps"></a>További lépések

További minták, mint például a hangfájlok beszédének beolvasása és a lefordított szöveg szintetizált beszédként való kiírása, a GitHubon érhetők el.

> [!div class="nextstepaction"]
> [A Java-minták megismerése a GitHubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Rövid útmutató: Beszédfelismerés felismerése, Java (Windows, Linux) ](quickstart-java-jre.md)
- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
