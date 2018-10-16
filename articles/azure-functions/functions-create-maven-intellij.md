---
title: Azure-függvény létrehozása a Java és az intellij-vel |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre és tehet közzé egy egyszerű HTTP-eseményindítóval aktivált, kiszolgáló nélküli alkalmazást az Azure-ban a Java és az intellij-vel.
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: az Azure functions, függvények, eseményfeldolgozás, számítás, kiszolgáló nélküli architektúra, a java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: eb8499ef6c0f872a0761f7be606e058387947b2b
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319880"
---
# <a name="create-your-first-azure-function-with-java-and-intellij-preview"></a>Az első Azure-függvény létrehozása a Java és az intellij-vel (előzetes verzió)

> [!NOTE]
> A Java for Azure Functions jelenleg előzetes verzióban érhető el.

A cikk ismerteti:
- Hogyan hozhat létre egy [kiszolgáló nélküli](https://azure.microsoft.com/overview/serverless-computing/) Functions-projektet IntelliJ IDEA és az Apache Maven
- Lépések teszteléshez és hibakereséshez a függvény a saját számítógépén integrált fejlesztőkörnyezet (IDE)
- Ismerteti, hogyan telepíthető a Functions-projektet az Azure Functions

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

A Java és az intellij-vel függvény fejlesztése, telepítse a következő szoftvereket:

- [Java fejlesztői készlet](https://www.azul.com/downloads/zulu/) (JDK), 8-as verzió
- [Az Apache Maven](https://maven.apache.org), 3.0-s vagy újabb verzió
- [Az IntelliJ IDEA](https://www.jetbrains.com/idea/download), közösségi vagy Ultimate verziók a Mavennel
- [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT]
> A JAVA_HOME környezeti változót a JDK telepítési helyére kell állítani a jelen cikkben ismertetett lépések végrehajtásához.

 Javasoljuk, hogy telepítse [Azure Functions Core Tools, 2. verzió](functions-run-local.md#v2). A írásához, futtatásához és a hibakeresést az Azure Functions helyi fejlesztési környezetet biztosít.

## <a name="create-a-functions-project"></a>A Functions-projekt létrehozása

1. Az IntelliJ IDEA, válassza ki a **új projekt létrehozása**.  
1. Az a **új projekt** ablakban válassza **Maven** a bal oldali ablaktáblán.
1. Válassza ki a **archetype létrehozása** jelölőnégyzetet, majd válassza ki **hozzáadása Archetype** számára a [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
1. Az a **hozzáadása Archetype** ablakban töltse ki a mezőket a következőképpen:
    - _GroupId_: következőt: com.microsoft.azure
    - _ArtifactId_: azure-functions-archetype
    - _Verzió_: használja a legújabb verziót [központi adattára](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![hozzon létre egy Maven-projektet az IntelliJ IDEA archetype](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Válassza ki **OK**, majd válassza ki **tovább**.
1. Aktuální projekt adja meg az adatait, és válassza ki **Befejezés**.

Maven azonos nevű új mappában hozza létre a projektfájlokat a _ArtifactId_ értéket. A projekt létrehozott kód egy egyszerű [HTTP-eseményindítóval aktivált](/azure/azure-functions/functions-bindings-http-webhook) -függvény, amely a riasztást kiváltó HTTP-kérelem törzsét.

## <a name="run-functions-locally-in-the-ide"></a>Függvény helyi futtatása az IDE-ben

> [!NOTE]
> Futtatását, és a függvények helyi hibakeresését, győződjön meg arról, hogy telepítette [Azure Functions Core Tools, 2. verzió](functions-run-local.md#v2).

1. Manuálisan importálja a módosításokat, vagy engedélyezze [automatikus importálás](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Nyissa meg a **Maven-projektekbe** eszköztárán.
1. Bontsa ki a **életciklus**, majd nyissa meg **csomag**. A megoldás felépítésének és a egy újonnan létrehozott céloldali könyvtár.
1. Bontsa ki a **beépülő modulok** > **az azure-functions** , és nyissa meg **azure-függvények: futtassa a** az Azure Functions helyi modul elindításához.  
  ![Az Azure Functions maven eszköztár](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

1. Ha elkészült, zárja be a Futtatás párbeszédpanelen a függvény tesztelése. Csak egy függvényt gazdagép lehet aktív és futó helyi egyszerre.

## <a name="debug-the-function-in-intellij"></a>Hibakeresés az intellij-ben a függvény

1. A függvény gazdagép indítása hibakeresési módban, adjon hozzá **- DenableDebug** argumentumként, a függvény futtatásakor. Vagy módosíthatja a konfigurációt [maven célok](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) vagy egy terminálablakban a következő parancsot:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Ez a parancs hatására a függvényt, 5005 hibakeresési port megnyitásához.

1. Az a **futtatása** menüjében válassza **konfigurációk szerkesztése**.
1. Válassza ki **(+)** hozzáadása egy **távoli**.
1. Végezze el a _neve_ és _beállítások_ mezőket, és válassza ki **OK** a konfiguráció mentéséhez.
1. Válassza ki a telepítés után **hibakeresése < távoli konfiguráció neve >** vagy a Shift + F9 a billentyűzeten a hibakeresés elindításához.

   ![Az intellij-ben függvények hibakeresése](media/functions-create-first-java-intellij/debug-configuration-intellij.PNG)

1. Ha elkészült, állítsa le a hibakeresőt és a futó folyamat. Csak egy függvényt gazdagép lehet aktív és futó helyi egyszerre.

## <a name="deploy-the-function-to-azure"></a>A függvény üzembe helyezése az Azure-ban

1. Mielőtt központilag telepíthetné a függvényt az Azure-ban, be kell [jelentkezzen be az Azure CLI használatával](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Helyezze üzembe a kódot egy új funkció használatával a `azure-functions:deploy` Maven-cél. Választhatja a **az azure-functions: üzembe helyezése** lehetőség a Maven-projektekbe ablakban.

   ```
   mvn azure-functions:deploy
   ```

1. Az URL-cím található a függvényt az Azure CLI-kimenet után a függvény telepítése sikerült.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>További lépések

- Ha további információra van szüksége a Java-függvények létrehozásával kapcsolatban, tekintse át a [Java-függvények fejlesztői útmutatóját](functions-reference-java.md).
- Különböző triggereket további funkciók hozzáadása a projekthez használatával a `azure-functions:add` Maven-cél.
