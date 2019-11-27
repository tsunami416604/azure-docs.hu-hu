---
title: Függvények közzététele az Azure-ban a Java és a Maven használatával
description: Hozzon létre és tegyen közzé egy HTTP által aktivált függvényt az Azure-ban a Java és a Maven használatával.
author: rloutlaw
ms.topic: quickstart
ms.date: 08/10/2018
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: cb43f558a5c983a8a4cc3823b278b75cb8cde78d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230745"
---
# <a name="quickstart-use-java-and-maven-to-create-and-publish-a-function-to-azure"></a>Gyors útmutató: függvények létrehozása és közzététele az Azure-ban a Java és a Maven használatával

Ez a cikk bemutatja, hogyan hozhat létre és tehet közzé Java-függvényeket Azure Functions a Maven parancssori eszközzel. Ha elkészült, a függvény kódja egy [kiszolgáló nélküli üzemeltetési](functions-scale.md#consumption-plan) csomagban fut az Azure-ban, és egy HTTP-kérelem indítja el.

<!--
> [!NOTE] 
> You can also create a Kotlin-based Azure Functions project by using the azure-functions-kotlin-archetype instead. Visit the [GitHub repository](https://github.com/microsoft/azure-maven-archetypes/tree/develop/azure-functions-kotlin-archetype) for more information.
-->

## <a name="prerequisites"></a>Előfeltételek

A Java-t használó függvények fejlesztéséhez a következőkre van szükség:

- [Java Developer Kit](https://aka.ms/azure-jdks), 8-as verzió
- [Apache Maven](https://maven.apache.org), 3,0-es vagy újabb verzió
- [Azure CLI]
- [Azure functions Core Tools](./functions-run-local.md#v2) 2.6.666 vagy újabb verzió
- Azure-előfizetés.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


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
> Ha a parancs futtatásával kapcsolatos problémákat tapasztal, tekintse meg, hogy milyen `maven-archetype-plugin`-verziót használ. Mivel a parancsot egy `.pom` fájl nélküli üres könyvtárban futtatja, előfordulhat, hogy a korábbi verzió beépülő modulját szeretné használni a `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin`ról, ha a Mavent egy régebbi verzióról frissítette. Ha igen, próbálja meg törölni a `maven-archetype-plugin` könyvtárat, és futtassa újra a parancsot.

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

A Maven megkéri, hogy a projektnek a telepítéskor való létrehozásának befejezéséhez szükséges értékeket is megkeresse. Ha a rendszer kéri, adja meg a következő értékeket:

| Érték | Leírás |
| ----- | ----------- |
| **groupId** | Egy érték, amely egyedileg azonosítja a projektet az összes projektben, a Java [csomag elnevezési szabályait](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) követve. Az ebben a rövid útmutatóban szereplő példák a `com.fabrikam.functions`t használják. |
| **artifactId** | Egy érték, amely a jar neve, verziószám nélkül. Az ebben a rövid útmutatóban szereplő példák a `fabrikam-functions`t használják. |
| **verziója** | Válassza ki `1.0-SNAPSHOT`alapértelmezett értékét. |
| **csomag** | Egy érték, amely a generált függvény kódjához tartozó Java-csomag. Használja az alapértelmezettet. Az ebben a rövid útmutatóban szereplő példák a `com.fabrikam.functions`t használják. |
| **appName** | Globálisan egyedi név, amely azonosítja az új Function alkalmazást az Azure-ban. Használja az alapértelmezett értéket, amely a _artifactId_ hozzáfűzése véletlenszerű számmal. Jegyezze fel ezt az értéket, ezért később szüksége lesz rá. |
| **appRegion** | Válasszon egy [régiót](https://azure.microsoft.com/regions/) a közelben, vagy a függvények által elért más szolgáltatások közelében. A mező alapértelmezett értéke: `westus`. Futtassa ezt az [Azure CLI] -parancsot az összes régió listájának lekéréséhez:<br/>`az account list-locations --query '[].{Name:name}' -o tsv` |
| **resourceGroup** | Az új [erőforráscsoport](../azure-resource-manager/resource-group-overview.md) neve, amelyben létre szeretné hozni a Function alkalmazást. Használja a `myResourceGroup`, amelyet a jelen rövid útmutató példákban használ. Az erőforráscsoporthoz egyedinek kell lennie az Azure-előfizetésében.|

A megerősítéshez írja be `Y` vagy nyomja le az ENTER billentyűt.

A Maven létrehoz egy új, _artifactId_nevű mappában található projektfájlt, amely ebben a példában `fabrikam-functions`. 

Nyissa meg az új function. Java fájlt egy szövegszerkesztőben a *src/Main/Java* elérési útról, és tekintse át a generált kódot. Ez a kód egy [http által aktivált](functions-bindings-http-webhook.md) függvény, amely megismétli a kérelem törzsét. 

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

Futtassa a következő parancsot, amely módosítja a könyvtárat az újonnan létrehozott projekt mappájába, majd létrehozza és futtatja a Function projektet:

```console
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

A következőhöz hasonló kimenet jelenik meg Azure Functions Core Tools a projekt helyi futtatásakor:

```Output
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpTrigger-Java: [GET,POST] http://localhost:7071/api/HttpTrigger-Java
...
```

Aktiválja a függvényt a parancssorból a cURL használatával egy új terminál ablakban:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

```Output
Hello AzureFunctions!
```
Helyileg történő futtatás esetén a [funkcióbillentyű](functions-bindings-http-webhook.md#authorization-keys) nem szükséges. A `Ctrl+C` billentyűparanccsal állítsa le a függvénykódot a terminálablakban.

## <a name="deploy-the-function-to-azure"></a>A függvény üzembe helyezése az Azure-ban

A Function alkalmazást és a kapcsolódó erőforrásokat az Azure-ban hozza létre a rendszer az első üzembe helyezéskor. Az üzembe helyezés előtt az az [login](/cli/azure/authenticate-azure-cli) Azure CLI-paranccsal jelentkezzen be az Azure-előfizetésbe. 

```azurecli
az login
```

> [!TIP]
> Ha a fiókja több előfizetéshez is hozzáfér, az [az Account set](/cli/azure/account#az-account-set) paranccsal állíthatja be az alapértelmezett előfizetést ehhez a munkamenethez. 

A következő Maven-paranccsal telepítheti a projektet egy új Function alkalmazásba. 

```azurecli
mvn azure-functions:deploy
```

Ez a `azure-functions:deploy` Maven-cél a következő erőforrásokat hozza létre az Azure-ban:

+ Erőforráscsoport. Elnevezve a megadott _resourceGroup_ .
+ Storage-fiók. A függvények igénylik. A név véletlenszerűen jön létre a Storage-fióknév követelményei alapján.
+ App Service-csomag. A megadott _appRegion_lévő Function App kiszolgáló nélküli üzemeltetése. A név véletlenszerűen jön létre.
+ Function alkalmazás. A functions alkalmazás a függvények üzembe helyezési és végrehajtási egysége. A név a _appName_, amely véletlenszerűen generált számmal van hozzáfűzve. 

Az üzemelő példány a Project fájljait is becsomagolja, és az új Function alkalmazásba telepíti a [zip-telepítést](functions-deployment-technologies.md#zip-deploy), és engedélyezve van a csomagon belüli mód.

Az üzembe helyezés befejezése után megjelenik az URL-cím, amellyel elérheti a Function app-végpontokat. Mivel a közzétett HTTP-trigger `authLevel = AuthorizationLevel.FUNCTION`használ, le kell kérnie a Function-végpontot a HTTP protokollon keresztül. A funkcióbillentyű beszerzésének legegyszerűbb módja a [Azure Portal].

## <a name="get-the-http-trigger-url"></a>HTTP-trigger URL-címének beolvasása

<!--- We can updates this to remove portal dependency after the Maven archetype returns the full URLs with keys on publish (https://github.com/microsoft/azure-maven-plugins/issues/571). -->

A függvény elindításához szükséges URL-címet a Azure Portalból kérheti le. 

1. Keresse meg a [Azure Portal], jelentkezzen be, írja be a _appName_ az oldal tetején található **Keresés** mezőbe, majd nyomja le az ENTER billentyűt.
 
1. A Function alkalmazásban bontsa ki a **függvények elemet (csak olvasható)** , válassza ki a függvényt, majd válassza a **</> a függvény URL-címének beolvasása** lehetőséget a jobb felső sarokban. 

    ![A függvény URL-címének másolása az Azure portálról](./media/functions-create-java-maven/get-function-url-portal.png)

1. Válassza az **alapértelmezett (funkcióbillentyű)** lehetőséget, majd válassza a **Másolás**lehetőséget. 

Mostantól a másolt URL-cím használatával is elérheti a függvényt.

## <a name="verify-the-function-in-azure"></a>A függvény ellenőrzése az Azure-ban

Az Azure-ban futó Function app `cURL`használatával történő ellenőrzéséhez cserélje le az alábbi minta URL-címét a portálról másolt URL-címre.

```azurecli
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Ez POST-kérést küld a függvény végpontjának `AzureFunctions` a kérelem törzsében. A következő válasz jelenik meg.

```Output
Hello AzureFunctions!
```

## <a name="next-steps"></a>Következő lépések

Létrehozott egy Java functions-projektet egy HTTP által aktivált függvénnyel, futtatta azt a helyi gépen, és üzembe helyezte az Azure-ban. Most terjessze ki a függvényt...

> [!div class="nextstepaction"]
> [Azure Storage-üzenetsor kimeneti kötésének hozzáadása](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Azure Portal]: https://portal.azure.com