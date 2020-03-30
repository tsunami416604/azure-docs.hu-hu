---
title: 'Rövid útmutató: Linux Java-alkalmazás létrehozása'
description: Az Azure App Service Linux-alkalmazásai üzembe helyezésével az első Java-alkalmazás egy Linux-tárolóba az App Service-ben.
keywords: az azure, app service, web app, linux, java, maven, quickstart
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 2192260c3106a6b8d3d3ffd0e063ee945734f308
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79136312"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-linux"></a>Rövid útmutató: Java-alkalmazás létrehozása linuxos Azure App Service szolgáltatáson

A [Linuxon futó App Service](app-service-linux-intro.md) hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával. Ez a rövid útmutató bemutatja, hogyan használhatja az [Azure CLI-t](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) az [Azure Web App Plugin for Maven segítségével](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) egy Java web archív (WAR) fájl üzembe helyezéséhez a Linux operációs rendszeren.

> [!NOTE]
>
> Ugyanez a dolog is lehet tenni a népszerű IDEs, mint az IntelliJ, Eclipse és vs kód. Tekintse meg hasonló dokumentumainkat az [Azure Toolkit for IntelliJ Quickstart](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app), [Azure Toolkit for Eclipse quickstart](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app) vagy [VS Code quickstart menüben.](https://code.visualstudio.com/docs/java/java-webapp)
>
![Az Azure App Service-ben futó mintaalkalmazás](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Java-alkalmazás létrehozása

A következő Maven parancs végrehajtása a Cloud Shell `helloworld`parancssorában egy új alkalmazás létrehozásához:

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp"
```
Ezután módosítsa a munkakönyvtárat a projekt mappára:

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>A Maven beépülő moduljának konfigurálása

Az Azure App Service üzembe helyezési folyamata az Azure CLI fiókhitelesítő adatait használja. A folytatás előtt [jelentkezzen be az Azure CLI-vel.](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

```azurecli
az login
```

Ezután konfigurálhatja a telepítést, futtathatja a maven parancsot a parancssorban, és használhatja az alapértelmezett konfigurációkat az **ENTER** billentyű lenyomásával, amíg meg nem kapja a **Confirm (Y/N)** parancsot, majd megnyomja az **"y"** gombot, és a konfiguráció befejeződött. 
```cmd
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.0:config
```
A mintafolyamat a következőképpen néz ki:

```cmd
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.0:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.9.0:config (default-cli) @ helloworld ---
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

> [!NOTE]
> Ebben a cikkben csak WAR-fájlokba csomagolt Java-alkalmazásokat használunk. Ez a beépülő modul támogatja a JAR-webalkalmazásokat is. Ennek kipróbálásához tekintse meg [a Java SE JAR-fájlok Linuxon futó App Service-ben való üzembe helyezését ismertető részt](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

Keresse `pom.xml` meg újra, hogy a plugin konfiguráció frissül, Módosíthatja más konfigurációk App Service közvetlenül a pom fájlt, ha szükséges, néhány gyakori is az alábbiakban felsorolt:

 Tulajdonság | Kötelező | Leírás | Verzió
---|---|---|---
`<schemaVersion>` | hamis | Adja meg a konfigurációs séma verzióját. A támogatott `v1`értékek `v2`a következők: , . | 1.5.2
`<resourceGroup>` | igaz | Azure Resource Group a webalkalmazáshoz. | 0.1.0+
`<appName>` | igaz | A webalkalmazás neve. | 0.1.0+
`<region>` | igaz | Megadja azt a régiót, ahol a webalkalmazás lesz tárolva; az alapértelmezett érték **westeurope**. A Támogatott régiók szakasz összes érvényes [régiója.](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) | 0.1.0+
`<pricingTier>` | hamis | A webalkalmazás tarifacsomagja. Az alapértelmezett érték **p1V2**.| 0.1.0+
`<runtime>` | igaz | A futásidejű környezet konfigurációja, akkor láthatjuk a részleteket [itt](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+
`<deployment>` | igaz | A központi telepítési konfiguráció, akkor láthatjuk a részleteket [itt](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=config)

## <a name="deploy-the-app"></a>Az alkalmazás üzembe helyezése

Helyezze üzembe a Java-alkalmazást az Azure-ban az alábbi paranccsal:

```bash
mvn package azure-webapp:deploy
```

Az üzembe helyezést követően keresse meg az üzembe helyezett alkalmazást a webböngészőjében a következő URL-cím használatával, például: `http://<webapp>.azurewebsites.net`. 

![Az Azure App Service-ben futó mintaalkalmazás](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Gratulálok!** Üzembe helyezte az első Java-webalkalmazását a Linuxon futó App Service-ben.

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Ha a jövőben nem várható, hogy szüksége lesz ezekre az erőforrásokra, törölje az erőforráscsoportot a portálról, vagy futassa a következő parancsot a Felhőrendszerhéjban:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

A parancs futtatása egy percig is eltarthat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Csatlakozás Azure SQL-adatbázishoz a Java segítségével](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Csatlakozás az Azure DB for MySQL-hez a Java segítségével](/azure/mysql/connect-java?toc=/azure/java/toc.json)

> [!div class="nextstepaction"]
> [Csatlakozás a PostgreSQL Azure DB-hez a Java segítségével](/azure/postgresql/connect-java?toc=/azure/java/toc.json)

> [!div class="nextstepaction"]
> [Java alkalmazás konfigurálása](configure-custom-container.md)

> [!div class="nextstepaction"]
> [CI/CD – Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Egyéb Azure for Java developers források](/java/azure/)

> [!div class="nextstepaction"]
> [További információ a Maven azure-beli beépülő modulokról](https://github.com/microsoft/azure-maven-plugins)
