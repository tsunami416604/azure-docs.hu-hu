---
title: 'Gyors útmutató: Recognize speech Java-környezetben (Windows vagy Linux)'
titleSuffix: Microsoft Cognitive Services
description: Ismerje meg, hogyan beszédfelismerést Java-környezetben (Windows vagy Linux)
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 08/16/2018
ms.author: fmegen
ms.openlocfilehash: 923ab3378d5e2d833e11c5111d4dd9964fea6dc4
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126613"
---
# <a name="quickstart-recognize-speech-in-java-windows-or-linux"></a>Gyors útmutató: Recognize speech Java-környezetben (Windows vagy Linux)

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ez a dokumentum ismerteti, hogyan hozhat létre egy Java-alapú konzolalkalmazást az a Java futtatási környezet (JRE), amelyek a beszéd SDK-t használja.
Az alkalmazás a Microsoft Cognitive Services SDK Maven csomag alapján történik.
Az integrált fejlesztési környezeteket (IDE), használjuk, eclipse-ben.

## <a name="prerequisites"></a>Előfeltételek

* A beszédfelismerési szolgáltatás egy előfizetési kulcsot. Lásd: [próbálja ki ingyenesen a speech service](get-started.md).
* Számítógép (x64 Windows, Ubuntu 16.04 x64) képesek futtatni az eclipse-ben működő mikrofonnal.
* 64 bites JRE/JDK 8 Javához készült.
* 4.8 verziója [Eclipse](https://www.eclipse.org), 64 bites verzióját.
* Ubuntu 16.04 futtassa a következő parancsokat a szükséges csomagok telepítéséhez:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

## <a name="create-and-configure-your-project"></a>A projekt létrehozása és konfigurálása

1. Indítsa el az eclipse-ben.

1. Az Eclipse indítása, adja meg az új könyvtár neve a **munkaterület** mező.
   Kattintson a **indítsa el a**.

   ![Eclipse-munkaterületének létrehozása](media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Később az Eclipse IDE fő ablakában megjelenik-e.
   Ha azt egy üdvözlő képernyőt, zárja be.

1. Válassza ki **fájl** \> **új** \> **projekt**.

1. Az a **új projekt** válassza a megjelenő varázsló **Java-projektek**, és kattintson a **tovább**.

   ![Válassza ki a varázsló](media/sdk/qs-java-jre-02-select-wizard.png)

1. Adja meg a következő ablak **rövid** a projekt nevére, és válassza **JavaSE-1.8-as** (vagy akár) végrehajtási környezetet.
   Kattintson a **Befejezés** gombra.

   ![Válassza ki a varázsló](media/sdk/qs-java-jre-03-create-java-project.png)

1. Ha egy ablakban nevű **társított perspektíva megnyitása?** POP felfelé, válassza ki **nyílt perspektíva**.

1. Az a **Package Explorerben**, kattintson a jobb gombbal a **rövid** projektre, és válassza ki **konfigurálása** \> **konvertálás Maven-projektet**.

   ![Konvertálás Maven-projektet](media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. A felugró ablakban adja meg **com.microsoft.cognitiveservices.speech.samples** , **csoportazonosító** és **rövid** , **összetevő azonosítója**. Válassza a **Finish** (Befejezés) elemet.

   ![Konfigurálja a Mavent POM](media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Szerkessze a **pom.xml** fájlt.

  * A záró címke, a fájl végén `</project>`, hozzon létre egy tárházak szakaszt a Maven tárházból való hivatkozással a Speech SDK-hoz készült:

    [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

  * Emellett adja hozzá ezt követően egy függőségek szakaszt verzió 0.6.0 beszéd SDK-val függőségként:

    [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

  * Mentse a módosításokat.

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Válassza ki **fájl** \> **új** \> **osztály** egy új üres osztály hozzáadása a Java-projektek.

1. Az ablakban **új Java-osztály** adja meg **speechsdk.quickstart** be a **csomag** mező, és **fő** be a **neve**  mező.

   ![Main osztály létrehozása](media/sdk/qs-java-jre-06-create-main-java.png)

1. Cserélje le az összes kódot `Main.java` a következő kódrészletre:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Cserélje le a karakterláncot `YourSubscriptionKey` az előfizetési kulccsal végzett.

1. Cserélje le a karakterláncot `YourServiceRegion` együtt a [régió](regions.md) az előfizetéséhez tartozó (például `westus` az ingyenes próba-előfizetésre).

1. Mentse a módosításokat a projekthez.

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

Nyomja le az F11, vagy válasszon **futtatása** \> **Debug**.
A következő 15 másodperc, a mikrofon a bemeneti fog elfogadtatni és a konzolablakban a naplóba.

![A sikeres felismerés után a konzol kimenete](media/sdk/qs-java-jre-07-console-output.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Keresse meg az ehhez a mintához a `quickstart/java-jre` mappát.

## <a name="next-steps"></a>További lépések

* [A minták letöltése](speech-sdk.md#get-the-samples)
