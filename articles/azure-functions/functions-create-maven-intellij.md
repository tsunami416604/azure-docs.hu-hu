---
title: Azure-függvényalkalmazás létrehozása a Java és az intellij-vel |} A Microsoft Docs
description: Az útmutató létrehozása és közzététele egy egyszerű, HTTP által aktivált Java és az intellij-vel az Azure Functions használatával kiszolgáló nélküli alkalmazás.
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: az Azure functions, függvények, eseményfeldolgozás, számítás, kiszolgáló nélküli architektúra, a java
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 5e265543e2ce5feeed095d89cdb47ede9817bad1
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40002678"
---
# <a name="create-your-first-function-with-java-and-intellij-preview"></a>Az első függvény létrehozása a Java és az intellij-vel (előzetes verzió)

> [!NOTE] 
> A Java for Azure Functions jelenleg előzetes verzióban érhető el.

Ez a cikk bemutatja, hogyan hozhat létre egy [kiszolgáló nélküli](https://azure.microsoft.com/overview/serverless-computing/) Functions-projektet IntelliJ IDEA és az Apache Maven, tesztelése és hibakeresés végrehajtása rajtuk a saját számítógépén az IDE-ből, és üzembe helyezése az Azure Functions. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

A Java és az IntelliJ-functions-alkalmazás fejlesztése, a következőkkel kell rendelkeznie:

-  A [Java Developer Kit](https://www.azul.com/downloads/zulu/) 8-as verziója.
-  Az [Apache Maven](https://maven.apache.org) 3.0-s vagy újabb verziója.
-  [Az IntelliJ IDEA](https://www.jetbrains.com/idea/download), közösségi vagy Ultimate a Mavennel hibakeresését.
-  [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> A rövid útmutató befejezéséhez a JAVA_HOME környezeti változót a JDK telepítési helyére kell beállítani.

Erősen ajánlott is telepíteni [Azure Functions Core Tools, 2. verzió](functions-run-local.md#v2), írásához, futtatásához és a hibakeresést az Azure Functions a biztosító helyi fejlesztési környezetet. 


## <a name="create-a-functions-project"></a>A Functions-projekt létrehozása

1. Az IntelliJ IDEA, kattintson a **új projekt létrehozása**.  
1. Válassza ki a létrehozni **Maven**
1. A jelölőnégyzet bejelölésével **archetype létrehozása** és **hozzáadása Archetype** számára a [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
    - GroupId: következőt: com.microsoft.azure
    - ArtifactId: az azure-functions-archetype
    - Verzió: Használható legújabb verziót [központi adattára](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![IntelliJ Maven létrehozása](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Kattintson a **tovább** , és adja meg a részleteket az aktuális projekthez, és végül **Befejezés**.

A Maven egy _artifactId_ nevű új mappában hozza létre a projektfájlokat. A projekt belül létrehozott kód egy egyszerű [HTTP által aktivált](/azure/azure-functions/functions-bindings-http-webhook) -függvény, amely a riasztást kiváltó HTTP-kérelem törzsét.

## <a name="run-functions-locally-in-the-ide"></a>Függvény helyi futtatása az IDE-ben

> [!NOTE]
> [Az Azure Functions Core Tools, 2. verzió](functions-run-local.md#v2) futtatását és a függvények helyi hibakeresését kell telepíteni.

1. Válassza ki a változások importálásához, vagy ellenőrizze, hogy [automatikus importálás](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html) is engedélyezve van.
1. Nyissa meg a **Maven-projektekbe** eszköztár
1. Életciklusa alatt kattintson duplán a **csomag** csomagolását és állítsa össze a megoldást, és hozzon létre egy cél könyvtárat.
1. Beépülő modulok -> azure-függvények kattintson duplán **azure-függvények: futtassa a** az azure functions helyi modul elindításához.  
  ![Az Azure Functions maven eszköztár](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

Ha elkészült, zárja be a Futtatás párbeszédpanelen a függvény tesztelése. Csak egy függvényt gazdagép lehet aktív és futó helyi egyszerre.

### <a name="debug-the-function-in-intellij"></a>Hibakeresés az intellij-ben a függvény
A függvény gazdagép indítása hibakeresési módban, adjon hozzá **- DenableDebug** argumentumként, a függvény futtatásakor. Nem sikerült futtassa az alábbi parancssori terminálban, vagy adja meg, a [maven célok](https://www.jetbrains.com/help/idea/maven-support.html#run_goal). A függvény gazdagép majd hibakeresési port, az 5005 megnyílik. 

```
mvn azure-functions:run -DenableDebug
```

Hibakeresése az intellij-ben, az a **futtatása** menüből válassza **konfigurációk szerkesztése**. Kattintson a ** + ** hozzáadása egy **távoli**. Töltse ki **neve** és **beállítások**, és kattintson a **OK** a konfiguráció mentéséhez. A telepítés után kattintson a **Debug** (a távoli Configuration Name), vagy nyomja le **Shift + F9** hibakeresésének elkezdéséhez.

![Az intellij-ben függvények hibakeresése](media/functions-create-first-java-intellij/debug-configuration-intellij.PNG)

Amikor végzett a hibakeresőt és a futó folyamat leállítása. Csak egy függvényt gazdagép lehet aktív és futó lévő helyi időben.

## <a name="deploy-the-function-to-azure"></a>A függvény üzembe helyezése az Azure-ban

Az Azure Functions üzembehelyezési folyamata az Azure parancssori felületről származó fiókhitelesítő-adatokat használja. [Jelentkezzen be az Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) a számítógép parancssor használatával a folytatás előtt.

```azurecli
az login
```

Helyezze üzembe a kódot egy új funkció használatával a `azure-functions:deploy` Maven-cél, vagy válassza ki az azure functions: üzembe helyezés lehetőség a Maven-projektekbe ablakban.

```
mvn azure-functions:deploy
```

Az üzembe helyezés végén megjelenik az URL-cím, amellyel bejelentkezhet az Azure-függvényalkalmazásba:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

## <a name="next-steps"></a>További lépések

- Ha további információra van szüksége a Java-függvények létrehozásával kapcsolatban, tekintse át a [Java-függvények fejlesztői útmutatóját](functions-reference-java.md).
- Adjon hozzá különböző triggereket használó további funkciókat a projekthez az `azure-functions:add` Maven-cél használatával.
