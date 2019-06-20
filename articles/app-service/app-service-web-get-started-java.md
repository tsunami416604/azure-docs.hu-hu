---
title: Java-webalkalmazás létrehozása a Windows - Azure App Service-ben
description: Ez a rövid útmutatóban az első Hello World Java az Azure App Service, a Windows percek alatt üzembe.
keywords: Azure, az app service, webalkalmazás, windows, a java, maven, a rövid útmutató
services: app-service\web
documentationcenter: ''
author: msangapu-msft
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jasonfreeberg
ms.custom: mvc
ms.openlocfilehash: bd11b5334fbffc28aa6869c9f37b2b9909692d56
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154792"
---
# <a name="quickstart-create-a-java-app-in-app-service"></a>Gyors útmutató: Hozzon létre egy Java-alkalmazás az App Service-ben

> [!NOTE]
> Ebben a cikkben egy alkalmazást helyezünk üzembe a Windowson futó App Service-ben. Az App Service-ben üzembe _Linux_, lásd: [létrehozása a Java-webalkalmazást Linux rendszeren](./containers/quickstart-java.md).
>

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás.  Ez a rövid útmutató bemutatja, hogyan használhatja a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) az a [Azure App Service-ben készült maven bővítmény](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) üzembe Java web archive-(WAR-) fájlt.

> [!NOTE]
> Ugyanezt megteheti is népszerű, például az IntelliJ és Eclipse ide használatával. Tekintse meg a következő hasonló dokumentumok [intellij-vel a rövid útmutató az Azure-eszközkészlet](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) vagy [eclipse-ben a rövid útmutató az Azure-eszközkészlet](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![Az Azure-ban futó mintaalkalmazás](./media/app-service-web-get-started-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

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
    <!-- Deploy to Tomcat in App Service Windows         -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.6.0</version>
        <configuration>
            <!-- Specify v2 schema -->
            <schemaVersion>v2</schemaVersion>
            <!-- App information -->
            <subscriptionId>${SUBSCRIPTION_ID}</subscriptionId>
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
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
| `SUBSCRIPTION_ID` | Egyedi azonosítója annak az előfizetésnek az alkalmazást telepíteni kívánja. Alapértelmezett előfizetés-azonosító megtalálja a Cloud Shellben vagy a parancssori felület használatával a `az account show` parancsot. Az összes rendelkezésre álló előfizetések, használja a `az account list` parancsot.|
| `RESOURCEGROUP_NAME` | Az új erőforráscsoport, amelyben létrehozza az alkalmazás neve. Ha egy alkalmazás összes erőforrását egy csoportban helyezi el, akkor mindet együtt kezelheti. Az erőforráscsoport törlésével például az alkalmazáshoz társított összes erőforrást törli. Frissítse ezt az értéket egy egyedi új erőforráscsoport-nevet, például *myResourceGroup*. Ezt az erőforráscsoport-nevet használjuk egy későbbi szakaszban az összes Azure-erőforrás eltávolításához. |
| `WEBAPP_NAME` | Az alkalmazás nevét az alkalmazás az Azure-ba (WEBAPP_NAME.azurewebsites.net) telepítésekor az állomás neve része lesz. Frissítse ezt az értéket fogja futtatni a Java-alkalmazás, például új App Service-alkalmazás egyedi nevet *contoso*. |
| `REGION` | Ha az alkalmazás üzemel, például egy Azure-régió *westus2*. A régiók listáját az `az account list-locations` paranccsal, a Cloud Shellben vagy a CLI-ben kérheti le. |

## <a name="deploy-the-app"></a>Az alkalmazás üzembe helyezése

Helyezze üzembe a Java-alkalmazást az Azure-ban az alábbi paranccsal:

```bash
mvn package azure-webapp:deploy
```

Az üzembe helyezést követően keresse meg az üzembe helyezett alkalmazást a webböngészőjében a következő URL-cím használatával, például: `http://<webapp>.azurewebsites.net/`.

![Az Azure-ban futó mintaalkalmazás](./media/app-service-web-get-started-java/java-hello-world-in-browser.png)

**Gratulálunk!** App Service-ben Windows elvégezte az első Java-alkalmazás üzembe helyezését.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Java fejlesztői erőforrások](/java/azure/)

> [!div class="nextstepaction"]
> [Egyéni tartomány leképezése](app-service-web-tutorial-custom-domain.md)