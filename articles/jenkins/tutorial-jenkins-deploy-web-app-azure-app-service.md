---
title: 'Oktatóanyag: üzembe helyezés a GitHubról Azure App Service a Jenkins használatával'
description: A a GitHub és a folyamatos üzembe helyezés (CD) és a Java-webalkalmazások Azure App Serviceához készült Jenkins beállítása a folyamatos integrációhoz (CI)
ms.topic: tutorial
ms.date: 10/23/2019
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 9fcf178b71ac1f07bfb58cd2502701ae5392b472
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158403"
---
# <a name="tutorial-deploy-from-github-to-azure-app-service-with-jenkins-continuous-integration-and-deployment"></a>Oktatóanyag: üzembe helyezés a GitHubról Azure App Service a Jenkins folyamatos integrációja és üzembe helyezése révén

Ez az oktatóanyag egy minta Java-webalkalmazást helyez üzembe a GitHubról a [Linuxon való Azure app Service a](/azure/app-service/containers/app-service-linux-intro) folyamatos integráció (CI) és a folyamatos üzembe helyezés (CD) a Jenkins-ben való létrehozásával. Ha a véglegesítések a GitHubba való küldésével frissíti az alkalmazást, a Jenkins automatikusan létrehozza és újból közzéteszi az alkalmazást Azure App Service. Az oktatóanyagban szereplő minta alkalmazás a [Spring boot](https://projects.spring.io/spring-boot/) Framework használatával lett kifejlesztve. 

![A GitHub és a Azure App Service üzembe helyezés áttekintése](media/tutorial-jenkins-deploy-web-app-azure-app-service/azure-continuous-integration-deployment-overview.png)

Ebben az oktatóanyagban a következő feladatokat hajtja végre:

> [!div class="checklist"]
> * Telepítse a Jenkins beépülő modulokat, hogy létrejöjjön a GitHubról, üzembe helyezhető Azure App Service és egyéb kapcsolódó feladatokhoz.
> * A GitHub-adattárat úgy tekintheti meg, hogy rendelkezik egy működő példánnyal.
> * A Jenkins és a GitHub összekapcsolását.
> * Hozzon létre egy Azure-beli egyszerű szolgáltatást, hogy a Jenkins hozzáférhessen az Azure-hoz a hitelesítő adatok használata nélkül.
> * Adja hozzá a szolgáltatásnevet a Jenkins szolgáltatáshoz.
> * Hozza létre a Jenkins-folyamatot, amely minden alkalommal létrehozza és telepíti a minta alkalmazást, amikor a GitHubon frissíti az alkalmazást.
> * Hozza létre a Jenkins-folyamat létrehozási és telepítési fájljait.
> * Mutasson a Jenkins-folyamatra a build és üzembe helyezési parancsfájlban.
> * Egy manuális Build futtatásával üzembe helyezheti a minta alkalmazást az Azure-ban.
> * Egy alkalmazás frissítésének leküldése a GitHubon, amely elindítja a Jenkins-t az Azure-ban való létrehozásához és újbóli üzembe helyezéséhez

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz ezekre az elemekre:

* Egy Azure Linux rendszerű virtuális gépen telepített Java Development Kit (JDK) és Maven-eszközöket tartalmazó [Jenkins](https://jenkins.io/) -kiszolgáló

  Ha nem rendelkezik Jenkins-kiszolgálóval, hajtsa végre ezeket a lépéseket most a Azure Portal: [hozzon létre Jenkins-kiszolgálót egy Azure Linux rendszerű virtuális gépen](/azure/jenkins/install-jenkins-solution-template)

* Egy [GitHub](https://github.com) -fiókkal, hogy a minta Java-webalkalmazáshoz egy működő másolatot (elágazást) kapjon. 

* [Azure CLI](/cli/azure/install-azure-cli), amely a helyi parancssorból vagy [Azure Cloud Shell](/azure/cloud-shell/overview) futtatható

## <a name="install-jenkins-plug-ins"></a>A Jenkins beépülő modulok telepítése

1. Jelentkezzen be a Jenkins webkonzolra ezen a helyen:

   `https://<Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`

1. A Jenkins főoldalán válassza a **Jenkins kezelése** > a **beépülő modulok kezelése**lehetőséget.

   ![Jenkins beépülő modulok kezelése](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-plugins.png)

1. A **rendelkezésre álló** lapon válassza ki az alábbi beépülő modulokat:

   - [Azure App Service](https://plugins.jenkins.io/azure-app-service)
   - [GitHub-ág forrása](https://plugins.jenkins.io/github-branch-source)
   - Jenkins [-környezet injektor beépülő modulja](https://plugins.jenkins.io/envinject)
   - [Azure-beli hitelesítő adatok](https://plugins.jenkins.io/azure-credentials)

   Ha ezek a beépülő modulok nem jelennek meg, győződjön meg arról, hogy még nincsenek telepítve a **telepített** lapon.

1. A kiválasztott beépülő modulok telepítéséhez válassza a **Letöltés most lehetőséget, majd az újraindítás után telepítse a telepítést**.

1. Miután elkészült, a Jenkins menüben válassza a **Jenkins kezelése** lehetőséget, hogy a későbbi lépésekhez térjen vissza a Jenkins-felügyeleti oldalára.

## <a name="fork-sample-github-repo"></a>Az elágazási minta GitHub-tárháza

1. [Jelentkezzen be a GitHub-tárházba a Spring boot-minta alkalmazáshoz](https://github.com/spring-guides/gs-spring-boot). 

1. A GitHub jobb felső sarkában válassza az **elágazás**lehetőséget.

   ![Elágazási példa a GitHubról](media/tutorial-jenkins-deploy-web-app-azure-app-service/fork-github-repo.png)

1. Az utasításokat követve válassza ki a GitHub-fiókját, és fejezze be az elágazást.

Ezután állítsa be a Jenkins-t a GitHub hitelesítő adataival.

## <a name="connect-jenkins-to-github"></a>A Jenkins összekötése a GitHubtal

Ahhoz, hogy a Jenkins figyelje a GitHubot, és válaszoljon arra, amikor új véglegesíti a webalkalmazását a GitHub-elágazásban, engedélyezze a [GitHub-webhookokat](https://developer.github.com/webhooks/) a Jenkins-ben.

> [!NOTE]
> 
> Ezek a lépések személyes hozzáférési jogkivonat hitelesítő adatait hoznak létre a Jenkins számára a GitHub-Felhasználónév és-jelszó használatával. 
> Ha azonban a GitHub-fiókja kétfaktoros hitelesítést használ, hozza létre a tokent a GitHubon, és állítsa be a Jenkinst a jogkivonat használatára. 
> További információkért lásd a [Jenkins GitHub beépülő moduljának](https://wiki.jenkins.io/display/JENKINS/GitHub+Plugin) dokumentációját.

1. A **Jenkins kezelése** lapon válassza a **rendszer konfigurálása**lehetőséget. 

   ![A System beállítása a Jenkins-ben](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-configure-system.png)

1. A **GitHub** szakaszban adja meg a GitHub-kiszolgáló adatait. A **GitHub-kiszolgáló hozzáadása** listában válassza a **GitHub-kiszolgáló**lehetőséget. 

   ![GitHub-kiszolgáló hozzáadása a Jenkins-ben](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-GitHub-server.png)

1. Ha nincs kiválasztva a **hookok kezelése** tulajdonság, válassza ki ezt a tulajdonságot. Válassza a **speciális** lehetőséget, így további beállításokat is megadhat. 

   ![A GitHub-kiszolgáló speciális Jenkins-beállításainak megadása](media/tutorial-jenkins-deploy-web-app-azure-app-service/advanced-GitHub-settings.png)

1. A **további GitHub-műveletek kezelése** listából válassza a **Bejelentkezés és jelszó konvertálása a tokenbe**lehetőséget.

   ![A bejelentkezési azonosító és a jelszó konvertálása a GitHub-tokenre](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-additional-actions.png)

1. Válassza ki **a bejelentkezési azonosítót és a jelszót** , így megadhatja a GitHub felhasználónevét és jelszavát. Ha elkészült, válassza a **jogkivonathoz tartozó hitelesítő adatok létrehozása**lehetőséget, amely létrehoz egy [GitHub személyes hozzáférési jogkivonatot (Pat)](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   

   ![GitHub PAT létrehozása a bejelentkezés és a jelszó alapján](media/tutorial-jenkins-deploy-web-app-azure-app-service/create-github-token-credentials.png)

1. A **GitHub-kiszolgáló** szakaszban, a **hitelesítő adatok** listából válassza ki az új jogkivonatot. Ellenőrizze, hogy a hitelesítés működik-e a **kapcsolat tesztelése**lehetőség kiválasztásával.

   ![A GitHub-kiszolgálóval létesített kapcsolatok keresése új PAT használatával](media/tutorial-jenkins-deploy-web-app-azure-app-service/check-github-connection.png)

Ezután hozza létre azt az Azure-szolgáltatásnevet, amelyet a Jenkins használ az Azure-erőforrások hitelesítéséhez és eléréséhez.

## <a name="create-service-principal"></a>Egyszerű szolgáltatás létrehozása

Egy későbbi szakaszban egy Jenkins-feldolgozási feladatot hoz létre, amely létrehozza az alkalmazást a GitHubon, és üzembe helyezi az alkalmazást Azure App Service. Ahhoz, hogy a Jenkins hozzáférhessen az Azure-hoz a hitelesítő adatok megadása nélkül, hozzon létre egy egyszerű szolgáltatásnevet a Azure Active Directory for Jenkins [szolgáltatásban](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) . Az egyszerű szolgáltatásnév egy különálló identitás, amelyet a Jenkins használhat az Azure-erőforrásokhoz való hozzáférés hitelesítéséhez. Az egyszerű szolgáltatás létrehozásához futtassa az Azure CLI-parancsot [ **`az ad sp create-for-rbac`** ](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)a helyi parancssorból vagy Azure Cloud Shellból, például: 

```azurecli-interactive
az ad sp create-for-rbac --name "yourAzureServicePrincipalName" --password yourSecurePassword
```

Ügyeljen arra, hogy idézőjelek között használja az egyszerű szolgáltatásnév nevét. Emellett hozzon létre egy erős jelszót a [Azure Active Directory jelszavának szabályai és korlátozásai](/azure/active-directory/active-directory-passwords-policy)alapján. Ha nem ad meg jelszót, az Azure CLI létrehoz egy jelszót. 

A **`create-for-rbac`** parancs által generált kimenet: 

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
> Ha már rendelkezik egy egyszerű szolgáltatással, ezt a személyazonosságot újra használhatja.
> Ha egyszerű szolgáltatásnév-értékeket ad meg a hitelesítéshez, használja a `appId`, `password`és `tenant` tulajdonságértékek értékét. 
> Egy meglévő szolgáltatásnév keresésekor használja a `displayName` tulajdonság értékét.

## <a name="add-service-principal-to-jenkins"></a>Egyszerű szolgáltatásnév hozzáadása a Jenkins szolgáltatáshoz

1. A Jenkins főoldalán válassza a **hitelesítő adatok** > **rendszer**lehetőséget. 

1. A **rendszer** lap **tartomány**területén válassza a **globális hitelesítő adatok (korlátlan)** lehetőséget.

1. A bal oldali menüben válassza a **hitelesítő adatok hozzáadása**elemet.

1. A **fajta** listából válassza ki az **Azure-szolgáltatásnév**elemet.

1. Adja meg a szolgáltatásnév és az Azure-előfizetés adatait az ebben a lépésben szereplő táblázatban leírt tulajdonságokkal:

   ![Azure-szolgáltatásnév hitelesítő adatainak hozzáadása](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-service-principal-credentials.png)

   | Tulajdonság | Érték | Leírás | 
   |----------|-------|-------------| 
   | **Előfizetés azonosítója** | <*yourAzureSubscription-ID*> | Az Azure-előfizetéshez tartozó GUID-érték <p>**Tipp**: Ha nem ismeri az Azure-előfizetés azonosítóját, futtassa ezt az Azure CLI-parancsot a parancssorból vagy a Cloud shell, majd használja a `id` GUID értéket: <p>`az account list` | 
   | **Ügyfél-azonosító** | <*yourAzureServicePrincipal-ID*> | Az Azure-szolgáltatáshoz korábban létrehozott `appId` GUID-érték | 
   | **Ügyfél titka** | <*yourSecurePassword*> | Az Azure-szolgáltatáshoz megadott `password` érték vagy titkos kulcs | 
   | **Bérlő azonosítója** | <*yourAzureActiveDirectoryTenant-ID*> | A Azure Active Directory bérlő `tenant` GUID-értéke | 
   | **Azonosító** | <*yourAzureServicePrincipalName*> | Az Azure-szolgáltatásnév `displayName` értéke | 

1. Az egyszerű szolgáltatás működésének ellenőrzéséhez válassza a **szolgáltatásnév ellenőrzése**lehetőséget. Amikor elkészült, válassza az **OK** lehetőséget.

Ezután hozza létre a Jenkins-folyamatot, amely létrehozza és telepíti az alkalmazást.

## <a name="create-jenkins-pipeline"></a>Jenkins-folyamat létrehozása

A Jenkins-ben hozza létre az alkalmazás felépítéséhez és üzembe helyezéséhez szükséges folyamat-feladatot.

1. Térjen vissza a Jenkins kezdőlapjára, és válassza az **új elem**lehetőséget. 

   ![Jenkins-folyamat létrehozása](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-new-item.png)

1. Adja meg a folyamat feladatainak nevét, például: "My-Java-Web-App", és válassza a **folyamat**lehetőséget. Az alján kattintson az **OK gombra**.  

   ![A Jenkins-folyamat feladatainak neve](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-pipeline.png)

1. A Jenkins és a szolgáltatás egyszerű beállítása, hogy a Jenkins a saját hitelesítő adatai használata nélkül is üzembe helyezhető az Azure-ban.

   1. Az **általános** lapon válassza a **környezet előkészítése a futtatáshoz**lehetőséget. 

   1. A megjelenő **Tulajdonságok tartalom** mezőben adja hozzá ezeket a környezeti változókat és azok értékeit. 

      ```ini
      AZURE_CRED_ID=yourAzureServicePrincipalName
      RES_GROUP=yourWebAppAzureResourceGroupName
      WEB_APP=yourWebAppName
      ```

      ![Környezet előkészítése a futtatáshoz és a környezeti változók beállítása](media/tutorial-jenkins-deploy-web-app-azure-app-service/prepare-environment-for-jenkins-run.png)

1. Amikor elkészült, válassza a **Mentés** lehetőséget.

Következő lépésként hozzon létre Build-és üzembe helyezési parancsfájlokat a Jenkins számára.

## <a name="create-build-and-deployment-files"></a>Build-és központi telepítési fájlok létrehozása

Most hozza létre a Jenkins által az alkalmazás létrehozásához és üzembe helyezéséhez használt fájlokat.

1. A GitHub-elágazás `src/main/resources/` mappájában hozza létre az alkalmazás `web.config`nevű konfigurációs fájlját, amely tartalmazza ezt az XML-t, de cserélje le a `$(JAR_FILE_NAME)` a `gs-spring-boot-0.1.0.jar`ra:

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

1. A GitHub-elágazás gyökérkönyvtárában hozza létre ezt a (z) `Jenkinsfile`nevű Build és Deployment szkriptet, amely tartalmazza ezt a szöveget ([forrás a githubban](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se)):

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

1. Véglegesítse `web.config` és `Jenkinsfile` fájlokat a GitHub-elágazásban, és küldje el a módosításokat.

## <a name="point-pipeline-at-script"></a>Pont folyamata parancsfájlban

Most adja meg azt a létrehozási és üzembe helyezési parancsfájlt, amelyet a Jenkins használni fog.

1. A Jenkins területen válassza ki a korábban létrehozott folyamat feladatot. 

   ![Válassza ki a Jenkins-feldolgozási feladatot a webalkalmazáshoz](media/tutorial-jenkins-deploy-web-app-azure-app-service/select-pipeline-job.png)

1. A bal oldali menüben válassza a **Konfigurálás**lehetőséget.

1. A **folyamat** lapon, a **definíció** listából válassza ki a **folyamat parancsfájlt az SCM-ből**.

   1. A megjelenő **SCM** mezőben válassza a **git** lehetőséget a forrás vezérlőelemként. 

   1. A **adattárak** szakaszban az **adattár URL**-címe mezőben adja meg a GitHub-elágazás URL-címét, például: 

      `https://github.com/<your-GitHub-username>/gs-spring-boot`

   1. A **hitelesítő adatok**területen válassza ki a korábban létrehozott GitHub személyes hozzáférési tokent.

   1. A **parancsfájl elérési útja** mezőben adja meg a "Jenkinsfile" parancsfájl elérési útját.

   Ha elkészült, a folyamat definíciója a következő példához hasonlóan néz ki: 

   ![A Jenkins-folyamat mutatása a parancsfájlban](media/tutorial-jenkins-deploy-web-app-azure-app-service/set-up-jenkins-github.png)

1. Amikor elkészült, válassza a **Mentés** lehetőséget.

Ezután hozza létre és telepítse az alkalmazást Azure App Servicere. 

## <a name="build-and-deploy-to-azure"></a>Az Azure létrehozása és üzembe helyezése

1. Az Azure CLI-vel, vagy a parancssorból vagy a Azure Cloud Shellból hozzon létre egy [Azure app Service webalkalmazást Linuxon](/azure/app-service/containers/app-service-linux-intro) , ahol a Jenkins üzembe helyezi a webalkalmazást a Build befejezése után. Győződjön meg arról, hogy a webalkalmazás egyedi névvel rendelkezik.

   ```azurecli-interactive
   az group create --name yourWebAppAzureResourceGroupName --location yourAzureRegion
   az appservice plan create --name yourLinuxAppServicePlanName --resource-group yourWebAppAzureResourceGroupName --is-linux
   az webapp create --name yourWebAppName --resource-group yourWebAppAzureResourceGroupName --plan yourLinuxAppServicePlanName --runtime "java|1.8|Tomcat|8.5"
   ```

   Az Azure CLI-parancsokkal kapcsolatos további információkért tekintse meg a következő lapokat:

   * [**`az group create`**](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

   * [**`az appservice plan create`**](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)

   * [**`az webapp create`**](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)

1. A Jenkins területen válassza ki a folyamat **felépítését**, és válassza a Build most lehetőséget.

   A létrehozás befejeződése után a Jenkins üzembe helyezi az alkalmazást, amely most már az Azure-ban is elérhető a kiadvány URL-címén, például: 

   `http://<your-Java-web-app>.azurewebsites.net`

   ![Az üzembe helyezett alkalmazás megtekintése az Azure-ban](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-unedited.png)

## <a name="push-changes-and-redeploy"></a>Módosítások leküldése és ismételt üzembe helyezés

1. A böngészőben nyissa meg ezt a helyet a webalkalmazás GitHub-villájában:

   `complete/src/main/java/Hello/Application.java`
   
1. A GitHub jobb felső sarkában válassza **a fájl szerkesztése**lehetőséget.

1. Végezze el ezt a módosítást a `commandLineRunner()` metóduson, és véglegesítse a változást a tárház `master` ágában. Ez a `master`-ág elvégzése elindítja a Jenkins-ben való kiépítést. 
   
   ```java
   System.out.println("Let's inspect the beans provided by Spring Boot on Azure");
   ```

1. A létrehozás befejeződése után a Jenkins újra üzembe helyezi az Azure-ba, és frissíti az alkalmazást, amely most már megjeleníti a frissítést.

   ![Az üzembe helyezett alkalmazás megtekintése az Azure-ban](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-edited.png)

## <a name="troubleshooting-the-jenkins-plug-in"></a>A Jenkins beépülő modul hibaelhárítása

Ha a Jenkins beépülő moduljaival kapcsolatos hibákba ütközik, akkor az adott összetevőhöz tartozó [Jenkins-JIRA](https://issues.jenkins-ci.org/) egy problémát kell megadnia.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure-beli virtuális gépek buildügynökként való használata](/azure/jenkins/jenkins-azure-vm-agents)
