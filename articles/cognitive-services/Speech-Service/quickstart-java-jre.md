---
title: 'Gyors útmutató: Recognize speech, Java (Windows, Linux) - Speech Services'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató bemutatja egy egyszerű Java-alkalmazás, amely rögzíti, és felhasználói beszéd, a számítógép mikrofon transcribes létrehozása.
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: fmegen
ms.openlocfilehash: 9185d8d236b2a3283cf5a542002adadd0ac74686
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820538"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-java"></a>Gyors útmutató: Beszédfelismerés, beszédfelismerési SDK for Java felismerése

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebben a cikkben létre fog hozni egy Java-konzolalkalmazást a [Speech Service SDK](speech-sdk.md) használatával. A számítógép mikrofonjába beszélve valós időben konvertálhat át beszédet szöveggé. Az alkalmazást a Speech SDK Maven-csomag és az Eclipse a Java IDE (v4.8) a Windows 64 bites vagy 64 bites Ubuntu Linux 16.04 létrehozása / 18.04. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

> [!NOTE]
> A Speech Devices SDK-ról és a Roobo eszközről lásd: [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* Operációs rendszer: Windows (64 bites) vagy Ubuntu Linux 16.04/18.04 (64 bites)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) vagy [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A beszédfelismerési szolgáltatás egy Azure-előfizetés kulcs. [Igényeljen ingyenesen egy](get-started.md).

Ha Ubuntu 16.04/18.04 rendszert használ, győződjön meg arról, ezek a függősége telepítve van az Eclipse indítása előtt.

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
```

Ha Windows (64 bites) ellenőrizze, hogy telepítve van a Microsoft Visual C++ újraterjeszthető csomag a platform.
* [A Microsoft Visual C++ terjeszthető csomag Visual Studio 2017 letöltése](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)


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
