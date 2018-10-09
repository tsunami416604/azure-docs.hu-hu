---
title: Webalkalmazások üzembe helyezése az Azure-ban a Jenkins használatával
description: A Jenkins és a Docker használatával beállíthatja Java-webalkalmazásai folyamatos integrációját a GitHubról az Azure App Service-be.
ms.service: jenkins
keywords: jenkins, azure, devops, app service, folyamatos integráció, ci, folyamatos üzembe helyezés, cd
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 81959159d4860512c184ada25930da814b5ae044
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946578"
---
# <a name="set-up-continuous-integration-and-deployment-to-azure-app-service-with-jenkins"></a>Az Azure App Service-be történő folyamatos integráció és üzembe helyezés beállítása a Jenkinsszel

Ez az oktatóanyagban beállítja egy [Spring Boot](http://projects.spring.io/spring-boot/) keretrendszerben fejlesztett minta Java-webalkalmazás folyamatos integrációját és üzembe helyezését (CI/CD) az [Azure App Service Web App on Linuxon](/azure/app-service/containers/app-service-linux-intro) a Jenkins használatával.

Az oktatóanyagban a következő feladatokat fogja elvégezni:

> [!div class="checklist"]
> * Az Azure App Service-ben való üzembe helyezéshez szükséges Jenkins beépülő modulok telepítése.
> * Jenkins-feladat definiálása a Docker-rendszerképek GitHub-adattárból történő létrehozására az új véglegesítések leküldésekor.
> * Új Azure Web App for Linux definiálása, majd konfigurálása az Azure Container Registry-tárolóadatbázisba leküldött Docker-rendszerképek üzembe helyezésére.
> * Az Azure App Service Jenkins beépülő modul konfigurálása.
> * A mintaalkalmazás üzembe helyezése az Azure App Service-ben egy manuális build használatával.
> * Jenkins-build aktiválása, majd a webalkalmazás frissítése a változások GitHubba történő leküldésével.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* [Jenkins](https://jenkins.io/) konfigurált JDK-val és Maven-eszközökkel. Ha még nincs Jenkins-rendszere, hozzon létre egyet most az Azure-ban a [Jenkins-megoldássablonból](/azure/jenkins/install-jenkins-solution-template).
* Egy [GitHub](https://github.com)-fiók.
* [Azure CLI](/cli/azure) a helyi parancssorban vagy az [Azure Cloud Shellben](/azure/cloud-shell/overview)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-jenkins-plug-ins"></a>A Jenkins beépülő modulok telepítése

1. Nyissa meg egy webböngészőben a Jenkins-webkonzolt, és válassza a bal oldali menü **Manage Jenkins** (Jenkins kezelése) elemét, majd a **Manage Plugins** (Beépülő modulok kezelése) lehetőséget.
2. Válassza az **Available** (Elérhető) lapot.
3. Keresse meg a következő beépülő modulokat, és jelölje be a mellettük található jelölőnégyzetet:   

    - [Azure App Service Plug-in](https://plugins.jenkins.io/azure-app-service) (Azure App Service beépülő modul)
    - [GitHub Branch Source Plug-in](https://plugins.jenkins.io/github-branch-source) (GitHub Branch Source beépülő modul)

    Ha a beépülő modulok nem jelennek meg, az **Installed** (Telepített) lapon ellenőrizze, hogy nincsenek-e már telepítve.

1. A **Download now and install after restart** (Letöltés most, és telepítés újraindítás után) jelölőnégyzet bejelölésével engedélyezze a beépülő modulokat a Jenkins konfigurációjában.

## <a name="configure-github-and-jenkins"></a>A GitHub és a Jenkins konfigurálása

Állítsa be a Jenkinst, hogy fogadja a [GitHub-webhookokat](https://developer.github.com/webhooks/), amikor új véglegesítéseket küld le a fiókjában lévő adattárakba.

1. Válassza a **Manage Jenkins** (Jenkins kezelése), majd a **Configure System** (Rendszer konfigurálása) lehetőséget. A **GitHub** szakaszban győződjön meg róla, hogy a **Manage hooks** (Hookok kezelése) beállítás be van jelölve, majd válassza a **Manage additional GitHub actions** (További GitHub-műveletek kezelése), majd a **Convert login and password to token** (Bejelentkezési név és jelszó konvertálása jogkivonattá) lehetőséget.
2. Jelölje be a **From login and password** (Bejelentkezési névből és jelszóból) választógombot, és adja meg GitHub-felhasználónevét és -jelszavát. A **Create token credentials** (Jogkivonat hitelesítő adatainak létrehozása) elemre kattintva hozzon létre egy új [GitHub személyes hozzáférési jogkivonatot](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   
   ![GitHub személyes hozzáférési jogkivonat (PAT) létrehozása bejelentkezési név és jelszó alapján](media/jenkins-java-quickstart/create_github_credentials.png)
3.  Válassza ki az újonnan létrehozott jogkivonatot a **Credentials** (Hitelesítő adatok) legördülő menüben a GitHub-kiszolgálók konfigurációjában. Kattintson a **Test connection** (Kapcsolat tesztelése) elemre a hitelesítés működésének ellenőrzéséhez.   
   ![GitHub-kapcsolat ellenőrzése a személyes hozzáférési jogkivonat konfigurálása után](media/jenkins-java-quickstart/verify_github_connection.png)

> [!NOTE]
> Ha a GitHub-fiókon engedélyezve van a kéttényezős hitelesítés, hozza létre a jogkivonatot a GitHubon, és konfigurálja a Jenkinst a használatára. További információért tekintse át a [Jenkins GitHub beépülő moduljának](https://wiki.jenkins.io/display/JENKINS/Github+Plugin) dokumentációját.

## <a name="fork-the-sample-repo-and-create-a-jenkins-job"></a>A mintaadattár elágaztatása és egy Jenkins-feladat létrehozása 

1. Nyissa meg a [Spring Boot mintaalkalmazás adattárát](https://github.com/spring-guides/gs-spring-boot-docker), és ágaztassa el a saját GitHub-fiókjába a jobb felső sarokban a **Fork** (Elágaztatás) gombra kattintva.   
    ![Elágaztatás a GitHubról](media/jenkins-java-quickstart/fork_github_repo.png)
1. A Jenkins webkonzolon kattintson a **New Item** (Új elem) gombra, adja meg a **MyJavaApp** nevet, válassza a **Freestyle project** (Szabad stílusú projekt) lehetőséget, majd kattintson az **OK** gombra.   
    ![Új szabad stílusú Jenkins-projekt](media/jenkins-java-quickstart/jenkins_freestyle.png)
2. A **General** (Általános) szakaszban válassza a **GitHub project** lehetőséget, majd adja meg az elágaztatott adattár URL-címét, például: https://github.com/raisa/gs-spring-boot-docker
3. A **Source code management** (Forráskódkezelés) szakaszban válassza a **Git** elemet, majd adja meg az elágaztatott adattár `.git` URL-címét, például: https://github.com/raisa/gs-spring-boot-docker.git
4. A **Build Triggers** (Eseményindítók létrehozása) szakaszban válassza a **GitHub hook trigger for GITScm polling** (GitHub beavatkozási pont eseményindító GITScm lekérdezés esetén) lehetőséget.
5. A **Build** (Buildelés) szakaszban válassza az **Add build step** (Buildelési lépés hozzáadása), majd az **Invoke top-level Maven targets** (Felső szintű Maven-célok meghívása) lehetőséget. Írja be a `package` szöveget a **Goals** (Célok) mezőbe.
6. Kattintson a **Mentés** gombra. A feladat teszteléséhez kattintson a **Build Now** (Buildelés most) gombra a projekt lapján.

## <a name="configure-azure-app-service"></a>Az Azure App Service konfigurálása 

1. Az Azure CLI vagy a [Cloud Shell](/azure/cloud-shell/overview) használatával hozzon létre egy új [Web App on Linux-alkalmazást](/azure/app-service/containers/app-service-linux-intro). Az oktatóanyagban a `myJavaApp` nevet használjuk a webalkalmazáshoz, de a saját alkalmazásához egyedi nevet kell használnia.
   
    ```azurecli-interactive
    az group create --name myResourceGroupJenkins --location westus
    az appservice plan create --is-linux --name myLinuxAppServicePlan --resource-group myResourceGroupJenkins 
    az webapp create --name myJavaApp --resource-group myResourceGroupJenkins --plan myLinuxAppServicePlan --runtime "java|1.8|Tomcat|8.5"
    ```

2. Hozzon létre egy [Azure Container Registry](/azure/container-registry/container-registry-intro)-beli tárolóregisztrációs adatbázist a Jenkins által létrehozott Docker–rendszerképek tárolásához. Az oktatóanyagban a `jenkinsregistry` nevet használjuk a tárolóregisztrációs adatbázishoz, de a saját adatbázisához egy egyedi nevet kell használnia. 

    ```azurecli-interactive
    az acr create --name jenkinsregistry --resource-group myResourceGroupJenkins --sku Basic --admin-enabled
    ```
3. Konfigurálja a webalkalmazást, hogy futtassa a tárolóregisztrációs adatbázisba leküldött Docker-rendszerképeket, és adja meg, hogy a tárolóban futó alkalmazás a 8080-as porton figyelje a kéréseket.   

    ```azurecli-interactive
    az webapp config container set -c jenkinsregistry/webapp --resource-group myResourceGroupJenkins --name myJavaApp
    az webapp config appsettings set --resource-group myResourceGroupJenkins --name myJavaApp --settings PORT=8080
    ```

## <a name="configure-the-azure-app-service-jenkins-plug-in"></a>Az Azure App Service Jenkins beépülő modul konfigurálása

1. A Jenkins-webkonzolon válassza ki a létrehozott **MyJavaApp** feladatot, majd válassza a **Configure** (Konfigurálás) lehetőséget a lap bal oldalán.
2. Görgessen le a **Post-build Actions** (Buildelés utáni műveletek) területig, és válassza az **Add post-build action** (Buildelés utáni művelet hozzáadása), majd a **Publish an Azure Web App** (Azure-webalkalmazás közzététele) lehetőséget.
3. Az **Azure Profile Configuration** (Azure-profil konfigurálása) területen kattintson az **Add** (Hozzáadás) elemre az **Azure Credentials** (Azure-beli hitelesítő adatok) elem mellett, majd válassza a **Jenkins**lehetőséget.
4. Az **Add Credentials** (Hitelesítő adatok hozzáadása) párbeszédpanelen válassza a **Microsoft Azure Service Principal** (Microsoft Azure-szolgáltatásnév) lehetőséget a **Kind** (Altípus) legördülő menüben.
5. Hozzon létre egy Active Directory-szolgáltatásnevet az Azure CLI vagy a [Cloud Shell](/azure/cloud-shell/overview) használatával.
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
6. Adja meg a szolgáltatásnév hitelesítő adatait az **Add credentials** (Hitelesítő adatok hozzáadása) párbeszédpanelen. Ha nem ismeri az Azure-előfizetés azonosítóját, lekérdezheti a parancssori felületen:
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    ![Azure-szolgáltatásnév konfigurálása](media/jenkins-java-quickstart/azure_service_principal.png)
6. A **Verify Service Principal** (Szolgáltatásnév ellenőrzése) elem kiválasztásával ellenőrizze, hogy a szolgáltatásnév hitelesíthető-e az Azure-ban. 
7. A hitelesítő adatok mentéséhez válassza az **Add** (Hozzáadás) lehetőséget.
8. A **Publish an Azure Web App** (Azure-webalkalmazás közzététele) konfigurációra visszalépve válassza ki az imént hozzáadott szolgáltatásnév-hitelesítő adatokat az **Azure Credentials** (Azure-beli hitelesítő adatok) legördülő menüben.
9. Az **App Configuration** (Alkalmazáskonfiguráció) felületen válassza ki az erőforráscsoport és a webalkalmazás nevét a legördülő menüben.
10. Kattintson a **Publish via Docker** (Közzététel a Dockeren keresztül) választógombra.
11. A **Dockerfile path** (Docker-fájl elérési útja) mezőben adja meg a `complete/Dockerfile` útvonalat.
12. A **Docker registry URL** (Docker-jegyzék URL-címe) mezőben adja meg a `https://jenkinsregistry.azurecr.io` címet.
13. Kattintson a **Registry Credentials** (Regisztrációs adatbázis hitelesítő adatai) elem melletti **Add** (Hozzáadás) gombra. 
14. A **Username** (Felhasználónév) mezőben adja meg az Azure Container Registry-adatbázishoz létrehozott rendszergazdai felhasználónevét.
15. A **Password** (Jelszó) mezőben adja meg az Azure Container Registry-adatbázis jelszavát. A felhasználónevet és a jelszót az Azure Portalon vagy a következő parancssori felületi paranccsal kérheti le:

    ```azurecli-interactive
    az acr credential show -n jenkinsregistry
    ```
    ![A tárolóregisztrációs adatbázis hitelesítő adatainak hozzáadása](media/jenkins-java-quickstart/enter_acr_credentials.png)
15. A hitelesítő adatok mentéséhez kattintson az **Add** (Hozzáadás) gombra.
16. A **Publish an Azure Web App** (Azure Web App közzététele) felület **App Configuration** (Alkalmazáskonfiguráció) paneljén válassza ki az újonnan létrehozott hitelesítő adatokat a **Registry credentials** (Regisztrációs adatbázis hitelesítő adatai) legördülő menüben. Az elkészült build utáni művelet a következő képhez hasonlóan néz ki:   
    ![Buildelés utáni művelet konfigurációja az Azure App Service-ben való üzembe helyezéshez](media/jenkins-java-quickstart/appservice_plugin_configuration.png)
17. A feladat konfigurációjának mentéséhez válassza a **Mentés** lehetőséget.

## <a name="deploy-the-app-from-github"></a>Az alkalmazás üzembe helyezése a GitHubból

1. A Jenkins-projektben válassza a **Build Now** (Buildelés most) lehetőséget a mintaalkalmazás Azure-ban való üzembe helyezéséhez.
2. A buildelés befejezése után az alkalmazás a közzétételi URL-címén fut az Azure-ban, például: http://myjavaapp.azurewebsites.net.   
   ![Az üzembe helyezett alkalmazás megtekintése az Azure-ban](media/jenkins-java-quickstart/hello_docker_world_unedited.png)

## <a name="push-changes-and-redeploy"></a>Módosítások leküldése és ismételt üzembe helyezés

1. Az elágaztatott GitHub-ágról lépjen a weben a következő helyre: `complete/src/main/java/Hello/Application.java`. Válassza **A fájl szerkesztése** hivatkozást a GitHub-felület jobb oldalán.
2. Végezze el a következő módosítást a `home()` metóduson, és véglegesítse az adattár fő ágában.
   
    ```java
    return "Hello Docker World on Azure";
    ```
3. A Jenkinsben elindul egy új build, amit az adattár `master` ágán végrehajtott új véglegesítés vált ki. Miután ez befejeződött, töltse újra az alkalmazást az Azure-ban.     
      ![Az üzembe helyezett alkalmazás megtekintése az Azure-ban](media/jenkins-java-quickstart/hello_docker_world.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>A Jenkins beépülő modul hibáinak elhárítása

Ha a Jenkins beépülő modulok használata során bármilyen hibát tapasztal, jelentse be a problémát az adott összetevő [Jenkins JIRA](https://issues.jenkins-ci.org/) felületén.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure-beli virtuális gépek buildügynökként való használata](/azure/jenkins/jenkins-azure-vm-agents)