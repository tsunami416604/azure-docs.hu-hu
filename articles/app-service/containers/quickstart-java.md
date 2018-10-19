---
title: Rövid útmutató Java-webalkalmazás a Linuxon futó Azure App Service-ben történő létrehozásához
description: Ezzel a rövid útmutatóval percek alatt üzembe helyezheti első Hello World Java-alkalmazását a Linuxon futó Azure App Service-ben.
services: app-service\web
documentationcenter: ''
author: msangapu
manager: cfowler
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/07/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 92dde19e42c1adb6d83e1708106f844f228e8989
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239350"
---
# <a name="quickstart-create-a-java-web-app-in-app-service-on-linux"></a>Rövid útmutató: Java-webalkalmazás létrehozása a Linuxon futó App Service-ben

A [Linuxon futó App Service](app-service-linux-intro.md) hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával. Ez a rövid útmutató bemutatja, hogyan használható az [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) és az [Azure Web Apps előzetes verziójához készült Maven bővítmény](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) egy Java-webalkalmazás webarchívumfájljának üzembe helyezéséhez.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Java-alkalmazás létrehozása

Hajtsa végre a következő Maven-parancsot a Cloud Shellben egy új, `helloworld` nevű webalkalmazás létrehozásához:

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
        <version>1.4.0</version>
        <configuration>
   
            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
   
            <!-- Java Runtime Stack for Web App on Linux-->
            <linuxRuntime>tomcat 8.5-jre8</linuxRuntime>
   
        </configuration>
    </plugin>
</plugins>
```    


> [!NOTE] 
> Ebben a cikkben csak WAR-fájlokba csomagolt Java-alkalmazásokat használunk. A beépülő modul a JAR webalkalmazásokat is támogatja. Ezekhez az alkalmazásokhoz a következő másodlagos bővítménydefiníciókat használhatja. Ez a konfiguráció egy Mavenben fejlesztett JAR-fájlt helyez üzembe a helyi fájlrendszer `${project.build.directory}/${project.build.finalName}.jar` helyén.
>
>```xml
><plugin>
>            <groupId>com.microsoft.azure</groupId>
>            <artifactId>azure-webapp-maven-plugin</artifactId>
>            <version>1.4.0</version>
>            <configuration>
>                <deploymentType>jar</deploymentType>
>
>           <!-- Web App information -->
>            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
>            <appName>${WEBAPP_NAME}</appName>
>            <region>${REGION}</region>  
>
>                <!-- Java Runtime Stack for Web App on Linux-->
>                <linuxRuntime>jre8</linuxRuntime>
>            </configuration>
>         </plugin>
>```    


Frissítse a következő helyőrzőket a bővítmény konfigurációjában:

| Helyőrző | Leírás |
| ----------- | ----------- |
| `RESOURCEGROUP_NAME` | Az új erőforráscsoport neve, amelyben létrehozza a webalkalmazást. Ha egy alkalmazás összes erőforrását egy csoportban helyezi el, akkor mindet együtt kezelheti. Az erőforráscsoport törlésével például az alkalmazáshoz társított összes erőforrást törli. Frissítse ezt az értéket egy egyedi új erőforráscsoport-névre, például *TestResources*. Ezt az erőforráscsoport-nevet használjuk egy későbbi szakaszban az összes Azure-erőforrás eltávolításához. |
| `WEBAPP_NAME` | Az alkalmazásnév az alkalmazás gazdagépnevének része lesz az Azure-beli üzembe helyezést követően (WEBAPP_NAME.azurewebsites.net). Frissítse ezt az értéket egy egyedi névre, amelyet a Java-alkalmazást futtató új Azure-webalkalmazás kap, például: *contoso*. |
| `REGION` | Az Azure-régió, ahol a webalkalmazást üzemeltetik, például `westus2`. A régiók listáját az `az account list-locations` paranccsal, a Cloud Shellben vagy a CLI-ben kérheti le. |

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

Ebben az útmutatóban egy Java-webalkalmazást hozott létre a Mavennel, konfigurálta az [Azure Web Apps előzetes verziójához készült Maven bővítményt](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin), majd üzembe helyezett egy webarchívumba csomagolt Java-webalkalmazást a Linuxon futó App Service-ben. A Java és az Azure használatával kapcsolatosan további tudnivalókat az alábbi hivatkozást követve talál.

> [!div class="nextstepaction"]
> [Azure Java-fejlesztőknek](https://docs.microsoft.com/java/azure/)

