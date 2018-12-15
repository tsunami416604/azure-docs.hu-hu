---
title: Oktatóanyag – a jenkins-szel az Azure App Service üzembe helyezése a Githubról
description: Állítsa be a Jenkins folyamatos integrációs (CI) a GitHub és a folyamatos készregyártás (CD) az Azure App Service-Java-webalkalmazások esetében
services: jenkins
ms.service: jenkins
author: tomarcher
ms.author: tarcher
manager: jeconnoc
ms.topic: tutorial
ms.date: 11/15/2018
ms.openlocfilehash: b65972b79fd16b912abfbd2e35642ef5d9f5adc4
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438472"
---
# <a name="tutorial-deploy-from-github-to-azure-app-service-with-jenkins-continuous-integration-and-deployment"></a>Oktatóanyag: Üzembe helyezés a Githubról Azure App Service-ben a Jenkins folyamatos integrációs és üzembe helyezés

Ebben az oktatóanyagban egy egyszerű Java webalkalmazást a Githubról, telepíti [Linuxon futó Azure App Service](/azure/app-service/containers/app-service-linux-intro) folyamatos integrációs (CI) és a Jenkins folyamatos készregyártás (CD) beállítása. Ha az alkalmazás módosítja a Githubra a véglegesítéseket, a Jenkins automatikusan létrehozza, és addig az Azure App Service-alkalmazás. Ez az oktatóanyag a mintaalkalmazás fejlesztette használatával a [Spring Boot](http://projects.spring.io/spring-boot/) keretrendszer. 

![Áttekintés](media/tutorial-jenkins-deploy-web-app-azure-app-service/overview.png)

Ez az oktatóanyag ezen feladatok fogja végrehajtani:

> [!div class="checklist"]
> * Telepítés Jenkins beépülő modulok, így Ön is létrehozhatja a Githubról, üzembe helyezését az Azure App Service- és egyéb kapcsolódó feladatokat.
> * A minta GitHub-tárház elágaztatását, így másolata.
> * A Jenkins csatlakozás a Githubhoz.
> * Azure-beli szolgáltatásnév létrehozása, a Jenkins Azure hozzáférhet a hitelesítő adatok használata nélkül.
> * A Jenkins adja hozzá az egyszerű szolgáltatást.
> * Hozzon létre a Jenkins-folyamatot, amely felépítése és üzembe helyezése a mintaalkalmazás minden alkalommal, amikor frissíti az alkalmazást a Githubról.
> * A Jenkins-folyamat buildelési és üzembe helyezési fájlokat hoz létre.
> * A Jenkins folyamatot mutat a buildelési és üzembe helyezési parancsfájlt.
> * A mintaalkalmazás telepítése az Azure-bA build manuális futtatásával.
> * Küldje le egy alkalmazás frissítése a Githubon, amely elindítja a Jenkins hozhat létre és ismételt üzembe helyezése az Azure-bA.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége ezeket az elemeket:

* A [Jenkins](https://jenkins.io/) eszközökkel a Java fejlesztői készlet (JDK) és a Maven-beli Linux rendszerű virtuális gépen telepített kiszolgáló

  Ha nem rendelkezik Jenkins-kiszolgáló, hajtsa végre ezeket a lépéseket, most már az Azure Portalon: [Jenkins-kiszolgáló létrehozása az Azure Linux rendszerű virtuális gépen](/azure/jenkins/install-jenkins-solution-template)

* A [GitHub](https://github.com) , így másolata fiókra ([elágazás](#fork)) a minta Java-webalkalmazás. 

* [Az Azure CLI](/cli/azure/install-azure-cli), futtatható vagy a helyi parancssor vagy [Azure Cloud Shell](/azure/cloud-shell/overview)

## <a name="install-jenkins-plug-ins"></a>A Jenkins beépülő modulok telepítése

1. Jelentkezzen be a Jenkins webkonzol ezen a helyen:

   `https://<Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`

1. A Jenkins fő oldalon válassza ki a **Jenkins kezelése** > **beépülő modulok kezelése**.

   ![A Jenkins beépülő modulok kezelése](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-plugins.png)

1. Az a **elérhető** lapra, válassza ki a beépülő modullal:

   - [Azure App Service](https://plugins.jenkins.io/azure-app-service)
   - [GitHub-ág forrás](https://plugins.jenkins.io/github-branch-source)
   - A Jenkins [környezet Injector beépülő modul](https://plugins.jenkins.io/envinject)
   - [Azure-beli hitelesítő](https://plugins.jenkins.io/azure-credentials)

   Ha a beépülő modulok nem jelennek meg, ellenőrizze, hogy még nem úgy, hogy telepítette a **telepített** fülre.

1. A kiválasztott modulok telepítéséhez válassza **letöltése és telepítése az újraindítást követően**.

1. Miután elkészült, a Jenkins menüben válassza ki a **Jenkins kezelése** így visszatérhet a későbbi lépésekben a Jenkins felügyeleti oldalára.

## <a name="fork-sample-github-repo"></a>Minta GitHub-tárház elágaztatása

1. [Jelentkezzen be a GitHub-adattárat a Spring Boot-mintaalkalmazást a](https://github.com/spring-guides/gs-spring-boot). 

1. Válassza a jobb felső sarokban a GitHub **elágazás**.

   ![GitHub-elágazásba mintatárból](media/tutorial-jenkins-deploy-web-app-azure-app-service/fork-github-repo.png)

1. Kövesse az utasításokat, jelölje be a GitHub-fiókot, és ezzel Befejezés.

Következő lépésként állítsa be a Jenkins a GitHub hitelesítő adataival.

## <a name="connect-jenkins-to-github"></a>A Jenkins csatlakozás a Githubhoz

Engedélyezni szeretné, hogy a Jenkins, GitHub figyelheti és reagálhat rájuk, amikor új véglegesítéseket első leküldte a webalkalmazás a GitHub-elágazásba, [GitHub-webhookok](https://developer.github.com/webhooks/) a Jenkinsben.

> [!NOTE]
> 
> Ezeket a lépéseket személyes hozzáférési jogkivonat hitelesítő adatok a Jenkins használata a GitHub használatával, a GitHub-felhasználónevét és jelszavát, és hozzon létre. 
> Ha a GitHub-fiók kétfaktoros hitelesítést használ, a GitHub-tokenjének létrehozásához, és inkább ezt a jogkivonatot a használata a Jenkins beállítása. 
> További információkért lásd: a [Jenkins-GitHub beépülő modul](https://wiki.jenkins.io/display/JENKINS/GitHub+Plugin) dokumentációját.

1. Az a **Jenkins kezelése** lapon jelölje be **rendszer konfigurálása**. 

   ![Rendszer konfigurálása](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-configure-system.png)

1. A **GitHub** területén adja meg a GitHub-kiszolgáló adatait. Az a **GitHub-kiszolgáló hozzáadása** listáról válassza ki **GitHub kiszolgáló**. 

   ![GitHub-kiszolgáló hozzáadása](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-GitHub-server.png)

1. Ha a **hurkokat kezelése** tulajdonság nincs kiválasztva, válassza ezt a tulajdonságot. Válasszon **speciális** adhatja meg egyéb beállításokat. 

   ![Válassza a "Speciális" További beállítások](media/tutorial-jenkins-deploy-web-app-azure-app-service/advanced-GitHub-settings.png)

1. Az a **további GitHub-műveletek kezelése** listáról válassza ki **bejelentkezési nevet és jelszót átalakítása jogkivonat**.

   ![Válassza a "Kezelés további GitHub műveletek"](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-additional-actions.png)

1. Válassza ki **a bejelentkezési nevet és jelszót** úgy adhatja meg a GitHub-felhasználónevét és jelszavát. Ha elkészült, válassza ki a **token hitelesítő adatok létrehozása**, ami létrehoz egy [GitHub személyes hozzáférési jogkivonat (PAT)](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   

   ![Hozzon létre a GitHub PAT a bejelentkezési nevet és jelszót](media/tutorial-jenkins-deploy-web-app-azure-app-service/create-github-token-credentials.png)

1. Az a **GitHub-kiszolgáló** szakaszban, a **hitelesítő adatok** listájához, válassza ki az új jogkivonatot. Ellenőrizze, hogy a hitelesítés működik-e választva **kapcsolat tesztelése**.

   ![Az új PAT GitHub-kiszolgálóval való kapcsolat ellenőrzése](media/tutorial-jenkins-deploy-web-app-azure-app-service/check-github-connection.png)

Ezután az Azure egyszerű szolgáltatás létrehozása a Jenkins által a hitelesítés és Azure-erőforrások eléréséhez.

## <a name="create-service-principal"></a>Egyszerű szolgáltatás létrehozása

Egy későbbi szakaszban olvashat a Jenkins, amely létrehozza az alkalmazást a Githubról, és üzembe helyezte az alkalmazást az Azure App Service-folyamat feladatot hoz létre. Szeretné, hogy a Jenkins az Azure a hitelesítő adatok megadása nélkül érhetnek, hozzon létre egy [szolgáltatásnév](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) -hoz készült Jenkins az Azure Active Directoryban. A szolgáltatásnév olyan egy külön identitás, amely a Jenkins az Azure-erőforrásokhoz való hozzáférés hitelesítéséhez használható. Egyszerű szolgáltatás létrehozásához futtassa az Azure CLI-paranccsal [ **`az ad sp create-for-rbac`** ](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest), vagy a helyi parancssorból vagy az Azure Cloud Shellt, például: 

```azurecli-interactive
az ad sp create-for-rbac --name "yourAzureServicePrincipalName" --password yourSecurePassword
```

Ellenőrizze, hogy használja az egyszerű szolgáltatásnév idézőjelek közé. Is, hozzon létre egy erős jelszót, alapján a [Azure Active Directory-jelszavakra vonatkozó szabályokat és korlátozásokat](/azure/active-directory/active-directory-passwords-policy). Ha nem ad meg egy jelszót, az Azure CLI létrehoz egy jelszót. 

Íme a kimenetet a **`create-for-rbac`** parancsot: 

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
> Ha már rendelkezik egy egyszerű szolgáltatást, ehelyett felhasználhatja az identitásukat.
> Ha megadta a szolgáltatás egyszerű értékek a hitelesítéshez, használja a `appId`, `password`, és `tenant` tulajdonságértékeket. 
> Meglévő szolgáltatásnév keresésekor használja a `displayName` tulajdonság értéke.

## <a name="add-service-principal-to-jenkins"></a>Szolgáltatásnév hozzáadása a Jenkinsben

1. A Jenkins fő oldalon válassza ki a **hitelesítő adatok** > **rendszer**. 

1. Az a **rendszer** lap **tartomány**, jelölje be **globális hitelesítő adatokat (korlátlan)**.

1. A bal oldali menüben válassza ki a **hitelesítő adatok hozzáadása**.

1. Az a **Kind** listáról válassza ki **Azure egyszerű szolgáltatás**.

1. Adja meg a szolgáltatás egyszerű és az Azure előfizetését az a Tulajdonságok leírtak szerint ebben a lépésben a tábla adatait:

   ![Azure-szolgáltatás egyszerű szolgáltatásnév hitelesítő adatainak hozzáadása](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-service-principal-credentials.png)

   | Tulajdonság | Érték | Leírás | 
   |----------|-------|-------------| 
   | **előfizetés-azonosító** | <*yourAzureSubscription-azonosító*> | A GUID azonosítót az Azure-előfizetés <p>**Tipp**: Ha nem ismeri az Azure előfizetés-azonosító, futtassa a következő Azure CLI-parancsot, vagy a parancssorból vagy a Cloud Shellben, és használja a `id` GUID-érték: <p>`az account list` | 
   | **Ügyfél-azonosító** | <*yourAzureServicePrincipal-azonosító*> | A `appId` GUID érték, az Azure-szolgáltatás egyszerű korábban létrehozott | 
   | **Titkos Ügyfélkód** | <*yourSecurePassword*> | A `password` érték vagy a "secret" az Azure-szolgáltatás egyszerű megadott | 
   | **Bérlő azonosítója** | <*yourAzureActiveDirectoryTenant-azonosító*> | A `tenant` GUID azonosítót az Azure Active Directory-bérlő | 
   | **Azonosító** | <*yourAzureServicePrincipalName*> | A `displayName` az Azure-szolgáltatás egyszerű érték | 
   |||| 

1. Győződjön meg arról, hogy működik-e az egyszerű szolgáltatást, válassza a **egyszerű szolgáltatás ellenőrzése**. Ha elkészült, válassza ki a **OK**.

Ezután hozza létre a Jenkins-folyamatot, amely az alkalmazás létrehozása és telepítése.

## <a name="create-jenkins-pipeline"></a>Jenkins-folyamat létrehozása

A jenkins kifejezést a folyamat feladat létrehozásához és telepítéséhez az alkalmazás létrehozása.

1. A Jenkins kezdőlap adja vissza, és válassza ki **új elem**. 

   ![Válassza ki az "Új elemet"](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-new-item.png)

1. Adjon meg egy nevet, például "My-Java-Web-App", és válassza a folyamat feladatában **folyamat**. Válassza a lap alján, **OK**.  

   ![Válassza ki a "Futószalag"](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-pipeline.png)

1. A Jenkins beállítása az egyszerű szolgáltatás, a Jenkins nélkül üzembe helyezheti az Azure-bA a saját hitelesítő adataival.

   1. Az a **általános** lapon jelölje be **készítse elő a környezetet a Futtatás**. 

   1. Az a **tulajdonságok tartalom** megjelenő, adja hozzá ezekhez a környezeti változókhoz és értékeikhez. 

      ```text
      AZURE_CRED_ID=yourAzureServicePrincipalName
      RES_GROUP=yourWebAppAzureResourceGroupName
      WEB_APP=yourWebAppName
      ```

      ![Válassza a "a Futtatás-környezet előkészítése" és a környezeti változók beállítása](media/tutorial-jenkins-deploy-web-app-azure-app-service/prepare-environment-for-run.png)

1. Ha elkészült, kattintson a **Mentés** gombra.

Ezután hozza létre a buildelési és üzembe helyezési parancsfájlok-hoz készült Jenkins.

## <a name="create-build-and-deployment-files"></a>Buildelési és üzembe helyezési fájlok létrehozása

Most hozzon létre, amely a Jenkins létrehozásához és az alkalmazás telepítéséhez használja a fájlokat.

1. A GitHub-elágazásba a `src/main/resources/` mappában hozzon létre az alkalmazás konfigurációs fájlt `web.config`, amely tartalmazza az XML, de cserélje le `$(JAR_FILE_NAME)` a `gs-spring-boot-0.1.0.jar`:

   ```xml
   <?xml version="1.0" encoding="UTF-8">
   <configuration>
      <system.webServer>
         <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
         </handlers>
         <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\${JAR_FILE_NAME}&quot;"></httpPlatform>
      </system.webServer>
   </configuration>
   ```

1. A GitHub-elágazásba legfelső szintű mappában hozza létre a buildelési és üzembe helyezési szkript nevű `Jenkinsfile`, amely tartalmazza ezt a szöveget ([a GitHub forrás Itt](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se)):

   ```text  
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

1. Mindkét véglegesítése `web.config` és `Jenkinsfile` a GitHub-fájlok elágaztatását, és a módosításokat.

## <a name="point-pipeline-at-script"></a>A parancsfájl pont folyamat

Most adja meg azt szeretné, hogy a Jenkins használata a buildelési és üzembe helyezési parancsfájlt.

1. A jenkins kifejezést válassza ki a korábban létrehozott folyamat feladatában. 

   ![Válassza ki a webalkalmazás folyamat feladatában](media/tutorial-jenkins-deploy-web-app-azure-app-service/select-pipeline-job.png)

1. A bal oldali menüben válassza ki a **konfigurálása**.

1. A a **folyamat** lapon, a a **definíció** listáról válassza ki **SCM parancsfájlt folyamat**.

   1. Az a **SCM** mezőre, amely akkor jelenik meg, válassza ki **Git** , a verziókövetés. 

   1. Az a **Tárházak** szakaszban a **adattár URL-címe**, adja meg a GitHub-elágazásba URL-CÍMÉT, például: 

      `https://github.com/<your-GitHub-username>/gs-spring-boot`

   1. A **hitelesítő adatok**, válassza ki a korábban létrehozott GitHub személyes hozzáférési tokent.

   1. Az a **parancsfájl elérési útján** mezőbe az elérési út hozzáadása a "Jenkinsfile" parancsfájl.

   Ha elkészült, a folyamat definíciója hasonlít ebben a példában: 

   ![A parancsfájl pont folyamat](media/tutorial-jenkins-deploy-web-app-azure-app-service/set-up-jenkins-github.png)

1. Ha elkészült, kattintson a **Mentés** gombra.

Ezután hozhat létre, és telepítse az alkalmazást az Azure App Service-ben. 

## <a name="build-and-deploy-to-azure"></a>Létrehozása és üzembe helyezése az Azure-bA

1. Az Azure CLI használatával, vagy a parancssorból vagy az Azure Cloud Shellben hozzon létre egy [a Linuxos Azure App Service web Apps](/azure/app-service/containers/app-service-linux-intro) ahol a Jenkins üzembe helyezi a webalkalmazás egy build befejezése után. Ellenőrizze, hogy a webalkalmazás egyedi névvel rendelkezik.

   ```azurecli-interactive
   az group create --name yourWebAppAzureResourceGroupName --location yourAzureRegion
   az appservice plan create --name yourLinuxAppServicePlanName --resource-group yourWebAppAzureResourceGroupName --is-linux
   az webapp create --name yourWebAppName --resource-group yourWebAppAzureResourceGroupName --plan yourLinuxAppServicePlanName --runtime "java|1.8|Tomcat|8.5"
   ```

   Azure CLI-parancsokkal kapcsolatos további információkért tekintse meg ezeket az oldalakat:

   * [**`az group create`**](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

   * [**`az appservice plan create`**](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)

   * [**`az webapp create`**](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)

1. A jenkins kifejezést, válassza ki a folyamat feladatot, és válassza ki **hozhat létre most**.

   A létrehozás befejezése után a Jenkins üzembe helyezte az alkalmazást, amely most az Azure-ban a közzétételi URL-címen élő például: 

   `http://<your-Java-web-app>.azurewebsites.net`

   ![Az üzembe helyezett alkalmazás megtekintése az Azure-ban](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-unedited.png)

## <a name="push-changes-and-redeploy"></a>Módosítások leküldése és ismételt üzembe helyezés

1. A böngészőben nyissa meg a GitHub-elágazásba a webalkalmazás ezen a helyen:

   `complete/src/main/java/Hello/Application.java`
   
1. Válassza a jobb felső sarokban a Githubban, **szerkessze a fájlt**.

1. Győződjön meg arról, a módosítás a `commandLineRunner()` metódust, és a módosítás az adattárhoz véglegesítési `master` ágat. Ez az véglegesítése a `master` ág a Jenkinsben elindul a build. 
   
   ```java
   System.out.println("Let's inspect the beans provided by Spring Boot on Azure");
   ```

1. Miután a létrehozás befejezése, és a Jenkins ismételt üzembe helyezése az Azure-ba, frissítse az alkalmazást, amelyen mostantól látható a frissítés.

   ![Az üzembe helyezett alkalmazás megtekintése az Azure-ban](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-edited.png)

## <a name="troubleshooting-the-jenkins-plug-in"></a>Hibaelhárítás a Jenkins beépülő modul

A Jenkins beépülő modulok hibák hibát tapasztal, ha a probléma fájlt a [Jenkins JIRA](https://issues.jenkins-ci.org/) az adott összetevő.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure-beli virtuális gépek buildügynökként való használata](/azure/jenkins/jenkins-azure-vm-agents)