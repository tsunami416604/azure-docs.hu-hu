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
ms.author: routlaw, glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: dfbd8425048ddc5c96349bfd6a7462dcd32dc1d9
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55727756"
---
# <a name="create-your-first-function-with-java-and-maven-preview"></a>Az első függvény létrehozása a Java és a Maven használatával (előzetes verzió)

> [!NOTE] 
> A Java for Azure Functions jelenleg előzetes verzióban érhető el.

Ez a rövid útmutató végigvezeti egy [kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) a Mavennel, helyi tesztelés, és hogyan helyezheti üzembe az Azure Functions-projektet. Ha elkészült, a Java-függvénykódot a felhőben fut, és is elindítható a HTTP-kérést.

![„Hello World” függvény elérése a parancssorból a cURL használatával](media/functions-create-java-maven/hello-azure.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek
Ha függvényalkalmazást szeretne létrehozni a Java használatával, akkor a számítógépre a következőket kell telepíteni:

-  A [Java Developer Kit](https://www.azul.com/downloads/zulu/) 8-as verziója.
-  Az [Apache Maven](https://maven.apache.org) 3.0-s vagy újabb verziója.
-  [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> A rövid útmutató befejezéséhez a JAVA_HOME környezeti változót a JDK telepítési helyére kell beállítani.

## <a name="install-the-azure-functions-core-tools"></a>Az Azure Functions Core Tools telepítése

Az [Azure Functions Core Tools 2.0](https://www.npmjs.com/package/azure-functions-core-tools) helyi fejlesztési környezetet biztosít az Azure-függvények írásához, futtatásához és a hibakereséshez. 

A telepítéshez nyissa meg az Azure Functions Core Tools projekt [Telepítés](https://github.com/azure/azure-functions-core-tools#installing) szakaszát, és keresse meg az operációs rendszeréhez tartozó utasításokat.

Manuálisan is telepítheti az [npm](https://www.npmjs.com/) használatával, amely a [Node.js](https://nodejs.org/) részét képezi, miután telepítette az alábbiakat:

-  A [.NET Core](https://www.microsoft.com/net/core) legújabb verziója.
-  A [Node.js](https://nodejs.org/download/) 8.6-os vagy újabb verziója.

Az npm-alapú telepítés folytatásához futtassa a következőt:

```
npm install -g azure-functions-core-tools
```

> [!NOTE]
> Ha az Azure Functions Core Tools 2.0-s verziójának telepítése során problémába ütközik, tekintse meg a [2.x-es verziók futtatókörnyezetével](/azure/azure-functions/functions-run-local) foglalkozó részt.

## <a name="generate-a-new-functions-project"></a>Új Functions-projekt létrehozása

Egy üres mappában futtassa a következő parancsot a Functions-projekt [Maven archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)-ból való létrehozásához.

### <a name="linuxmacos"></a>Linux/MacOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

### <a name="windows-cmd"></a>Windows (parancssor)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype
```

A Maven kérni fogja a projekt létrehozásához szükséges értékeket. A _groupId_, _artifactId_ és _version_ értékek a [Maven elnevezési konvenciókra](https://maven.apache.org/guides/mini/guide-naming-conventions.html) vonatkozó referenciákban találhatók. Az _appName_ értékének egyedinek kell lennie az Azure-ban, ezért a Maven alapértelmezés szerint az előzőleg megadott _artifactId_ érték alapján létrehoz egy alkalmazásnevet. A _packageName_ érték meghatározza a Java-csomagot a létrehozott függvénykódhoz.

Az alábbi `com.fabrikam.functions` és `fabrikam-functions` azonosítók példaként szolgálnak, és könnyebben átláthatóvá teszik a rövid útmutató későbbi lépéseit. Javasoljuk, hogy adja meg a saját értékekeit a Mavennek ebben a lépésben.

```Output
Define value for property 'groupId': com.fabrikam.functions
Define value for property 'artifactId' : fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Confirm properties configuration: Y
```

A Maven az _artifactId_ nevét viselő új mappában hozza létre a projektfájlokat, amely ebben a példában `fabrikam-functions`. A projekten belül létrehozott futásra kész kód egy egyszerű [HTTP-triggert](/azure/azure-functions/functions-bindings-http-webhook) használó függvény, amely a kérés törzsét adja vissza:

```java
public class Function {
    /**
     * This function listens at endpoint "/api/hello". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/hello
     * 2. curl {your host}/api/hello?name=HTTP%20Query
     */
    @FunctionName("hello")
    public HttpResponseMessage<String> hello(
            @HttpTrigger(name = "req", methods = {"get", "post"}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");

        // Parse query parameter
        String query = request.getQueryParameters().get("name");
        String name = request.getBody().orElse(query);

        if (name == null) {
            return request.createResponse(400, "Please pass a name on the query string or in the request body");
        } else {
            return request.createResponse(200, "Hello, " + name);
        }
    }
}

```

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

Módosítsa a könyvtárt az újonnan létrehozott projektmappára, majd hozza létre és futtassa a függvényt a Maven használatával:

```
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

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/hello
```

```Output
Hello LocalFunction!
```

A `Ctrl-C` billentyűparanccsal állítsa le a függvénykódot a terminálablakban.

## <a name="deploy-the-function-to-azure"></a>A függvény üzembe helyezése az Azure-ban

Az Azure Functions üzembehelyezési folyamata az Azure parancssori felületről származó fiókhitelesítő-adatokat használja. [Jelentkezzen be az Azure CLI-vel](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) a folytatás előtt.

```azurecli
az login
```

Helyezze üzembe a kódot egy új függvényalkalmazásban az `azure-functions:deploy` Maven-cél használatával.

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

Tesztelje az Azure-ban futó függvényalkalmazást a `cURL` használatával. Az alábbi mintában található URL-címet módosítsa az előző lépésben üzembe helyezett saját függvényalkalmazása címére.

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/hello -d AzureFunctions
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

Mentse a módosításokat, és helyezze ismét üzembe a terminálon az `azure-functions:deploy` parancs futtatásával, ahogy korábban tette. A függvényalkalmazás frissül, és ez a kérés:

```bash
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
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
