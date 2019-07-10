---
title: 'Gyors útmutató: A beszédfelismerés eszközök SDK futtathatnak Windows - beszédszolgáltatások'
titleSuffix: Azure Cognitive Services
description: Előfeltételek és a egy Windows Speech eszközök SDK – első lépések utasítások.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: erhopf
ms.openlocfilehash: ac0ed218aa27a36b3b8cd8ed8123e2baef6948c6
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723383"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-windows"></a>Gyors útmutató: A Windows Speech Devices SDK-val mintaalkalmazás futtatása

Ebben a rövid útmutatóban elsajátíthatja a beszédfelismerési eszközök SDK for Windows használatával hozhat létre egy beszédfeldolgozó terméket, vagy használhatja, egy [beszélgetés Beszédátírási](conversation-transcription-service.md) eszköz. Jelenleg csak a [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) használata támogatott.

Az alkalmazás össze lett a Speech SDK-csomagot, és az Eclipse a Java IDE (v4) a 64 bites Windows. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

Ehhez az Útmutatóhoz egy [Azure Cognitive Services](get-started.md) beszédszolgáltatások erőforrás rendelkező fiókot. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://azure.microsoft.com/try/cognitive-services/) egy előfizetői azonosító beszerzéséhez.

A forráskódja a [mintaalkalmazás](https://aka.ms/sdsdk-download-JRE) a Speech eszközök SDK részét képezi. Is [elérhető a Githubon](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* Operációs rendszer: 64 bites Windows
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) vagy [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) csak.
* [Microsoft Visual C++ újraterjeszthető csomag](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* A beszédfelismerési szolgáltatás egy Azure-előfizetés kulcs. [Igényeljen ingyenesen egy](get-started.md).
* Töltse le a legújabb verzióját a [Speech Devices SDK-val](https://aka.ms/sdsdk-download-JRE) a Java és a kivonatot a .zip lépjen a munkakönyvtárba.
   > [!NOTE]
   > A JRE-minta-Release.zip fájl tartalmazza a JRE-mintaalkalmazást, és ez a rövid útmutató azt feltételezi, hogy az alkalmazás C:\SDSDK\JRE-Sample-Release kicsomagolta

Beszélgetés Beszédátírási jelenleg csak az "en-US" és "zh-CN", "centralus" és "eastasia" régiókban érhető el. Ezen beszélgetés Beszédátírási használandó régiók egyikében speech kulccsal kell rendelkeznie.

Ha azt tervezi, hogy a leképezések használata szüksége lesz egy [intelligens hangfelismerési szolgáltatás (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) előfizetés. A LUIS és szándékfelismerés kapcsolatos további tudnivalókért lásd: [ismeri fel a LUIS, beszédfelismerés leképezések C# ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). A [LUIS-modellnek minta](https://aka.ms/sdsdk-luis) érhető el ehhez az alkalmazáshoz.

## <a name="create-and-configure-the-project"></a>A projekt létrehozása és konfigurálása

1. Indítsa el az Eclipse-et.

1. Az a **Eclipse IDE indítója**, a a **munkaterület** mezőben adjon meg egy új munkaterületet könyvtár nevét. Ezután válassza a **Launch** (Indítás) lehetőséget.

   ![Az Eclipse Launcher képernyőképe](media/speech-devices-sdk/eclipse-launcher.png)

1. Az Eclipse IDE főablaka hamarosan megjelenik. Ha megnyílt, zárja be az üdvözlőképernyőt.

1. Az Eclipse menüsoron hozzon létre egy új projektet kiválasztásával **fájl** > **új** > **Java-projektek**. Ha nem érhető el válasszon **projekt** , majd **Java-projektek**.

1. A **új Java-projektek** varázsló elindul. **Tallózás** a mintaprojekt helyéhez. Válassza a **Finish** (Befejezés) elemet.

   ![A New Java Project varázsló képernyőképe](media/speech-devices-sdk/eclipse-new-java-project.png)

1. Az a **Package Explorerben**, kattintson a jobb gombbal a projekt. A helyi menüben kattintson a **Configure** > **Convert to Maven Project** (Konfigurálás, Konvertálás Maven-projektté) parancsra. Válassza a **Finish** (Befejezés) elemet.

   ![A Package Explorer képernyőképe](media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Másolás `kws.table`, `participants.properties` és `Microsoft.CognitiveServices.Speech.extension.pma.dll` lépjen a projektmappára **target\classes**

## <a name="configure-the-sample-application"></a>A mintaalkalmazás konfigurálása

1. A beszédfelismerés előfizetési kulcs hozzáadása a forráskódban. Ha azt szeretné, próbálkozhat szándékának felismerése, is hozzáadhat a [hangfelismerési szolgáltatás](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) előfizetési kulcs és az alkalmazás azonosítóját.

   A beszéd- és LUIS adatait hiányzóra `FunctionsList.java`:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Beszélgetés beszédátírási használja, ha a speech kulcs és a régió adatait is szükségesek az `Cts.java`:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. Az alapértelmezett ébresztési szó (kulcsszó) az "Számítógép". Megpróbálhatja egy másik megadott ébresztési szavakat, például "Gép" vagy "Assistant". Az erőforrásfájlokat, az alternatív ébresztési szavak szerepelnek, a beszéd Devices SDK-val, a kulcsszó mappában. Ha például `C:\SDSDK\JRE-Sample-Release\keyword\Computer` ébresztési "Számítógép" szót használt fájlokat tartalmazza.

   > [!TIP]
   > Emellett [hozzon létre egy egyéni ébresztési szó](speech-devices-sdk-create-kws.md).

    Új ébresztési szó használatához frissítse az alábbi két sort a `FunctionsList.java`, és másolja a hálózati ébresztési word csomagot az alkalmazásban. Például használja az ébresztési word csomagból ébresztési szót "Machine" `kws-machine.zip`:

   * Az ébresztési word-csomagot másolja a projektmappába **/osztályok**.

   * Frissítés a `FunctionsList.java` kulcsszó és a csomag neve:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

## <a name="run-the-sample-application-from-eclipse"></a>A mintaalkalmazás futtatása az Eclipse-ből

1. Az Eclipse menüsoron **futtatása** > **futtató** > **Java-alkalmazás**. Válassza ki **FunctionsList** és **OK**.

   ![Válassza ki a Java-alkalmazás képernyőképe](media/speech-devices-sdk/eclipse-run-sample.png)

1. A Speech Devices SDK-val példa alkalmazás elindul, és megjeleníti a következő beállításokat:

   ![Beszéd Devices SDK-val példa mintaalkalmazás és beállítások](media/speech-devices-sdk/java-sample-app-windows.png)

1. Próbálja ki az új **beszélgetés Beszédátírási** bemutató. Indítsa el a átírás **munkamenet** > **Start**. Alapértelmezés szerint a Vendég a mindenki számára. Azonban ha résztvevő hangalapú aláírásokat, elhelyezheti egy fájlba `participants.properties` a projektmappában **/osztályok**. A beszédfelismerési aláírást létrehozni, tekintse meg [beszélgetések (SDK) alapuló átírás](how-to-use-conversation-transcription-service.md).

   ![Beszélgetés Beszédátírási bemutatóalkalmazást](media/speech-devices-sdk/cts-sample-app-windows.png)

## <a name="create-and-run-a-standalone-application"></a>Létrehozása és futtatása a önálló alkalmazás

1. Az a **Package Explorerben**, kattintson a jobb gombbal a projekt. Válasszon **exportálása**. 

1. A **exportálása** ablak jelenik meg. Bontsa ki a **Java** válassza **futtatható JAR-fájl** majd **tovább**.

   ![Az exportálási ablak képernyőképe](media/speech-devices-sdk/eclipse-export-windows.png) 

1. A **futtatható JAR fájlok exportálása** ablak jelenik meg. Válasszon egy **exportálási cél** az alkalmazást, és válassza ki a **Befejezés**.
 
   ![Képernyőkép a futtatható JAR-fájl exportálása](media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. Részletekért `kws.table`, `participants.properties`, `unimic_runtime.dll`, `pma.dll` és `Microsoft.CognitiveServices.Speech.extension.pma.dll` ezeket a fájlokat, amennyire az alkalmazás által a fent kiválasztott a célmappában.

1. Futtatása a önálló alkalmazás

     ```powershell
     java -jar SpeechDemo.jar
     ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tekintse át a kibocsátási megjegyzések](devices-sdk-release-notes.md)
