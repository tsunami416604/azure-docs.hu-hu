---
title: 'Gyors útmutató: Beszédfelismerés felismerése, Java (Windows, Linux) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy egyszerű Java-alkalmazást, amely a számítógép mikrofonjának felhasználói beszédét rögzíti és átírja.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 498e41b08133113be9789ef49291b8e2bb0f3705
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68554109"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-java"></a>Gyors útmutató: Beszéd felismerése a Javához készült Speech SDK-val

A gyors üzembe helyezési útmutató [](quickstart-translate-speech-java-jre.md) a beszédfelismeréshez, a fordításhoz és a [hang-első virtuális asszisztenshez](quickstart-virtual-assistant-java-jre.md)is elérhető.

Ha szükséges, válasszon másik programozási nyelvet és/vagy környezetet:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebben a cikkben létre fog hozni egy Java-konzolalkalmazást a [Speech SDK](speech-sdk.md) használatával. A számítógép mikrofonjába beszélve valós időben konvertálhat át beszédet szöveggé. Az alkalmazás a Speech SDK Maven-csomaggal, valamint az Eclipse Java IDE (v 4.8) a 64 bites Windows, 64 bites Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9), illetve macOS 10,13 vagy újabb rendszeren készült. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

> [!NOTE]
> A Speech Devices SDK-ról és a Roobo eszközről lásd: [Speech Devices SDK](speech-devices-sdk.md).

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

Ha Windows rendszert futtat (64 bites), győződjön meg arról, hogy telepítette a C++ platformhoz a Microsoft vizualizációs terjeszthető csomagját.
* [A Visual Studio C++ 2019-hoz készült Microsoft vizualizációs terjeszthető csomag letöltése](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="create-and-configure-project"></a>Projekt létrehozása és konfigurálása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Ha új, üres osztályt szeretne hozzáadni a Java-projekthez, válassza a **File** > **New** > **Class** (Fájl, Új, Osztály) lehetőséget.

1. A **New Java Class** (Új Java-osztály) ablakban írja be a **speechsdk.quickstart** kifejezést a **Package** (Csomag), a **Main** (Fő) kifejezést pedig a **Név** mezőbe.

   ![A New Java Class varázsló képernyőképe](media/sdk/qs-java-jre-06-create-main-java.png)

1. Cserélje le a `Main.java` összes kódját az alábbi kódrészletre:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. Mentse a projekt módosításait.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

Nyomja le az F11 billentyűt, vagy válassza a **Run** > **Debug** (Futtatás, Hibakeresés) lehetőséget.
A mikrofonból érkező következő 15 másodpercnyi beszédet a rendszer felismeri, és a konzolablakban naplózza.

![Képernyőkép a konzolról a sikeres felismerést követően](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>További lépések

A GitHubon további minták is elérhetők, például a hangfájlok beszédének olvasása.

> [!div class="nextstepaction"]
> [A Java-minták megismerése a GitHubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Rövid útmutató: Translate speech, Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
