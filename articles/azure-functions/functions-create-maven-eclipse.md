---
title: Azure-függvényalkalmazás létrehozása Java és Eclipse segítségével
description: Útmutató egy egyszerű HTTP-vel indított kiszolgáló nélküli alkalmazás létrehozásához és közzétételéhez Java és Eclipse használatával az Azure Functions hez.
author: jeffhollan
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: c82432a3d76a84eba1ad921d936b2f3ba064e2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136833"
---
# <a name="create-your-first-function-with-java-and-eclipse"></a>Az első funkció létrehozása Java és Eclipse segítségével 

Ez a cikk bemutatja, hogyan hozhat létre egy [kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) függvényprojektet az Eclipse IDE és az Apache Maven segítségével, tesztelje és debugolhatja, majd telepítse az Azure Functions-be. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

A Java és az Eclipse funkcióalkalmazás fejlesztéséhez a következőkkel kell rendelkeznie:

-  [Java Fejlesztői készlet,](https://www.azul.com/downloads/zulu/)8-as verzió.
-  [Apache Maven](https://maven.apache.org), 3.0-s vagy újabb verzió.
-  [Eclipse](https://www.eclipse.org/downloads/packages/), Java és Maven támogatással.
-  [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> A rövid útmutató befejezéséhez a JAVA_HOME környezeti változót a JDK telepítési helyére kell beállítani.

Erősen ajánlott az [Azure Functions Core Tools 2-es verziójú](functions-run-local.md#v2)telepítése is, amely helyi környezetet biztosít az Azure Functions futtatásához és hibakereséséhez. 

## <a name="create-a-functions-project"></a>Functions projekt létrehozása

1. Az Eclipse **(Fájl)** menüben válassza az **Új&gt; – Maven projekt parancsot.** 
1. Fogadja el az alapértelmezett értékeket az **Új Maven projekt** párbeszédablakban, és válassza a **Tovább**lehetőséget.
1. Válassza **az Archetípus hozzáadása lehetőséget,** és adja hozzá az [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)bejegyzéseit.
    - Archetípus-csoport azonosítója: com.microsoft.azure
    - Archetípus-műtermék azonosítója: azure-functions-archetípus
    - Verzió: Ellenőrizze és használja a legújabb verziót [a központi adattár](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Eclipse Maven létre](media/functions-create-first-java-eclipse/functions-create-eclipse.png)  
1. Kattintson **az OK,** majd a **Tovább**gombra.  Ügyeljen arra, hogy töltse ki az `resourceGroup` `appName`értékeket `appRegion` az összes mező, beleértve , és (kérjük, használjon egy másik appName eltérő **fabrikam-function-20170920101928**), és végül **befejezés**.
    ![Eclipse Maven létrehozása2](media/functions-create-first-java-eclipse/functions-create-eclipse2.png)  

A Maven egy _artifactId_ nevű új mappában hozza létre a projektfájlokat. A létrehozott kód a projektben egy egyszerű [HTTP-aktivált](/azure/azure-functions/functions-bindings-http-webhook) függvény, amely a kiváltó HTTP-kérelem törzsét visszhangozza.

## <a name="run-functions-locally-in-the-ide"></a>Funkciók futtatása helyileg az IDE-ben

> [!NOTE]
> [Az Azure Functions Core Tools 2-es verzióját](functions-run-local.md#v2) telepíteni kell a helyi futtatáshoz és hibakeresési funkciók futtatásához.

1. Kattintson a jobb gombbal a létrehozott projektre, majd válassza a **Futtatás másként** és a **Maven build parancsot.**
1. A **Konfiguráció szerkesztése** `package` párbeszédpanelen írja be a **Célok** és **név** mezőket, majd válassza a Futtatás **lehetőséget.** Ez létrehozza és csomagolja a függvénykódot.
1. Miután a build befejeződött, hozzon `azure-functions:run` létre egy másik Futtatási konfigurációt a fentiek szerint, célként és névként használva. Válassza a **Futtatás** lehetőséget az IDE függvény futtatásához.

Ha befejezte a funkció tesztelését, szakítsa meg a futási időt a konzolablakban. Egyszerre csak egy függvényállomás lehet aktív és helyileg fut.

### <a name="debug-the-function-in-eclipse"></a>Hibakeresés a funkció eclipse

Az előző lépésben beállított **Futtatás konfigurációban** módosítsa `azure-functions:run` a frissített konfigurációt, `azure-functions:run -DenableDebug` és futtassa a függvényalkalmazást hibakeresési módban.

Válassza a **Futtatás** menüt, és nyissa meg a **Hibakeresési konfigurációk parancsot.** Válassza **a Távoli Java-alkalmazás lehetőséget,** és hozzon létre egy újat. Adjon nevet a konfigurációnak, és töltse ki a beállításokat. A portnak összhangban kell lennie a függvényállomás által megnyitott hibakeresési porttal, amely alapértelmezés szerint `5005`a . A telepítés után `Debug` kattintson a hibakeresés megkezdéséhez.

![Hibakeresési függvények az Eclipse programban](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

Állítsa be a töréspontokat, és vizsgálja meg a függvény objektumait az IDE segítségével. Ha végzett, állítsa le a hibakeresőt és a futó függvényállomást. Egyszerre csak egy függvényállomás lehet aktív és helyileg fut.

## <a name="deploy-the-function-to-azure"></a>A függvény üzembe helyezése az Azure-ban

Az Azure Functions üzembehelyezési folyamata az Azure parancssori felületről származó fiókhitelesítő-adatokat használja. [Jelentkezzen be az Azure CLI-vel,](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) mielőtt folytatná a számítógép parancssori használatát.

```azurecli
az login
```

Telepítse a kódot egy új `azure-functions:deploy` függvényalkalmazásba a Maven cél használatával egy új **Futtatás másként** konfigurációban.

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
