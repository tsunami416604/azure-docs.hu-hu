---
title: Azure-függvény létrehozása Javával és IntelliJ
description: Megtudhatja, hogyan hozhat létre és tehet közzé egy egyszerű, HTTP-alapú, kiszolgáló nélküli alkalmazást az Azure-ban Java és IntelliJ használatával.
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 05074696ca2cc9d425269561523beb11eb18c4f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756446"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Az első Azure-függvény létrehozása Javával és IntelliJ

Ez a cikk:
- [Kiszolgáló](https://azure.microsoft.com/overview/serverless-computing/) nélküli Function Project létrehozása a IntelliJ IDEA és az Apache Maven használatával
- Az integrált fejlesztési környezet (IDE) függvényének tesztelésére és hibakeresésére szolgáló lépések a saját számítógépén
- Útmutatás a függvény projekt Azure Functions való üzembe helyezéséhez

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

A Java-és IntelliJ-függvények fejlesztéséhez telepítse a következő szoftvereket:

- [Java Developer Kit](https://www.azul.com/downloads/zulu/) (JDK), 8-as verzió
- [Apache Maven](https://maven.apache.org), 3,0-es vagy újabb verzió
- [INTELLIJ Idea](https://www.jetbrains.com/idea/download), közösségi vagy Ultimate verziók a Maven használatával
- [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT]
> A jelen cikkben ismertetett lépések végrehajtásához a JAVA_HOME környezeti változót a JDK telepítési helyére kell beállítani.

 Javasoljuk, hogy telepítse a [2. verziót Azure functions Core Tools](functions-run-local.md#v2). Helyi fejlesztési környezetet biztosít a Azure Functions írásához, futtatásához és hibakereséséhez.

## <a name="create-a-functions-project"></a>Functions-projekt létrehozása

1. A IntelliJ ÖTLETben válassza az **új projekt létrehozása**lehetőséget.  
1. Az **új projekt** ablakban válassza a **Maven** lehetőséget a bal oldali ablaktáblán.
1. Jelölje be a **Létrehozás az archetípus alapján** jelölőnégyzetet, majd válassza az **archetípus hozzáadása** az [Azure-functions-archetípushoz](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)lehetőséget.
1. Az **archetípus hozzáadása** ablakban végezze el a következő mezőket:
    - _GroupID_: com. microsoft. Azure
    - _ArtifactId_: Azure-functions-archetípus
    - _Verzió_: a [központi tárház](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![legújabb verziójának megkeresése és használata a IntelliJ IDEA-ban az archetípusból származó Maven-projekt létrehozása](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Válassza **az OK**, majd a **tovább**lehetőséget.
1. Adja meg az aktuális projekt adatait, majd kattintson a **Befejezés gombra**.

A Maven egy új mappában hozza létre a projektfájlt, amelynek a neve megegyezik a _ArtifactId_ értékével. A projekt generált kódja egy egyszerű [http-triggert](/azure/azure-functions/functions-bindings-http-webhook) használó függvény, amely a kiváltó HTTP-kérelem törzsét visszhangzik.

## <a name="run-functions-locally-in-the-ide"></a>Függvények helyi futtatása az IDE-ben

> [!NOTE]
> A függvények helyi futtatásához és hibakereséséhez győződjön meg róla, hogy telepítette a [2. verziót Azure functions Core Tools](functions-run-local.md#v2).

1. Manuálisan importálhatja a módosításokat, vagy engedélyezheti az [automatikus importálást](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Nyissa meg a **Maven-projektek** eszköztárat.
1. Bontsa ki az **életciklus**csomópontot, majd nyissa meg a **csomagot**. A megoldás egy újonnan létrehozott célkönyvtár keretében lett létrehozva és csomagolva.
1. Bontsa ki a **plugins** > **Azure-functions** és az **Azure-functions megnyitása: Futtatás** parancsot a Azure functions helyi futtatókörnyezet elindításához.  
  ![Maven-eszköztár a Azure Functionshoz](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

1. A függvény tesztelésének befejezése után zárjuk be a Futtatás párbeszédpanelt. Egyszerre csak egy Function Host lehet aktív és helyileg futni.

## <a name="debug-the-function-in-intellij"></a>A függvény hibakeresése a IntelliJ-ben

1. A Function Host hibakeresési módban való elindításához adja meg a **-DenableDebug** paramétert a függvény futtatásakor. Módosíthatja a [Maven-célok](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) konfigurációját, vagy futtathatja a következő parancsot egy terminál-ablakban:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   A parancs hatására a Function Host egy hibakeresési portot nyit meg a 5005-es időpontban.

1. A **Futtatás** menüben válassza a **konfigurációk szerkesztése**lehetőséget.
1. **Távoli**hozzáadásához válassza a **(+)** lehetőséget.
1. Fejezze be a _név_ és a _Beállítások_ mezőt, majd kattintson az **OK gombra** a konfiguráció mentéséhez.
1. A telepítés után válassza a **hibakeresés < a távoli konfiguráció neve >** vagy a billentyűzeten a SHIFT + F9 billentyűkombinációval indítsa el a hibakeresést.

1. Ha elkészült, állítsa le a hibakeresőt és a futó folyamatot. Egyszerre csak egy Function Host lehet aktív és helyileg futni.

## <a name="deploy-the-function-to-azure"></a>A függvény üzembe helyezése az Azure-ban

1. Mielőtt üzembe helyezi a függvényt az Azure-ban, be kell [jelentkeznie az Azure CLI használatával](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Telepítse a kódot egy új függvénybe a `azure-functions:deploy` Maven-cél használatával. A Maven-projektek ablakban az **Azure-functions: Deploy (üzembe helyezés** ) lehetőséget is kiválaszthatja.

   ```
   mvn azure-functions:deploy
   ```

1. Keresse meg a függvény URL-címét az Azure CLI kimenetében a függvény sikeres telepítése után.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>További lépések

- Ha további információra van szüksége a Java-függvények létrehozásával kapcsolatban, tekintse át a [Java-függvények fejlesztői útmutatóját](functions-reference-java.md).
- Vegyen fel további függvényeket különböző eseményindítókkal a projekthez `azure-functions:add` a Maven cél használatával.
