---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: df29ecf0a4ddb30aea1c1ab9ceed44a93fc61eb8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400122"
---
Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre beszédfelismerést támogató terméket a Beszédfelismerési eszközök SDK-val beszédfelismerésre képes termékként, illetve hogyan [használhatja azt beszélgetés-átírási](../conversation-transcription-service.md) eszközként. A beszélgetés átírása csak az [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) támogatott. Más beszédek esetén használjon lineáris mikrofontömböket, amelyek mikrofontömb-geometriát biztosítanak.

Az alkalmazás a Speech SDK csomaggal és az Eclipse Java IDE (v4) 64 bites Windows rendszerrel készült. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

Ez az útmutató egy [Azure Cognitive Services-fiókot](../get-started.md) igényel egy beszédszolgáltatás-erőforrással. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://azure.microsoft.com/try/cognitive-services/) egy előfizetői azonosító beszerzéséhez.

A [mintaalkalmazás](https://aka.ms/sdsdk-download-JRE) forráskódja megtalálható a beszédeszközök SDK-ban. A [GitHubon](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)is elérhető.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* Operációs rendszer: 64 bites Windows
* Mikrofontömb, például [az Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Csak Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) vagy [JDK 8.](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Microsoft Visual C++ újraterjeszthető csomag](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* Egy Azure-előfizetési kulcs a beszédfelismerési szolgáltatáshoz. [Kap egy részére szabad.](../get-started.md)
* Töltse le a [Beszédeszközök Java-hoz szolgáltatáshoz](https://aka.ms/sdsdk-download-JRE) szolgáltatásának legújabb verzióját, és bontsa ki a .zip fájlból a munkakönyvtárába.
   > [!NOTE]
   > Ez a rövid útmutató feltételezi, hogy az alkalmazás kibontása a C:\SDSDK\JRE-Release

Beszélgetés átírás jelenleg csak az "en-US" és a "zh-CN", a "centralus" és a "eastasia" régiókban. A beszélgetési átírás használatához az egyik ilyen régióban rendelkeznie kell egy beszédbillentyűvel.

Ha azt tervezi, hogy használja a szándékok szüksége lesz egy [nyelv-megértési szolgáltatás (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) előfizetés. A LUIS-ról és a szándékfelismerésről a [Beszédleképezések felismerése a LUIS, C# ( Beszédleképezések felismerése a LUIS, C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)) témakört. Ehhez az alkalmazáshoz [egy minta LUIS-modell](https://aka.ms/sdsdk-luis) érhető el.

## <a name="create-and-configure-the-project"></a>A projekt létrehozása és konfigurálása

1. Indítsa el az Eclipse-et.

1. Az **Eclipse IDE launcher**mezőben a **Munkaterület** mezőben adja meg egy új munkaterületi könyvtár nevét. Ezután válassza a **Launch** (Indítás) lehetőséget.

   ![Az Eclipse Launcher képernyőképe](../media/speech-devices-sdk/eclipse-launcher.png)

1. Az Eclipse IDE főablaka hamarosan megjelenik. Ha megnyílt, zárja be az üdvözlőképernyőt.

1. Az Eclipse menüsorban hozzon létre egy új projektet az**Új** > **Java projekt** **fájlja** > parancsra. Ha nem érhető el, válassza a **Project,** majd a **Java Project lehetőséget.**

1. Elindul **az Új Java Projekt** varázsló. **Keresse meg** a mintaprojekt helyét. Válassza a **Finish** (Befejezés) elemet.

   ![A New Java Project varázsló képernyőképe](../media/speech-devices-sdk/eclipse-new-java-project.png)

1. A **Csomagkezelőben**kattintson a jobb gombbal a projektre. Válassza a helyi menü Konvertálás **konfigurálása** > **Maven-projektté** parancsát. Válassza a **Finish** (Befejezés) elemet.

   ![A Package Explorer képernyőképe](../media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Nyissa meg és szerkessze a pom.xml fájlt.

    A fájl végén, a záró `</project>`címke `repositories` előtt `dependencies` hozzon létre és elemeket, ahogy itt látható, és győződjön meg arról, hogy a `version` jelenlegi verzió megfelel:
    ```xml
    <repositories>
         <repository>
             <id>maven-cognitiveservices-speech</id>
             <name>Microsoft Cognitive Services Speech Maven Repository</name>
             <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
         </repository>
    </repositories>
 
    <dependencies>
        <dependency>
             <groupId>com.microsoft.cognitiveservices.speech</groupId>
             <artifactId>client-sdk</artifactId>
             <version>1.11.0</version>
        </dependency>
    </dependencies>
   ```

1. Másolja a **Windows-x64** tartalmát a Java Project helyére, pl. **C:\SDSDK\JRE-Sample-Release**

1. Másolás `kws.table` `participants.properties` a `Microsoft.CognitiveServices.Speech.extension.pma.dll` projektmappába **cél\osztályok**

## <a name="configure-the-sample-application"></a>A mintaalkalmazás konfigurálása

1. Adja hozzá a beszéd-előfizetési kulcsot a forráskódhoz. Ha meg szeretné próbálni a szándékfelismerést, adja hozzá a [Language Understanding szolgáltatás előfizetési](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) kulcsát és az alkalmazásazonosítót is.

   A beszéd és a LUIS `FunctionsList.java`esetében az adatok a következőkbe kerülnek:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Ha beszélgetési átírást használ, a beszédkulcsra és `Cts.java`a régióra vonatkozó információkra is szükség van:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. Az alapértelmezett kulcsszó (kulcsszó) a "Számítógép". Kipróbálhatja a többi megadott kulcsszót is, például a "Gép" vagy az "Asszisztens". Az alternatív kulcsszavak erőforrásfájljai a Beszédeszközök SDK mappában, a kulcsszómappában találhatók. Például `C:\SDSDK\JRE-Sample-Release\keyword\Computer` a "Számítógép" kulcsszóhoz használt fájlokat tartalmazza.

    > [!TIP]
    > [Egyéni kulcsszót](../speech-devices-sdk-create-kws.md)is létrehozhat.

    Új kulcsszó használatához frissítse a következő `FunctionsList.java`sort a alkalmazásban, és másolja a kulcsszót az alkalmazásba. Például, hogy használja a kulcsszó "Gép" a kulcsszó csomag: `machine.zip`

   * Másolja `kws.table` a fájlt a zip csomagból a projektmappába **célba/osztályokba.**
   * Frissítse `FunctionsList.java` a kulcsszó nevével:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>A mintaalkalmazás futtatása az Eclipse-ből

1. Az Eclipse menüsorFuttatás **Run** > **Java-ként alkalmazásként** > **parancsa**látható. Ezután válassza **a FunctionsList** és **az OK lehetőséget.**

   ![Képernyőkép a Java-alkalmazás kiválasztásáról](../media/speech-devices-sdk/eclipse-run-sample.png)

1. A Beszédeszközök SDK példaalkalmazás elindul, és a következő beállításokat jeleníti meg:

   ![Minta beszédfelismerési eszközök SDK példa alkalmazás és beállítások](../media/speech-devices-sdk/java-sample-app-windows.png)

1. Próbálja ki az új **beszélgetésátírás** demót. Kezdje el az átírást a Munkamenet**indítása** **segítségével.** >  Alapértelmezés szerint mindenki vendég. Ha azonban a résztvevők hangaláírásai vannak, azok a `participants.properties` projektmappa **célmappájában/osztályaiban**lévő fájlba helyezhetők. A hangaláírás létrehozásához tekintse meg az [Átírási beszélgetéseket (SDK).](../how-to-use-conversation-transcription-service.md)

   ![Demo beszélgetés átírása alkalmazás](../media/speech-devices-sdk/cts-sample-app-windows.png)

## <a name="create-and-run-a-standalone-application"></a>Önálló alkalmazás létrehozása és futtatása

1. A **Csomagkezelőben**kattintson a jobb gombbal a projektre. Válassza **az Exportálás**lehetőséget.

1. Megjelenik **az Exportálás** ablak. Bontsa ki a **Java** csomópontot, és válassza **a Futtatható JAR-fájl** lehetőséget, majd válassza a **Tovább**gombot.

   ![Képernyőkép az Exportálás ablakról](../media/speech-devices-sdk/eclipse-export-windows.png)

1. Megjelenik **a futtatható JAR-fájl exportálása** ablak. Válassza ki az **alkalmazás exportálási célját,** majd válassza a **Befejezés gombot.**

   ![A futtatható JAR-fájl exportálásának képernyőképe](../media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. Kérjük, `participants.properties` `unimic_runtime.dll`tegye `pma.dll` `kws.table` `Microsoft.CognitiveServices.Speech.extension.pma.dll` , , és a cél mappában a fent kiválasztott, mivel ezek a fájlok szükségesek az alkalmazás.

1. Az önálló alkalmazás futtatása

   ```powershell
   java -jar SpeechDemo.jar
   ```
