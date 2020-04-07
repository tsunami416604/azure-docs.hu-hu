---
title: Azure-függvény létrehozása a Kotlin és az IntelliJ segítségével
description: Ismerje meg, hogyan hozhat létre és tehet közzé egy egyszerű, HTTP-alapú, kiszolgáló nélküli alkalmazást az Azure-ban a Kotlin és az IntelliJ segítségével.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: 2eb1a016e04a4150a76112c68683926810f5c66d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674096"
---
# <a name="quickstart-create-your-first-http-triggered-function-with-kotlin-and-intellij"></a>Rövid útmutató: Az első HTTP-aktivált függvény létrehozása a Kotlin és az IntelliJ segítségével

Ez a cikk bemutatja, hogyan hozhat létre [kiszolgáló nélküli](https://azure.microsoft.com/overview/serverless-computing/) függvényprojektet az IntelliJ IDEA és az Apache Maven segítségével. Azt is bemutatja, hogyan helyileg hibakeresés a függvénykódot az integrált fejlesztői környezetben (IDE), és majd üzembe helyezése a függvényprojekt az Azure-ban.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

A Kotlin és az IntelliJ funkciójának fejlesztéséhez telepítse a következő szoftvert:

- [Java Developer Kit](https://aka.ms/azure-jdks) (JDK), 8-as verzió
- [Apache Maven](https://maven.apache.org), 3.0-s vagy újabb verzió
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), közösségi vagy Ultimate változatok Mavennel
- [Azure CLI](https://docs.microsoft.com/cli/azure)
- Az Azure Functions core eszközeinek [2.x-es verziója.](functions-run-local.md#v2) Helyi fejlesztői környezetet biztosít az Azure Functions írásához, futtatásához és hibakereséséhez.

> [!IMPORTANT]
> A JAVA_HOME környezeti változót a JDK telepítési helyére kell állítani a cikkben leírt lépések végrehajtásához.

## <a name="create-a-functions-project"></a>Functions projekt létrehozása

1. Az IntelliJ IDEA **(Új projekt létrehozása)** területen válassza az Új projekt létrehozása lehetőséget.  
1. Az **Új projekt** ablakban válassza a **Maven** elemet a bal oldali ablaktáblából.
1. Jelölje be a **Létrehozás archetípusból** jelölőnégyzetet, majd az **Archetípus hozzáadása** az [azure-functions-kotlin-archetípushoz](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)lehetőséget.
1. Az **Archetípus hozzáadása** ablakban töltse ki a mezőket az alábbiak szerint:
    - _GroupId_: com.microsoft.azure
    - _ArtifactId_: azúrkék-függvény-kotlin-archetípus
    - _Verzió:_ Használja a legújabb verziót [a központi adattár](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)
    ![Létrehozása Maven projekt archetípus in IntelliJ IDEA](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
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
  ![Maven eszköztár az Azure Functionshez](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

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

   ![Hibakeresési függvények az IntelliJ-ben](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

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

Most, hogy üzembe helyezte az első Kotlin-függvényt az Azure-ban, tekintse át a [Java Functions fejlesztői útmutatót](functions-reference-java.md) a Java és a Kotlin függvények fejlesztésével kapcsolatos további információkért.
- A Maven-cél használatával további funkciókat adhat hozzá különböző eseményindítókkal a `azure-functions:add` projekthez.
