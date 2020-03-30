---
title: Azure-függvény létrehozása Java és IntelliJ segítségével
description: Ismerje meg, hogyan hozhat létre és tehet közzé egy egyszerű, HTTP-alapú, kiszolgáló nélküli alkalmazást az Azure-ban java-val és IntelliJ-vel.
author: jeffhollan
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 7003dc19a7bfc405809de91534028aba8e0416c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136850"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Az első Azure-függvény létrehozása java és IntelliJ segítségével

Ez a cikk:
- Hogyan hozzunk létre egy [szerver nélküli](https://azure.microsoft.com/overview/serverless-computing/) funkció projekt IntelliJ IDEA és Apache Maven
- A funkció tesztelésének és hibakeresésének lépései az integrált fejlesztői környezetben (IDE) a saját számítógépén
- A függvényprojekt Azure Functions ben történő üzembe helyezésére vonatkozó utasítások

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

A Java és az IntelliJ funkció jának fejlesztéséhez telepítse a következő szoftvert:

- [Java Developer Kit](https://www.azul.com/downloads/zulu/) (JDK), 8-as verzió
- [Apache Maven](https://maven.apache.org), 3.0-s vagy újabb verzió
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), közösségi vagy Ultimate változatok Mavennel
- [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT]
> A JAVA_HOME környezeti változót a JDK telepítési helyére kell állítani a cikkben leírt lépések végrehajtásához.

 Javasoljuk, hogy telepítse az [Azure Functions Core Tools 2-es verzióját.](functions-run-local.md#v2) Helyi fejlesztői környezetet biztosít az Azure Functions írásához, futtatásához és hibakereséséhez.

## <a name="create-a-functions-project"></a>Functions projekt létrehozása

1. Az IntelliJ IDEA **(Új projekt létrehozása)** területen válassza az Új projekt létrehozása lehetőséget.  
1. Az **Új projekt** ablakban válassza a **Maven** elemet a bal oldali ablaktáblából.
1. Jelölje be a **Létrehozás archetípusból** jelölőnégyzetet, majd az **Archetípus hozzáadása** az [azure-functions-archetype mezőben.](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
1. Az **Archetípus hozzáadása** ablakban töltse ki a mezőket az alábbiak szerint:
    - _GroupId_: com.microsoft.azure
    - _ArtifactId_: azure-functions-archetípus
    - _Verzió:_ Ellenőrizze és használja a legújabb verziót [a központi adattár](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Hozzon létre egy Maven projekt archetípus in IntelliJ IDEA](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Válassza **az OK**gombot, majd a **Tovább**gombot.
1. Adja meg az aktuális projekt adatait, és válassza a **Befejezés gombot.**

A Maven az _ArtifactId_ értékkel megegyező nevű új mappában hozza létre a projektfájlokat. A projekt által generált kód egy egyszerű HTTP-aktivált függvény, amely a kiváltó [HTTP-kérelem](/azure/azure-functions/functions-bindings-http-webhook) törzsét visszhangozza.

## <a name="run-functions-locally-in-the-ide"></a>Funkciók futtatása helyileg az IDE-ben

> [!NOTE]
> A helyi funkciók futtatásához és hibakereséséhez győződjön meg arról, hogy telepítette [az Azure Functions Core Tools 2-es verzióját.](functions-run-local.md#v2)

1. A módosításokimportálása manuálisan, illetve [az automatikus importálás](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html)engedélyezése.
1. Nyissa meg a **Maven Projektek eszköztárat.**
1. Bontsa ki **az Életciklus csomópontot,** majd nyissa meg a **csomagot.** A megoldás egy újonnan létrehozott célkönyvtárba van csomagolva.
1. Bővítse ki a Bővítmények**azúrkék függvényeit,** **Plugins** > és nyissa meg az **azure-functions:run az** Azure Functions helyi futásidejű elindításához.  
  ![Maven eszköztár az Azure Functionshez](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

1. Zárja be a futtatási párbeszédpanelt, ha végzett a funkció tesztelésével. Egyszerre csak egy függvényállomás lehet aktív és helyileg fut.

## <a name="debug-the-function-in-intellij"></a>Hibakeresés a funkció IntelliJ

1. A függvényállomás hibakeresési módban történő elindításához a függvény futtatásakor adja hozzá argumentumként a **-DenableDebug** parancsot. Módosíthatja a [maven-célok konfigurációját,](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) vagy futtathatja a következő parancsot egy terminálablakban:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Ezzel a paranccsal a függvényállomás 5005-ös hibakeresési portot nyit meg.

1. A **Futtatás** menüben válassza a **Konfigurációk szerkesztése parancsot.**
1. Válassza a **(+) lehetőséget** **távoli eszköz hozzáadásához.**
1. Töltse ki a _Név_ és _beállítások_ mezőket, majd a konfiguráció mentéséhez kattintson az **OK gombra.**
1. A telepítés után válassza **a Debug < Távoli konfiguráció neve >** lehetőséget, vagy nyomja le a Shift+F9 billentyűkombinációt a billentyűzeten a hibakeresés megkezdéséhez.

1. Ha végzett, állítsa le a hibakeresőt és a futó folyamatot. Egyszerre csak egy függvényállomás lehet aktív és helyileg fut.

## <a name="deploy-the-function-to-azure"></a>A függvény üzembe helyezése az Azure-ban

1. Mielőtt üzembe helyezheti a függvényt az Azure-ban, be kell [jelentkeznie az Azure CLI használatával.](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

   ``` azurecli
   az login
   ```

1. Telepítse a kódot egy új `azure-functions:deploy` függvénybe a Maven cél használatával. Az **azure-functions:deploy** lehetőséget is kiválaszthatja a Maven-projektek ablakban.

   ```
   mvn azure-functions:deploy
   ```

1. Keresse meg a függvény URL-címét az Azure CLI kimenetében a függvény sikeres üzembe helyezése után.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>További lépések

- Ha további információra van szüksége a Java-függvények létrehozásával kapcsolatban, tekintse át a [Java-függvények fejlesztői útmutatóját](functions-reference-java.md).
- A Maven-cél használatával további funkciókat adhat hozzá különböző eseményindítókkal a `azure-functions:add` projekthez.
