---
title: "Telepítése az Azure App Service a Jenkins beépülő modul használatával |} Microsoft Docs"
description: "Az Azure-bA Jenkins a Java-webalkalmazás telepítése az Azure App Service Jenkins beépülő modul használata"
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 7/24/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 9b79e3b498e51e626e7e9a87d2bb1a66366acff5
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2017
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>Telepítése az Azure App Service a Jenkins beépülő modul használatával 

Java-webalkalmazás telepítése az Azure-ba, használja az Azure parancssori felület a [Jenkins csővezeték](/azure/jenkins/execute-cli-jenkins-pipeline) vagy használhatja a [Azure App Service Jenkins beépülő modul](https://plugins.jenkins.io/azure-app-service). A Jenkins 1.0-s beépülő modul verziója támogatja a folyamatos üzembe helyezés keresztül Azure App Service Web Apps szolgáltatással:
* Git- vagy FTP.
* A Web Apps Linux docker.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Jenkins is telepíthet webalkalmazásokat, Git vagy FTP segítségével konfigurálja.
> * Webes alkalmazás a tárolók telepítése Jenkins konfigurálása.

## <a name="create-and-configure-a-jenkins-instance"></a>Hozza létre és konfigurálja a Jenkins példánya

Ha még nem rendelkezik egy Jenkins Master, kezdje a [megoldássablonban](install-jenkins-solution-template.md), mely tartalmazza a Java fejlesztői készlet (JDK) 8-as verzió, és a következő Jenkins beépülő modulok szükséges:

* [Jenkins Git ügyfél beépülő modul](https://plugins.jenkins.io/git-client) 2.4.6 verziója 
* [Docker Commons beépülő modul](https://plugins.jenkins.io/docker-commons) 1.4.0 verziója
* [Azure hitelesítő adataival](https://plugins.jenkins.io/azure-credentials) 1.2-es verziója
* [Az Azure App Service](https://plugins.jenkins.io/azure-app-server) 0,1 verziója

A Jenkins beépülő modul segítségével bármilyen nyelven Web Apps, például a C#, PHP, Java és Node.js által támogatott webalkalmazás üzembe helyezése. Ebben az oktatóanyagban használjuk a [egyszerű Azure Java-webalkalmazás](https://github.com/azure-devops/javawebappsample). Oszthatja ketté a tárházban saját GitHub-fiók számára, jelölje ki a **elágazás** a GitHub-felület jobb felső sarkában gombjára.  
> [!NOTE]
> A Java JDK és Maven van szükség a Java-projekt felépítéséhez. Telepítse ezeket az összetevőket a Jenkins fő, vagy a Virtuálisgép-ügynök Ha használja az ügynök folyamatos integrációt. 

Az összetevők telepítéséhez, jelentkezzen be a Jenkins SSH-példány, és futtassa a következő parancsokat:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Szeretné telepíteni a webalkalmazás az tárolókat, telepítse a Docker a Jenkins fő vagy a Virtuálisgép-ügynök, amely a build szolgál. Útmutatásért lásd: [Docker telepíti az Ubuntu](https://docs.docker.com/engine/installation/linux/ubuntu/).

##<a name="service-principal"></a>Egy egyszerű Azure szolgáltatás hozzáadása a Jenkins hitelesítő adatok

Egy Azure szolgáltatás egyszerű Azure telepíteni kell. 


1. Hozzon létre egy Azure-szolgáltatás egyszerű, használja a [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) vagy a [Azure-portálon](/azure/azure-resource-manager/resource-group-create-service-principal-portal).
2. Az Jenkins irányítópultra, válassza ki a **hitelesítő adatok** > **rendszer**. Ezt követően válassza **globális credentials(unrestricted)**.
3. A Microsoft Azure egyszerű szolgáltatásnév hozzáadásához válassza **adja hozzá a hitelesítő adatok**. Adja meg az értékeket a **előfizetés-azonosító**, **ügyfél-azonosító**, **Ügyfélkulcs**, és **OAuth 2.0 Token-végpont** mezőket. Állítsa be a **azonosító** mezőről **mySp**. Ezt az Azonosítót a későbbi lépésekben ebben a cikkben használjuk.


## <a name="configure-jenkins-to-deploy-web-apps-by-uploading-files"></a>Jenkins is telepíthet webalkalmazásokat fájlok feltöltésével konfigurálása

A projekt telepítésére a webalkalmazások, Git vagy FTP segítségével feltöltheti a build összetevők (például egy WAR-fájlt a Java).

A feladat Jenkins beállítása előtt kell az Azure App Service-csomag és a webes alkalmazás a Java-alkalmazás futtatását.


1. Az Azure App Service-csomagot hozzon létre a **szabad** IP-címek használatával a `az appservice plan create` [Azure CLI parancs](/cli/azure/appservice/plan#create). Az App Service-csomag határozza meg az alkalmazások futtatásához használt fizikai erőforrásokat. Egy App Service-csomagra hozzárendelt összes alkalmazások ossza meg ezeket az erőforrásokat. Megosztott erőforrások menthetők a költségek, ha több alkalmazást üzemeltető nyújt segítséget.
2. Hozzon létre egy webes alkalmazást. Használhatja a [Azure-portálon](/azure/app-service-web/web-sites-configure) vagy a következő `az` Azure CLI-parancsot:
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. Állítsa be az alkalmazást Java runtime konfigurációt. A következő Azure CLI parancs beállítja a webalkalmazás futtatásához a legutóbbi JDK 8-as és [Apache Tomcat](http://tomcat.apache.org/) 8.0-s verzió:
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>A Jenkins feladat beállítása

1. Hozzon létre egy új **freestyle** projekt a Jenkins irányítópulton.
2. Konfigurálja a **forrás kód felügyeleti** a helyi elágazás a használandó mezőt a [egyszerű Azure Java-webalkalmazás](https://github.com/azure-devops/javawebappsample). Adja meg a **tárház URL-CÍMÉT** érték. Például: http://github.com/&lt;your_ID > / javawebappsample.
3. Adjon hozzá a projekt elkészítéséhez Maven használatával adja hozzá a lépést a **hajtható végre a rendszerhéj** parancsot. Ebben a példában a parancs nevezze át kell a \*.war fájlt a cél mappában **ROOT.war**:   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. Adja hozzá a létrehozás után végrehajtandó művelet kiválasztásával **közzététele az Azure Web Apps**.
5. Adja meg **mySp** , az Azure szolgáltatás egyszerű. Rendszerbiztonsági tárolta, mint a [Azure hitelesítő adatok](#service-principal) az előző lépésben.
6. Az a **Alkalmazáskonfiguráció** területen válassza ki az erőforrás-csoport és a webes alkalmazás az előfizetésben. A Jenkins beépülő modul automatikusan észleli, hogy a webes alkalmazás alapul Windows vagy Linux rendszerű. A Windows webalkalmazás a **közzététele fájlok** beállítás számára jelenik meg.
7. Töltse ki a fájlokat, amely számára telepíteni kívánja. Például adja meg a WAR-csomagot, Java használata. Használja az opcionális **forráskönyvtár** és **célkönyvtár** paramétereit, és adja meg a forrás és cél mappákat fájlfeltöltés használandó. A Java-webalkalmazás az Azure-on fut egy Tomcat kiszolgálót. Ezért Java feltöltött a WAR-csomag a webapps mappát. Például állítsa be a **forráskönyvtár** egy érték **cél** és a **célkönyvtár** egy érték **webapps**.
8. Ha szeretne telepíteni egy nem éles tárolóhelyre, akkor is megadhatja a **tárolóhely** nevét.
9. Mentse a projektet, és állítsa be úgy. A webalkalmazás az Azure-bA telepítve van, a build befejezésekor.

### <a name="deploy-web-apps-by-uploading-files-using-jenkins-pipeline"></a>Webalkalmazások telepítését a Jenkins Kimenetátirányításának segítségével fájlok feltöltése

Az Azure App Service Jenkins beépülő modul az adatcsatorna használatra kész. A következő mintát a GitHub-tárház hivatkozhat.

1. A GitHub-felületén, nyissa meg a **Jenkinsfile_ftp_plugin** fájlt. A fájl szerkesztéséhez válassza ki a ceruza ikonra. Frissítés a **resourceGroup** és **webAppName** a webalkalmazás a definíciók sorok 11 és 12, illetve:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Állítsa be a **withCredentials** definíciót 14 Jenkins betűtípusainak a hitelesítő adat azonosító:
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Hozzon létre egy Jenkins folyamat

1. Nyissa meg a Jenkins egy webböngészőben. Válassza ki **új elem**.
2. Adjon meg egy nevet a feladatot, és válasszon **csővezeték**. Kattintson az **OK** gombra.
3. Válassza ki a **csővezeték** fülre.
4. Az a **Definition** érték, jelölje be **parancsfájl az SCM-feldolgozási folyamat**.
5. Az a **SCM** érték, jelölje be **Git**. Adja meg a GitHub URL-címet a villás tárházban. Például: https://&lt;your_forked_repo > .git.
6. Frissítés a **parancsfájl elérési útján** egy érték **Jenkinsfile_ftp_plugin**.
7. Válassza ki **mentése** lehetőséget és futtatni a feladatot.

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>Webes alkalmazás a tárolók telepítése Jenkins konfigurálása

Web Apps Linux Docker használatával támogatja a központi telepítését. Telepítse a web app Docker segítségével, meg kell adnia egy Dockerfile, amely a szolgáltatás futtatási idő mellett a webalkalmazás csomagokat a Docker-lemezkép. A Jenkins beépülő modul majd a képet alkot, leküldi a Docker-beállításjegyzék és a lemezkép telepíti a webalkalmazás.

Web Apps Linux támogatja a hagyományos telepítési módszerek, például a Git és az FTP, de csak beépített nyelveket (.NET Core, Node.js, PHP és Ruby) is. A többi nyelvet kell az alkalmazás kódja és szolgáltatás futásidejű együtt csomagolni egy Docker-lemezképet, és a Docker használatával történő telepítése.

Mielőtt beállítaná a Jenkins feladatot, a webes alkalmazás Linux kell. Szükség egy tároló beállításjegyzék tárolja, és a titkos Docker-tároló lemezképek kezeléséhez. DockerHub segítségével hozza létre a tároló beállításkulcs. A jelen példában használjuk Azure tároló beállításjegyzék.

* [A webalkalmazás létrehozása Linux rendszeren](../app-service/containers/quickstart-nodejs.md).
* Az Azure tároló beállításjegyzék egy felügyelt [Docker beállításjegyzék](https://docs.docker.com/registry/) szolgáltatás, amely a nyílt forráskódú Docker beállításjegyzék 2.0-s verziója alapján. [Hozzon létre egy Azure-tárolót beállításjegyzék](/azure/container-registry/container-registry-get-started-azure-cli). DockerHub is használható.

### <a name="set-up-the-jenkins-job-for-docker"></a>A Docker a Jenkins feladat beállítása

1. Hozzon létre egy új **freestyle** projekt a Jenkins irányítópulton.
2. Konfigurálja a **forrás kód felügyeleti** a helyi elágazás a használandó mezőt a [egyszerű Azure Java-webalkalmazás](https://github.com/azure-devops/javawebappsample). Adja meg a **tárház URL-CÍMÉT** érték. Például: http://github.com/&lt;your_ID > / javawebappsample.
3. A projekt elkészítéséhez hozzáadásával Maven használatával lépés hozzáadása egy **hajtható végre a rendszerhéj** parancsot. A következő sor szerepeljenek a parancsban:
    ```bash
    mvn clean package
    ```

4. Adja hozzá a létrehozás után végrehajtandó művelet kiválasztásával **közzététele az Azure Web Apps**.
5. Adja meg **mySp** , az Azure szolgáltatás egyszerű. Rendszerbiztonsági tárolta, mint a [Azure hitelesítő adatok](#service-principal) az előző lépésben.
6. Az a **Alkalmazáskonfiguráció** területen válassza ki az erőforráscsoportot és egy Linux-webalkalmazást az előfizetésben.
7. Válasszon **keresztül Docker közzététele**.
8. Töltse ki a **Dockerfile** elérési út értéke. Az alapértelmezett érték /Dockerfile biztosítható.
Az a **Docker beállításjegyzék URL-cím** értéket, az URL-címet megadni a formátum https://&lt;yourRegistry >. Ha Azure-tároló rendszerleíró azurecr.io. Ha DockerHub használatához hagyja üresen az érték.
9. Az a **beállításjegyzék hitelesítő adatok** értékét, adja hozzá a tároló beállításjegyzék hitelesítő adatait. Az Azure parancssori felületen az alábbi parancsok futtatásával beszerezheti a felhasználói azonosítót és jelszót. Az első parancs lehetővé teszi, hogy a rendszergazdai fiók:
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. A Docker kép neve és a címke értéke a **speciális** lapon opcionálisak. Alapértelmezés szerint a lemezkép neve értékét a lemezkép neve az Azure-portálon a konfigurált szerzett a **Docker-tároló** beállítást. A címke $BUILD_NUMBER jönnek létre.
    > [!NOTE]
    > Ügyeljen arra, hogy adja meg a lemezkép neve az Azure portálon, vagy adjon meg egy **Docker kép** értéket a **speciális** fülre. Például állítsa be a **Docker kép** egy érték &lt;your_Registry >.azurecr.io/calculator és annak megszüntetéséről a **Docker Képcímke** érték üres.

11. A telepítés sikertelen lesz, ha a beépített Docker lemezkép beállítást használja. Módosítsa a Docker konfigurációját, és az egyéni lemezképet használ a **Docker-tároló** beállítása az Azure portálon. Beépített-lemezképek a fájl feltöltése megközelítés telepítéséhez használható.
12. A fájl feltöltése megközelítés hasonló, választhat egy másik **tárolóhely** eltérő nevet **éles**.
13. Mentse, és a projekt felépítéséhez. A tároló lemezképét a rendszer előkészítésre továbbít a beállításjegyzékben, és a webes alkalmazás telepítve van.

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>A tárolók webalkalmazás telepítése Jenkins Kimenetátirányításának segítségével

1. A GitHub-felületén, nyissa meg a **Jenkinsfile_container_plugin** fájlt. A fájl szerkesztéséhez válassza ki a ceruza ikonra. Frissítés a **resourceGroup** és **webAppName** a webalkalmazás a definíciók sorok 11 és 12, illetve:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Módosítsa a tároló beállításjegyzék kiszolgáló sort 13:
    ```java
    def registryServer = '<registryURL>'
    ```

3. Módosítsa a Hitelesítőadat-azonosító használata Jenkins betűtípusainak 16 sort:
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Hozzon létre egy Jenkins folyamat    

1. Nyissa meg a Jenkins egy webböngészőben. Válassza ki **új elem**.
2. Adjon meg egy nevet a feladatot, és válasszon **csővezeték**. Kattintson az **OK** gombra.
3. Válassza ki a **csővezeték** fülre.
4. Az a **Definition** érték, jelölje be **parancsfájl az SCM-feldolgozási folyamat**.
5. Az a **SCM** érték, jelölje be **Git**. Adja meg a GitHub URL-címet a villás tárházban. Például: https://&lt;your_forked_repo > .git.
7. Frissítés a **parancsfájl elérési útján** egy érték **Jenkinsfile_container_plugin**.
8. Válassza ki **mentése** lehetőséget és futtatni a feladatot.

## <a name="verify-your-web-app"></a>A webalkalmazás ellenőrzése

1. Győződjön meg arról, hogy a WAR-fájl sikeresen telepítve lett a webalkalmazás, nyissa meg webböngészővel.
2. Keresse fel a http://&lt;your_app_name >.azurewebsites.net/api/calculator/ping. Cserélje le &lt;your_app_name > a webalkalmazás nevét. Az üzenet jelenik meg:
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. Keresse fel a http://&lt;your_app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y >. Cserélje le &lt;x > és &lt;y > bármely számokkal x összege beolvasandó + y. A Számológép összegét mutatja: ![Számológép: hozzáadása](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-azure-app-service-on-linux"></a>Azure App Service Linux rendszeren

1. A webalkalmazás ellenőrzéséhez futtassa a következő parancsot az Azure parancssori felület:
    ```CLI
    az acr repository list -n <myRegistry> -o json
    ```
    A következő üzenet jelenik meg:
    ```CLI
    ["calculator"]
    ```
    
2. Keresse fel a http://&lt;your_app_name >.azurewebsites.net/api/calculator/ping. Cserélje le &lt;your_app_name > a webalkalmazás nevét. Az üzenet jelenik meg: 
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

3. Keresse fel a http://&lt;your_app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y >. Cserélje le &lt;x > és &lt;y > bármely számokkal x összege beolvasandó + y.
    
## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure App Service Jenkins beépülő modul központi telepítéséhez használt Azure-bA.

Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure App Service segítségével FTP telepítése Jenkins konfigurálása 
> * Központi telepítése a webes alkalmazás a tárolók Jenkins konfigurálása 
