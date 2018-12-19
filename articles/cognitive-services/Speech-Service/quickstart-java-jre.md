---
title: 'Gyors útmutató: Beszéd, a Java - beszédszolgáltatások felismerésére'
titleSuffix: Azure Cognitive Services
description: Arra vonatkozó információk, hogyan ismerhet fel beszédet a Javán (Windows vagy Linux rendszeren)
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: fmegen
ms.openlocfilehash: 9b8327a529baf230bc64dd9abcc808924b31293b
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53603440"
---
# <a name="quickstart-recognize-speech-in-java-on-windows-or-linux-by-using-the-speech-service-sdk"></a>Gyors útmutató: A beszédfelismerési szolgáltatás SDK-val beszédfelismerést Windows vagy Linux rendszeren Java nyelven

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebben a cikkben létre fog hozni egy Java-konzolalkalmazást a [Speech Service SDK](speech-sdk.md) használatával. A számítógép mikrofonjába beszélve valós időben konvertálhat át beszédet szöveggé. Az alkalmazást a Speech SDK Maven-csomag és az Eclipse a Java IDE (v4.8) 64 bites Windows-vagy Ubuntu Linux 16.04 létrehozása / 18.04. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

> [!NOTE]
> A Speech Devices SDK-ról és a Roobo eszközről lásd: [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez szüksége van a Speech Service előfizetői azonosítójára, amelyet ingyenesen is beszerezhet. További részletekért tekintse át a [Speech Service ingyenes kipróbálását](get-started.md) ismertető részt.


## <a name="create-and-configure-project"></a>Projekt létrehozása és konfigurálása

Használatakor Ubuntu 16.04 vagy 18.04, az Eclipse indítása előtt, futtassa a következő parancsok futtatásával győződjön meg arról, hogy a szükséges csomagok telepítve vannak-e.

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

1. Indítsa el az Eclipse-et.

1. Az Eclipse Launcher **Workspace** (Munkaterület) mezőjébe írja be az új munkaterület-könyvtár nevét. Ezután válassza a **Launch** (Indítás) lehetőséget.

   ![Az Eclipse Launcher képernyőképe](media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Az Eclipse IDE főablaka hamarosan megjelenik. Ha megnyílt, zárja be az üdvözlőképernyőt.

1. Az Eclipse menüsávján kattintson a **File** > **New** > **Project** (Fájl, Új, Projekt) elemre egy új projekt létrehozásához.

1. Megjelenik a **New project** (Új projekt) párbeszédpanel. Válassza a **Java Project** (Java-projekt) lehetőséget, majd kattintson a **Next** (Tovább) gombra.

   ![A New project párbeszédpanel képernyőképe a kiemelt Java Project lehetőséggel](media/sdk/qs-java-jre-02-select-wizard.png)

1. Elindul a New Java Project (Új Java-projekt) varázsló. A **Project name** (Projekt neve) mezőbe írja be a **quickstart** (rövid útmutató) kifejezést, és válassza ki a **JavaSE-1.8** futtatókörnyezetet. Válassza a **Finish** (Befejezés) elemet.

   ![A New Java Project varázsló képernyőképe](media/sdk/qs-java-jre-03-create-java-project.png)

1. Ha az **Open Associated Perspective?** (Megnyitja a társított perspektívát?) ablak megjelenik, válassza az **Open Perspective** (Perspektíva megnyitása) lehetőséget.

1. A **Package Explorerben** kattintson a jobb gombbal a **v** nevű projektre. A helyi menüben kattintson a **Configure** > **Convert to Maven Project** (Konfigurálás, Konvertálás Maven-projektté) parancsra.

   ![A Package Explorer képernyőképe](media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. Megjelenik a **Create new POM** (Új POM létrehozása) ablak. A **Group Id** (Csoportazonosító) mezőbe írja be a **com.microsoft.cognitiveservices.speech.samples** azonosítót, az **Artifact Id** (Összetevő-azonosító) mezőbe pedig a **quickstart** kifejezést. Ezután kattintson a **Befejezés** gombra.

   ![A Create new POM ablak képernyőképe](media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Nyissa meg és szerkessze a **pom.xml** fájlt.

   * A fájl végén, a `</project>` záró címke előtt hozzon létre egy `repositories` elemet, amely a Speech SDK Maven-adattárára hivatkozik, az itt bemutatott módon:

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

  * Is hozzáadhat egy `dependencies` elem, a beszéd SDK függőségként a 1.2.0-s vagy annál újabb verzió:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

   * Mentse a módosításokat.

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

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
A jelen útmutatóban használt mintát a `quickstart/java-jre` mappában találja.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szándék felismerése beszédből a Javához készült Speech SDK-val](how-to-recognize-intents-from-speech-java.md)

## <a name="see-also"></a>Lásd még

- [Beszéd fordítása](how-to-translate-speech-csharp.md)
- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
