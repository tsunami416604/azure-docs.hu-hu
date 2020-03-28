---
title: 'Oktatóanyag: Üzembe helyezés a GitHubról az Azure App Service szolgáltatásba jenkins-szel'
description: Jenkins beállítása folyamatos integrációra (CI) a GitHubról és a folyamatos üzembe helyezéshez (CD) az Azure App Service for Java webalkalmazásokba
ms.topic: tutorial
ms.date: 10/23/2019
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 9fcf178b71ac1f07bfb58cd2502701ae5392b472
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74158403"
---
# <a name="tutorial-deploy-from-github-to-azure-app-service-with-jenkins-continuous-integration-and-deployment"></a>Oktatóanyag: Üzembe helyezés a GitHubról az Azure App Service szolgáltatásba a Jenkins folyamatos integrációval és üzembe helyezéssel

Ez az oktatóanyag egy minta Java webalkalmazást telepít a GitHubról [az Azure App Service Linuxra](/azure/app-service/containers/app-service-linux-intro) a folyamatos integráció (CI) és a folyamatos üzembe helyezés (CD) jenkins-i beállításával. Amikor frissíti az alkalmazást a GitHubra való véglegesítések lenyomásával, a Jenkins automatikusan létrehozza és újra közzéteszi az alkalmazást az Azure App Service szolgáltatásban. Az oktatóanyagminta alkalmazása a [Spring Boot](https://projects.spring.io/spring-boot/) keretrendszer használatával lett kifejlesztve. 

![A GitHub és az Azure App Service üzembe helyezése – áttekintés](media/tutorial-jenkins-deploy-web-app-azure-app-service/azure-continuous-integration-deployment-overview.png)

Ebben az oktatóanyagban az alábbi feladatokat hajthatja végre:

> [!div class="checklist"]
> * Telepítse a Jenkins beépülő modulokat, hogy a GitHubról építhessen, telepíthessen az Azure App Service-be és egyéb kapcsolódó feladatokat.
> * Elágazás a minta GitHub-tárlató, így van egy működő példányát.
> * Csatlakoztassa a Jenkinst a GitHubhoz.
> * Hozzon létre egy egyszerű Azure-szolgáltatást, hogy a Jenkins a hitelesítő adatok használata nélkül is elérhesse az Azure-t.
> * Adja hozzá az egyszerű szolgáltatásszolgáltatást a Jenkinshez.
> * Hozza létre a Jenkins-folyamatot, amely létrehozza és telepíti a mintaalkalmazást minden alkalommal, amikor frissíti az alkalmazást a GitHubon.
> * Hozzon létre build- és üzembe helyezési fájlokat a Jenkins-folyamathoz.
> * Irányítsa a Jenkins-folyamatot a build- és üzembe helyezési parancsfájlra.
> * Manuális build futtatásával telepítheti mintaalkalmazását az Azure-ba.
> * Leküldéses egy alkalmazásfrissítés a GitHubon, amely elindítja a Jenkins-t az Azure-ba való létrehozáshoz és újratelepítéshez.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez az alábbi elemekre van szükség:

* Egy Azure Linux virtuális gépre telepített Java Development Kit (JDK) és Maven eszközökkel rendelkező [Jenkins-kiszolgáló](https://jenkins.io/)

  Ha nem rendelkezik Jenkins-kiszolgálóval, hajtsa végre az alábbi lépéseket az Azure Portalon: [Jenkins-kiszolgáló létrehozása Egy Azure Linux virtuális gépen](/azure/jenkins/install-jenkins-solution-template)

* Egy [GitHub-fiók,](https://github.com) így egy működő példányt (villát) kaphat a minta Java webalkalmazáshoz. 

* [Az Azure CLI](/cli/azure/install-azure-cli), amelyet a helyi parancssorból vagy az [Azure Cloud Shellből](/azure/cloud-shell/overview) futtathat

## <a name="install-jenkins-plug-ins"></a>A Jenkins beépülő modulok telepítése

1. Jelentkezzen be a Jenkins webkonzolra ezen a helyen:

   `https://<Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`

1. A Jenkins főlapján válassza a > **Jenkins-bővítmények kezelése lehetőséget.** **Manage Jenkins**

   ![Jenkins beépülő modulok kezelése](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-plugins.png)

1. Az **Elérhető** lapon jelölje ki a következő bővítményeket:

   - [Azure App Service](https://plugins.jenkins.io/azure-app-service)
   - [GitHub ágforrása](https://plugins.jenkins.io/github-branch-source)
   - Jenkins [környezet befecskendező beépülő modul](https://plugins.jenkins.io/envinject)
   - [Azure-beli Hitelesítő adatok](https://plugins.jenkins.io/azure-credentials)

   Ha ezek a beépülő modulok nem jelennek meg, ellenőrizze, hogy nincsenek-e még telepítve a **Telepített** lapon.

1. A kiválasztott bővítmények telepítéséhez válassza a Letöltés most lehetőséget, és az újraindítás után telepítse a **programot.**

1. Miután elkészült, a Jenkins menüben válassza a **Jenkins kezelése** lehetőséget, hogy a későbbi lépésekhez térjen vissza a Jenkins-kezelés lapra.

## <a name="fork-sample-github-repo"></a>Villa minta GitHub-tárpa

1. [Jelentkezzen be a GitHub-tárházban a tavaszi rendszerindítási mintaalkalmazáshoz.](https://github.com/spring-guides/gs-spring-boot) 

1. A GitHub jobb felső sarkában válassza az **Elágazás**lehetőséget.

   ![Villa mintatára a GitHubról](media/tutorial-jenkins-deploy-web-app-azure-app-service/fork-github-repo.png)

1. Kövesse az utasításokat a GitHub-fiók kiválasztásához és a forking befejezéséhez.

Ezután állítsa be a Jenkinst a GitHub-hitelesítő adataival.

## <a name="connect-jenkins-to-github"></a>Jenkins csatlakoztatása a GitHubhoz

Ha azt szeretné, hogy a Jenkins figyelje a GitHubot, és válaszoljon, amikor új véglegesítések leküldéses a webalkalmazásba a GitHub-eltakad, engedélyezze a [GitHub webhooks](https://developer.github.com/webhooks/) a Jenkinsben.

> [!NOTE]
> 
> Ezek a lépések személyes hozzáférési jogkivonat-hitelesítő adatokat hoznak létre a Jenkins számára a GitHub felhasználónevével és jelszavával való együttműködésre. 
> Ha azonban a GitHub-fiók kétfaktoros hitelesítést használ, hozza létre a jogkivonatot a GitHubon, és állítsa be a Jenkinst, hogy ezt a jogkivonatot használja helyette. 
> További információt a [Jenkins GitHub beépülő modul dokumentációjában talál.](https://wiki.jenkins.io/display/JENKINS/GitHub+Plugin)

1. A **Manage Jenkins (Jenkins kezelése)** lapon válassza a **Rendszer konfigurálása**lehetőséget. 

   ![Rendszer konfigurálása a Jenkinsben](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-configure-system.png)

1. A **GitHub-szakaszban** adja meg a GitHub-kiszolgáló részleteit. A **GitHub-kiszolgáló hozzáadása** listában válassza a **GitHub-kiszolgáló lehetőséget.** 

   ![GitHub-kiszolgáló hozzáadása a Jenkinsben](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-GitHub-server.png)

1. Ha a **Manage hooks** tulajdonság nincs kijelölve, jelölje be ezt a tulajdonságot. Válassza a **Speciális** lehetőséget, hogy más beállításokat is megadhasson. 

   ![Speciális Jenkins-beállítások megadása a GitHub-kiszolgálóhoz](media/tutorial-jenkins-deploy-web-app-azure-app-service/advanced-GitHub-settings.png)

1. A **További GitHub-műveletek kezelése** listában válassza **a Bejelentkezés és jelszó konvertálása tokenre lehetőséget.**

   ![A bejelentkezésés a jelszó konvertálása a GitHub tokenjével](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-additional-actions.png)

1. Válassza **a Bejelentkezési és jelszó,** így megadhatja a GitHub felhasználónevét és jelszavát. Ha elkészült, válassza **a Jogkivonat-hitelesítő adatok létrehozása**lehetőséget, amely létrehoz egy [GitHub-személyes hozzáférési jogkivonatot (PAT).](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)   

   ![GitHub személyes hozzáférési jogkivonat (PAT) létrehozása bejelentkezési név és jelszó alapján](media/tutorial-jenkins-deploy-web-app-azure-app-service/create-github-token-credentials.png)

1. A **GitHub-kiszolgáló** szakaszban a **Hitelesítő adatok** listájában válassza ki az új jogkivonatot. Ellenőrizze, hogy működik-e a hitelesítés a **Kapcsolat tesztelése**lehetőséget választva.

   ![A GitHub-kiszolgálóval való kapcsolat ellenőrzése új PAT-tal](media/tutorial-jenkins-deploy-web-app-azure-app-service/check-github-connection.png)

Ezután hozza létre az Azure egyszerű azure-szolgáltatást, amelyet a Jenkins az Azure-erőforrások hitelesítéséhez és eléréséhez használ.

## <a name="create-service-principal"></a>Egyszerű szolgáltatás létrehozása

Egy későbbi szakaszban hozzon létre egy Jenkins-folyamat feladatot, amely létrehozza az alkalmazást a GitHubról, és telepíti az alkalmazást az Azure App Service-be. Ha azt szeretné, hogy a Jenkins a hitelesítő adatok megadása nélkül férhessen hozzá az Azure-hoz, hozzon létre egy [egyszerű szolgáltatást](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) az Azure Active Directory jenkins-ben. Az egyszerű szolgáltatás egy külön identitás, amelyet a Jenkins használhat az Azure-erőforrásokhoz való hozzáférés hitelesítéséhez. Az egyszerű szolgáltatás létrehozásához futtassa [**`az ad sp create-for-rbac`**](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)az Azure CLI parancsot, például a helyi parancssorból vagy az Azure Cloud Shellből: 

```azurecli-interactive
az ad sp create-for-rbac --name "yourAzureServicePrincipalName" --password yourSecurePassword
```

Győződjön meg arról, hogy idézőjeleket használ az egyszerű szolgáltatás neve körül. Emellett hozzon létre egy erős jelszót az [Azure Active Directory jelszószabályai és korlátozásai](/azure/active-directory/active-directory-passwords-policy)alapján. Ha nem ad meg jelszót, az Azure CLI létrehoz egy jelszót az Ön számára. 

Itt van a kimenet által **`create-for-rbac`** generált parancs: 

```json
{
   "appId": "yourAzureServicePrincipal-ID", // A GUID such as AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA
   "displayName": "yourAzureServicePrincipalName", // A user-friendly name for your Azure service principal
   "name": "http://yourAzureServicePrincipalName",
   "password": "yourSecurePassword",
   "tenant": "yourAzureActiveDirectoryTenant-ID" // A GUID such as BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB
}
```

> [!TIP]
> 
> Ha már rendelkezik egy egyszerű szolgáltatással, újra felhasználhatja ezt az identitást.
> A hitelesítésegyszerű szolgáltatásértékek megadásakor `appId`használja `password`a `tenant` , és a tulajdonságértékeket. 
> Meglévő egyszerű szolgáltatás keresésekor használja `displayName` a tulajdonság értékét.

## <a name="add-service-principal-to-jenkins"></a>Egyszerű szolgáltatás hozzáadása a Jenkinshez

1. A Jenkins főlapján válassza a **Hitelesítő adatok** > **rendszere lehetőséget.** 

1. A **Rendszer** lap **Domain (Tartomány)** területén válassza a **Globális hitelesítő adatok (korlátlan)** lehetőséget.

1. A bal oldali menüben válassza **a Hitelesítő adatok hozzáadása parancsot.**

1. A **Kind listából** válassza az **Azure Service Principal**lehetőséget.

1. Adja meg az egyszerű szolgáltatás és az Azure-előfizetés adatait a táblázat által ebben a lépésben leírt tulajdonságokban:

   ![Azure-szolgáltatásnév hitelesítő adatainak hozzáadása](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-service-principal-credentials.png)

   | Tulajdonság | Érték | Leírás | 
   |----------|-------|-------------| 
   | **Előfizetés azonosítója** | <*yAzureSubscription-ID*> | Az Azure-előfizetés GUID-értéke <p>**Tipp**: Ha nem ismeri az Azure-előfizetés-azonosítóját, futtassa ezt az Azure CLI parancsot a parancssorból vagy a Cloud Shellben, majd használja a `id` GUID értéket: <p>`az account list` | 
   | **Ügyfélazonosító** | <*önAzureServicePrincipal-ID*> | Az `appId` Azure egyszerű szolgáltatásához korábban létrehozott GUID-érték | 
   | **Ügyfél titok** | <*aSecurePassword*> | Az `password` Azure egyszerű szolgáltatásához megadott érték vagy "titkos" | 
   | **Bérlőazonosító** | <*yourAzureActiveDirectoryTenant-ID*> | Az `tenant` Azure Active Directory-bérlő GUID-értéke | 
   | **Id** | <*önAzureServicePrincipalName*> | Az `displayName` Azure egyszerű szolgáltatásának értéke | 

1. A szolgáltatásnév működésének ellenőrzéséhez válassza az **Egyszerű szolgáltatás ellenőrzése lehetőséget.** Amikor elkészült, válassza az **OK** lehetőséget.

Ezután hozza létre a Jenkins-folyamatot, amely létrehozza és telepíti az alkalmazást.

## <a name="create-jenkins-pipeline"></a>Jenkins-folyamat létrehozása

A Jenkinsben hozza létre a folyamatfeladatot az alkalmazás létrehozásához és üzembe helyezéséhez.

1. Térjen vissza a Jenkins kezdőlapjára, és válassza **az Új elem lehetőséget.** 

   ![Jenkins-folyamat létrehozása](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-new-item.png)

1. Adja meg a folyamatfolyamat-feladat nevét, például "My-Java-Web-App", és válassza **a Folyamat lehetőséget.** Alul válassza az **OK gombot.**  

   ![A Jenkins-folyamat feladat elnevezése](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-pipeline.png)

1. Állítsa be a Jenkinst az egyszerű szolgáltatással, hogy a Jenkins saját hitelesítő adatai nélkül telepíthesse az Azure-ba.

   1. Az **Általános** lapon válassza a **Környezet előkészítése a futtatáshoz**lehetőséget. 

   1. A **megjelenő Tulajdonságok tartalom** mezőben adja hozzá ezeket a környezeti változókat és azok értékeit. 

      ```ini
      AZURE_CRED_ID=yourAzureServicePrincipalName
      RES_GROUP=yourWebAppAzureResourceGroupName
      WEB_APP=yourWebAppName
      ```

      ![Környezet előkészítése a futtatáshoz és a környezeti változók beállítása](media/tutorial-jenkins-deploy-web-app-azure-app-service/prepare-environment-for-jenkins-run.png)

1. Amikor elkészült, válassza a **Mentés** lehetőséget.

Ezután hozzon létre build- és üzembe helyezési parancsfájlokat a Jenkins számára.

## <a name="create-build-and-deployment-files"></a>Build- és telepítési fájlok létrehozása

Most hozza létre azokat a fájlokat, amelyeket a Jenkins az alkalmazás létrehozásához és üzembe helyezéséhez használ.

1. A GitHub-előétel `src/main/resources/` mappájában hozza létre ezt `web.config`az alkalmazás konfigurációs `$(JAR_FILE_NAME)` `gs-spring-boot-0.1.0.jar`fájlját, amely tartalmazza ezt az XML-t, de lecseréli a következőre:

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
      <system.webServer>
         <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
         </handlers>
         <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\${JAR_FILE_NAME}&quot;"></httpPlatform>
      </system.webServer>
   </configuration>
   ```

1. A GitHub-villa gyökérmappájában hozza létre ezt a `Jenkinsfile`build- és telepítési parancsfájlt, amely ezt a szöveget tartalmazza ([forrás a GitHubon itt):](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se)

   ```groovy
   node {
      stage('init') {
         checkout scm
      }
      stage('build') {
         sh '''
            mvn clean package
            cd target
            cp ../src/main/resources/web.config web.config
            cp todo-app-java-on-azure-1.0-SNAPSHOT.jar app.jar 
            zip todo.zip app.jar web.config
         '''
      }
      stage('deploy') {
         azureWebAppPublish azureCredentialsId: env.AZURE_CRED_ID,
         resourceGroup: env.RES_GROUP, appName: env.WEB_APP, filePath: "**/todo.zip"
      }
   }
   ```

1. Véglegesítse `Jenkinsfile` mind a fájlokat `web.config` a GitHub-eltadra, és lenyomja a módosításokat.

## <a name="point-pipeline-at-script"></a>Pontfolyamat a parancsfájlnál

Most adja meg a build és üzembe helyezési parancsfájlt, amelyet a Jenkins használni szeretne.

1. A Jenkins ben válassza ki a korábban létrehozott folyamatfeladatot. 

   ![Válassza ki a Jenkins-folyamat feladatot a webalkalmazáshoz](media/tutorial-jenkins-deploy-web-app-azure-app-service/select-pipeline-job.png)

1. A bal oldali menüben válassza a **Konfigurálás parancsot.**

1. A **Folyamat** lap **Definíció** listájában válassza a Pipeline parancsfájl t **az SCM listából.**

   1. A megjelenő **SCM** mezőben válassza a **Git** lehetőséget forrásvezérlőként. 

   1. Az **Adattárak** szakaszban a **Tárház URL-címéhez**adja meg a GitHub-előd URL-címét, például: 

      `https://github.com/<your-GitHub-username>/gs-spring-boot`

   1. A **hitelesítő adatok,** válassza ki a korábban létrehozott GitHub személyes hozzáférési jogkivonatot.

   1. A **Parancsfájl elérési útja** mezőben adja hozzá az elérési utat a "Jenkinsfile" parancsfájlhoz.

   Ha elkészült, a folyamatdefiníció a következő példához hasonlóan néz ki: 

   ![A Jenkins-folyamat rámutatása a parancsfájlra](media/tutorial-jenkins-deploy-web-app-azure-app-service/set-up-jenkins-github.png)

1. Amikor elkészült, válassza a **Mentés** lehetőséget.

Ezután készítse el és telepítse alkalmazását az Azure App Service-be. 

## <a name="build-and-deploy-to-azure"></a>Az Azure-ban való üzembe helyezés és üzembe helyezés

1. Az Azure CLI-vel akár a parancssorból, akár az Azure Cloud Shellből hozzon létre egy [Azure App Service webalkalmazást Linuxon,](/azure/app-service/containers/app-service-linux-intro) ahol a Jenkins telepíti a webalkalmazást a build befejezése után. Győződjön meg arról, hogy a webalkalmazás egyedi névvel rendelkezik.

   ```azurecli-interactive
   az group create --name yourWebAppAzureResourceGroupName --location yourAzureRegion
   az appservice plan create --name yourLinuxAppServicePlanName --resource-group yourWebAppAzureResourceGroupName --is-linux
   az webapp create --name yourWebAppName --resource-group yourWebAppAzureResourceGroupName --plan yourLinuxAppServicePlanName --runtime "java|1.8|Tomcat|8.5"
   ```

   Az Azure CLI-parancsokról az alábbi lapokon talál további információt:

   * [**`az group create`**](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

   * [**`az appservice plan create`**](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)

   * [**`az webapp create`**](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)

1. A Jenkins ben válassza ki a folyamatfolyamat-feladatot, és válassza **a Build Now**lehetőséget.

   A build befejezése után a Jenkins telepíti az alkalmazást, amely most már élőben jelenik meg az Azure-ban a kiadvány URL-címén, például: 

   `http://<your-Java-web-app>.azurewebsites.net`

   ![Az üzembe helyezett alkalmazás megtekintése az Azure-ban](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-unedited.png)

## <a name="push-changes-and-redeploy"></a>Módosítások leküldése és ismételt üzembe helyezés

1. A webböngészőben nyissa meg ezt a helyet a webalkalmazás GitHub-eltel:

   `complete/src/main/java/Hello/Application.java`
   
1. A GitHub jobb felső sarkában válassza **a Fájl szerkesztése**lehetőséget.

1. Módosítsa ezt a `commandLineRunner()` módszert a metóduson, és véglegesítse a módosítást a tárnaágának. `master` Ez a `master` véglegesítés az ágban létrehoz egy buildet a Jenkinsben. 
   
   ```java
   System.out.println("Let's inspect the beans provided by Spring Boot on Azure");
   ```

1. Miután a build befejeződött, és a Jenkins újratelepíti az Azure-ba, frissítse az alkalmazást, amely most már megjeleníti a frissítést.

   ![Az üzembe helyezett alkalmazás megtekintése az Azure-ban](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-edited.png)

## <a name="troubleshooting-the-jenkins-plug-in"></a>A Jenkins beépülő modul hibaelhárítása

Ha bármilyen hibát tapasztal a Jenkins beépülő modulokkal, nyújtson be egy problémát a [Jenkins JIRA-ban](https://issues.jenkins-ci.org/) az adott összetevőhöz.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure-beli virtuális gépek buildügynökként való használata](/azure/jenkins/jenkins-azure-vm-agents)
