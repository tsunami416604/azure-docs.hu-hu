---
title: 'Gyors útmutató: Recognize speech, Java (Windows, Linux) - Speech Services'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban megismerheti, egyszerű Java-alkalmazás létrehozása, amely rögzíti, és felhasználói beszéd, a számítógép mikrofon transcribes.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: dce0a1b2adf20b2301402f37307e7ee1284c9aee
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605154"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-java"></a>Gyors útmutató: Beszédfelismerés, beszédfelismerési SDK for Java felismerése

Rövid útmutatók érhetők el is [speech, beszédalapú fordítási](quickstart-translate-speech-java-jre.md) és [hang-és felhőközpontú virtuális asszisztensek](quickstart-virtual-assistant-java-jre.md).

Szükség esetén válasszon egy másik programozási nyelvet és/vagy a környezetben:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebben a cikkben létre fog hozni egy Java-konzolalkalmazást a [Speech SDK](speech-sdk.md) használatával. A számítógép mikrofonjába beszélve valós időben konvertálhat át beszédet szöveggé. Az alkalmazást a létrehozása a Speech SDK Maven-csomag és az Eclipse a Java IDE (v4.8) a Windows 64 bites, 64 bites Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9), vagy a macOS 10.13 vagy újabb rendszeren. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

> [!NOTE]
> A Speech Devices SDK-ról és a Roobo eszközről lásd: [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* Operációs rendszer: 64 bites Windows, 64 bites Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9), vagy a macOS 10.13 vagy újabb
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) vagy [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A beszédfelismerési szolgáltatás egy Azure-előfizetés kulcs. [Igényeljen ingyenesen egy](get-started.md).

Ha Linux rendszert használ, győződjön meg arról, ezek a függősége telepítve van az Eclipse indítása előtt.

* On Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* A Debian 9-on:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

Ha Windows (64 bites) futtat, győződjön meg arról, telepítette a Microsoft Visual C++ a platformjához tartozó terjeszthető változatát.
* [Töltse le a Microsoft Visual C++ Redistributable a Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

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

További példákat, például a beszéd beolvasása egy hangfájlt a Githubon érhetők el.

> [!div class="nextstepaction"]
> [Ismerkedés a Java-példák a Githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Rövid útmutató: Translate speech, Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
