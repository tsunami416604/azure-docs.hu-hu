---
title: 'Gyors útmutató: Java-alkalmazás létrehozása Windows rendszeren'
description: Percek alatt üzembe helyezheti az első Java-"Helló világ!" alkalmazás Azure App Service Windows rendszeren. A Mavenhez készült Azure Web App beépülő modul lehetővé teszi a Java-alkalmazások üzembe helyezését.
keywords: Azure, app Service, Web App, Windows, Java, Maven, gyors útmutató
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: d8f03d714ab44dc01d9e138a63a89892ead60fe9
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85249465"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-windows"></a>Gyors útmutató: Java-alkalmazás létrehozása Azure App Service Windows rendszeren

> [!NOTE]
> Ebben a cikkben egy alkalmazást helyezünk üzembe a Windowson futó App Service-ben. A _linuxon_app Service való üzembe helyezéssel kapcsolatban lásd: [Java-Webalkalmazás létrehozása Linuxon](./containers/quickstart-java.md).
>

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás.  Ez a rövid útmutató bemutatja, hogyan használhatja az [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) -t a [Mavenhez készült Azure Web App beépülő modullal](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) egy Java Web Archive-(War-) fájl telepítéséhez.

> [!NOTE]
> Ugyanezt a népszerű ide-ket is megteheti, például a IntelliJ és az Eclipse-et. Tekintse meg a hasonló dokumentumokat a [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) rövid útmutatóban vagy [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app)gyors útmutatóban.
>
![Azure App Service futó minta alkalmazás](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

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

Az üzembe helyezés folyamata Azure App Service az Azure CLI-vel automatikusan felveheti az Azure-beli hitelesítő adatait. Ha nem telepítette az Azure CLI-t, a Maven beépülő modul bejelentkezik a OAuth vagy az eszköz bejelentkezésével. Ha szükséges, tekintse meg a [Maven beépülő modulokkal történő hitelesítés](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication) részleteit.

Futtassa a következő Maven-parancsot a parancssorban a központi telepítés konfigurálásához, válassza a **"2"** lehetőséget az első lépésben a **Windows** operációs rendszerhez, majd fogadja el az alapértelmezett beállításokat az **ENTER** billentyű lenyomásával, amíg meg nem jelenik a **Confirm (y/N)** üzenet, majd nyomja le az **"y"** gombot, és a konfiguráció elkészült. 

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```

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
`<resourceGroup>` | igaz | Azure-erőforráscsoport a webalkalmazáshoz. | 0.1.0 +
`<appName>` | igaz | A webalkalmazás neve. | 0.1.0 +
`<region>` | igaz | Meghatározza azt a régiót, ahol a webalkalmazás üzemeltetve lesz; az alapértelmezett érték a **westeurope**. A [támogatott régiók](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) szakaszban található összes érvényes régió. | 0.1.0 +
`<pricingTier>` | hamis | A webalkalmazás díjszabási szintje. Az alapértelmezett érték a **P1V2**.| 0.1.0 +
`<runtime>` | igaz | A futásidejű környezet konfigurációja a részleteket [itt](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)tekintheti meg. | 0.1.0 +
`<deployment>` | igaz | A központi telepítés konfigurálásával [itt](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)láthatja a részleteket. | 0.1.0 +

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=app-service-web-get-started-java&step=config)

## <a name="deploy-the-app"></a>Az alkalmazás üzembe helyezése

A Azure App Service üzembe helyezési folyamata a fiók hitelesítő adatait használja az Azure CLI-ből. [A folytatás előtt jelentkezzen be az Azure CLI-vel](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) .

```azurecli
az login
```
Ezután az alábbi paranccsal telepítheti a Java-alkalmazást az Azure-ba:

```bash
mvn package azure-webapp:deploy
```

Az üzembe helyezést követően keresse meg az üzembe helyezett alkalmazást a webböngészőjében a következő URL-cím használatával, például: `http://<webapp>.azurewebsites.net/`.

![Azure App Service futó minta alkalmazás](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

**Gratulálunk!** Üzembe helyezte az első Java-alkalmazást, hogy App Service Windows rendszeren.

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
> [Egyéni tartomány leképezése](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [További információ az Azure-hoz készült Maven beépülő modulokról](https://github.com/microsoft/azure-maven-plugins)
