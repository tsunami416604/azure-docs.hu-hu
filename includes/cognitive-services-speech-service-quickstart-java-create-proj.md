---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: a542e02c51bf2ce9b3b13bc50a39584ce079ec72
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330828"
---
1. Indítsa el az Eclipse-et.

1. Az Eclipse Launcher **Workspace** (Munkaterület) mezőjébe írja be az új munkaterület-könyvtár nevét. Ezután válassza a **Launch** (Indítás) lehetőséget.

   ![Az Eclipse Launcher képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Az Eclipse IDE főablaka hamarosan megjelenik. Ha van ilyen, zárjuk be az **üdvözlőképernyőn** .

1. Az Eclipse menüsávján kattintson a **File** > **New** > **Project** (Fájl, Új, Projekt) elemre egy új projekt létrehozásához.

1. Megjelenik a **New project** (Új projekt) párbeszédpanel. Válassza a **Java Project** (Java-projekt) lehetőséget, majd kattintson a **Next** (Tovább) gombra.

   ![A New project párbeszédpanel képernyőképe a kiemelt Java Project lehetőséggel](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-02-select-wizard.png)

1. Elindul az **új Java-projekt** varázsló. A **Project name** (Projekt neve) mezőbe írja be a **quickstart** (rövid útmutató) kifejezést, és válassza ki a **JavaSE-1.8** futtatókörnyezetet. Válassza a **Finish** (Befejezés) elemet.

   ![A New Java Project varázsló képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-03-create-java-project.png)

1. Ha az **Open Associated Perspective?** (Megnyitja a társított perspektívát?) ablak megjelenik, válassza az **Open Perspective** (Perspektíva megnyitása) lehetőséget.

1. A **Package Explorerben** kattintson a jobb gombbal a **v** nevű projektre. A helyi menüben kattintson a **Configure** > **Convert to Maven Project** (Konfigurálás, Konvertálás Maven-projektté) parancsra.

   ![A Package Explorer képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. Megjelenik a **Create new POM** (Új POM létrehozása) ablak. A **csoport azonosítója** mezőben adja meg a *com. microsoft. cognitiveservices. Speech. Samples*értéket, és az összetevő- **azonosító** mezőben adja meg a gyors *útmutatót.* Ezután kattintson a **Befejezés** gombra.

   ![A Create new POM ablak képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Nyissa meg és szerkessze a *pom.xml* fájlt.

   * A fájl végén, a `</project>` záró címke előtt hozzon létre egy `repositories` elemet, amely a Speech SDK Maven-adattárára hivatkozik, az itt bemutatott módon:

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/pom.xml#repositories)]

   * Vegyen fel egy `dependencies` elemet is, amely a Speech SDK verziójának 1.10.0 függ:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/pom.xml#dependencies)]

   * Mentse a módosításokat.
