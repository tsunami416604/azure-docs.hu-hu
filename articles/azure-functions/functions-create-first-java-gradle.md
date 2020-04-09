---
title: Függvény közzététele az Azure-ban java és gradle használatával
description: Hozzon létre és tegyen közzé egy HTTP-aktivált függvényt az Azure-ban a Java és a Gradle segítségével.
author: KarlErickson
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: 5e18e035bd237fd489b715986e58d7ede726348d
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886602"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Függvény létrehozása és közzététele az Azure-ban a Java és a Gradle használatával

Ez a cikk bemutatja, hogyan hozhat létre és tehet közzé java függvényprojektet az Azure Functions számára a Gradle parancssori eszközzel. Ha elkészült, a függvénykód az Azure-ban fut egy [kiszolgáló nélküli üzemeltetési csomagban,](functions-scale.md#consumption-plan) és egy HTTP-kérelem váltja ki. 

> [!NOTE]
> Ha Gradle nem a preferált fejlesztési eszköz, nézd meg a hasonló útmutatók Java devlopers segítségével [Maven](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java), [IntelliJ IDEA](/azure/java/intellij/azure-toolkit-for-intellij-quickstart-functions) és [VS Kód](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java).

## <a name="prerequisites"></a>Előfeltételek

Ha függvényeket szeretne fejleszteni a Java használatával, akkor a számítógépre a következőket kell telepíteni:

- A [Java Developer Kit](https://aka.ms/azure-jdks) 8-as verziója
- [Azure CLI]
- [Az Azure Functions Core Tools](./functions-run-local.md#v2) 2.6.666-os vagy újabb verziója
- [Gradle](https://gradle.org/), 4.10-es és újabb verzió

Aktív Azure-előfizetésre is szüksége van. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> A rövid útmutató befejezéséhez a JAVA_HOME környezeti változót a JDK telepítési helyére kell beállítani.

## <a name="prepare-a-functions-project"></a>Functions projekt előkészítése

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

Nyissa meg az új Function.java fájlt az *src/main/java* elérési útról egy szövegszerkesztőben, és tekintse át a generált kódot. Ez a kód egy [HTTP-aktivált](functions-bindings-http-webhook.md) függvény, amely a kérelem törzsét visszhangozza. 

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

A következő parancs futtatásával, majd a függvényprojekt futtatásával:

```bash
gradle jar --info
gradle azureFunctionsRun
```
A projekt helyi futtatásakor az Azure Functions Core Tools a következőhöz hasonló kimenetet lát:

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

Indítsa el a függvényt a parancssorból a következő cURL paranccsal egy új terminálablakban:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

A várt kimenet a következő:

<pre>
Hello AzureFunctions!
</pre>

A [függvénykulcs](functions-bindings-http-webhook-trigger.md#authorization-keys) nem szükséges helyi futtatásesetén.  
A `Ctrl+C` billentyűparanccsal állítsa le a függvénykódot a terminálablakban.

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

```bash
gradle azureFunctionsDeploy
```

Ez a build.gradle fájl értékei alapján hozza létre a következő erőforrásokat az Azure-ban:

+ Erőforráscsoport. A megadott _erőforráscsoporttal_ kapta a nevét.
+ Tárfiók. A függvények által megkövetelve. A név véletlenszerűen jön létre a tárfiók névkövetelményei alapján.
+ App Service-csomag. Kiszolgáló nélküli fogyasztási csomag üzemeltetése a függvényalkalmazáshoz a megadott _appRégióban._ A név véletlenszerűen jön létre.
+ Függvény alkalmazás. A függvényalkalmazás a függvények üzembe helyezésének és végrehajtási egységének a feladatai. A név az ön _appName ,_ hozzáfűzve egy véletlenszerűen generált számot. 

A központi telepítés is csomagolja a projektfájlokat, és telepíti őket az új függvényalkalmazásba [a zip központi telepítés](functions-deployment-technologies.md#zip-deploy)használatával, a csomagról való futtatás izolátus isztikáns módban.

Mivel az általunk `authLevel = AuthorizationLevel.FUNCTION`közzétett HTTP-eseményindító használ, be kell szereznie a függvénykulcsot a függvényvégpont HTTP-n keresztüli hívásához. A funkciókulcs bekésezésének legegyszerűbb módja az [Azure Portal.]

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>A HTTP-eseményindító URL-címének beszereznie

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

<pre>
Hello AzureFunctions!
</pre>

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>További lépések

Létrehozott egy Java függvényprojektet egy HTTP-aktivált funkcióval, futtatta azt a helyi gépen, és üzembe helyezte az Azure-ban. Most pedig nyújtsa ki a funkcióját...

> [!div class="nextstepaction"]
> [Azure Storage-várólista kimeneti kötésének hozzáadása](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Azure Portal]: https://portal.azure.com
