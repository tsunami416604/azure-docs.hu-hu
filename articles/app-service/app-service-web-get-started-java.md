---
title: 'Rövid útmutató: Java-alkalmazás létrehozása Windows rendszeren'
description: Az első Java Hello World telepítése az Azure App Service-be a Windows rendszeren percek alatt. Az Azure Web App Plugin for Maven kényelmessé teszi a Java-alkalmazások telepítését.
keywords: az azure, app service, web app, windows, java, maven, quickstart
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 6681b2688c7e8884a197ebe27fb784b1a195f4b5
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732166"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-windows"></a>Rövid útmutató: Java-alkalmazás létrehozása a Windows Azure App Service szolgáltatásában

> [!NOTE]
> Ebben a cikkben egy alkalmazást helyezünk üzembe a Windowson futó App Service-ben. A _Linuxon_lévő App Service szolgáltatásba való telepítésről a [Java webalkalmazás létrehozása Linux on](./containers/quickstart-java.md)című témakörben talál.
>

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás.  Ez a rövid útmutató bemutatja, hogyan használhatja az [Azure CLI-t](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) az [Azure Web App Plugin for Maven egy](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) Java web archív (WAR) fájl üzembe helyezéséhez.

> [!NOTE]
> Ugyanez a dolog is lehet tenni a népszerű IDEs, mint az IntelliJ és eclipse. Tekintse meg hasonló dokumentumainkat az [Azure Toolkit for IntelliJ Quickstart](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) vagy [az Azure Toolkit for Eclipse quickstart menüben.](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app)
>
![Az Azure App Service-ben futó mintaalkalmazás](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Java-alkalmazás létrehozása

A következő Maven parancs végrehajtása a Cloud Shell `helloworld`parancssorában egy új alkalmazás létrehozásához:

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>A Maven beépülő moduljának konfigurálása

A Mavenből való üzembe helyezéshez használja a Cloud Shell kódszerkesztőjét a `helloworld` könyvtár `pom.xml` projektfájljának megnyitásához. 

```bash
code pom.xml
```

Ezután adja hozzá a következő bővítménydefiníciót a `pom.xml` fájl `<build>` részéhez.

```xml
<plugins>
    <!--*************************************************-->
    <!-- Deploy to Tomcat in App Service Windows         -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.9.0</version>
        <configuration>
            <!-- Specify v2 schema -->
            <schemaVersion>v2</schemaVersion>
            <!-- App information -->
            <subscriptionId>SUBSCRIPTION_ID</subscriptionId>
            <resourceGroup>RESOURCEGROUP_NAME</resourceGroup>
            <appName>WEBAPP_NAME</appName>
            <region>REGION</region>
            <!-- Java Runtime Stack for App Service on Windows-->
            <runtime>
                <os>windows</os>
                <javaVersion>1.8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>
            <deployment>
                <resources>
                    <resource>
                        <directory>${project.basedir}/target</directory>
                        <includes>
                            <include>*.war</include>
                        </includes>
                    </resource>
                </resources>
            </deployment>
        </configuration>
    </plugin>
</plugins>
```

> [!NOTE]
> Ebben a cikkben csak WAR-fájlokba csomagolt Java-alkalmazásokat használunk. Ez a beépülő modul támogatja a JAR-webalkalmazásokat is. Ennek kipróbálásához tekintse meg [a Java SE JAR-fájlok Linuxon futó App Service-ben való üzembe helyezését ismertető részt](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).


Frissítse a következő helyőrzőket a bővítmény konfigurációjában:

| Helyőrző | Leírás |
| ----------- | ----------- |
| `SUBSCRIPTION_ID` | Annak az előfizetésnek az egyedi azonosítója, amelybe telepíteni szeretné az alkalmazást. Az alapértelmezett előfizetés azonosítója megtalálható a Cloud Shell vagy `az account show` a CLI a parancs segítségével. Az összes elérhető előfizetéshez `az account list` használja a parancsot.|
| `RESOURCEGROUP_NAME` | Annak az új erőforráscsoportnak a neve, amelyben az alkalmazást létre szeretné hozni. Ha egy alkalmazás összes erőforrását egy csoportban helyezi el, akkor mindet együtt kezelheti. Az erőforráscsoport törlésével például az alkalmazáshoz társított összes erőforrást törli. Frissítse ezt az értéket egy egyedi új erőforráscsoport névvel, például *a myResourceGroup értékkel.* Ezt az erőforráscsoport-nevet használjuk egy későbbi szakaszban az összes Azure-erőforrás eltávolításához. |
| `WEBAPP_NAME` | Az alkalmazás neve az Azure-ba (WEBAPP_NAME.azurewebsites.net) üzembe helyezve az alkalmazás állomásnevének része lesz. Frissítse ezt az értéket az új App Service alkalmazás egyedi nevével, amely a Java alkalmazást fogja üzemeltetni, például *contoso.* |
| `REGION` | Egy Azure-régió, ahol az alkalmazás található, például *westus2.* A régiók listáját az `az account list-locations` paranccsal, a Cloud Shellben vagy a CLI-ben kérheti le. |

## <a name="deploy-the-app"></a>Az alkalmazás üzembe helyezése

Helyezze üzembe a Java-alkalmazást az Azure-ban az alábbi paranccsal:

```bash
mvn package azure-webapp:deploy
```

Az üzembe helyezést követően keresse meg az üzembe helyezett alkalmazást a webböngészőjében a következő URL-cím használatával, például: `http://<webapp>.azurewebsites.net/`.

![Az Azure App Service-ben futó mintaalkalmazás](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

**Gratulálok!** Az első Java-alkalmazást a Windows App Service szolgáltatásba telepítette.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Csatlakozás Azure SQL-adatbázishoz a Java segítségével](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Csatlakozás az Azure DB for MySQL-hez a Java segítségével](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [Csatlakozás a PostgreSQL Azure DB-hez a Java segítségével](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [Az Azure for Java fejlesztői erőforrások](/java/azure/)

> [!div class="nextstepaction"]
> [Egyéni tartomány leképezése](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [További információ a Maven azure-beli beépülő modulokról](https://github.com/microsoft/azure-maven-plugins)
