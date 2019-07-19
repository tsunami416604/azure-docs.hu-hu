---
title: Az első függvény létrehozása az Azure-ban a Java és a Maven használatával | Microsoft Docs
description: Hozzon létre és tegyen közzé egy egyszerű, HTTP-triggert használó függvényt az Azure-ban a Java és a Maven használatával.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, függvények, eseményfeldolgozás, számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.devlang: java
ms.topic: quickstart
ms.date: 08/10/2018
ms.author: routlaw
ms.reviewer: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 20327e64080182518fd28b1d367ffe37be5ce9a4
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323971"
---
# <a name="create-your-first-function-with-java-and-maven"></a>Az első függvény létrehozása a Java és a Maven használatával

Ez a cikk végigvezeti a Maven parancssori eszköz használatával a Java-függvények Azure Functions való létrehozásához és közzétételéhez. Ha elkészült, a függvény kódja az Azure-beli [felhasználási terven](functions-scale.md#consumption-plan) fut, és HTTP-kérelem használatával indítható el.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A Java-t használó függvények fejlesztéséhez a következőkre van szükség:

- [Java Developer Kit](https://aka.ms/azure-jdks), 8-as verzió
- [Apache Maven](https://maven.apache.org), 3,0-es vagy újabb verzió
- [Azure CLI](https://docs.microsoft.com/cli/azure)
- [Azure functions Core Tools](./functions-run-local.md#v2) 2.6.666 vagy újabb verzió

> [!IMPORTANT]
> A rövid útmutató befejezéséhez a JAVA_HOME környezeti változót a JDK telepítési helyére kell beállítani.

## <a name="generate-a-new-functions-project"></a>Új Functions-projekt létrehozása

Egy üres mappában futtassa a következő parancsot a Functions-projekt [Maven archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)-ból való létrehozásához.

### <a name="linuxmacos"></a>Linux/macOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

> [!NOTE]
> Ha a parancs futtatásával kapcsolatos problémákat tapasztal, tekintse meg, hogy milyen `maven-archetype-plugin` verziót használ. Mivel a parancsot egy üres, fájl nélküli `.pom` könyvtárban futtatja, előfordulhat, hogy a régebbi `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` verzió beépülő modulját próbálja használni, ha a mavent egy régebbi verzióról frissítette. Ha igen, próbálja meg törölni `maven-archetype-plugin` a könyvtárat, és futtassa újra a parancsot.

### <a name="windows"></a>Windows

```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-archetype"
```

```cmd
mvn archetype:generate ^
    "-DarchetypeGroupId=com.microsoft.azure" ^
    "-DarchetypeArtifactId=azure-functions-archetype"
```

A Maven kérni fogja a projekt létrehozásához szükséges értékeket. A _groupId_, _artifactId_ és _version_ értékek a [Maven elnevezési konvenciókra](https://maven.apache.org/guides/mini/guide-naming-conventions.html) vonatkozó referenciákban találhatók. Az _appName_ értékének egyedinek kell lennie az Azure-ban, ezért a Maven alapértelmezés szerint az előzőleg megadott _artifactId_ érték alapján létrehoz egy alkalmazásnevet. A _packageName_ érték meghatározza a Java-csomagot a létrehozott függvénykódhoz.

Az alábbi `com.fabrikam.functions` és `fabrikam-functions` azonosítók példaként szolgálnak, és könnyebben átláthatóvá teszik a rövid útmutató későbbi lépéseit. Javasoljuk, hogy adja meg a saját értékekeit a Mavennek ebben a lépésben.

```Output
Define value for property 'groupId' (should match expression '[A-Za-z0-9_\-\.]+'): com.fabrikam.functions
Define value for property 'artifactId' (should match expression '[A-Za-z0-9_\-\.]+'): fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Define value for property 'appRegion' westus: :
Define value for property 'resourceGroup' java-functions-group: :
Confirm properties configuration: Y
```

A Maven az _artifactId_ nevét viselő új mappában hozza létre a projektfájlokat, amely ebben a példában `fabrikam-functions`. A készen áll a generált kód futtatására a projektben egy [http által aktivált](/azure/azure-functions/functions-bindings-http-webhook) függvény, amely megismétli a kérelem törzsét. Cserélje le a *src/Main/Java/com/Fabrikam/functions/function. Java* kódot a következő kódra: 

```java
package com.fabrikam.functions;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class Function {
    /**
     * This function listens at endpoint "/api/hello". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/hello
     * 2. curl {your host}/api/hello?name=HTTP%20Query
     */
    @FunctionName("hello")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");

        // Parse query parameter
        String query = request.getQueryParameters().get("name");
        String name = request.getBody().orElse(query);

        if (name == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
        } else {
            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        }
    }
}

```

## <a name="enable-extension-bundles"></a>Bővítmény-csomagok engedélyezése

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

Módosítsa a könyvtárt az újonnan létrehozott projektmappára, majd hozza létre és futtassa a függvényt a Maven használatával:

```CMD
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]
> Ha Java 9 használata esetén a `javax.xml.bind.JAXBException` kivételt észleli, tekintse meg a megkerülő megoldást a [GitHubon](https://github.com/jOOQ/jOOQ/issues/6477).

Ha a függvény helyben, az Ön rendszerén fut, és készen áll a HTTP-kérelmekre való válaszadásra, a következő kimenet látható:

```Output
Listening on http://localhost:7071
Hit CTRL-C to exit...

Http Functions:

   hello: http://localhost:7071/api/hello
```

Aktiválja a függvényt a parancssorból egy új terminálablakban a curl használatával:

```CMD
curl -w "\n" http://localhost:7071/api/hello -d LocalFunction
```

```Output
Hello LocalFunction!
```

A `Ctrl-C` billentyűparanccsal állítsa le a függvénykódot a terminálablakban.

## <a name="deploy-the-function-to-azure"></a>A függvény üzembe helyezése az Azure-ban

Az Azure Functions üzembehelyezési folyamata az Azure parancssori felületről származó fiókhitelesítő-adatokat használja. [A folytatás előtt jelentkezzen be az Azure CLI-vel](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) .

```azurecli
az login
```

Helyezze üzembe a kódot egy új függvényalkalmazásban az `azure-functions:deploy` Maven-cél használatával. Ez egy olyan [zip-telepítést](functions-deployment-technologies.md#zip-deploy) hajt végre, amelyen engedélyezve van a csomag mód futtatása.

> [!NOTE]
> Ha Visual Studio Code-ot használ a Function alkalmazás üzembe helyezéséhez, ne felejtsen el nem ingyenes előfizetést választani, vagy hibaüzenetet kap. Az előfizetést az IDE oldal bal oldalán tekintheti meg.

```azurecli
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

Tesztelje az Azure-ban futó függvényalkalmazást a `cURL` használatával. Az alábbi mintában található URL-címet módosítsa az előző lépésben üzembe helyezett saját függvényalkalmazása címére.

> [!NOTE]
> Győződjön meg arról, hogy a **hozzáférési jogosultságokat** a `Anonymous`következőre állítja be:. Ha az alapértelmezett szintet `Function`választja, akkor a függvény-végpont eléréséhez a kérelmekben be kell mutatnia a [függvény kulcsát](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) .

```azurecli
curl -w "\n" https://fabrikam-function-20170920120101928.azurewebsites.net/api/hello -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>Módosítások elvégzése és ismételt üzembe helyezés

Szerkessze a létrehozott projektben az `src/main.../Function.java` forrásfájlt a függvényalkalmazás által visszaadott szöveg módosításához. Módosítsa ezt a sort:

```java
return request.createResponse(200, "Hello, " + name);
```

A következőre:

```java
return request.createResponse(200, "Hi, " + name);
```

Mentse a módosításokat. Futtassa a MVN tiszta csomagot, és végezze `azure-functions:deploy` el az újbóli üzembe helyezést a terminálról a korábban lefuttatva. A függvényalkalmazás frissül, és ez a kérés:

```bash
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/hello
```

Frissített kimenettel rendelkezik:

```Output
Hi, AzureFunctionsTest
```

## <a name="next-steps"></a>További lépések

Létrehozott egy Java-függvényalkalmazást egy egyszerű HTTP-triggerrel, és üzembe helyezte azt az Azure Functionsben.

- Ha további információra van szüksége a Java-függvények létrehozásával kapcsolatban, tekintse át a [Java-függvények fejlesztői útmutatóját](functions-reference-java.md).
- Adjon hozzá különböző triggereket használó további funkciókat a projekthez az `azure-functions:add` Maven-cél használatával.
- Helyileg írjon függvényeket, és végezze el azok hibakeresését a [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), az [IntelliJ](functions-create-maven-intellij.md) és az [Eclipse](functions-create-maven-eclipse.md) használatával. 
- Végezze el az Azure-ban üzembe helyezett függvények hibakeresését a Visual Studio Code-dal. Utasításokért tekintse meg a Visual Studio Code [kiszolgáló nélküli Java-alkalmazásokat](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud) ismertető dokumentációját.
