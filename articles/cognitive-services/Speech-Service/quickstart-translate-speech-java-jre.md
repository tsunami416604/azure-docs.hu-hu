---
title: 'Gyors útmutató: Translate speech, Java (Windows, Linux) - Speech Services'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutatóban egy egyszerű Java-alkalmazás felhasználói speech rögzítése, egy másik nyelvre lefordíthatja és a parancssorhoz szöveget akarjuk fog létrehozni. Ez az útmutató a Windows vagy Linux-felhasználók lett tervezve.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 5dc1852a57970c2994d9f36cbd7242a18b580a61
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020996"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-java"></a>Gyors útmutató: Beszédfelismerés, beszédfelismerési SDK for Java fordítása

Ebben a rövid útmutatóban fog létrehozni egy egyszerű Java-alkalmazás, amely rögzíti a felhasználó beszéd, a számítógép mikrofon, a rendszer lefordítja a beszédfelismerési és transcribes a lefordított szöveg valós időben a parancssorba. Ezt az alkalmazást a Windows 64 bites vagy 64-bit-es Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9), vagy a macOS 10.13 vagy újabb rendszerhez készült. A beszédfelismerés SDK Maven-csomag és az Eclipse a Java IDE készült.

Beszédalapú fordítási elérhető nyelvek teljes listáját lásd: [nyelvi támogatás](language-support.md).

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

A beszéd, a bemeneti adatok a mikrofon fog megjelenített érzéseket, német, és a konzolablakban a naplóba. Nyomja le az "Enter" rögzítés speech.

![Képernyőkép a konzolról a sikeres felismerést követően](media/sdk/qs-translate-java-jre-output.png)

## <a name="next-steps"></a>További lépések

További példákat, beszéd olvasni hangfájl, és a lefordított szöveg szintetizált, mint például a Githubon érhetők el.

> [!div class="nextstepaction"]
> [Ismerkedés a Java-példák a Githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Rövid útmutató: Recognize speech, Java (Windows, Linux)](quickstart-java-jre.md)
- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
