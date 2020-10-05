---
title: Az első függvény létrehozása az Azure-ban a Kotlin és a Maven használatával
description: HTTP által aktivált függvény létrehozása és közzététele az Azure-ban a Kotlin és a Maven használatával.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 526287feb93052d10428971dfd8f660d4265c831
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "89071261"
---
# <a name="quickstart-create-your-first-function-with-kotlin-and-maven"></a>Rövid útmutató: az első függvény létrehozása a Kotlin és a Maven használatával

Ez a cikk végigvezeti a Maven parancssori eszköz használatával, amely a Kotlin-függvény projektjeinek összeállítására és közzétételére Azure Functions. Ha elkészült, a függvény kódja az Azure-beli [használatalapú csomagban](functions-scale.md#consumption-plan) fut, és HTTP-kérelemmel aktiválható.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A függvények Kotlin használatával történő fejlesztéséhez a következőkre van szükség:

- A [Java Developer Kit](https://aka.ms/azure-jdks) 8-as verziója
- Az [Apache Maven](https://maven.apache.org) 3.0-s vagy újabb verziója
- [Azure CLI](/cli/azure)
- [Azure functions Core Tools](./functions-run-local.md#v2) 2.6.666 vagy újabb verzió

> [!IMPORTANT]
> A rövid útmutató befejezéséhez a JAVA_HOME környezeti változót a JDK telepítési helyére kell beállítani.

## <a name="generate-a-new-functions-project"></a>Új Functions-projekt létrehozása

Egy üres mappában futtassa a következő parancsot a Functions-projekt [Maven archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)-ból való létrehozásához.

# <a name="bash"></a>[bash](#tab/bash)
```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

> [!NOTE]
> Ha a parancs futtatásával kapcsolatos problémákat tapasztal, tekintse meg, hogy milyen `maven-archetype-plugin` verziót használ. Mivel a parancsot egy üres, fájl nélküli könyvtárban futtatja `.pom` , előfordulhat, hogy a régebbi verzió beépülő modulját próbálja használni, `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` Ha a mavent egy régebbi verzióról frissítette. Ha igen, próbálja meg törölni a `maven-archetype-plugin` könyvtárat, és futtassa újra a parancsot.

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-kotlin-archetype"
```

# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```
---

A Maven kéri a projekt generálásának befejezéséhez szükséges értékek megadását. A _groupId_, _artifactId_ és _version_ értékek a [Maven elnevezési konvenciókra](https://maven.apache.org/guides/mini/guide-naming-conventions.html) vonatkozó referenciákban találhatók. Az _appName_ értékének egyedinek kell lennie az Azure-ban, ezért a Maven alapértelmezés szerint az előzőleg megadott _artifactId_ érték alapján létrehoz egy alkalmazásnevet. A _packageName_ érték határozza meg a Kotlin-csomagot a generált függvény kódjához.

Az alábbi `com.fabrikam.functions` és `fabrikam-functions` azonosítók példaként szolgálnak, és könnyebben átláthatóvá teszik a rövid útmutató későbbi lépéseit. Javasoljuk, hogy adja meg a saját értékeit a Mavennek ebben a lépésben.

<pre>
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: artifactId, Value: fabrikam-function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: packageInPathFormat, Value: com/fabrikam/function
[INFO] Parameter: appName, Value: fabrikam-function-20190524171507291
[INFO] Parameter: resourceGroup, Value: java-functions-group
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: appRegion, Value: westus
[INFO] Parameter: artifactId, Value: fabrikam-function
</pre>

A Maven az _artifactId_ nevét viselő új mappában hozza létre a projektfájlokat, amely ebben a példában `fabrikam-functions`. A projekten belül létrehozott futásra kész kód egy egyszerű [HTTP-triggert](./functions-bindings-http-webhook.md) használó függvény, amely a kérés törzsét adja vissza:

```kotlin
class Function {

    /**
     * This function listens at endpoint "/api/HttpTrigger-Java". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/HttpTrigger-Java&code={your function key}
     * 2. curl "{your host}/api/HttpTrigger-Java?name=HTTP%20Query&code={your function key}"
     * Function Key is not needed when running locally, it is used to invoke function deployed to Azure.
     * More details: https://aka.ms/functions_authorization_keys
     */
    @FunctionName("HttpTrigger-Java")
    fun run(
            @HttpTrigger(
                    name = "req",
                    methods = [HttpMethod.GET, HttpMethod.POST],
                    authLevel = AuthorizationLevel.FUNCTION) request: HttpRequestMessage<Optional<String>>,
            context: ExecutionContext): HttpResponseMessage {

        context.logger.info("HTTP trigger processed a ${request.httpMethod.name} request.")

        val query = request.queryParameters["name"]
        val name = request.body.orElse(query)

        name?.let {
            return request
                    .createResponseBuilder(HttpStatus.OK)
                    .body("Hello, $name!")
                    .build()
        }

        return request
                .createResponseBuilder(HttpStatus.BAD_REQUEST)
                .body("Please pass a name on the query string or in the request body")
                .build()
    }
}
```

## <a name="run-the-function-locally"></a>A függvény helyi futtatása

Módosítsa a könyvtárt az újonnan létrehozott projektmappára, majd hozza létre és futtassa a függvényt a Maven használatával:

```
cd fabrikam-function
mvn clean package
mvn azure-functions:run
```

> [!NOTE]
> Ha Java 9 használata esetén a `javax.xml.bind.JAXBException` kivételt észleli, tekintse meg a megkerülő megoldást a [GitHubon](https://github.com/jOOQ/jOOQ/issues/6477).

Ha a függvény helyben, az Ön rendszerén fut, és készen áll a HTTP-kérelmekre való válaszadásra, a következő kimenet látható:

<pre>
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpTrigger-Java: [GET,POST] http://localhost:7071/api/HttpTrigger-Java
</pre>

Aktiválja a függvényt a parancssorból egy új terminálablakban a curl használatával:

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/HttpTrigger-Java
```

<pre>
Hello LocalFunction!
</pre>

A `Ctrl-C` billentyűparanccsal állítsa le a függvénykódot a terminálablakban.

## <a name="deploy-the-function-to-azure"></a>A függvény üzembe helyezése az Azure-ban

Az Azure Functions üzembehelyezési folyamata az Azure parancssori felületről származó fiókhitelesítő-adatokat használja. [A folytatás előtt jelentkezzen be az Azure CLI-vel](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) .

```azurecli
az login
```

Helyezze üzembe a kódot egy új függvényalkalmazásban az `azure-functions:deploy` Maven-cél használatával.

> [!NOTE]
> Ha Visual Studio Code-ot használ a Function alkalmazás üzembe helyezéséhez, ne felejtsen el nem ingyenes előfizetést választani, vagy hibaüzenetet kap. Az előfizetést az IDE oldal bal oldalán tekintheti meg.

```
mvn azure-functions:deploy
```

Az üzembe helyezés végén megjelenik az URL-cím, amellyel bejelentkezhet az Azure-függvényalkalmazásba:

<pre>
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
</pre>

Tesztelje az Azure-ban futó függvényalkalmazást a `cURL` használatával. Az alábbi mintában található URL-címet módosítsa az előző lépésben üzembe helyezett saját függvényalkalmazása címére.

> [!NOTE]
> Győződjön meg arról, hogy a **hozzáférési jogosultságokat** a következőre állítja be: `Anonymous` . Ha az alapértelmezett szintet választja `Function` , akkor a függvény-végpont eléréséhez a kérelmekben be kell mutatnia a [függvény kulcsát](functions-bindings-http-webhook-trigger.md#authorization-keys) .

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/HttpTrigger-Java -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>Módosítások elvégzése és ismételt üzembe helyezés

Szerkessze a létrehozott projektben az `src/main.../Function.java` forrásfájlt a függvényalkalmazás által visszaadott szöveg módosításához. Módosítsa ezt a sort:

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hello, $name!")
        .build()
```

A következő kódra:

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hi, $name!")
        .build()
```

Mentse a módosításokat, és helyezze ismét üzembe a terminálon az `azure-functions:deploy` parancs futtatásával, ahogy korábban tette. A függvényalkalmazás frissül, és ez a kérés:

```
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

Megjelenik a frissített kimenet:

<pre>
Hi, AzureFunctionsTest
</pre>


## <a name="reference-bindings"></a>Hivatkozási kötések

A [függvények eseményindítóinak és](functions-triggers-bindings.md) a http-eseményindítók és időzítő-eseményindítók eltérő kötések kezeléséhez kötési bővítményeket kell telepítenie. Habár ez a cikk nem írja elő, tudnia kell, hogyan engedélyezheti a bővítmények használatát más kötési típusok használata esetén.

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

## <a name="next-steps"></a>További lépések

Egy egyszerű HTTP-triggerrel létrehozott egy Kotlin-függvény alkalmazást, és üzembe helyezte azt a Azure Functions.

- A Java és a Kotlin függvények fejlesztésével kapcsolatos további információkért tekintse át a  [Java functions fejlesztői útmutatóját](functions-reference-java.md) .
- Adjon hozzá különböző triggereket használó további funkciókat a projekthez az `azure-functions:add` Maven-cél használatával.
- Helyileg írjon függvényeket, és végezze el azok hibakeresését a [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), az [IntelliJ](functions-create-maven-intellij.md) és az [Eclipse](functions-create-maven-eclipse.md) használatával. 
- Végezze el az Azure-ban üzembe helyezett függvények hibakeresését a Visual Studio Code-dal. Utasításokért tekintse meg a Visual Studio Code [kiszolgáló nélküli Java-alkalmazásokat](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud) ismertető dokumentációját.
