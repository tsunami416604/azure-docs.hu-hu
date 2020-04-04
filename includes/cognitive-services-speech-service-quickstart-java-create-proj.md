---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: e0bc7eeff276d44f3cd23e30eb54c9bd1ca94efd
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658302"
---
1. Indítsa el az Eclipse-et.

1. Az Eclipse Launcher **Workspace** (Munkaterület) mezőjébe írja be az új munkaterület-könyvtár nevét. Ezután válassza a **Launch** (Indítás) lehetőséget.

   ![Az Eclipse Launcher képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Az Eclipse IDE főablaka hamarosan megjelenik. Zárja be az **üdvözlőképernyőt,** ha van ilyen.

1. Az Eclipse menüsorban hozzon létre egy új projektet az**Új** > **projekt** **fájlja** > gombra választva.

1. Megjelenik a **New project** (Új projekt) párbeszédpanel. Válassza a **Java Project** (Java-projekt) lehetőséget, majd kattintson a **Next** (Tovább) gombra.

   ![A New project párbeszédpanel képernyőképe a kiemelt Java Project lehetőséggel](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-02-select-wizard.png)

1. Elindul **az Új Java Projekt** varázsló. A **Project name** (Projekt neve) mezőbe írja be a **quickstart** (rövid útmutató) kifejezést, és válassza ki a **JavaSE-1.8** futtatókörnyezetet. Válassza a **Finish** (Befejezés) elemet.

   ![A New Java Project varázsló képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-03-create-java-project.png)

1. Ha az **Open Associated Perspective?** (Megnyitja a társított perspektívát?) ablak megjelenik, válassza az **Open Perspective** (Perspektíva megnyitása) lehetőséget.

1. A **Package Explorerben** kattintson a jobb gombbal a **v** nevű projektre. Válassza a helyi menü Konvertálás **konfigurálása** > **Maven-projektté** parancsát.

   ![A Package Explorer képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. Megjelenik a **Create new POM** (Új POM létrehozása) ablak. A **Csoportazonosító** mezőbe írja be a *com.microsoft.cognitiveservices.speech.samples értéket,* a **Műtermék azonosítója** mezőbe pedig írja be a *rövid útmutatót.* Ezután kattintson a **Befejezés** gombra.

   ![A Create new POM ablak képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Nyissa meg és szerkessze a *pom.xml* fájlt.

   * A fájl végén, a `</project>` záró címke előtt hozzon létre egy `repositories` elemet, amely a Speech SDK Maven-adattárára hivatkozik, az itt bemutatott módon:

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/pom.xml#repositories)]

   * Adjon hozzá `dependencies` egy elemet is, amelynek a Speech SDK 1.11.0-s verziója függőségként szerepel:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/pom.xml#dependencies)]

   * Mentse a módosításokat.
