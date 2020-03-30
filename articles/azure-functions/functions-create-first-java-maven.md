---
title: Függvény közzététele az Azure-ban java és Maven/Gradle használatával
description: Hozzon létre és tegyen közzé egy HTTP-aktivált függvényt az Azure-ban a Java és a Maven vagy gradle használatával.
author: KarlErickson
ms.author: karler
ms.topic: quickstart
ms.date: 08/10/2018
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: ad3b38a12020c56c31e03879b3fbcb9a8dda25f1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79136867"
---
# <a name="quickstart-use-java-and-mavengradle-to-create-and-publish-a-function-to-azure"></a>Rövid útmutató: A Java és a Maven/Gradle használatával hozzon létre és tegyen közzé egy függvényt az Azure-ban

Ez a cikk bemutatja, hogyan hozhat létre és tehet közzé java függvényt az Azure Functions a Maven/Gradle parancssori eszközzel. Ha elkészült, a függvénykód az Azure-ban fut egy [kiszolgáló nélküli üzemeltetési csomagban,](functions-scale.md#consumption-plan) és egy HTTP-kérelem váltja ki.

<!--
> [!NOTE] 
> You can also create a Kotlin-based Azure Functions project by using the azure-functions-kotlin-archetype instead. Visit the [GitHub repository](https://github.com/microsoft/azure-maven-archetypes/tree/develop/azure-functions-kotlin-archetype) for more information.
-->

## <a name="prerequisites"></a>Előfeltételek

Ha függvényeket szeretne fejleszteni a Java használatával, akkor a számítógépre a következőket kell telepíteni:

- A [Java Developer Kit](https://aka.ms/azure-jdks) 8-as verziója
- [Azure CLI]
- [Az Azure Functions Core Tools](./functions-run-local.md#v2) 2.6.666-os vagy újabb verziója
::: zone pivot="java-build-tools-maven" 
- Az [Apache Maven](https://maven.apache.org) 3.0-s vagy újabb verziója
::: zone-end

::: zone pivot="java-build-tools-gradle"  
- [Gradle](https://gradle.org/), 4.10-es és újabb verzió
::: zone-end 

Aktív Azure-előfizetésre is szüksége van. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


> [!IMPORTANT]
> A rövid útmutató befejezéséhez a JAVA_HOME környezeti változót a JDK telepítési helyére kell beállítani.

## <a name="prepare-a-functions-project"></a>Functions projekt előkészítése

::: zone pivot="java-build-tools-maven" 
Egy üres mappában futtassa a következő parancsot a Functions-projekt [Maven archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)-ból való létrehozásához.

```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype 
```

> [!NOTE]
> Powershell használata esetén ne felejtse el hozzáadni a "" szót a paraméterek hez.

> [!NOTE]
> Ha problémákat tapasztal a parancs futtatásával kapcsolatban, `maven-archetype-plugin` tekintse meg, hogy milyen verziót használ. Mivel a parancsot egy fájl nélküli `.pom` üres könyvtárban futtatja, előfordulhat, hogy a `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` régebbi verzió egyik bővítményét próbálja használni, ha a Maven-t egy régebbi verzióról frissítette. Ha igen, próbálja meg `maven-archetype-plugin` a könyvtárat, majd futtassa újra a parancsot.

A Maven olyan értékeket kér, amelyek a projekt üzembe helyezéséhez szükségesek. Amikor a rendszer kéri, adja meg a következő értékeket:

| Érték | Leírás |
| ----- | ----------- |
| **csoportazonosító** | Olyan érték, amely egyedileg azonosítja a projektet az összes projektben, a Java [csomagelnevezési szabályainak](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) megfelelően. A rövid útmutató ban `com.fabrikam.functions`szereplő példák . |
| **artifactId** | Az edény neve verziószám nélküli érték. A rövid útmutató ban `fabrikam-functions`szereplő példák . |
| **Változat** | A érték ének `1.0-SNAPSHOT`alapértelmezett értékét adja meg. |
| **Csomag** | Olyan érték, amely a létrehozott függvénykód Java-csomagja. Használja az alapértelmezettet. A rövid útmutató ban `com.fabrikam.functions`szereplő példák . |
| **appName alkalmazásneve** | Globálisan egyedi név, amely azonosítja az új függvényalkalmazást az Azure-ban. Használja az alapértelmezett, amely a _artifactId_ hozzáfűzve egy véletlen számmal. Jegyezze fel ezt az értéket, később szüksége lesz rá. |
| **appRegion** | Válasszon egy [régiót](https://azure.microsoft.com/regions/) a közelben, vagy a függvények által elért más szolgáltatások közelében. A mező alapértelmezett értéke: `westus`. Futtassa ezt az [Azure CLI] parancsot az összes régió listájának leéséhez:<br/>`az account list-locations --query '[].{Name:name}' -o tsv` |
| **erőforrásCsoport** | Annak az új [erőforráscsoportnak](../azure-resource-manager/management/overview.md) a neve, amelyben a függvényalkalmazást létre szeretné hozni. Használja `myResourceGroup`a t, amelyet a rövid útmutató példái használnak. Az erőforráscsoportnak egyedinek kell lennie az Azure-előfizetésében.|

A `Y` megerősítéshez írja be vagy nyomja le az Enter billentyűt.

A Maven egy új mappában hozza létre a projektfájlokat, `fabrikam-functions`amelynek neve _artifactId_, amely ebben a példában a. Futtassa a következő parancsot a könyvtár létrehozott projektmappára való módosításához.
```bash
cd fabrikam-function
```

::: zone-end 
::: zone pivot="java-build-tools-gradle"
A mintaprojekt klónozásához használja a következő parancsot:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Nyissa `build.gradle` meg `appName` és módosítsa a következő szakaszban egy egyedi nevet, hogy elkerülje a tartománynév-ütközést az Azure-ba való üzembe helyezéskor. 

```gradle
azurefunctions {
    resourceGroup = 'java-functions-group'
    appName = 'azure-functions-sample-demo'
    pricingTier = 'Consumption'
    region = 'westus'
    runtime {
      os = 'windows'
    }
    localDebug = "transport=dt_socket,server=y,suspend=n,address=5005"
}
```
::: zone-end

Nyissa meg az új Function.java fájlt az *src/main/java* elérési útról egy szövegszerkesztőben, és tekintse át a generált kódot. Ez a kód egy [HTTP-aktivált](functions-bindings-http-webhook.md) függvény, amely a kérelem törzsét visszhangozza. 

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

A következő parancs futtatásával, majd a függvényprojekt futtatásával:

::: zone pivot="java-build-tools-maven" 
```bash
mvn clean package 
mvn azure-functions:run
```
::: zone-end 

::: zone pivot="java-build-tools-gradle"  
```bash
gradle jar --info
gradle azureFunctionsRun
```
::: zone-end 

A projekt helyi futtatásakor az Azure Functions Core Tools a következőhöz hasonló kimenetet fog látni:

```output
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
```

Indítsa el a függvényt a parancssorból a cURL használatával egy új terminálablakban:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

```output
Hello AzureFunctions!
```
A [függvénykulcs](functions-bindings-http-webhook-trigger.md#authorization-keys) nem szükséges helyi futtatásesetén. A `Ctrl+C` billentyűparanccsal állítsa le a függvénykódot a terminálablakban.

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=local-run)

## <a name="deploy-the-function-to-azure"></a>A függvény üzembe helyezése az Azure-ban

Egy függvényalkalmazás és a kapcsolódó erőforrások jönnek létre az Azure-ban, amikor először telepíti a függvényalkalmazást. Üzembe helyezés előtt az [Azure](/cli/azure/authenticate-azure-cli) CLI-vel jelentkezzen be az Azure-előfizetésbe. 

```azurecli
az login
```

> [!TIP]
> Ha a fiókja több előfizetéshez is hozzáférhet, az [az-fiók beállításával](/cli/azure/account#az-account-set) állítsa be az alapértelmezett előfizetést ehhez a munkamenethez. 

A következő paranccsal telepítheti a projektet egy új függvényalkalmazásba. 


::: zone pivot="java-build-tools-maven" 
```bash
mvn azure-functions:deploy
```
::: zone-end 

::: zone pivot="java-build-tools-gradle"  
```bash
gradle azureFunctionsDeploy
```
::: zone-end

Ez a következő erőforrásokat hozza létre az Azure-ban:

+ Erőforráscsoport. A megadott _erőforráscsoporttal_ kapta a nevét.
+ Tárfiók. A függvények által megkövetelve. A név véletlenszerűen jön létre a tárfiók névkövetelményei alapján.
+ Alkalmazásszolgáltatási csomag. Kiszolgáló nélküli üzemeltetés a függvényalkalmazáshoz a megadott _appRégióban._ A név véletlenszerűen jön létre.
+ Függvény alkalmazás. A függvényalkalmazás a függvények üzembe helyezésének és végrehajtási egységének a feladatai. A név az ön _appName ,_ hozzáfűzve egy véletlenszerűen generált számot. 

A központi telepítés is csomagolja a projektfájlokat, és telepíti őket az új függvényalkalmazásba [a zip központi telepítés](functions-deployment-technologies.md#zip-deploy)használatával, a csomagról való futtatás izolátus isztikáns módban.

A központi telepítés befejezése után láthatja a függvényalkalmazás-végpontok eléréséhez használható URL-címet. Mivel az általunk `authLevel = AuthorizationLevel.FUNCTION`közzétett HTTP-eseményindító használ, be kell szereznie a függvénykulcsot a függvényvégpont HTTP-n keresztüli hívásához. A funkciókulcs bekésezésének legegyszerűbb módja az [Azure Portal.]

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>A HTTP-eseményindító URL-címének beszereznie

<!--- We can updates this to remove portal dependency after the Maven archetype returns the full URLs with keys on publish (https://github.com/microsoft/azure-maven-plugins/issues/571). -->

A függvény aktiválásához szükséges URL-címet a függvényksegítségével az Azure Portalon keresztül kaphatja le. 

1. Tallózzon az [Azure Portalon,]jelentkezzen be, írja be a függvényalkalmazás _alkalmazásnevét_ a lap tetején található **Keresés** mezőbe, és nyomja le az Enter billentyűt.
 
1. A függvényalkalmazásban bontsa ki a **Functions (Írásvédett)** elemet, válassza ki a függvényt, majd válassza **a</> A függvény URL-címének beolvasása** lehetőséget a jobb felső sarokban. 

    ![A függvény URL-címének másolása az Azure portálról](./media/functions-create-java-maven/get-function-url-portal.png)

1. Válassza **az alapértelmezett (Funkcióbillentyű)** lehetőséget, majd a **Másolás parancsot.** 

Most már használhatja a másolt URL-t a funkció eléréséhez.

## <a name="verify-the-function-in-azure"></a>A funkció ellenőrzése az Azure-ban

Az Azure-on futó függvényalkalmazás ellenőrzéséhez `cURL`cserélje le az alábbi minta URL-címét a portálról másolt URL-címre.

```console
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpExample?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Ez postakérést küld a `AzureFunctions` függvény végpontjának a kérelem törzsében. A következő válasz jelenik meg.

```output
Hello AzureFunctions!
```

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>További lépések

Létrehozott egy Java függvényprojektet egy HTTP-aktivált funkcióval, futtatta azt a helyi gépen, és üzembe helyezte az Azure-ban. Most pedig nyújtsa ki a funkcióját...

> [!div class="nextstepaction"]
> [Azure Storage-várólista kimeneti kötésének hozzáadása](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Azure-portál]: https://portal.azure.com
