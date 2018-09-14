---
title: Az első függvény létrehozása az Azure-ban a Java és a Maven használatával | Microsoft Docs
description: Hozzon létre és tegyen közzé egy egyszerű, HTTP-triggert használó függvényt az Azure-ban a Java és a Maven használatával.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, függvények, eseményfeldolgozás, számítás, kiszolgáló nélküli architektúra
ms.service: functions
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/10/2018
ms.author: routlaw, glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 3840d8d1e18e73c1f08c450859032c07e441cff2
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43699175"
---
# <a name="create-your-first-function-with-java-and-maven-preview"></a>Az első függvény létrehozása a Java és a Maven használatával (előzetes verzió)

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

Ez a rövid útmutató végigvezeti a [kiszolgáló nélküli](https://azure.microsoft.com/overview/serverless-computing/) függvények a Maven használatával való létrehozásának, a helyszíni tesztelésnek és az Azure Functionsben való üzembe helyezésének lépésein. Az oktatóanyag eredménye egy, az Azure-ban futó HTTP-triggert használó függvényalkalmazás lesz.

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

Az Azure Functions Core Tools helyi fejlesztési környezetet biztosít az Azure Functions-függvények írásához, futtatásához, valamint a terminálból vagy parancssorból történő hibakereséséhez. 

A folytatás előtt telepítse a [Core Tools 2. verzióját](functions-run-local.md#v2) a helyi számítógépen.

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

Az `appRegion` érték határozza meg, hogy melyik [Azure-régióban](https://azure.microsoft.com/global-infrastructure/regions/) szeretné futtatni a telepített függvényalkalmazást. A régiónévértékek listáját az Azure CLI `az account list-locations` parancsával kérheti le. A `resourceGroup` érték határozza meg, hogy melyik Azure-erőforráscsoportban jön létre a függvényalkalmazás.

Az alábbi `com.fabrikam.functions` és `fabrikam-functions` azonosítók példaként szolgálnak, és könnyebben átláthatóvá teszik a rövid útmutató későbbi lépéseit. Javasoljuk, hogy adja meg a saját értékekeit a Mavennek ebben a lépésben.

```Output
Define value for property 'groupId': com.fabrikam.functions
Define value for property 'artifactId' : fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Define value for property 'appRegion' westus : 
Define value for property 'resourceGroup' java-functions-group: 
Confirm properties configuration: Y
```

A Maven az _artifactId_ nevét viselő új mappában hozza létre a projektfájlokat, amely ebben a példában `fabrikam-functions`. A projekten belül létrehozott futásra kész kód egy egyszerű [HTTP-triggert](/azure/azure-functions/functions-bindings-http-webhook) használó függvény, amely egy „Hello” karaktersor után a kérés törzsét adja vissza.

```java
public class Function {
    @FunctionName("HttpTrigger-Java")
    public HttpResponseMessage HttpTriggerJava(
    @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,final ExecutionContext context) {
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

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

Módosítsa a könyvtárt az újonnan létrehozott projektmappára, majd hozza létre és futtassa a függvényt a Maven használatával:

```
cd fabrikam-functions
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]
> Ha Java 9 használata esetén a `javax.xml.bind.JAXBException` kivételt észleli, tekintse meg a megkerülő megoldást a [GitHubon](https://github.com/jOOQ/jOOQ/issues/6477).

Ha a függvény helyben, az Ön rendszerén fut, és készen áll a HTTP-kérelmekre való válaszadásra, a következő kimenet látható:

```Output
Listening on http://0.0.0.0:7071/
Hit CTRL-C to exit...

Http Functions:

        HttpTrigger-Java: http://localhost:7071/api/HttpTrigger-Java
```

Aktiválja a függvényt a parancssorból egy új terminálablakban a curl használatával:

```
curl -w '\n' -d LocalFunctionTest http://localhost:7071/api/HttpTrigger-Java
```

```Output
Hello, LocalFunctionTest
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
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

```Output
Hello, AzureFunctionsTest
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
