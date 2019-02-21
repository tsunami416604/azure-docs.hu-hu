---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: 758577f91766d621220d7efdd72e7c2705ccff7f
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56448245"
---
1. Indítsa el az Eclipse-et.

1. Az Eclipse Launcher **Workspace** (Munkaterület) mezőjébe írja be az új munkaterület-könyvtár nevét. Ezután válassza a **Launch** (Indítás) lehetőséget.

   ![Az Eclipse Launcher képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Az Eclipse IDE főablaka hamarosan megjelenik. Ha megnyílt, zárja be az üdvözlőképernyőt.

1. Az Eclipse menüsávján kattintson a **File** > **New** > **Project** (Fájl, Új, Projekt) elemre egy új projekt létrehozásához.

1. Megjelenik a **New project** (Új projekt) párbeszédpanel. Válassza a **Java Project** (Java-projekt) lehetőséget, majd kattintson a **Next** (Tovább) gombra.

   ![A New project párbeszédpanel képernyőképe a kiemelt Java Project lehetőséggel](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-02-select-wizard.png)

1. Elindul a New Java Project (Új Java-projekt) varázsló. A **Project name** (Projekt neve) mezőbe írja be a **quickstart** (rövid útmutató) kifejezést, és válassza ki a **JavaSE-1.8** futtatókörnyezetet. Válassza a **Finish** (Befejezés) elemet.

   ![A New Java Project varázsló képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-03-create-java-project.png)

1. Ha az **Open Associated Perspective?** (Megnyitja a társított perspektívát?) ablak megjelenik, válassza az **Open Perspective** (Perspektíva megnyitása) lehetőséget.

1. A **Package Explorerben** kattintson a jobb gombbal a **v** nevű projektre. A helyi menüben kattintson a **Configure** > **Convert to Maven Project** (Konfigurálás, Konvertálás Maven-projektté) parancsra.

   ![A Package Explorer képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. Megjelenik a **Create new POM** (Új POM létrehozása) ablak. A **Group Id** (Csoportazonosító) mezőbe írja be a **com.microsoft.cognitiveservices.speech.samples** azonosítót, az **Artifact Id** (Összetevő-azonosító) mezőbe pedig a **quickstart** kifejezést. Ezután kattintson a **Befejezés** gombra.

   ![A Create new POM ablak képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Nyissa meg és szerkessze a **pom.xml** fájlt.

   * A fájl végén, a `</project>` záró címke előtt hozzon létre egy `repositories` elemet, amely a Speech SDK Maven-adattárára hivatkozik, az itt bemutatott módon:

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

   * Is hozzáadhat egy `dependencies` elem, a beszéd SDK függőségként 1.3.0 verziója:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

   * Mentse a módosításokat.
