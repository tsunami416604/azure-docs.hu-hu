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
ms.openlocfilehash: 8289b21da5009459d2eb7ddc8d26b549f0920317
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88085167"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Gyors útmutató: Java-alkalmazás létrehozása Azure App Service

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás.  Ez a rövid útmutató bemutatja, hogyan használhatja az [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) -t a [Mavenhez készült Azure Web App beépülő modullal](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) egy Java Web Archive-(War-) fájl telepítéséhez.

> [!NOTE]
> Ugyanezt a népszerű ide-ket, például a IntelliJ és az Eclipse-et is megteheti. Tekintse meg a hasonló dokumentumokat a [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) rövid útmutatóban vagy [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app)gyors útmutatóban.
>
![Azure App Service futó minta alkalmazás](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Java-alkalmazás létrehozása

A következő Maven-parancs végrehajtásával hozzon létre egy nevű új alkalmazást a Cloud Shell promptban `helloworld` :

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

Ezután módosítsa a munkakönyvtárat a projekt mappájába:

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>A Maven beépülő moduljának konfigurálása

A Azure App Service üzembe helyezése az Azure CLI-ből automatikusan kiválaszthatja az Azure-beli hitelesítő adatait. A Maven beépülő modul a OAuth vagy az eszköz bejelentkezését fogja bejelentkezni, ha az Azure CLI nincs helyileg telepítve. Ha szükséges, tekintse meg a [Maven beépülő modulokkal történő hitelesítés](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication) részleteit.

Az alábbi Maven-parancs futtatásával konfigurálhatja az üzemelő példányt
```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```

::: zone pivot="platform-windows"  
A rendszer felkéri, hogy válasszon 
* **Operációs rendszer (alapértelmezett: `linux` )**
* **Java-verzió (alapértelmezett: `1.8` )**
* **Webes tároló (alapértelmezett: `tomcat 8.5` )** 

Ügyeljen arra, hogy a **`2`** **Windows** operációs rendszer első lépéseként válassza ki a kívánt adatokat. A többi konfiguráció az **ENTER**billentyű lenyomásával hagyható el. Végül nyomja meg **`Y`** a **Confirm (i/N)** promptot a konfigurálás befejezéséhez.

Egy mintavételi folyamat A következőképpen néz ki:

```console
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.9.1:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use: 2
Define value for javaVersion(Default: 1.8): 
1. 1.7
2. 1.7.0_191_ZULU
3. 1.7.0_51
4. 1.7.0_71
5. 1.7.0_80
6. 1.8 [*]
7. 1.8.0_102
8. 1.8.0_111
9. 1.8.0_144
10. 1.8.0_172
11. 1.8.0_172_ZULU
12. 1.8.0_181
13. 1.8.0_181_ZULU
14. 1.8.0_202
15. 1.8.0_202_ZULU
16. 1.8.0_25
17. 1.8.0_60
18. 1.8.0_73
19. 1.8.0_92
20. 11
21. 11.0.2_ZULU
Enter index to use:
Define value for webContainer(Default: tomcat 8.5): 
1. jetty 9.1
2. jetty 9.1.0.20131115
3. jetty 9.3
4. jetty 9.3.13.20161014
5. tomcat 7.0
6. tomcat 7.0.50
7. tomcat 7.0.62
8. tomcat 8.0
9. tomcat 8.0.23
10. tomcat 8.5 [*]
11. tomcat 8.5.20
12. tomcat 8.5.31
13. tomcat 8.5.34
14. tomcat 8.5.37
15. tomcat 8.5.6
16. tomcat 9.0
17. tomcat 9.0.0
18. tomcat 9.0.12
19. tomcat 9.0.14
20. tomcat 9.0.8
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1590394316693
ResourceGroup : helloworld-1590394316693-rg
Region : westeurope
PricingTier : PremiumV2_P1v2
OS : Windows
Java : 1.8
WebContainer : tomcat 8.5
Deploy to slot : false
Confirm (Y/N)? :
[INFO] Saving configuration to pom.
```
::: zone-end
::: zone pivot="platform-linux"  
Egy mintavételi folyamat A következőképpen néz ki:

```cmd
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.9.1:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use:
Define value for javaVersion(Default: jre8):
1. Java 11
2. Java 8 [*]
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. TOMCAT 8.5 [*]
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1558400876966
ResourceGroup : helloworld-1558400876966-rg
Region : westeurope
PricingTier : Premium_P1V2
OS : Linux
RuntimeStack : TOMCAT 8.5-jre8
Deploy to slot : false
Confirm (Y/N)? : Y
```
::: zone-end

> [!NOTE]
> Ebben a cikkben csak WAR-fájlokba csomagolt Java-alkalmazásokat használunk. Ez a beépülő modul támogatja a JAR-webalkalmazásokat is. Ennek kipróbálásához tekintse meg [a Java SE JAR-fájlok Linuxon futó App Service-ben való üzembe helyezését ismertető részt](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

`pom.xml`A frissített konfiguráció megjelenítéséhez nyissa meg a következőt:.

```bash
code pom.xml
```

Szükség esetén közvetlenül a Pom-fájlban módosíthatja a App Service konfigurációit, néhány gyakori érték az alábbiak szerint jelenik meg:

 Tulajdonság | Kötelező | Leírás | Verzió
---|---|---|---
`<schemaVersion>` | hamis | Határozza meg a konfigurációs séma verzióját. A támogatott értékek a következők: `v1` , `v2` . | 1.5.2
`<resourceGroup>` | true | Azure-erőforráscsoport a webalkalmazáshoz. | 0.1.0 +
`<appName>` | true | A webalkalmazás neve. | 0.1.0 +
`<region>` | true | Meghatározza azt a régiót, ahol a webalkalmazás üzemeltetve lesz; az alapértelmezett érték a **westeurope**. A [támogatott régiók](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) szakaszban található összes érvényes régió. | 0.1.0 +
`<pricingTier>` | hamis | A webalkalmazás díjszabási szintje. Az alapértelmezett érték a **P1V2**.| 0.1.0 +
`<runtime>` | true | A futásidejű környezet konfigurációja a részleteket [itt](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)tekintheti meg. | 0.1.0 +
`<deployment>` | true | A központi telepítés konfigurálásával [itt](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)láthatja a részleteket. | 0.1.0 +

::: zone pivot="platform-windows"
Ügyeljen arra, hogy a `<appName>` és a `<resourceGroup>` ( `helloworld-1590394316693` és `helloworld-1590394316693-rg` ennek megfelelően a bemutatóban) értékeit később is használni fogjuk.
::: zone-end
::: zone pivot="platform-linux"
::: zone-end
> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>Az alkalmazás üzembe helyezése

A Azure App Service üzembe helyezési folyamata a fiók hitelesítő adatait használja az Azure CLI-ből. [A folytatás előtt jelentkezzen be az Azure CLI-vel](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) .

```azurecli
az login
```
Ezután az alábbi paranccsal telepítheti a Java-alkalmazást az Azure-ba:

```bash
mvn package azure-webapp:deploy
```

::: zone pivot="platform-windows"
Az üzembe helyezés befejezése után az alkalmazás készen áll `http://<appName>.azurewebsites.net/` ( `http://helloworld-1590394316693.azurewebsites.net` a bemutatóban). Nyissa meg az URL-címet a helyi webböngészővel, és tekintse meg a következőt:

![Azure App Service futó minta alkalmazás](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

**Gratulálunk!** Üzembe helyezte az első Java-alkalmazást, hogy App Service Windows rendszeren.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]
::: zone-end
::: zone pivot="platform-linux"
Az üzembe helyezést követően keresse meg az üzembe helyezett alkalmazást a webböngészőjében a következő URL-cím használatával, például: `http://<webapp>.azurewebsites.net`. 

![Azure App Service futó minta alkalmazás](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Gratulálunk!** Üzembe helyezte az első Java-webalkalmazását a Linuxon futó App Service-ben.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Ha nem várható, hogy a jövőben szüksége lenne ezekre az erőforrásokra, törölje az erőforráscsoportot a portálról, vagy futtassa a következő parancsot a Cloud Shellban:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

A parancs futtatása egy percig is eltarthat.
::: zone-end

Az üzembe helyezés befejezése után az alkalmazás készen áll `http://<appName>.azurewebsites.net/` ( `http://helloworld-1590394316693.azurewebsites.net` a bemutatóban). Nyissa meg az URL-címet a helyi webböngészővel, és tekintse meg a következőt:

![Azure App Service futó minta alkalmazás](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Gratulálunk!** Az első Java-alkalmazás üzembe helyezése App Service.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?quickstart-java&step=deploy)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]


## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Kapcsolódás Azure SQL Database Javával](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Kapcsolódás a MySQL-hez készült Azure DB-hez a Javával](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [Kapcsolódás a PostgreSQL-hez készült Azure-ADATBÁZIShoz Java használatával](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [Azure Java-fejlesztőknek – erőforrások](/java/azure/)

> [!div class="nextstepaction"]
> [Java-alkalmazás konfigurálása](configure-language-java.md)

> [!div class="nextstepaction"]
> [CI/CD – Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Egyéni tartomány leképezése](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [További információ az Azure-hoz készült Maven beépülő modulokról](https://github.com/microsoft/azure-maven-plugins)