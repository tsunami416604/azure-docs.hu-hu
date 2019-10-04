---
title: 'Gyors útmutató: Beszédfelismerési beszéd, Java (Windows, Linux, macOS) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy egyszerű Java-alkalmazást, amely rögzíti és szintetizálja a szöveget, és játssza le az alapértelmezett hangszóróval.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: 832525ae1441fca85f8df661b4a187c0be8d91dc
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803984"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-java"></a>Gyors útmutató: Beszédfelismerés a Javához készült Speech SDK-val

A gyors üzembe helyezést a [beszédfelismerés](quickstart-java-jre.md), [a](quickstart-translate-speech-java-jre.md)beszédfelismerés és a beszéd – fordítás, valamint a [hangvezérelt virtuális asszisztens](quickstart-virtual-assistant-java-jre.md)is elérhetővé teszi.

Ebben a cikkben létre fog hozni egy Java-konzolalkalmazást a [Speech SDK](speech-sdk.md) használatával. A beszédfelismerést a szövegből szintetizálhatja, és a számítógép alapértelmezett hangszóróján játssza le. Az alkalmazás a Speech SDK Maven-csomaggal, valamint az Eclipse Java IDE (v 4.8) a 64 bites Windows, 64 bites Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9), illetve macOS 10,13 vagy újabb rendszeren készült. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

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

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. Mentse a projekt módosításait.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

Nyomja le az F11 billentyűt, vagy válassza a **Run** > **Debug** (Futtatás, Hibakeresés) lehetőséget.
Adjon meg egy szöveget az előléptetéskor, és itt lesz az alapértelmezett beszélőtől kapott szintetizált hang.

## <a name="next-steps"></a>További lépések

A GitHubon további minták is elérhetők, például a hangfájlok beszédének olvasása.

> [!div class="nextstepaction"]
> [A Java-minták megismerése a GitHubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Rövid útmutató: Beszédfelismerés felismerése, Java (Windows, Linux, macOS) ](quickstart-java-jre.md)
- [Rövid útmutató: Beszéd fordítása, Java (Windows, Linux, macOS) ](quickstart-translate-speech-java-jre.md)
- [Hangbetűkészletek testreszabása](how-to-customize-voice-font.md)
- [Hangminták rögzítése](record-custom-voice-samples.md)
