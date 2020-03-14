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
ms.openlocfilehash: 2192260c3106a6b8d3d3ffd0e063ee945734f308
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136312"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-linux"></a>Gyors útmutató: Java-alkalmazás létrehozása Azure App Service Linuxon

A [Linuxon futó App Service](app-service-linux-intro.md) hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával. Ez a rövid útmutató bemutatja, hogyan használhatja az [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) -t a [Mavenhez készült Azure Web App beépülő modullal](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) egy Java Web Archive-(War-) fájl Linux operációs rendszeren való üzembe helyezéséhez.

> [!NOTE]
>
> Ugyanezt is megteheti a népszerű ide-IntelliJ, az Eclipse és a VS Code használatával is. Tekintse meg a hasonló dokumentumokat [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app)rövid útmutató, [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app) gyors útmutató vagy a [vs Code](https://code.visualstudio.com/docs/java/java-webapp)gyors üzembe helyezése című útmutatóban.
>
![Azure App Service futó minta alkalmazás](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Java-alkalmazás létrehozása

Hajtsa végre a következő Maven-parancsot a Cloud Shell promptban egy új, `helloworld`nevű alkalmazás létrehozásához:

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp"
```
Ezután módosítsa a munkakönyvtárat a projekt mappájába:

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>A Maven beépülő moduljának konfigurálása

A Azure App Service üzembe helyezési folyamata a fiók hitelesítő adatait használja az Azure CLI-ből. [A folytatás előtt jelentkezzen be az Azure CLI-vel](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) .

```azurecli
az login
```

Ezután konfigurálhatja a központi telepítést, futtathatja a Maven parancsot a parancssorban, és az alapértelmezett konfigurációkat az **ENTER** billentyű lenyomásával állíthatja be, amíg meg nem kapja a **Confirm (i/N)** kérést, majd nyomja le az **"y"** gombot, és a konfiguráció elkészült. 
```cmd
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.0:config
```
Egy mintavételi folyamat A következőképpen néz ki:

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

A beépülő modul konfigurációjának megtekintéséhez navigáljon `pom.xml` újra, ha szükséges, módosíthatja a App Service egyéb konfigurációit is, ha szükséges, néhány gyakori elem az alábbi listában látható:

 Tulajdonság | Kötelező | Leírás | Verzió
---|---|---|---
`<schemaVersion>` | false | Határozza meg a konfigurációs séma verzióját. A támogatott értékek a következők: `v1`, `v2`. | 1.5.2
`<resourceGroup>` | true | Azure-erőforráscsoport a webalkalmazáshoz. | 0.1.0 +
`<appName>` | true | A webalkalmazás neve. | 0.1.0 +
`<region>` | true | Meghatározza azt a régiót, ahol a webalkalmazás üzemeltetve lesz; az alapértelmezett érték a **westeurope**. A [támogatott régiók](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) szakaszban található összes érvényes régió. | 0.1.0 +
`<pricingTier>` | false | A webalkalmazás díjszabási szintje. Az alapértelmezett érték a **P1V2**.| 0.1.0 +
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

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Ha nem várható, hogy a jövőben szüksége lenne ezekre az erőforrásokra, törölje az erőforráscsoportot a portálról, vagy futtassa a következő parancsot a Cloud Shellban:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

A parancs futtatása egy percig is eltarthat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Kapcsolódás az Azure SQL Database-hez a Javával](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Kapcsolódás a MySQL-hez készült Azure DB-hez a Javával](/azure/mysql/connect-java?toc=/azure/java/toc.json)

> [!div class="nextstepaction"]
> [Kapcsolódás a PostgreSQL-hez készült Azure-ADATBÁZIShoz Java használatával](/azure/postgresql/connect-java?toc=/azure/java/toc.json)

> [!div class="nextstepaction"]
> [Java-alkalmazás konfigurálása](configure-custom-container.md)

> [!div class="nextstepaction"]
> [CI/CD a Jenkins-szel](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Egyéb Azure Java-fejlesztői erőforrásokhoz](/java/azure/)

> [!div class="nextstepaction"]
> [További információ az Azure-hoz készült Maven beépülő modulokról](https://github.com/microsoft/azure-maven-plugins)
