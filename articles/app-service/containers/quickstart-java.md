---
title: 'Gyors útmutató: linuxos Java-alkalmazás létrehozása'
description: Ismerkedjen meg a Linux-alkalmazásokkal Azure App Service az első Java-alkalmazás üzembe helyezésével a App Service Linux-tárolójában.
keywords: Azure, app Service, Web App, Linux, Java, Maven, gyors útmutató
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 0c872865cec4dbea75a9adfee62c26e7c4d40763
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87056413"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-linux"></a>Gyors útmutató: Java-alkalmazás létrehozása Azure App Service Linuxon

A [Linuxon futó App Service](app-service-linux-intro.md) hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával. Ez a rövid útmutató bemutatja, hogyan használhatja az [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) -t a [Mavenhez készült Azure Web App beépülő modullal](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) egy Java Web Archive-(War-) fájl Linux operációs rendszeren való üzembe helyezéséhez.

> [!NOTE]
>
> Ugyanezt is megteheti a népszerű ide-IntelliJ, az Eclipse és a VS Code használatával is. Tekintse meg a hasonló dokumentumokat [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app)rövid útmutató, [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app) gyors útmutató vagy a [vs Code](https://code.visualstudio.com/docs/java/java-webapp)gyors üzembe helyezése című útmutatóban.
>
![Azure App Service futó minta alkalmazás](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

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

Az üzembe helyezés folyamata Azure App Service az Azure CLI-vel automatikusan felveheti az Azure-beli hitelesítő adatait. Ha nem telepítette az Azure CLI-t, a Maven beépülő modul bejelentkezik a OAuth vagy az eszköz bejelentkezésével. Ha szükséges, tekintse meg a [Maven beépülő modulokkal történő hitelesítés](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication) részleteit.

A központi telepítés konfigurálásához futtassa a Maven parancsot a parancssorban, és használja az alapértelmezett konfigurációkat az **ENTER** billentyű lenyomásával, amíg meg nem jelenik a **Confirm (y/N)** üzenet, majd nyomja le az **"y"** gombot, és a konfiguráció elkészült. 
```cmd
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```
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

> [!NOTE]
> Ebben a cikkben csak WAR-fájlokba csomagolt Java-alkalmazásokat használunk. Ez a beépülő modul támogatja a JAR-webalkalmazásokat is. Ennek kipróbálásához tekintse meg [a Java SE JAR-fájlok Linuxon futó App Service-ben való üzembe helyezését ismertető részt](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

A Megnyitás gombra `pom.xml` kattintva megtekintheti a frissített konfigurációt.

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

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=config)

## <a name="deploy-the-app"></a>Az alkalmazás üzembe helyezése

Helyezze üzembe a Java-alkalmazást az Azure-ban az alábbi paranccsal:

```bash
mvn package azure-webapp:deploy
```

Az üzembe helyezést követően keresse meg az üzembe helyezett alkalmazást a webböngészőjében a következő URL-cím használatával, például: `http://<webapp>.azurewebsites.net`. 

![Azure App Service futó minta alkalmazás](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Gratulálunk!** Üzembe helyezte az első Java-webalkalmazását a Linuxon futó App Service-ben.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Ha nem várható, hogy a jövőben szüksége lenne ezekre az erőforrásokra, törölje az erőforráscsoportot a portálról, vagy futtassa a következő parancsot a Cloud Shellban:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

A parancs futtatása egy percig is eltarthat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kapcsolódás Azure SQL Database Javával](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Kapcsolódás a MySQL-hez készült Azure DB-hez a Javával](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [Kapcsolódás a PostgreSQL-hez készült Azure-ADATBÁZIShoz Java használatával](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [Java-alkalmazás konfigurálása](configure-language-java.md)

> [!div class="nextstepaction"]
> [CI/CD – Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Egyéb Azure Java-fejlesztői erőforrásokhoz](/java/azure/)

> [!div class="nextstepaction"]
> [További információ az Azure-hoz készült Maven beépülő modulokról](https://github.com/microsoft/azure-maven-plugins)
