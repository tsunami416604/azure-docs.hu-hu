---
title: Java-webalkalmazás létrehozása linuxon – az Azure App Service-ben
description: Ezzel a rövid útmutatóval percek alatt üzembe helyezheti első Hello World Java-alkalmazását a Linuxon futó Azure App Service-ben.
keywords: azure, app service, web app, linux, java, maven, quickstart
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: c97d4a373970514b920581aa258b61c1b1cb978c
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684004"
---
# <a name="quickstart-create-a-java-app-in-app-service-on-linux"></a>Gyors útmutató: Egy Java-alkalmazás létrehozása Linuxon futó App Service-ben

A [Linuxon futó App Service](app-service-linux-intro.md) hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával. Ez a rövid útmutató bemutatja, hogyan használhatja a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) az a [Azure App Service-ben készült maven bővítmény](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) üzembe Java web archive-(WAR-) fájlt.
> [!NOTE]
>
> Ugyanezt megteheti is népszerű, például az IntelliJ és Eclipse ide használatával. Tekintse meg a következő hasonló dokumentumok [intellij-vel a rövid útmutató az Azure-eszközkészlet](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) vagy [eclipse-ben a rövid útmutató az Azure-eszközkészlet](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![Az Azure-ban futó mintaalkalmazás](media/quickstart-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Java-alkalmazás létrehozása

Hajtsa végre a következő Maven-parancsot a Cloud Shell használatával hozzon létre egy új alkalmazást nevű `helloworld`:

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
    <!-- Deploy to Tomcat in App Service Linux           -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.5.4</version>
        <configuration>
            <!-- Specify v2 schema -->
            <schemaVersion>v2</schemaVersion>
            <!-- App information -->
            <subscriptionId>${SUBSCRIPTION_ID}</subscriptionId>
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
   
            <!-- Java Runtime Stack for App on Linux-->
            <runtime>
                <os>linux</os>
                <javaVersion>jre8</javaVersion>
                <webContainer>tomcat 8.5</webContainer>
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
| `SUBSCRIPTION_ID` | Egyedi azonosítója annak az előfizetésnek az alkalmazást telepíteni kívánja. Alapértelmezett előfizetés-azonosító megtalálja a Cloud Shellben vagy a parancssori felület használatával a `az account show` parancsot. Az összes rendelkezésre álló előfizetések, használja a `az account list` parancsot.|
| `RESOURCEGROUP_NAME` | Az új erőforráscsoport, amelyben létrehozza az alkalmazás neve. Ha egy alkalmazás összes erőforrását egy csoportban helyezi el, akkor mindet együtt kezelheti. Az erőforráscsoport törlésével például az alkalmazáshoz társított összes erőforrást törli. Frissítse ezt az értéket egy egyedi új erőforráscsoport-névre, például *TestResources*. Ezt az erőforráscsoport-nevet használjuk egy későbbi szakaszban az összes Azure-erőforrás eltávolításához. |
| `WEBAPP_NAME` | Az alkalmazás neve lesz az állomásnév az alkalmazás telepítésekor az Azure-ba (WEBAPP_NAME.azurewebsites.net) rész. Frissítse ezt az értéket fogja futtatni a Java-alkalmazás, például új App Service-alkalmazás egyedi nevet *contoso*. |
| `REGION` | Ha az alkalmazás üzemel, például egy Azure-régió `westus2`. A régiók listáját az `az account list-locations` paranccsal, a Cloud Shellben vagy a CLI-ben kérheti le. |

## <a name="deploy-the-app"></a>Az alkalmazás üzembe helyezése

Helyezze üzembe a Java-alkalmazást az Azure-ban az alábbi paranccsal:

```bash
mvn package azure-webapp:deploy
```

Az üzembe helyezést követően keresse meg az üzembe helyezett alkalmazást a webböngészőjében a következő URL-cím használatával, például: `http://<webapp>.azurewebsites.net/helloworld`. 

![Az Azure-ban futó mintaalkalmazás](media/quickstart-java/java-hello-world-in-browser-curl.png)

**Gratulálunk!** Üzembe helyezte az első Java-webalkalmazását a Linuxon futó App Service-ben.

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Java-vállalati alkalmazás és PostgreSQL](tutorial-java-enterprise-postgresql-app.md)

> [!div class="nextstepaction"]
> [Java-alkalmazás konfigurálása](configure-custom-container.md)

> [!div class="nextstepaction"]
> [CI/CD a jenkins-szel](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Más Azure Java fejlesztői erőforrások](/java/azure/)
