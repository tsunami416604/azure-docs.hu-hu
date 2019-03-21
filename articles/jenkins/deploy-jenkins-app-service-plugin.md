---
title: Üzembe helyezés az Azure App Service-ben a Jenkins beépülő modullal
description: Megtudhatja, hogyan használhatja az Azure App Service Jenkins beépülő modulját egy Java-webalkalmazás üzembe helyezéséhez
ms.service: jenkins
keywords: jenkins, azure, devops, app service
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 58835b66824d55b64b77e34df64d34c8da1c269a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57864813"
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>Üzembe helyezés az Azure App Service-ben a Jenkins beépülő modullal 

Ha Java-webalkalmazást szeretne üzembe helyezni az Azure-ban, használhatja az Azure CLI-t a [Jenkins-folyamatban](/azure/jenkins/execute-cli-jenkins-pipeline), vagy az [Azure App Service Jenkins beépülő modulját](https://plugins.jenkins.io/azure-app-service). A Jenkins beépülő modul 1.0-s verziója támogatja a folyamatos üzembe helyezést az Azure App Service Web Apps funkciójának használatával, a következőn keresztül:
* Fájlfeltöltés.
* Docker a Web Apps on Linuxhoz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * A Jenkins konfigurálása Web Apps üzembe helyezéséhez fájlfeltöltésen keresztül.
> * A Jenkins konfigurálása Web App for Containers üzembe helyezéséhez.

## <a name="create-and-configure-a-jenkins-instance"></a>Jenkins-példányok létrehozása és konfigurálása

Ha még nem rendelkezik Jenkins-főkiszolgálóval, kezdje a [megoldássablonnal](install-jenkins-solution-template.md), amely tartalmazza a Java fejlesztői készlet (JDK) 8-as verzióját és az alábbi Jenkins beépülő modulokat, amelyekre szüksége lesz:

* [Jenkins Git-ügyfél beépülő modul](https://plugins.jenkins.io/git-client), 2.4.6-os verzió 
* [Docker Commons beépülő modul](https://plugins.jenkins.io/docker-commons), 1.4.0-s verzió
* [Azure Credentials](https://plugins.jenkins.io/azure-credentials), 1.2-es verzió
* [Azure App Service](https://plugins.jenkins.io/azure-app-service), 0.1-es verzió

A Jenkins beépülő modullal a Web Apps által támogatott bármelyik nyelvvel (például C#, PHP, Java és Node.js) üzembe helyezhetők a webalkalmazások. Ebben az oktatóanyagban egy, [az Azure-hoz készült egyszerű Java-webalkalmazást](https://github.com/azure-devops/javawebappsample) használunk. Ahhoz, hogy elágaztassa az adattárat a saját GitHub-fiókja felé, kattintson a **Fork** (Elágaztatás) gombra a GitHub-felület jobb felső sarkában.  

> [!NOTE]
> A Java-projekt létrehozásához szükség van a Java JDK-ra és a Mavenre. Ezeket az összetevőket a Jenkins-főkiszolgálón telepítse, vagy a virtuálisgép-ügynökön, ha az ügynököt folyamatos integráláshoz használja. Ha Java SE alkalmazást helyez üzembe, a ZIP-re is szükség lesz a buildkiszolgálón.
>

Az összetevők telepítéséhez jelentkezzen be a Jenkis-példányba az SSH-val, és futtassa az alábbi parancsokat:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

A Web App for Containers szolgáltatásban való üzembe helyezéshez telepítse a Dockert a Jenkis-főkiszolgálóra vagy a létrehozáshoz használt virtuálisgép-ügynökre. Az utasításokért lásd [a Docker Ubuntun történő telepítését](https://docs.docker.com/engine/installation/linux/ubuntu/) ismertető cikket.

## <a name="service-principal"></a> Azure-szolgáltatásnév hozzáadása a Jenkins hitelesítő adataihoz

Az Azure-ban való üzembe helyezéshez Azure-szolgáltatásnévre van szükség. 


1. Az Azure egyszerű szolgáltatás létrehozásához használja a [Azure CLI-vel](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) vagy a [az Azure portal](/azure/azure-resource-manager/resource-group-create-service-principal-portal).
2. A Jenkins irányítópultján válassza a **Credentials** > **System** (Hitelesítő adatok, Rendszer) lehetőséget. Ezután válassza a **Global credentials (unrestricted)** (Globális hitelesítő adatok (korlátlan)) elemet.
3. Microsoft Azure-szolgáltatásnév hozzáadásához válassza az **Add Credentials** (Hitelesítő adatok hozzáadása) lehetőséget. Adja meg a következő mezők értékét: **Subscription ID** (Előfizetés azonosítója), **Client ID** (Ügyfél-azonosító), **Client Secret** (Titkos ügyfélkód) és **OAuth 2.0 Token Endpoint** (Jogkivonatcserélő OAuth 2.0-végpont). Az **ID** (Azonosító) mezőt állítsa **mySp** értékre. Ezt az azonosítót fogja használni a cikk következő lépéseiben.


## <a name="configure-jenkins-to-deploy-web-apps-by-uploading-files"></a>A Jenkins konfigurálása a Web Apps fájlfeltöltéssel történő üzembe helyezéséhez

Ha a Web Appsben szeretné üzembe helyezni a projektjeit, feltöltheti a buildösszetevőket fájlfeltöltésen keresztül. Az Azure App Service különböző üzembehelyezési lehetőségeket támogat. Az Azure App Service Jenkins beépülő modulja egyszerűvé teszi a választást és meghatározza az üzembehelyezési lehetőséget a fájltípus alapján. 

* Java EE-alkalmazások esetén a [WAR üzembe helyezés](/azure/app-service/deploy-zip#deploy-war-file) használatos.
* Java SE-alkalmazások esetén a [ZIP üzembe helyezés](/azure/app-service/deploy-zip#deploy-zip-file) használatos.
* Más nyelvek esetén a [Git üzembe helyezés](/azure/app-service/deploy-local-git) használatos.

Mielőtt beállítaná a feladatot a Jenkinsben, szüksége van egy Azure App Service-csomagra és egy webalkalmazásra a Java-alkalmazás futtatásához.


1. Az `az appservice plan create` [Azure CLI-paranccsal](/cli/azure/appservice/plan#az-appservice-plan-create) hozzon létre egy Azure App Service-csomagot **INGYENES** tarifacsomaggal. Az App Service-csomag határozza meg az alkalmazások üzemeltetéséhez használt fizikai erőforrásokat. Az App Service-csomaghoz rendelt összes alkalmazás ugyanezeket az erőforrásokat használja. A megosztott erőforrásokkal csökkenthetők a költségek, ha több alkalmazást is üzemeltet.
2. Hozzon létre egy webalkalmazást. Ehhez használhatja az [Azure Portalt](/azure/app-service-web/web-sites-configure) vagy az alábbi `az` Azure CLI-parancsot:
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. Állítsa be az alkalmazáshoz szükséges Java-futtatókörnyezetet. Az alábbi Azure CLI-parancs a webalkalmazást arra konfigurálja, hogy a legutóbbi JDK 8-on és az [Apache Tomcat](https://tomcat.apache.org/) 8.0-s verzióján fusson:
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>A Jenkins-feladat beállítása

1. Hozzon létre egy új **szabad stílusú** projektet a Jenkins irányítópultján.
2. Konfigurálja a **Source code management** (Forráskódkezelés) mezőt az [Azure-hoz készült egyszerű Java-webalkalmazás](https://github.com/azure-devops/javawebappsample) helyi elágazásának használatára. Adja meg az **adattár URL-címét**. Például: http:\//github.com/&lt;your_ID > / javawebappsample.
3. Az **Execute shell** (Felület futtatása) paranccsal adjon hozzá egy lépést, amely létrehozza a projektet a Mavennel. Ehhez a példához egy további parancsra van szükség, amely a \*.war fájlt átnevezi **ROOT.war** fájlra a célmappában:   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. Adjon hozzá egy létrehozás utáni műveletet a **Publish an Azure Web App** (Azure-webalkalmazás közzététele) lehetőség kiválasztásával.
5. Adja meg a **mySp** értéket Azure-szolgáltatásnévként. Ez a szolgáltatásnév lett [Azure-beli hitelesítő adatként](#service-principal) tárolva az előző lépésben.
6. Az **alkalmazáskonfigurációs** szakaszban válassza az előfizetésében szereplő erőforráscsoportot és webalkalmazást. A Jenkins beépülő modul automatikusan megállapítja, hogy a webalkalmazás Windows- vagy Linux-alapú. Windows-webalkalmazás esetében megjelenik a **Publish Files** (Fájlok közzététele) lehetőség.
7. Adja meg a fájlokat, amelyeket üzembe szeretne helyezni. Ha például Javát használ, adja meg a WAR-csomagot. A választható **Source Directory** (Forráskönyvtár) és **Target Directory** (Célkönyvtár) paraméterekkel megadhatja a fájlfeltöltéshez használandó forrás- és célmappát. Az Azure-on található Java-webalkalmazás egy Tomcat-kiszolgálón fut. Ezért a Java, feltöltheti a WAR-csomag a webapps mappát. Ebben a példában a **forráskönyvtár** értéke legyen **target**, a **célkönyvtáré** pedig **webapps**.
8. Ha a termelési helytől eltérő helyre szeretne üzembe helyezni, a **Slot** (Hely) nevét is megadhatja.
9. Mentse a projektet, és hozza létre. A webalkalmazás akkor helyeződik üzembe az Azure-on, ha a létrehozás befejeződött.

### <a name="deploy-web-apps-by-uploading-files-using-jenkins-pipeline"></a>Web Apps üzembe helyezése fájlfeltöltéssel a Jenkins-folyamat használatával

Az Azure App Service Jenkins beépülő modulja készen áll a folyamatok elvégzésére. Az alábbi példát áttekintheti a GitHub-adattárban.

1. A GitHub-felületen nyissa meg a **Jenkinsfile_ftp_plugin** fájlt. A fájl szerkesztéséhez kattintson a ceruza ikonra. Frissítse a webalkalmazás **resourceGroup** és **webAppName** definícióit a 11. és a 12. sorban:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. A 14. sorban található **withCredentials** definícióját állítsa a Jenkins-példányban lévő hitelesítési azonosítóra:
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Jenkins-folyamat létrehozása

1. Nyissa meg a Jenkinst egy webböngészőben. Válassza a **New Item** (Új elem) lehetőséget.
2. Adja meg a feladat nevét, és válassza a **Pipeline** (Folyamat) lehetőséget. Kattintson az **OK** gombra.
3. Válassza ki a **Pipeline** (Folyamat) lapot.
4. A **Definition** (Definíció) értékeként válassza a **Pipeline script from SCM** (Folyamatszkript SCM-ből) lehetőséget.
5. Az **SCM** értékeként válassza a **Git** lehetőséget. Adja meg az elágaztatott adattár GitHub URL-címét. Például: https://&lt;saját_elágaztatott_adattár>.git.
6. A **Script Path** (Szkript elérési útja) legyen **Jenkinsfile_ftp_plugin**.
7. Válassza a **Save** (Mentés) gombot, és futtassa a feladatot.

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>A Jenkins konfigurálása Web App for Containers üzembe helyezéséhez

A Web Apps on Linux támogatja a Dockerrel végzett üzembe helyezést. A webes alkalmazás üzembe helyezése a Docker használatával, egy docker-fájlban, amely egy Docker-rendszerképet a web Apps, és a futtatókörnyezet csomagokat van szükség. A Jenkins beépülő modul ezután létrehozza a rendszerképet, leküldi a Docker-beállításjegyzékbe, majd üzembe helyezi a rendszerképet a webalkalmazásban.

A Web Apps on Linux a hagyományos üzembehelyezési módszereket is támogatja, például a Gitet és a fájlfeltöltést, de csak a beépített nyelvek esetében (.NET Core, Node.js, PHP, Ruby). Ha más nyelvet használ, be kell csomagolnia az alkalmazás kódját és a szolgáltatási futtatókörnyezetet egy Docker-rendszerképbe, és a Dockert kell használnia az üzembe helyezéshez.

Mielőtt beállítaná a feladatot a Jenkinsben, szüksége vagy egy webalkalmazásra a Linuxon. Egy tárolóregisztrációs adatbázisra is szüksége van a privát Docker-tároló rendszerképeinek tárolásához és kezeléséhez. A tárolóregisztrációs adatbázist a DockerHubbal hozhatja létre. Ebben a példában az Azure Container Registryt használjuk.

* [Hozza létre a webalkalmazást a Linuxon](../app-service/containers/quickstart-nodejs.md).
* Az Azure Container Registry egy felügyelt [Docker-beállításjegyzék](https://docs.docker.com/registry/), amely a nyílt forráskódú Docker Registry 2.0 technológiára épül. [Hozzon létre egy Azure-beli tárolóregisztrációs adatbázist](/azure/container-registry/container-registry-get-started-azure-cli). A DockerHubot is használhatja.

### <a name="set-up-the-jenkins-job-for-docker"></a>A Jenkins-feladat beállítása a Dockerhez

1. Hozzon létre egy új **szabad stílusú** projektet a Jenkins irányítópultján.
2. Konfigurálja a **Source code management** (Forráskódkezelés) mezőt az [Azure-hoz készült egyszerű Java-webalkalmazás](https://github.com/azure-devops/javawebappsample) helyi elágazásának használatára. Adja meg az **adattár URL-címét**. Például: http:\//github.com/&lt;your_ID > / javawebappsample.
3. Az **Execute shell** (Felület futtatása) paranccsal adjon hozzá egy lépést, amely létrehozza a projektet a Mavennel. Adja hozzá az alábbi sort a parancshoz:
    ```bash
    mvn clean package
    ```

4. Adjon hozzá egy létrehozás utáni műveletet a **Publish an Azure Web App** (Azure-webalkalmazás közzététele) lehetőség kiválasztásával.
5. Adja meg a **mySp** értéket Azure-szolgáltatásnévként. Ez a szolgáltatásnév lett [Azure-beli hitelesítő adatként](#service-principal) tárolva az előző lépésben.
6. Az **alkalmazáskonfigurációs** szakaszban válassza az előfizetésében szereplő erőforráscsoportot és Linux-webalkalmazást.
7. Válassza a **Publish via Docker** (Közzététel Dockerrel) lehetőséget.
8. Adja meg a **Docker-fájl** elérési útját. Az alapértelmezett /Dockerfile értéket is megtarthatja.
A **Docker-beállításjegyzék URL-címeként** adja meg az URL-címet a következő formátumban: https://&lt;sajátBeállításjegyzék>.azurecr.io, ha Azure Container Registryt használ. Ha a DockerHubot használja, ne adja meg az értéket.
9. A **Registry credentials** (Beállításjegyzék hitelesítő adatai) értékeként a tárolóregisztrációs adatbázis hitelesítő adatait adja meg. A felhasználóazonosítót és a jelszót az alábbi parancsok Azure CLI-ben történő futtatásával kérheti le. Az első parancs engedélyezi a rendszergazdai fiókot:
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. Eldöntheti, hogy megadja-e a Docker-rendszerkép nevét és címkéjét az **Advanced** (Speciális) lapon. Alapértelmezés szerint a rendszerkép neve az Azure Portalon, a **Docker-tároló** beállításainál konfigurált rendszerképnévvel fog megegyezni. A címke $BUILD_NUMBER alapján jön létre.
    > [!NOTE]
    > Mindenképpen adja meg rendszerkép nevét az Azure Portalon, vagy adja meg a **Docker-rendszerkép** értékét az **Advanced** (Speciális) lapon. Ebben a **Docker-rendszerkép** értéke legyen &lt;saját_Beállításjegyzék>.azurecr.io/calculator értékre, a **Docker-rendszerkép címkéjét** pedig hagyja üresen.

11. Az üzembe helyezés sikertelen lesz, ha a Docker-rendszerkép beépített beállításait használja. Módosítsa a Docker konfigurációját, hogy egyéni rendszerképet használjon a **Docker-tároló** beállításainál az Azure Portalon. Beépített rendszerkép esetében használja a fájlfeltöltéses módszert az üzembe helyezéshez.
12. A fájlfeltöltéses módszerhez hasonlóan itt is választhat a **termelési** helytől eltérő **helyet** (Slot).
13. Mentse el és hozza létre a projektet. A tárolórendszerképet ezzel leküldte a beállításjegyzékbe, és üzembe helyezte a webalkalmazást.

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>A Web App for Containers üzembe helyezése Jenkins-folyamattal

1. A GitHub-felületen nyissa meg a **Jenkinsfile_container_plugin** fájlt. A fájl szerkesztéséhez kattintson a ceruza ikonra. Frissítse a webalkalmazás **resourceGroup** és **webAppName** definícióit a 11. és a 12. sorban:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. A 13. sort cserélje le a tárolóregisztrációs adatbázis kiszolgálójára:
    ```java
    def registryServer = '<registryURL>'
    ```

3. Módosítsa a 16. sort, hogy a Jenkins-példány hitelesítési azonosítóját használja:
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Jenkins-folyamat létrehozása    

1. Nyissa meg a Jenkinst egy webböngészőben. Válassza a **New Item** (Új elem) lehetőséget.
2. Adja meg a feladat nevét, és válassza a **Pipeline** (Folyamat) lehetőséget. Kattintson az **OK** gombra.
3. Válassza ki a **Pipeline** (Folyamat) lapot.
4. A **Definition** (Definíció) értékeként válassza a **Pipeline script from SCM** (Folyamatszkript SCM-ből) lehetőséget.
5. Az **SCM** értékeként válassza a **Git** lehetőséget. Adja meg az elágaztatott adattár GitHub URL-címét. Például: https://&lt;saját_elágaztatott_adattár>.git.
7. A **Script Path** (Szkript elérési útja) legyen **Jenkinsfile_container_plugin**.
8. Válassza a **Save** (Mentés) gombot, és futtassa a feladatot.

## <a name="verify-your-web-app"></a>Webalkalmazás ellenőrzése

1. Ha ellenőrizni szeretné, hogy a WAR-fájl sikeresen üzembe lett-e helyezve a webalkalmazáshoz, nyisson meg egy webböngészőt.
2. Lépjen a http://&lt;your_app_name>.azurewebsites.net/api/calculator/ping helyre. Cserélje le a &lt;your_app_name> értéket a webalkalmazása nevére. A következő üzenet jelenik meg:
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. Lépjen a http://&lt;your_app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> helyre. Cserélje le az &lt;x> és az &lt;y> értékeket bármely számra, hogy megkapja az x + y összegét. A kalkulátor összegét mutatja: ![A Számológép: hozzáadása](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-azure-app-service-on-linux"></a>Azure App Service on Linux esetén

1. A webalkalmazás ellenőrzéséhez futtassa az alábbi parancsot az Azure CLI-ben:
    ```CLI
    az acr repository list -n <myRegistry> -o json
    ```
    Az alábbi üzenet jelenik meg:
    ```CLI
    ["calculator"]
    ```
    
2. Lépjen a http://&lt;your_app_name>.azurewebsites.net/api/calculator/ping helyre. Cserélje le a &lt;your_app_name> értéket a webalkalmazása nevére. A következő üzenet jelenik meg: 
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

3. Lépjen a http://&lt;your_app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> helyre. Cserélje le az &lt;x> és az &lt;y> értékeket bármely számra, hogy megkapja az x + y összegét.
    
## <a name="troubleshooting-the-jenkins-plugin"></a>A Jenkins beépülő modul hibáinak elhárítása

Ha a Jenkins beépülő modulok használata során bármilyen hibát tapasztal, jelentse be a problémát az adott összetevő [Jenkins JIRA](https://issues.jenkins-ci.org/) felületén.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure App Service Jenkins beépülő modulját használta az Azure-ban történő üzembe helyezéshez.

Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A Jenkins konfigurálása az Azure App Service fájlfeltöltésen keresztül történő üzembe helyezéséhez 
> * A Jenkins konfigurálása Web App for Containersbe történő üzembe helyezéséhez 
