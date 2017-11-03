---
title: "A webalkalmazások Azure telepítéséhez használható Jenkins |} Microsoft Docs"
description: "Beállíthat folyamatos integrációt a Githubról az Azure App Service a Jenkins és Docker használó Java-webalkalmazások számára."
author: rloutlaw
manager: douge
ms.service: jenkins
ms.search.scope: 
ms.devlang: java
ms.topic: article
ms.workload: web
ms.date: 08/02/2017
ms.author: routlaw
ms.custom: Jenkins, devcenter
ms.openlocfilehash: 3a2635ac968d843226f05dc51cf4a5f078235c11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-continuous-integration-and-deployment-to-azure-app-service-with-jenkins"></a>Folyamatos integrációt és az Azure App Service Jenkins az üzembe helyezés beállítása

Ez az oktatóanyag állít be a folyamatos integrációt és telepítését (CI/CD) minta Java-webalkalmazás fejlesztése során a [rugó rendszerindító](http://projects.spring.io/spring-boot/) keretrendszer [Azure App Service Web Apps Linux](/azure/app-service/containers/app-service-linux-intro) Jenkins használatával.

Ebben az oktatóanyagban a következő feladatokat kell elvégezni:

> [!div class="checklist"]
> * Telepítse a Jenkins beépülő modulok az Azure App Service üzembe helyezéséhez szükséges.
> * Adja meg egy Docker-lemezképet rögzíthet a GitHub-tárház létrehozásához, amikor egy új véglegesítési fejlesztőre Jenkins feladat.
> * Adja meg egy új Azure Web Apps Linux rendszerhez készült, és konfigurálja úgy, hogy az Azure-tárolóba beállításjegyzék leküldött Docker-lemezképek központi telepítése.
> * Konfigurálja az Azure App Service Jenkins beépülő modult.
> * A mintaalkalmazás telepítése az Azure App Service manuális build a.
> * Indítás, Jenkins build, és frissítheti a webalkalmazását GitHub módosítások küldésével.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* [Jenkins](https://jenkins.io/) konfigurált JDK és Maven eszközökkel. Ha egy Jenkins rendszer nincs, hozzon létre egyet most az Azure-ban a [Jenkins megoldássablonban](/azure/jenkins/install-jenkins-solution-template).
* A [GitHub](https://github.com) fiók.
* [Az Azure CLI 2.0](/cli/azure/overview), a helyi parancssorból vagy a a [Azure Cloud rendszerhéj](/azure/cloud-shell/overview)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-jenkins-plug-ins"></a>Jenkins beépülő modulok telepítése

1. Nyisson meg egy webböngészőt a Jenkins webkonzolhoz, és válassza ki **kezelése Jenkins** a bal oldali menüből, majd válassza ki **kezelése beépülő modulok**.
2. Válassza ki a **elérhető** fülre.
3. Keresse meg, és jelölje be a következő beépülő modulok mellett:   

    - [Az Azure App Service beépülő modul](https://plugins.jenkins.io/azure-app-service)
    - [GitHub fiókirodai forrás beépülő modul](https://plugins.jenkins.io/github-branch-source)

    Ha a beépülő modulok nem jelennek meg, győződjön meg arról, még ellenőrzésével nincsenek telepítve a **telepített** fülre.

1. Válassza ki **letöltése és telepítése az újraindítást követően** Jenkins konfigurációs a beépülő modulok engedélyezése.

## <a name="configure-github-and-jenkins"></a>GitHub és Jenkins konfigurálása

Jenkins beállítása fogadására [GitHub webhook](https://developer.github.com/webhooks/) ha vannak új véglegesíti leküldve egy tárház fiókjában.

1. Válassza ki **Jenkins kezelése**, majd **rendszer konfigurálása**. Az a **GitHub** területen győződjön meg arról, hogy **hurkok kezelése** van kiválasztva, és válassza ki **kezelése a Githubon használható további műveletek** válassza **Convert bejelentkezési és jelszó a token**.
2. Válassza ki a **a felhasználónevet és jelszót** választógomb, és adja meg a GitHub-felhasználónevét és jelszavát. Válassza ki **token hitelesítő adatok létrehozása** hozzon létre egy új [GitHub személyes hozzáférési jogkivonat](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   
   ![GitHub PAT létre felhasználónevet és jelszót](media/jenkins-java-quickstart/create_github_credentials.png)
3.  Válassza ki az újonnan létrehozott jogkivonatot a **hitelesítő adatok** legördülő listán a GitHub-kiszolgálók a konfigurációban. Válassza ki **tesztkapcsolat** ellenőrzése, hogy működik-e a hitelesítés.   
   ![A GitHub kapcsolat ellenőrzése, ha PAT már konfigurálva van](media/jenkins-java-quickstart/verify_github_connection.png)

> [!NOTE]
> Ha a GitHub-fiók kéttényezős hitelesítést, a jogkivonat létrehozása a Githubon, és Jenkins a használatára konfigurálja. Tekintse át a [Jenkins GitHub beépülő modul](https://wiki.jenkins.io/display/JENKINS/Github+Plugin) teljes dokumentációjában.

## <a name="fork-the-sample-repo-and-create-a-jenkins-job"></a>A minta-tárház elágaztatásáról és Jenkins feladat létrehozása 

1. Nyissa meg a [rugó rendszerindító minta alkalmazás tárház](https://github.com/spring-guides/gs-spring-boot-docker) kiválasztásával a saját GitHub-fiók elágaztatásáról és **elágazás** jobb felső sarkában található.   
    ![Elágazás a Githubról](media/jenkins-java-quickstart/fork_github_repo.png)
1. A Jenkins webkonzol, válassza ki **új elem**, adjon neki nevet **MyJavaApp**, jelölje be **Freestyle projekt**, majd jelölje be **OK**.   
    ![Új Jenkins Freestyle projekt](media/jenkins-java-quickstart/jenkins_freestyle.png)
2. Az a **általános** szakaszban jelölje be **GitHub** projektre, és írja be a villás tárház URL-CÍMÉT, például a https://github.com/raisa/gs-spring-boot-docker
3. A a **kód felügyeleti forrás** szakaszban jelölje be **Git**, adja meg a villás tárház `.git` https://github.com/raisa/gs-spring-boot-docker.git például URL-címe
4. Az a **Build eseményindítók** szakaszban jelölje be **GitHub hook eseményindítója a következőnek: GITscm lekérdezési**.
5. Alatt a **Build** szakaszban jelölje be **Hozzáadás összeállítása lépés** válassza **meghívása a legfelső szintű Maven célok**. Adja meg `package` a a **célok** mező.
6. Kattintson a **Mentés** gombra. Tesztelheti a feladat választásával **Build most** a projekt lapról.

## <a name="configure-azure-app-service"></a>Az Azure App Service konfigurálása 

1. Az Azure parancssori felület használatával vagy [felhő rendszerhéj](/azure/cloud-shell/overview), hozzon létre egy új [Linux webalkalmazás](/azure/app-service/containers/app-service-linux-intro). Ebben az oktatóanyagban a webes alkalmazás neve `myJavaApp`, de szeretné használni a saját alkalmazás egyedi nevét.
   
    ```azurecli-interactive
    az group create --name myResourceGroupJenkins --location westus
    az appservice plan create --is-linux --name myLinuxAppServicePlan --resource-group myResourceGroupJenkins 
    az webapp create --name myJavaApp --resource-group myResourceGroupJenkins --plan myLinuxAppServicePlan
    ```

2. Hozzon létre egy [Azure tároló beállításjegyzék](/azure/container-registry/container-registry-intro) Jenkins által a Docker-lemezképek tárolásához. A tároló neve ebben az oktatóanyagban használt van `jenkinsregistry`, de szeretné használni a saját tároló beállításjegyzék egyedi nevét. 

    ```azurecli-interactive
    az acr create --name jenkinsregistry --resource-group myResourceGroupJenkins --sku Basic --admin-enabled
    ```
3. A webalkalmazás futtatásához a tároló beállításjegyzék leküldött Docker képek konfigurálása, és adja meg, hogy a tárolóban futó alkalmazást a kéréseket a 8080-as porton figyeli-e.   

    ```azurecli-interactive
    az webapp config container set -c jenkinsregistry/webapp --resource-group myResourceGroupJenkins --name myJavaApp
    az webapp config appsettings set --resource-group myResourceGroupJenkins --name myJavaApp --settings PORT=8080
    ```

## <a name="configure-the-azure-app-service-jenkins-plug-in"></a>Az Azure App Service Jenkins beépülő modul konfigurálása

1. A Jenkins webkonzol, válassza ki a **MyJavaApp** létrehozott feladat, és válassza ki **konfigurálása** a bal oldali a lap a.
2. Görgessen le a **utáni műveletek**, majd jelölje be **utáni műveletének hozzáadása** válassza **közzététele az Azure Web Apps**.
3. A **Azure profil konfigurációs**, jelölje be **Hozzáadás** mellett **Azure hitelesítő adatait** válassza **Jenkins**.
4. Az a **adja hozzá a hitelesítő adatok** párbeszédablakban válassza **Microsoft Azure szolgáltatás egyszerű** a a **jellegű** legördülő.
5. Hozzon létre egy Active Directory szolgáltatás egyszerű az Azure parancssori felületen vagy [felhő rendszerhéj](/azure/cloud-shell/overview).
    
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
6. Adja meg a hitelesítő adatokat az egyszerű szolgáltatásnév az a **adja hozzá a hitelesítő adatok** párbeszédpanel. Ha nem ismeri az Azure-előfizetése Azonosítóját, a parancssori lekérdezhető:
     
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

    ![Az Azure szolgáltatás egyszerű konfigurálása](media/jenkins-java-quickstart/azure_service_principal.png)
6. Ellenőrizze az egyszerű szolgáltatás végzi a hitelesítést Azure kiválasztásával **ellenőrizze a szolgáltatás egyszerű**. 
7. Válassza ki **Hozzáadás** mentheti a hitelesítő adatait.
8. Válassza ki a szolgáltatás egyszerű hitelesítő adatait az előzőekben adott hozzá a **Azure hitelesítő adatok** legördülő Ha áll vissza a **közzététele az Azure Web Apps** konfigurációs.
9. A **Alkalmazáskonfiguráció**, válassza ki az erőforráscsoport és a webes alkalmazás nevét a legördülő listán.
10. Válassza ki a **keresztül Docker közzététel** választógombot.
11. Adja meg `complete/Dockerfile` a **Dockerfile elérési**.
12. Adja meg `https://jenkinsregistry.azurecr.io` a a **Docker beállításjegyzék URL-cím** mező.
13. Válassza ki **Hozzáadás** melletti **beállításjegyzék hitelesítő adatok**. 
14. Adja meg a rendszergazda felhasználóneve a létrehozott Azure-tároló beállításjegyzék a **felhasználónév**.
15. Adja meg a jelszót az Azure-tárolóba beállításjegyzék a **jelszó** mező. Az Azure portálról vagy a következő parancssori parancsot kaphatunk a felhasználónevét és jelszavát:

    ```azurecli-interactive
    az acr credential show -n jenkinsregistry
    ```
    ![Adja hozzá a tároló beállításjegyzék hitelesítő adatokat](media/jenkins-java-quickstart/enter_acr_credentials.png)
15. Válassza ki **Hozzáadás** a hitelesítő adatok mentése.
16. Válassza ki az újonnan létrehozott hitelesítő a **beállításjegyzék hitelesítő adatok** a legördülő lista a **Alkalmazáskonfiguráció** a panel a **közzététele az Azure Web Apps**. A befejezett utáni művelet a következő kép kell hasonlítania:   
    ![POST build művelet konfigurációját az Azure App Service telepítése](media/jenkins-java-quickstart/appservice_plugin_configuration.png)
17. Válassza ki **mentése** a feladat konfigurációjának mentéséhez.

## <a name="deploy-the-app-from-github"></a>Telepítse az alkalmazást a Githubon

1. Válassza ki a Jenkins projekt **Build most** mintaalkalmazás telepítése az Azure-bA.
2. A létrehozás után az alkalmazás az élő Azure fennállt közzétételi URL-címen, például http://myjavaapp.azurewebsites.net.   
   ![A telepített alkalmazás megtekintése az Azure-on](media/jenkins-java-quickstart/hello_docker_world_unedited.png)

## <a name="push-changes-and-redeploy"></a>Módosításokat, és telepítse újra

1. A Github elágazás a keresse meg a webes `complete/src/main/java/Hello/Application.java`. Válassza ki a **a fájl szerkesztése** a hivatkozás leválasztása a GitHub-felület jobb oldalán.
2. Az alábbi módosítást a `home()` metódust, és aztán a Módosítás gombra a tárház főágába.
   
    ```java
    return "Hello Docker World on Azure";
    ```
3. Egy új build Jenkins, az új véglegesítési által indított indítja el a `master` a tárház főágába. Miután befejeződött, töltse be újra az alkalmazást az Azure-on.     
      ![A telepített alkalmazás megtekintése az Azure-on](media/jenkins-java-quickstart/hello_docker_world.png)
  
## <a name="next-steps"></a>Következő lépések

- [Használja az Azure virtuális gépeken, ügynökök létrehozása](/azure/jenkins/jenkins-azure-vm-agents)
- [A feladatok és az adatcsatorna esetén, amelyben az Azure CLI-erőforrások kezelése](/azure/jenkins/execute-cli-jenkins-pipeline)
 
