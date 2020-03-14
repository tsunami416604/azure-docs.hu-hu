---
title: Függvények közzététele az Azure-ban a Java és a Maven/Gradle használatával
description: HTTP által aktivált függvény létrehozása és közzététele az Azure-ban Java, Maven vagy Gradle használatával.
author: KarlErickson
ms.author: karler
ms.topic: quickstart
ms.date: 08/10/2018
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: ad3b38a12020c56c31e03879b3fbcb9a8dda25f1
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136867"
---
# <a name="quickstart-use-java-and-mavengradle-to-create-and-publish-a-function-to-azure"></a>Gyors útmutató: függvények létrehozása és közzététele az Azure-ban a Java és a Maven/Gradle használatával

Ez a cikk bemutatja, hogyan hozhat létre és tehet közzé Java-függvényeket, hogy Azure Functions a Maven/Gradle parancssori eszközzel. Ha elkészült, a függvény kódja egy [kiszolgáló nélküli üzemeltetési](functions-scale.md#consumption-plan) csomagban fut az Azure-ban, és egy HTTP-kérelem indítja el.

<!--
> [!NOTE] 
> You can also create a Kotlin-based Azure Functions project by using the azure-functions-kotlin-archetype instead. Visit the [GitHub repository](https://github.com/microsoft/azure-maven-archetypes/tree/develop/azure-functions-kotlin-archetype) for more information.
-->

## <a name="prerequisites"></a>Előfeltételek

Ha függvényeket szeretne fejleszteni a Java használatával, akkor a számítógépre a következőket kell telepíteni:

- A [Java Developer Kit](https://aka.ms/azure-jdks) 8-as verziója
- [Azure CLI]
- [Azure functions Core Tools](./functions-run-local.md#v2) 2.6.666 vagy újabb verzió
::: zone pivot="java-build-tools-maven" 
- Az [Apache Maven](https://maven.apache.org) 3.0-s vagy újabb verziója
::: zone-end

::: zone pivot="java-build-tools-gradle"  
- [Gradle](https://gradle.org/), 4,10-es vagy újabb verzió
::: zone-end 

Aktív Azure-előfizetésre is szüksége van. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


> [!IMPORTANT]
> A rövid útmutató befejezéséhez a JAVA_HOME környezeti változót a JDK telepítési helyére kell beállítani.

## <a name="prepare-a-functions-project"></a>Functions-projekt előkészítése

::: zone pivot="java-build-tools-maven" 
Egy üres mappában futtassa a következő parancsot a Functions-projekt [Maven archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)-ból való létrehozásához.

```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype 
```

> [!NOTE]
> Ha a PowerShellt használja, ne felejtse el hozzáadni a "" Around paramétereket.

> [!NOTE]
> Ha a parancs futtatásával kapcsolatos problémákat tapasztal, tekintse meg, hogy milyen `maven-archetype-plugin`-verziót használ. Mivel a parancsot egy `.pom` fájl nélküli üres könyvtárban futtatja, előfordulhat, hogy a korábbi verzió beépülő modulját szeretné használni a `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin`ról, ha a Mavent egy régebbi verzióról frissítette. Ha igen, próbálja meg törölni a `maven-archetype-plugin` könyvtárat, és futtassa újra a parancsot.

A Maven megkéri, hogy a projektnek a telepítéskor való létrehozásának befejezéséhez szükséges értékeket is megkeresse. Ha a rendszer kéri, adja meg a következő értékeket:

| Érték | Leírás |
| ----- | ----------- |
| **groupId** | Egy érték, amely egyedileg azonosítja a projektet az összes projektben, a Java [csomag elnevezési szabályait](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) követve. Az ebben a rövid útmutatóban szereplő példák a `com.fabrikam.functions`t használják. |
| **artifactId** | Egy érték, amely a jar neve, verziószám nélkül. Az ebben a rövid útmutatóban szereplő példák a `fabrikam-functions`t használják. |
| **verziója** | Válassza ki `1.0-SNAPSHOT`alapértelmezett értékét. |
| **csomag** | Egy érték, amely a generált függvény kódjához tartozó Java-csomag. Használja az alapértelmezettet. Az ebben a rövid útmutatóban szereplő példák a `com.fabrikam.functions`t használják. |
| **appName** | Globálisan egyedi név, amely azonosítja az új Function alkalmazást az Azure-ban. Használja az alapértelmezett értéket, amely a _artifactId_ hozzáfűzése véletlenszerű számmal. Jegyezze fel ezt az értéket, ezért később szüksége lesz rá. |
| **appRegion** | Válasszon egy [régiót](https://azure.microsoft.com/regions/) a közelben, vagy a függvények által elért más szolgáltatások közelében. A mező alapértelmezett értéke: `westus`. Futtassa ezt az [Azure CLI] -parancsot az összes régió listájának lekéréséhez:<br/>`az account list-locations --query '[].{Name:name}' -o tsv` |
| **resourceGroup** | Az új [erőforráscsoport](../azure-resource-manager/management/overview.md) neve, amelyben létre szeretné hozni a Function alkalmazást. Használja a `myResourceGroup`, amelyet a jelen rövid útmutató példákban használ. Az erőforráscsoporthoz egyedinek kell lennie az Azure-előfizetésében.|

A megerősítéshez írja be `Y` vagy nyomja le az ENTER billentyűt.

A Maven létrehoz egy új, _artifactId_nevű mappában található projektfájlt, amely ebben a példában `fabrikam-functions`. A következő parancs futtatásával módosítsa a könyvtárat a létrehozott projekt mappájába.
```bash
cd fabrikam-function
```

::: zone-end 
::: zone pivot="java-build-tools-gradle"
A minta projekt klónozásához használja az alábbi parancsot:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Nyissa meg a `build.gradle`t, és módosítsa a következő szakaszban található `appName`t egy egyedi névre, hogy elkerülje a tartománynevek ütközését az Azure-ba való üzembe helyezéskor. 

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

Nyissa meg az új function. Java fájlt egy szövegszerkesztőben a *src/Main/Java* elérési útról, és tekintse át a generált kódot. Ez a kód egy [http által aktivált](functions-bindings-http-webhook.md) függvény, amely megismétli a kérelem törzsét. 

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

A következő parancs futtatásával hozza létre a Function projektet:

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

A következőhöz hasonló kimenetet fog látni Azure Functions Core Tools a projekt helyi futtatásakor:

```output
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
```

Aktiválja a függvényt a parancssorból a cURL használatával egy új terminál ablakban:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

```output
Hello AzureFunctions!
```
Helyileg történő futtatás esetén a [funkcióbillentyű](functions-bindings-http-webhook-trigger.md#authorization-keys) nem szükséges. A `Ctrl+C` billentyűparanccsal állítsa le a függvénykódot a terminálablakban.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=local-run)

## <a name="deploy-the-function-to-azure"></a>A függvény üzembe helyezése az Azure-ban

A Function alkalmazást és a kapcsolódó erőforrásokat az Azure-ban hozza létre a rendszer az első üzembe helyezéskor. Az üzembe helyezés előtt az az [login](/cli/azure/authenticate-azure-cli) Azure CLI-paranccsal jelentkezzen be az Azure-előfizetésbe. 

```azurecli
az login
```

> [!TIP]
> Ha a fiókja több előfizetéshez is hozzáfér, az [az Account set](/cli/azure/account#az-account-set) paranccsal állíthatja be az alapértelmezett előfizetést ehhez a munkamenethez. 

A következő parancs használatával telepítheti a projektet egy új Function alkalmazásba. 


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

Ezzel a következő erőforrásokat fogja létrehozni az Azure-ban:

+ Erőforráscsoport. Elnevezve a megadott _resourceGroup_ .
+ Storage-fiók. A függvények igénylik. A név véletlenszerűen jön létre a Storage-fióknév követelményei alapján.
+ App Service-csomag. A megadott _appRegion_lévő Function App kiszolgáló nélküli üzemeltetése. A név véletlenszerűen jön létre.
+ Function alkalmazás. A functions alkalmazás a függvények üzembe helyezési és végrehajtási egysége. A név a _appName_, amely véletlenszerűen generált számmal van hozzáfűzve. 

Az üzemelő példány a Project fájljait is becsomagolja, és az új Function alkalmazásba telepíti a [zip-telepítést](functions-deployment-technologies.md#zip-deploy), és engedélyezve van a csomagon belüli mód.

Az üzembe helyezés befejezése után megjelenik az URL-cím, amellyel elérheti a Function app-végpontokat. Mivel a közzétett HTTP-trigger `authLevel = AuthorizationLevel.FUNCTION`használ, le kell kérnie a Function-végpontot a HTTP protokollon keresztül. A funkcióbillentyű beszerzésének legegyszerűbb módja a [Azure Portalra].

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>HTTP-trigger URL-címének beolvasása

<!--- We can updates this to remove portal dependency after the Maven archetype returns the full URLs with keys on publish (https://github.com/microsoft/azure-maven-plugins/issues/571). -->

A függvény elindításához szükséges URL-címet a Azure Portalból kérheti le. 

1. Keresse meg a [Azure Portalra], jelentkezzen be, írja be a _appName_ az oldal tetején található **Keresés** mezőbe, majd nyomja le az ENTER billentyűt.
 
1. A Function alkalmazásban bontsa ki a **függvények elemet (csak olvasható)** , válassza ki a függvényt, majd válassza a **</> a függvény URL-címének beolvasása** lehetőséget a jobb felső sarokban. 

    ![A függvény URL-címének másolása az Azure portálról](./media/functions-create-java-maven/get-function-url-portal.png)

1. Válassza az **alapértelmezett (funkcióbillentyű)** lehetőséget, majd válassza a **Másolás**lehetőséget. 

Mostantól a másolt URL-cím használatával is elérheti a függvényt.

## <a name="verify-the-function-in-azure"></a>A függvény ellenőrzése az Azure-ban

Az Azure-ban futó Function app `cURL`használatával történő ellenőrzéséhez cserélje le az alábbi minta URL-címét a portálról másolt URL-címre.

```console
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpExample?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Ez POST-kérést küld a függvény végpontjának `AzureFunctions` a kérelem törzsében. A következő válasz jelenik meg.

```output
Hello AzureFunctions!
```

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>Következő lépések

Létrehozott egy Java functions-projektet egy HTTP által aktivált függvénnyel, futtatta azt a helyi gépen, és üzembe helyezte az Azure-ban. Most terjessze ki a függvényt...

> [!div class="nextstepaction"]
> [Azure Storage-üzenetsor kimeneti kötésének hozzáadása](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Azure Portalra]: https://portal.azure.com
