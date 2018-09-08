---
title: Azure-függvényalkalmazás létrehozása a Java és az eclipse-ben |} A Microsoft Docs
description: Az útmutató létrehozása és közzététele egy egyszerű, HTTP által aktivált Java és az eclipse-ben az Azure Functions használatával kiszolgáló nélküli alkalmazás.
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
ms.openlocfilehash: 7d29cd3801bf997bf5c28ed0845996725aaf1840
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44093651"
---
# <a name="create-your-first-function-with-java-and-eclipse-preview"></a>Az első függvény létrehozása a Java és az eclipse-ben (előzetes verzió)

> [!NOTE] 
> A Java for Azure Functions jelenleg előzetes verzióban érhető el.

Ez a cikk bemutatja, hogyan hozhat létre egy [kiszolgáló nélküli](https://azure.microsoft.com/overview/serverless-computing/) Functions-projektet az Eclipse IDE és az Apache Maven, tesztelheti és hibakeresés végrehajtása rajtuk, majd üzembe helyezése az Azure Functions. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

Függvényalkalmazást szeretne létrehozni egy Java-és az Eclipse, a következőkkel kell rendelkeznie:

-  A [Java Developer Kit](https://www.azul.com/downloads/zulu/) 8-as verziója.
-  Az [Apache Maven](https://maven.apache.org) 3.0-s vagy újabb verziója.
-  [Az eclipse](https://www.eclipse.org/downloads/packages/), a Java és Maven támogatja.
-  [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> A rövid útmutató befejezéséhez a JAVA_HOME környezeti változót a JDK telepítési helyére kell beállítani.

Erősen javasoljuk, hogy még a telepítés [Azure Functions Core Tools, 2. verzió](functions-run-local.md#v2), futtatására és a hibakeresést az Azure Functions biztosító helyi környezetben. 

## <a name="create-a-functions-project"></a>A Functions-projekt létrehozása

1. Az eclipse-ben a **fájl** menüben, majd válassza ki **projekt**. 
1. Nyissa meg a **Java-projektek** mappájában a **új projekt** ablakban, majd válassza **Maven-projektet**, majd **tovább**.
1. Elfogadhatja az alapértelmezett beállításokat, az a **új Maven-projektet** párbeszéd, és válassza ki **tovább**.
1. Válassza ki **hozzáadása Archetype** , és adja hozzá a bejegyzést a [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
    - Archetype Csoportazonosító: következőt: com.microsoft.azure
    - Archetype összetevő-azonosító: az azure-functions-archetype
    - Verzió: Használható legújabb verziót [központi adattára](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Eclipse Maven létrehozása](media/functions-create-first-java-eclipse/functions-create-eclipse.png)  
1. Kattintson a **OK** , és adja meg a részleteket az aktuális projekthez, és végül **Befejezés**.

A Maven egy _artifactId_ nevű új mappában hozza létre a projektfájlokat. A projekt belül létrehozott kód egy egyszerű [HTTP által aktivált](/azure/azure-functions/functions-bindings-http-webhook) -függvény, amely a riasztást kiváltó HTTP-kérelem törzsét.

## <a name="run-functions-locally-in-the-ide"></a>Függvény helyi futtatása az IDE-ben

> [!NOTE]
> [Az Azure Functions Core Tools, 2. verzió](functions-run-local.md#v2) futtatását és a függvények helyi hibakeresését kell telepíteni.

1. Kattintson a jobb gombbal a létrehozott projektre, majd kattintson a **futtató** és **Maven build**.
1. Az a **-konfiguráció szerkesztése** párbeszédpanelen adjon meg `package` a a **célok** és **neve** mezőket, majd válassza ki **futtatása**. Ezzel létrehozása és csomagolása a függvénykódot.
1. A létrehozás befejezése után hozzon létre egy másik futtató konfiguráció, a fenti használatával `azure-functions:run` a cél és a neve. Válassza ki **futtatása** a funkció futtatásához az IDE-ben.

A modul a konzolablakban az leáll, ha elkészült a függvény tesztelése. Csak egy függvényt gazdagép lehet aktív és futó helyi egyszerre.

### <a name="debug-the-function-in-eclipse"></a>Hibakeresés az eclipse-ben a függvény

Az a **futtató** állítsa be az előző lépésben, a módosítás konfigurációs `azure-functions:run` való `mvn azure-functions:run -DenableDebug` , és futtassa a frissített konfigurációt, a függvényalkalmazás elindításához hibakeresési módban.

Válassza ki a **futtatása** menüt, és nyissa meg **konfigurációk hibakeresése**. Válasszon **távoli Java-alkalmazás** , és hozzon létre egy újat. Adja meg a konfiguráció nevét, és adja meg a beállításokat. A port a hibakeresési port függvény gazdagépre, amely alapértelmezés szerint az által megnyitott összhangban kell lennie `5005`. A telepítés után kattintson a `Debug` hibakeresésének elkezdéséhez.

![Az eclipse-ben függvények hibakeresése](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

Állítson be töréspontokat, és vizsgálja meg az objektumok a függvényben az IDE használatával. Amikor végzett, állítsa le a hibakeresőt és a futó függvény gazdagép. Csak egy függvényt gazdagép lehet aktív és futó lévő helyi időben.

## <a name="deploy-the-function-to-azure"></a>A függvény üzembe helyezése az Azure-ban

Az Azure Functions üzembehelyezési folyamata az Azure parancssori felületről származó fiókhitelesítő-adatokat használja. [Jelentkezzen be az Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) a számítógép parancssor használatával a folytatás előtt.

```azurecli
az login
```

Helyezze üzembe a kódot egy új funkció használatával a `azure-functions:deploy` az új Maven-cél **futtató** konfigurációja.

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
