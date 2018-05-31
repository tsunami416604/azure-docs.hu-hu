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
ms.openlocfilehash: 2018f5b7051f2b6906372dad3319c763974b93b1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34355185"
---
# <a name="quickstart-create-a-java-web-app-in-app-service-on-linux"></a>Rövid útmutató: Java-webalkalmazás létrehozása a Linuxon futó App Service-ben

A Linuxon futó App Service jelenleg előzetes verziójú funkcióként biztosítja a Java-webalkalmazások támogatását. Az előzetes verziókkal kapcsolatos további információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

A [Linuxon futó App Service](app-service-linux-intro.md) hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával. Ez a rövid útmutató bemutatja, hogyan használható az [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) és az [Azure Web Apps előzetes verziójához készült Maven bővítmény](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) egy Java-webalkalmazás egy beépített Linux-rendszerképpel történő üzembe helyezéséhez.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-java/java-hello-world-in-browser.png)

A [Java-webalkalmazások üzembe helyezése a felhőben futó Linux-alapú tárolókban az IntelliJ-hez készült Azure-eszközkészlettel](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-hello-world-web-app-linux) egy alternatív módszer a Java-alkalmazások saját tárolókban való üzembe helyezéséhez.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez: 

* Az [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)-s vagy újabb verziója helyben telepítve.
* [Apache Maven](http://maven.apache.org/).



## <a name="create-a-java-app"></a>Java-alkalmazás létrehozása

Hajtsa végre a következő parancsot a Mavennel egy új *helloworld* webalkalmazás létrehozásához:  

    mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp

Váltson át az új *helloworld* projekt könyvtárára, és hozza létre az összes modult az alábbi paranccsal:

    mvn verify

Ez a parancs ellenőriz és létrehoz minden modult, köztük a *helloworld.war* fájlt a *helloworld/target* alkönyvtárban.


## <a name="deploying-the-java-app-to-app-service-on-linux"></a>A Java-alkalmazás üzembe helyezése a Linuxon futó App Service-ben

A Java-webalkalmazás Linuxon futó App Service-ben való üzembe helyezésének több lehetséges módja is van. Ezek a lehetőségek a következők:

* [Üzembe helyezés az Azure Web Apps Maven bővítményével](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)
* [Üzembe helyezés ZIP- vagy WAR-fájlból](https://docs.microsoft.com/azure/app-service/app-service-deploy-zip)
* [Üzembe helyezés FTP használatával](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)

Ebben a gyors útmutatóban az Azure Web Apps Maven bővítményét használjuk. A bővítmény előnye, hogy könnyen használható a Mavenből, és létrehozza a szükséges Azure-erőforrásokat (erőforráscsoportot, App Service-csomagot és webalkalmazást).

### <a name="deploy-with-maven"></a>Üzembe helyezés a Maven használatával

A Mavenből való üzembe helyezéshez adja hozzá a következő bővítménydefiníciót a *pom.xml* fájl `<build>` részéhez:

```xml
    <plugins>
      <plugin>
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-webapp-maven-plugin</artifactId> 
        <version>1.1.0</version>
        <configuration> 
          <resourceGroup>YOUR_RESOURCE_GROUP</resourceGroup> 
          <appName>YOUR_WEB_APP</appName> 
          <linuxRuntime>tomcat 9.0-jre8</linuxRuntime>
          <deploymentType>ftp</deploymentType> 
          <resources> 
              <resource> 
                  <directory>${project.basedir}/target</directory> 
                  <targetPath>webapps</targetPath> 
                  <includes> 
                      <include>*.war</include> 
                  </includes> 
                  <excludes> 
                      <exclude>*.xml</exclude> 
                  </excludes> 
              </resource> 
          </resources> 
        </configuration>
      </plugin>
    </plugins>
```    

Frissítse a következő helyőrzőket a bővítmény konfigurációjában:

| Helyőrző | Leírás |
| ----------- | ----------- |
| `YOUR_RESOURCE_GROUP` | Az új erőforráscsoport neve, amelyben létrehozza a webalkalmazást. Ha egy alkalmazás összes erőforrását egy csoportban helyezi el, akkor mindet együtt kezelheti. Az erőforráscsoport törlésével például az alkalmazáshoz társított összes erőforrást törli. Frissítse ezt az értéket egy egyedi új erőforráscsoport-névre, például *TestResources*. Ezt az erőforráscsoport-nevet használjuk egy későbbi szakaszban az összes Azure-erőforrás eltávolításához. |
| `YOUR_WEB_APP` | Az alkalmazásnév az alkalmazás gazdagépnevének része lesz az Azure-beli üzembe helyezést követően (YOUR_WEB_APP.azurewebsites.net). Frissítse ezt az értéket egy egyedi névre, amelyet a Java-alkalmazást futtató új Azure-webalkalmazás kap, például: *contoso*. |

A konfiguráció `linuxRuntime` eleme határozza meg, hogy az alkalmazás melyik beépített Linux-rendszerképet használja.

Hajtsa végre az alábbi parancsot, és kövesse az utasításokat az Azure CLI-vel való hitelesítéshez:

    az login

Helyezze üzembe a Java-alkalmazást a webalkalmazásban az alábbi paranccsal:

    mvn clean package azure-webapp:deploy


Az üzembe helyezést követően keresse meg az üzembe helyezett alkalmazást a webböngészőjében a következő URL-cím használatával.

```bash
http://<app_name>.azurewebsites.net/helloworld
```

A Java-mintakód beépített rendszerképpel rendelkező webalkalmazásban fut.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-java/java-hello-world-in-browser-curl.png)

**Gratulálunk!** Üzembe helyezte az első Java-webalkalmazását a Linuxon futó App Service-ben.


[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]


## <a name="next-steps"></a>További lépések

Ebben a cikkben egy Java-webalkalmazást hoztunk létre a Mavennel, majd üzembe helyeztük az alkalmazást a Linuxon futó App Service-ben. A Java és az Azure használatával kapcsolatosan további tudnivalókat az alábbi hivatkozást követve talál.

> [!div class="nextstepaction"]
> [Azure Java-fejlesztőknek](https://docs.microsoft.com/java/azure/)

