---
title: 'Gyors útmutató: Java-alkalmazás létrehozása Azure App Service'
description: Percek alatt üzembe helyezheti az első Java-"Helló világ!" alkalmazás Azure App Service. A Mavenhez készült Azure Web App beépülő modul lehetővé teszi a Java-alkalmazások üzembe helyezését.
keywords: Azure, app Service, Web App, Windows, Linux, Java, Maven, gyors útmutató
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 4041623d1c6ae464afd20e3beff753fb89e0a350
ms.sourcegitcommit: ada9a4a0f9d5dbb71fc397b60dc66c22cf94a08d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2020
ms.locfileid: "91405071"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Gyors útmutató: Java-alkalmazás létrehozása Azure App Service

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás.  Ez a rövid útmutató bemutatja, hogyan használhatja az [Azure CLI](/cli/azure/get-started-with-azure-cli) -t a [Mavenhez készült Azure Web App beépülő modullal](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) egy. jar-fájl vagy. War-fájl üzembe helyezéséhez. A lapok használatával válthat a Java SE és a Tomcat utasítások között.


> [!NOTE]
> Ugyanezt a népszerű ide-ket, például a IntelliJ és az Eclipse-et is megteheti. Tekintse meg a hasonló dokumentumokat a [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) rövid útmutatóban vagy [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app)gyors útmutatóban.


![Azure App Service futó minta alkalmazás](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Java-alkalmazás létrehozása

# <a name="java-se"></a>[Java SE](#tab/javase)

A [Spring Boot első lépések](https://github.com/spring-guides/gs-spring-boot) minta projekt klónozása.

```bash
git clone https://github.com/spring-guides/gs-spring-boot
```

Váltsa a könyvtárat a befejezett projektre.

```bash
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

A következő Maven-parancs végrehajtásával hozzon létre egy nevű új alkalmazást a Cloud Shell promptban `helloworld` :

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

Ezután módosítsa a munkakönyvtárat a projekt mappájába:

```bash
cd helloworld
```

---

## <a name="configure-the-maven-plugin"></a>A Maven beépülő moduljának konfigurálása

A Azure App Service üzembe helyezési folyamata automatikusan az Azure CLI-vel fogja használni az Azure-beli hitelesítő adatait. Ha az Azure CLI nincs helyileg telepítve, akkor a Maven beépülő modul a OAuth vagy az eszköz bejelentkezésével fog hitelesíteni. További információ: [hitelesítés a Maven beépülő](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication)modulokkal.

Futtassa az alábbi Maven-parancsot az üzemelő példány konfigurálásához. Ez a parancs segítséget nyújt a App Service operációs rendszer, a Java-verzió és a Tomcat-verzió beállításához.

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.11.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. Ha a rendszer megkérdezi az **előfizetést** , válassza ki a megfelelő értéket, ha `Subscription` beírja a kívánt számú nyomtatást a sor elején.
1. Ha a rendszer a **webalkalmazást** kéri, fogadja el a defaut lehetőséget az `<create>` ENTER billentyű lenyomásával.
1. Ha a rendszer az **operációs rendszer** beállítását kéri, válassza a **Windows** lehetőséget a beírásával `2` .
1. Használja az alapértelmezett Java-verziót (1,8) az ENTER billentyű lenyomásával.
1. Végül nyomja le az ENTER billentyűt az utolsó üzenetben, hogy erősítse meg a beállításokat.

    Az összefoglalás kimenete az alább látható kódrészlethez hasonlóan fog kinézni.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : westeurope
    PricingTier : PremiumV2_P1v2
    OS : Windows
    Java : 1.8
    WebContainer : java 8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Ha a rendszer megkérdezi az **előfizetést** , válassza ki a megfelelő értéket, ha `Subscription` beírja a kívánt számú nyomtatást a sor elején.
1. Ha a rendszer a **webalkalmazást** kéri, fogadja el a defaut lehetőséget az `<create>` ENTER billentyű lenyomásával.
1. Ha a rendszer az **operációs rendszer** beállítását kéri, válassza a **Windows** lehetőséget a beírásával `2` .
1. Használja az alapértelmezett Java-verziót (1,8) az ENTER billentyű lenyomásával.
1. A Tomcat 8,5 alapértelmezett webes tárolót az ENTER billentyű lenyomásával használhatja.
1. Végül nyomja le az ENTER billentyűt az utolsó üzenetben, hogy erősítse meg a beállításokat.

    Az összefoglalás kimenete az alább látható kódrészlethez hasonlóan fog kinézni.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : westeurope
    PricingTier : PremiumV2_P1v2
    OS : Windows
    Java : 1.8
    WebContainer : tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. Ha a rendszer megkérdezi az **előfizetést** , válassza ki a megfelelő értéket, ha `Subscription` beírja a kívánt számú nyomtatást a sor elején.
1. Ha a rendszer a **webalkalmazást** kéri, fogadja el a defaut lehetőséget az `<create>` ENTER billentyű lenyomásával.
1. Ha a rendszer az **operációs rendszer** beállítását kéri, az ENTER billentyű lenyomásával válassza a **Linux** lehetőséget.
1. Használja az alapértelmezett Java-verziót (1,8) az ENTER billentyű lenyomásával.
1. Végül nyomja le az ENTER billentyűt az utolsó üzenetben, hogy erősítse meg a beállításokat.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : westeurope
    PricingTier : PremiumV2_P1v2
    OS : Linux
    RuntimeStack : JAVA 8-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    ```

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Ha a rendszer megkérdezi az **előfizetést** , válassza ki a megfelelő értéket, ha `Subscription` beírja a kívánt számú nyomtatást a sor elején.
1. Ha a rendszer a **webalkalmazást** kéri, fogadja el a defaut lehetőséget az `<create>` ENTER billentyű lenyomásával.
1. Ha a rendszer az **operációs rendszer** beállítását kéri, az ENTER billentyű lenyomásával válassza a **Linux** lehetőséget.
1. Használja az alapértelmezett Java-verziót (1,8) az ENTER billentyű lenyomásával.
1. A Tomcat 8,5 alapértelmezett webes tárolót az ENTER billentyű lenyomásával használhatja.
1. Végül nyomja le az ENTER billentyűt az utolsó üzenetben, hogy erősítse meg a beállításokat.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : westeurope
    PricingTier : PremiumV2_P1v2
    OS : Linux
    RuntimeStack : TOMCAT 8.5-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end

Szükség esetén közvetlenül is módosíthatja App Service konfigurációit `pom.xml` . Néhány gyakori lista alább látható:

Tulajdonság | Kötelező | Leírás | Verzió
---|---|---|---
`<schemaVersion>` | hamis | Határozza meg a konfigurációs séma verzióját. A támogatott értékek a következők: `v1` , `v2` . | 1.5.2
`<subscriptionId>` | hamis | Határozza meg az előfizetés azonosítóját. | 0.1.0 +
`<resourceGroup>` | true | Azure-erőforráscsoport a webalkalmazáshoz. | 0.1.0 +
`<appName>` | true | A webalkalmazás neve. | 0.1.0 +
`<region>` | true | Meghatározza azt a régiót, ahol a webalkalmazás üzemeltetve lesz; az alapértelmezett érték a **westeurope**. A [támogatott régiók](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) szakaszban található összes érvényes régió. | 0.1.0 +
`<pricingTier>` | hamis | A webalkalmazás díjszabási szintje. Az alapértelmezett érték a **P1V2**.| 0.1.0 +
`<runtime>` | true | A futásidejű környezet konfigurációja a részleteket [itt](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)tekintheti meg. | 0.1.0 +
`<deployment>` | true | A központi telepítés konfigurálásával [itt](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)láthatja a részleteket. | 0.1.0 +

Ügyeljen arra, hogy a `<appName>` és a `<resourceGroup>` ( `helloworld-1590394316693` és `helloworld-1590394316693-rg` ennek megfelelően a bemutatóban) értékeit később is használni fogjuk.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>Az alkalmazás üzembe helyezése

A Maven beépülő modul a fiók hitelesítő adatait használja az Azure CLI-ből a App Services üzembe helyezéséhez. [A folytatás előtt jelentkezzen be az Azure CLI-vel](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) .

```azurecli
az login
```

Ezután az alábbi paranccsal telepítheti a Java-alkalmazást az Azure-ba.

```bash
mvn package azure-webapp:deploy
```

Az üzembe helyezés befejezése után az alkalmazás készen áll `http://<appName>.azurewebsites.net/` ( `http://helloworld-1590394316693.azurewebsites.net` a bemutatóban). Nyissa meg az URL-címet a helyi webböngészővel, és tekintse meg a következőt:

![Azure App Service futó minta alkalmazás](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Gratulálunk!** Az első Java-alkalmazás üzembe helyezése App Service.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Ha nem várható, hogy a jövőben szüksége lenne ezekre az erőforrásokra, törölje az erőforráscsoportot a portálról, vagy futtassa a következő parancsot a Cloud Shellban:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

A parancs futtatása egy percig is eltarthat.

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Kapcsolódás Azure SQL Database Javával](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)

> [!div class="nextstepaction"]
> [Kapcsolódás a MySQL-hez készült Azure DB-hez a Javával](../mysql/connect-java.md)

> [!div class="nextstepaction"]
> [Kapcsolódás a PostgreSQL-hez készült Azure-ADATBÁZIShoz Java használatával](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [Azure Java-fejlesztőknek – erőforrások](/java/azure/)

> [!div class="nextstepaction"]
> [Java-alkalmazás konfigurálása](configure-language-java.md)

> [!div class="nextstepaction"]
> [CI/CD – Jenkins](/azure/developer/jenkins/deploy-to-azure-app-service-using-plugin)

> [!div class="nextstepaction"]
> [Egyéni tartomány leképezése](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [További információ az Azure-hoz készült Maven beépülő modulokról](https://github.com/microsoft/azure-maven-plugins)
