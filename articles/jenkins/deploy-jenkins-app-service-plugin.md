---
title: Azure App Service üzembe helyezése a Jenkins beépülő modul használatával
description: Ismerje meg, hogyan használhatja az Azure App Service Jenkins beépülő modul egy Java webes alkalmazás üzembe helyezése a Jenkins az Azure-bA
ms.topic: article
ms.author: tarcher
author: tomarcher
manager: jpconnock
ms.service: devops
ms.custom: jenkins
ms.date: 07/25/2018
ms.openlocfilehash: 407ec2bbb145e73b1a903886204b660aadc9a65f
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284414"
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>Azure App Service üzembe helyezése a Jenkins beépülő modul használatával 

Egy Java webes alkalmazás üzembe helyezése az Azure-ba, használhatja az Azure CLI a [Jenkins folyamat](/azure/jenkins/execute-cli-jenkins-pipeline) vagy használhatja a [Azure App Service Jenkins beépülő modul](https://plugins.jenkins.io/azure-app-service). A Jenkins beépülő modul 1.0-s verziója támogatja a folyamatos üzembe helyezés keresztül az Azure App Service Web Apps funkciójának használatával:
* A Git vagy FTP.
* Docker, a Web Apps on Linuxhoz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Állítsa be a Jenkins és helyezhet üzembe webalkalmazásokat a Git vagy FTP használatával.
> * Állítsa be a Jenkins üzembe helyezése Web App for Containers és.

## <a name="create-and-configure-a-jenkins-instance"></a>Hozzon létre, és a egy Jenkins-példány konfigurálása

Ha még nem rendelkezik Jenkins-főkiszolgáló, kezdje a [megoldássablon](install-jenkins-solution-template.md), amely tartalmazza a Java fejlesztői készlet (JDK) 8-as verzió, és a következő Jenkins beépülő modulok szükséges:

* [A Jenkins Git ügyfél beépülő modul](https://plugins.jenkins.io/git-client) 2.4.6 verzió 
* [Docker Commons beépülő modul](https://plugins.jenkins.io/docker-commons) 1.4.0-s verzió
* [Azure-beli hitelesítő](https://plugins.jenkins.io/azure-credentials) 1.2-es verziója
* [Az Azure App Service](https://plugins.jenkins.io/azure-app-service) verzió 0.1-es frissítés

A Jenkins beépülő modul használatával bármilyen nyelven, amely támogatja a webalkalmazásokat, például a C#, PHP, Java és Node.js-webalkalmazás üzembe helyezése. Ebben az oktatóanyagban használjuk egy [egyszerű Azure Java-webalkalmazás](https://github.com/azure-devops/javawebappsample). Adattárház elágaztatása a saját GitHub-fiókhoz, jelölje be a **elágazás** gombra a jobb felső sarokban, a GitHub-felületet.  
> [!NOTE]
> A Java JDK és a maven használatával hozza létre a Java projektet szükségesek. Telepítse ezeket az összetevőket a Jenkins-főkiszolgáló a, vagy a Virtuálisgép-ügynök használatakor az ügynök-t folyamatos integrációhoz. 

Az összetevők telepítéséhez jelentkezzen be a Jenkins-példány az ssh-val, és futtassa a következő parancsokat:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Web App for containers szolgáltatásban való üzembe helyezéséhez telepíthető a Docker, a Jenkins-főkiszolgáló a vagy a Virtuálisgép-ügynök, amely a build szolgál. Útmutatásért lásd: [telepítése Docker ubuntun](https://docs.docker.com/engine/installation/linux/ubuntu/).

##<a name="service-principal"></a> Azure-beli szolgáltatásnév hozzáadása a Jenkins hitelesítő adatok

Szüksége van Azure-beli szolgáltatásnév üzembe helyezéséhez az Azure-bA. 


1. Az Azure egyszerű szolgáltatás létrehozásához használja a [Azure CLI-vel](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) vagy a [az Azure portal](/azure/azure-resource-manager/resource-group-create-service-principal-portal).
2. A Jenkins irányítópultján válassza ki a **hitelesítő adatok** > **rendszer**. Ezután válassza ki **globális credentials(unrestricted)**.
3. A Microsoft Azure-szolgáltatásnév hozzáadásához válassza **hitelesítő adatok hozzáadása**. Értéket ad meg a **előfizetés-azonosító**, **ügyfél-azonosító**, **titkos Ügyfélkód**, és **OAuth 2.0 jogkivonat-végpont** mezőket. Állítsa be a **azonosító** mezőt **mySp**. Ezt az Azonosítót a ebben a cikkben a későbbi lépésekben használjuk.


## <a name="configure-jenkins-to-deploy-web-apps-by-uploading-files"></a>A fájlok feltöltésével webes alkalmazások üzembe helyezése a Jenkins konfigurálása

Projekt telepítése a Web Apps szolgáltatásban, feltöltheti a build-összetevőket (például egy WAR-fájlt a Java) Git- vagy FTP használatával.

A feladat a Jenkins beállítása előtt szüksége a Java-alkalmazás futtatását Azure App Service-csomag és egy webalkalmazást.


1. Hozzon létre egy Azure App Service-csomagot a **ingyenes** tarifacsomag használatával a `az appservice plan create` [Azure CLI-paranccsal](/cli/azure/appservice/plan#az_appservice_plan_create). Az App Service-csomag határozza meg, hogy az alkalmazások üzemeltetéséhez használt fizikai erőforrások. Egy App Service-csomaghoz rendelt összes alkalmazás ossza meg ezeket az erőforrásokat. Megosztott erőforrások segítségével több alkalmazás üzemeltetése esetén csökkenthetők a költségek.
2. Hozzon létre egy webes alkalmazást. Használhatja a [az Azure portal](/azure/app-service-web/web-sites-configure) vagy a következő `az` Azure CLI-parancsot:
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. Állítsa be az alkalmazás által igényelt Java futásidejű konfigurációja. Az Azure CLI-parancsot az alkalmazás futtatásához a legutóbbi JDK 8-as konfigurálja és [Apache Tomcat](http://tomcat.apache.org/) 8.0-s verzió:
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>Állítsa be a Jenkins-feladat

1. Hozzon létre egy új **freestyle** projekt a Jenkins irányítópultján.
2. Konfigurálja a **Source Code Management** helyi elágazása használandó mezőt a [egyszerű Azure Java-webalkalmazás](https://github.com/azure-devops/javawebappsample). Adja meg a **adattár URL-címe** értéket. Például: http://github.com/&lt; your_ID > / javawebappsample.
3. A projekt létrehozásához adja hozzá a Maven használatával lépés hozzáadása a **hajtsa végre a rendszerhéj** parancsot. Ebben a példában a parancs átnevezése ellenőriznünk kell a \*.war-fájlt a cél mappában **ROOT.war**:   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. Adja hozzá a felépítés utáni művelet kiválasztásával **közzététele az Azure Web Apps**.
5. Adja meg **mySp** , az Azure-szolgáltatás egyszerű. A rendszerbiztonsági tag lett tárolva az [Azure hitelesítő adatok](#service-principal) az előző lépésben.
6. Az a **Alkalmazáskonfiguráció** az erőforrás-csoport és a webes alkalmazás válassza az előfizetésében. A Jenkins beépülő modul automatikusan észleli-e a webes alkalmazás Windows vagy Linux rendszeren alapul. Windows webalkalmazásokhoz a **közzététele fájlok** lehetőség jelenik meg.
7. Töltse ki a fájlokat, amely számára telepíteni kívánja. Adja meg például a WAR-csomag, a Java használata. A választható **forráskönyvtár** és **céloldali könyvtár** paraméterek használatával adja meg a forrás- és Célmappák feltöltés használandó. A Java-webalkalmazás Azure-on fut, a Tomcat kiszolgálón. Ezért a Java, feltöltheti a WAR-csomag a webapps mappát. Például állítsa be a **forráskönyvtár** értéket a következőre **cél** és a **céloldali könyvtár** értéket a következőre **webapps**.
8. Ha szeretne telepíteni egy nem éles tárhely, azt is beállíthatja a **tárolóhely** nevét.
9. Mentse a projektet, és építse fel. A webalkalmazás az Azure-ban üzemel, a fordítás befejeződése.

### <a name="deploy-web-apps-by-uploading-files-using-jenkins-pipeline"></a>Webes alkalmazások üzembe helyezése a Jenkins-folyamat használatával fájlok feltöltésével

Az Azure App Service Jenkins beépülő modul a folyamat használatra kész. A GitHub-adattárat a következő mintát is hivatkozunk.

1. A GitHub-felületén nyissa meg a **Jenkinsfile_ftp_plugin** fájlt. A fájl szerkesztéséhez válassza a ceruza ikonra. Frissítés a **resourceGroup** és **webAppName** a webalkalmazás-definíciói vonalak 11 – 12, illetve:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Állítsa be a **withCredentials** definíció 14. sor, a Jenkins-példány hitelesítőadat-Azonosítóval:
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Hozzon létre egy Jenkins-folyamat

1. Nyissa meg a Jenkins egy webböngészőben. Válassza a **New Item** (Új elem) lehetőséget.
2. Adjon meg egy nevet a feladat, és válasszon **folyamat**. Kattintson az **OK** gombra.
3. Válassza ki a **folyamat** fülre.
4. Az a **definíció** értéket, válassza ki **SCM parancsfájlt folyamat**.
5. Az a **SCM** értéket, válassza ki **Git**. Adja meg az elágaztatott GitHub URL-CÍMÉT. Például: https://&lt;your_forked_repo > .git.
6. Frissítés a **parancsfájl elérési útján** értéket a következőre **Jenkinsfile_ftp_plugin**.
7. Válassza ki **mentése** és a feladat futtatásához.

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>A Jenkins üzembe helyezéséhez a Web App for Containers konfigurálása

A Linuxos Web Apps üzembe helyezési támogatja a Docker használatával. A webes alkalmazás üzembe helyezése a Docker használatával, egy docker-fájlban, amely egy Docker-rendszerképet a web Apps, és a futtatókörnyezet csomagokat van szükség. A Jenkins beépülő majd létrehozza a rendszerképet leküldi azt egy Docker-beállításjegyzék és üzembe helyezi a rendszerkép a webalkalmazáshoz.

A Linuxos Web Apps is támogatja a hagyományos központi telepítési módszer, például a Git és FTP-, de csak a beépített nyelveket (.NET Core, Node.js, PHP vagy Ruby). Más nyelven kell egy Docker-rendszerképet együtt az alkalmazás kódja és a szolgáltatás futtatókörnyezete csomagolni és üzembe helyezése a Docker használatával.

Mielőtt beállítaná a feladat a jenkins kifejezést, szüksége van egy webalkalmazás Linux rendszeren. Emellett egy tároló-beállításjegyzéket, tárolhatja és kezelheti a privát Docker-tárolórendszerképekhez. DockerHub segítségével a tárolóregisztrációs adatbázis létrehozása. Ebben a példában az Azure Container Registry használjuk.

* [A webes alkalmazás létrehozása Linux rendszeren](../app-service/containers/quickstart-nodejs.md).
* Az Azure Container Registry egy felügyelt [Docker-beállításjegyzék](https://docs.docker.com/registry/) szolgáltatás, amely a nyílt forráskódú Docker Registry 2.0-s verziója alapján. [Hozzon létre egy Azure container registry](/azure/container-registry/container-registry-get-started-azure-cli). DockerHub is használhatja.

### <a name="set-up-the-jenkins-job-for-docker"></a>Állítsa be a Jenkins-feladatot a Docker

1. Hozzon létre egy új **freestyle** projekt a Jenkins irányítópultján.
2. Konfigurálja a **Source Code Management** helyi elágazása használandó mezőt a [egyszerű Azure Java-webalkalmazás](https://github.com/azure-devops/javawebappsample). Adja meg a **adattár URL-címe** értéket. Például: http://github.com/&lt; your_ID > / javawebappsample.
3. A projekt létrehozásához adja hozzá a Maven használatával lépés hozzáadása egy **hajtsa végre a rendszerhéj** parancsot. A parancs tartalmazza a következő sort:
    ```bash
    mvn clean package
    ```

4. Adja hozzá a felépítés utáni művelet kiválasztásával **közzététele az Azure Web Apps**.
5. Adja meg **mySp** , az Azure-szolgáltatás egyszerű. A rendszerbiztonsági tag lett tárolva az [Azure hitelesítő adatok](#service-principal) az előző lépésben.
6. Az a **Alkalmazáskonfiguráció** területen válassza ki az erőforráscsoportot és a egy Linuxos webalkalmazásokat az előfizetésében.
7. Válasszon **Docker-n keresztül közzétenni**.
8. Töltse ki a **Dockerfile** elérési útja értéket. Az alapértelmezett érték /Dockerfile tárolhatja.
Az a **Docker registry URL** értékét, adja meg az URL-címet a formátum https:// használatával&lt;yourRegistry >. azurecr.io, ha az Azure Container Registry használata. Ha DockerHub használja, hagyja üresen az értéket.
9. Az a **a Tárolójegyzék hitelesítő adatainak** értékét, adja hozzá a tárolójegyzék hitelesítő adatait. Az Azure CLI az alábbi parancsok futtatásával kaphat a felhasználói azonosítót és jelszót. Az első parancs lehetővé teszi, hogy a rendszergazdai fiók:
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. A Docker rendszerkép nevét és címkéjét értéket a **speciális** lap megadása nem kötelező. Alapértelmezés szerint a rendszerkép nevének értékét a rendszerkép neve az Azure Portalon, a nyert a **Docker-tároló** beállítás. A címke $BUILD_NUMBER alapján jön létre.
    > [!NOTE]
    > Ügyeljen arra, hogy a rendszerkép nevének megadása az Azure Portalon, vagy adjon meg egy **Docker-rendszerkép** értékét a **speciális** fülre. Például állítsa be a **Docker-rendszerkép** értéket a következőre &lt;your_Registry >.azurecr.io/calculator és hagyja a **Docker rendszerkép Tag** érték üres.

11. Az üzembe helyezés sikertelen lesz, ha a beépített Docker rendszerkép beállítást használja. Az egyéni rendszerkép használata a Docker konfigurációjának módosítása a **Docker-tároló** beállítása az Azure Portalon. A beépített rendszerképpel a fájl feltöltési módszer használatával helyezhet üzembe.
12. A fájl feltöltési módszer hasonlóan választhat egy másik **tárolóhely** eltérő nevet **éles**.
13. Mentse, és a projekt buildjének elkészítéséhez. A tároló rendszerképének van leküldte a tárolójegyzékbe, és a webalkalmazás telepítve lesz.

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>Web App for Containers üzembe helyezése a Jenkins-folyamat használatával

1. A GitHub-felületén nyissa meg a **Jenkinsfile_container_plugin** fájlt. A fájl szerkesztéséhez válassza a ceruza ikonra. Frissítés a **resourceGroup** és **webAppName** a webalkalmazás-definíciói vonalak 11 – 12, illetve:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. 13. sorban módosítsa a tároló beállításjegyzék-kiszolgálóra:
    ```java
    def registryServer = '<registryURL>'
    ```

3. Módosítsa a Jenkins-példány hitelesítőadat-Azonosítóval használandó 16 sort:
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Hozzon létre egy Jenkins-folyamat    

1. Nyissa meg a Jenkins egy webböngészőben. Válassza a **New Item** (Új elem) lehetőséget.
2. Adjon meg egy nevet a feladat, és válasszon **folyamat**. Kattintson az **OK** gombra.
3. Válassza ki a **folyamat** fülre.
4. Az a **definíció** értéket, válassza ki **SCM parancsfájlt folyamat**.
5. Az a **SCM** értéket, válassza ki **Git**. Adja meg az elágaztatott GitHub URL-CÍMÉT. Például: https://&lt;your_forked_repo > .git.
7. Frissítés a **parancsfájl elérési útján** értéket a következőre **Jenkinsfile_container_plugin**.
8. Válassza ki **mentése** és a feladat futtatásához.

## <a name="verify-your-web-app"></a>Ellenőrizze a webalkalmazás

1. Győződjön meg arról, hogy a WAR-fájl sikeresen telepítve lett a webalkalmazás, egy webböngészőben nyissa meg.
2. Go to http://&lt;your_app_name>.azurewebsites.net/api/calculator/ping. Cserélje le &lt;your_app_name > a webalkalmazás nevére. Az üzenet jelenik meg:
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. Go to http://&lt;your_app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>. Cserélje le &lt;x > és &lt;y > beolvasni az összeg, az x bármilyen számokkal + y. A kalkulátor összegét mutatja: ![Számológép: hozzáadása](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-azure-app-service-on-linux"></a>Az Azure App Service Linux rendszeren

1. A webes alkalmazás ellenőrzéséhez futtassa a következő parancsot az Azure CLI-ben:
    ```CLI
    az acr repository list -n <myRegistry> -o json
    ```
    A következő üzenet jelenik meg:
    ```CLI
    ["calculator"]
    ```
    
2. Go to http://&lt;your_app_name>.azurewebsites.net/api/calculator/ping. Cserélje le &lt;your_app_name > a webalkalmazás nevére. Az üzenet jelenik meg: 
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

3. Go to http://&lt;your_app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>. Cserélje le &lt;x > és &lt;y > beolvasni az összeg, az x bármilyen számokkal + y.
    
## <a name="troubleshooting"></a>Hibaelhárítás

Ha bármely hibái a Jenkins beépülő modulok, a fájl be a problémát a [Jenkins JIRA](https://issues.jenkins-ci.org/) az adott összetevő.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag, amellyel az Azure App Service Jenkins beépülő modul üzembe helyezése az Azure-bA.

Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az Azure App Service FTP használatával üzembe helyezése a Jenkins konfigurálása 
> * A Web App for containers szolgáltatásban való üzembe helyezése a Jenkins konfigurálása 
