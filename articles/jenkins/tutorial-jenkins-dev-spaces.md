---
title: A Jenkins Azure dev Spaces beépülő moduljának használata az Azure Kubernetes Service használatával
description: Ismerje meg, hogyan használható az Azure dev Spaces beépülő modul a folyamatos integrációs folyamatokban.
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 42d732cda26f0c34f0a54fffc0b1b9c54def94ad
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158735"
---
# <a name="tutorial-using-the-azure-dev-spaces-plug-in-for-jenkins-with-azure-kubernetes-service"></a>Oktatóanyag: a Jenkins Azure dev Spaces beépülő moduljának használata az Azure Kubernetes Service használatával 

Az Azure dev Spaces lehetővé teszi, hogy tesztelje és iteratív az Azure Kubernetes szolgáltatásban (ak) futó Service-alkalmazást, anélkül, hogy replikálni vagy kigúnyolni kellene a függőségeket. A Jenkins-hez készült Azure dev Spaces beépülő modul segítséget nyújt a folyamatos integráció és a továbbítás (CI/CD) folyamatában a fejlesztői terek használatához.

Ez az oktatóanyag a Azure Container Registry (ACR) használatát is használja. Az ACR rendszerképeket tárol, és az ACR-feladatok Docker-és Helm-összetevőket építenek. Az ACR és az ACR feladatának az összetevő-generációhoz való használata esetén nincs szükség további szoftverek, például a Docker telepítésére a Jenkins-kiszolgálón. 

Ebben az oktatóanyagban a következő feladatokat hajtja végre:

> [!div class="checklist"]
> * Azure dev Spaces-kompatibilis AK-fürt létrehozása
> * Többszolgáltatásos alkalmazás üzembe helyezése az AK-ban
> * A Jenkins-kiszolgáló előkészítése
> * Az Azure dev Spaces beépülő modul használata a Jenkins-folyamatokban a kód módosításainak előnézetéhez, mielőtt összevonja őket a projektbe

Ez az oktatóanyag az alapvető Azure-szolgáltatások, az AK, az ACR, az Azure dev Spaces, a Jenkins- [folyamatok](https://jenkins.io/doc/book/pipeline/) és a githubok közti ismeretét feltételezi. A kubectl és a Helm támogató eszközeinek alapszintű ismerete hasznos.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

* Egy GitHub-fiók. Ha nem rendelkezik GitHub-fiókkal, hozzon létre egy [ingyenes fiókot](https://github.com/) a Kezdés előtt.

* A [Visual Studio Code](https://code.visualstudio.com/download) és az [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) bővítmény telepítve van.

* [Azure CLI telepítve](/cli/azure/install-azure-cli?view=azure-cli-latest), 2.0.43 vagy újabb verzió.

* Egy Jenkins-főkiszolgáló. Ha még nem rendelkezik Jenkins-főkiszolgálóval [, az ebben](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template)a rövid útmutatóban ismertetett lépéseket követve telepítse a [Jenkins](https://aka.ms/jenkins-on-azure) az Azure-ban. 

* A Jenkins-kiszolgálónak mind a Helm, mind a kubectl telepítve kell lennie, és elérhetőnek kell lennie a Jenkins-fiók számára az oktatóanyag későbbi részében ismertetett módon.

* VS Code, a VS Code terminál vagy a WSL és a bash. 


## <a name="create-azure-resources"></a>Azure-erőforrások létrehozása

Ebben a szakaszban az Azure-erőforrásokat hozza létre:

* Az oktatóanyaghoz tartozó összes Azure-erőforrást tartalmazó erőforráscsoport.
* Egy [Azure Kubernetes-szolgáltatás](https://docs.microsoft.com/azure/aks/) (ak) fürtje.
* Egy [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) (ACR), amely felépíti (ACR-feladatokat használ) és Docker-rendszerképeket tárol.

1. Hozzon létre egy erőforráscsoportot.

    ```bash
    az group create --name MyResourceGroup --location westus2
    ```

2. Hozzon létre egy AK-fürtöt. Hozza létre az AK-fürtöt egy olyan [régióban, amely támogatja a fejlesztői helyeket](../dev-spaces/about.md#supported-regions-and-configurations).

    ```bash
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Állítsa be az AK-t a fejlesztői szóközök használatára.

    ```bash
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    Ez a lépés telepíti a `azds` CLI-bővítményt.

4. Hozzon létre egy tároló-beállításjegyzéket.

    ```bash
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Minta alkalmazások üzembe helyezése az AK-fürtön

Ebben a szakaszban egy fejlesztői területet állít be, és üzembe helyez egy minta alkalmazást az utolsó szakaszban létrehozott AK-fürtön. Az alkalmazás két részből áll: a *webfrontendből* és a *mywebapi*. Mindkét összetevő egy fejlesztői térben van üzembe helyezve. Az oktatóanyag későbbi részében egy lekéréses kérelmet küld a mywebapi-től a CI-folyamat elindításához a Jenkins-ben.

Az Azure dev Spaces és a többszolgáltatásos fejlesztés Azure dev Spaces használatával történő használatáról további információért lásd: az [Azure dev Spaces használatának első lépései a Javával](https://docs.microsoft.com/azure/dev-spaces/get-started-java)és [Az Azure dev Spaces szolgáltatással történő többfunkciós fejlesztés](https://docs.microsoft.com/azure/dev-spaces/multi-service-java). Ezek az oktatóanyagok további háttér-információkat is biztosítanak itt.

1. Töltse le az https://github.com/Azure/dev-spaces-tárházat a GitHubról.

2. Nyissa meg a `samples/java/getting-started/webfrontend` mappát a VS Code-ban. (Figyelmen kívül hagyhat minden olyan alapértelmezett kérést, amely az objektumok hibakeresésére vagy a projekt visszaállítására vonatkozik.)

3. A `/src/main/java/com/ms/sample/webfrontend/Application.java` frissítése a következőhöz hasonlóan néz ki:

    ```java
    package com.ms.sample.webfrontend;

    import java.io.*;
    import java.net.*;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.web.bind.annotation.*;

    @SpringBootApplication
    @RestController
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }

        @RequestMapping(value = "/greeting", produces = "text/plain")
        public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
            URLConnection conn = new URL("http://mywebapi/").openConnection();
            conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
            try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
            {
                return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
            }
        }
    }
    ```

4. Kattintson a **nézet** , majd a **terminál** lehetőségre az integrált terminálnak a vs Code-ban való megnyitásához.

5. Futtassa az `azds prep` parancsot, hogy előkészítse az alkalmazást egy fejlesztői térben való futtatásra. Az alkalmazás megfelelő előkészítéséhez `dev-spaces/samples/java/getting-started/webfrontend` kell futtatni a parancsot:

    ```bash
    azds prep --public
    ```

    A dev Spaces CLI `azds prep` parancsa alapértelmezett beállításokkal hoz létre Docker-és Kubernetes-eszközöket. Ezek a fájlok továbbra is a projekt élettartama alatt maradnak, és testre szabhatók:

    * `./Dockerfile` és `./Dockerfile.develop` leírja az alkalmazás tárolójának képét, valamint azt, hogy a forráskód hogyan épül fel és fut a tárolón belül.
    * A [ alatt található ](https://helm.sh/docs/developing_charts/)Helm-diagram`./charts/webfrontend` ismerteti a konténer Kubernetesben történő üzembe helyezését.
    * `./azds.yaml` az Azure dev Spaces konfigurációs fájlja.

    További információ: [Az Azure dev Spaces működése és konfigurálása](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works).

6. Hozza létre és futtassa az alkalmazást az AK-ban az `azds up` parancs használatával:

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>Ellenőrizze a konzol kimenetét az `up` parancs által létrehozott URL-címmel kapcsolatos információkért. Ez az alábbi formátumban lesz:

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Nyissa meg az URL-címet egy böngészőablakban, és tekintse meg a webalkalmazást. Ahogy a tároló futni kezd, a rendszer `stdout` és `stderr` kimenetet streamel a terminálablakba.

8. Következő lépésként állítsa be és telepítse a *mywebapi*:

    1. `dev-spaces/samples/java/getting-started/mywebapi` könyvtárának módosítása

    2. Futtassa a következőt:

        ```bash
        azds prep
        ```

    3. Futtassa a következőt:

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Jenkins-kiszolgáló előkészítése

Ebben a szakaszban a Jenkins-kiszolgáló előkészíti a minta CI-folyamat futtatását.

* Beépülő modulok telepítése
* A Helm és a Kubernetes parancssori felületének telepítése
* Hitelesítő adatok hozzáadása

### <a name="install-plug-ins"></a>Beépülő modulok telepítése

1. Jelentkezzen be a Jenkins-kiszolgálóra. Válassza a **Jenkins kezelése > a beépülő modulok kezelése**lehetőséget.
2. A **rendelkezésre álló** lapon válassza ki a következő beépülő modulokat:
    * [Azure dev-helyek](https://plugins.jenkins.io/azure-dev-spaces)
    * [Azure Container Registry feladatok](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Környezeti injektor](https://plugins.jenkins.io/envinject)
    * [GitHub-integráció](https://plugins.jenkins.io/github-pullrequest)

    Ha ezek a beépülő modulok nem jelennek meg a listában, tekintse meg a **telepített** lapot, és ellenőrizze, hogy telepítve vannak-e már.

3. A beépülő modulok telepítéséhez válassza a **Letöltés most lehetőséget, majd az újraindítás után telepítse a telepítést**.

4. A telepítés befejezéséhez indítsa újra a Jenkins-kiszolgálót.

### <a name="install-helm-and-kubectl"></a>A Helm és a kubectl telepítése

A minta folyamat Helm és kubectl használatával helyezi üzembe a fejlesztői területet. A Jenkins telepítésekor létrejön egy *Jenkins*nevű rendszergazdai fiók. A Helm és a kubectl is elérhetőnek kell lennie a Jenkins-felhasználó számára.

1. Létesítsen SSH-kapcsolatokat a Jenkins-főkiszolgálóval. 

2. Váltson a `jenkins` felhasználóra:
    ```bash
    sudo su jenkins
    ```

3. Telepítse a Helm CLI-t. További információ: a [Helm telepítése](https://helm.sh/docs/using_helm/#installing-helm).

4. Telepítse a kubectl. További információ: [**az ACS kubernetes install-CLI**](https://helm.sh/docs/using_helm/#installing-helm).

### <a name="add-credentials-to-jenkins"></a>Hitelesítő adatok hozzáadása a Jenkins-hez

1. A Jenkins-nek szüksége van egy Azure-szolgáltatásra az Azure-erőforrások hitelesítéséhez és eléréséhez. Az egyszerű szolgáltatásnév létrehozásához tekintse meg az üzembe helyezés a Azure App Service oktatóanyagban az [egyszerű szolgáltatás létrehozása](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) című szakaszt. Ügyeljen arra, hogy mentse a kimenet másolatát `create-for-rbac`, mert a következő lépés végrehajtásához szüksége lesz erre az információra. A kimenet a következőhöz hasonlóan fog kinézni:

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Adjon hozzá egy *Microsoft Azure egyszerű szolgáltatásnév* -típust a Jenkins-ben az előző lépés egyszerű szolgáltatásnév információinak használatával. Az alábbi képernyőképen szereplő nevek megfelelnek a `create-for-rbac`kimenetének.

    Az **azonosító** mező az egyszerű szolgáltatáshoz tartozó Jenkins-beli hitelesítő adat neve. A példa a `displayName` (ebben a példányban `xxxxxxxjenkinssp`) értékét használja, de bármilyen szöveget használhat. A hitelesítő adat neve a AZURE_CRED_ID környezeti változó értéke a következő szakaszban.

    ![Egyszerű szolgáltatásnév hitelesítő adatainak hozzáadása a Jenkins szolgáltatáshoz](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    A **Leírás** megadása nem kötelező. Részletesebb útmutatásért lásd: [szolgáltatásnév hozzáadása a Jenkins](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) szakasz a Deploy to Azure app Service oktatóanyagban. 



3. Az ACR hitelesítő adatainak megjelenítéséhez futtassa a következő parancsot:

    ```bash
    az acr credential show -n <yourRegistryName>
    ```

    Készítsen másolatot a JSON-kimenetről, amelynek a következőhöz hasonlóan kell kinéznie:

    ```json
    {
      "passwords": [
        {
          "name": "password",
          "value": "vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz"
        },
        {
          "name": "password2",
          "value": "zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz"
        }
      ],
      "username": "acr01"
    }
    ```

4. Adjon hozzá egy jelszó típusú hitelesítő adatokkal *rendelkező felhasználónevet* a Jenkins-ben. A **Felhasználónév** az utolsó lépésből származó Felhasználónév, ebben a példában `acr01`. A **jelszó** az első jelszó értéke, ebben a példában `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`. A hitelesítő adat **azonosítója** a ACR_CRED_ID értéke.

5. Hozzon létre egy AK-beli hitelesítő adatokat. Adjon hozzá egy *Kubernetes-konfigurációs (kubeconfig)* hitelesítőadat-típust a Jenkins-ben (használja a "közvetlen bevitel" lehetőséget). Az AK-fürthöz tartozó hozzáférési hitelesítő adatok lekéréséhez futtassa a következő parancsot:

    ```cmd
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   A hitelesítő adat **azonosítójának** értéke KUBE_CONFIG_ID a következő szakaszban.

## <a name="create-a-pipeline"></a>Folyamat létrehozása

A példában a folyamathoz kiválasztott forgatókönyv egy valós mintázaton alapul: A lekéréses kérelem egy olyan CI-folyamatot indít el, amely létrehoz, majd üzembe helyezi a javasolt módosításokat egy Azure fejlesztői tárhelyen tesztelés és felülvizsgálat céljából. A felülvizsgálat eredményétől függően a módosítások egyesítve lettek, és a rendszer az AK-ban helyezi üzembe, vagy elveti őket. Végül eltávolítja a fejlesztői területet.

A Jenkins-folyamat konfigurációja és Jenkinsfile határozza meg a CI-folyamat szakaszait. Ez a folyamatábra a Jenkinsfile által definiált folyamat szakaszait és döntési pontjait jeleníti meg:

![Jenkins-folyamat folyamatábrája](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. Töltse le a *mywebapi* -projekt módosított verzióját https://github.com/azure-devops/mywebapi. Ez a projekt több, a folyamat létrehozásához szükséges fájlt tartalmaz, beleértve a *Jenkinsfile*, a *Dockerfiles*és a Helm diagramot is.

2. Jelentkezzen be a Jenkinsbe. A bal oldali menüben válassza az **elem hozzáadása elemet**.

3. Válassza a **folyamat**lehetőséget, majd adjon meg egy nevet az **adja meg az elem nevét** mezőben. Kattintson az **OK gombra**, majd a folyamat-konfiguráció képernyő automatikusan meg fog nyílni.

4. Az **általános** lapon keresse meg **a környezet előkészítése a futtatáshoz lehetőséget**. 

5. A **Jenkins környezeti változóinak megőrzése** és a **Jenkins-Build változók megőrzése**.

6. A **Tulajdonságok tartalma** mezőben adja meg a következő környezeti változókat:

    ```
    AZURE_CRED_ID=[your credential ID of service principal]
    RES_GROUP=[your resource group of the function app]
    ACR_RES_GROUP=[your ACR resource group]
    ACR_NAME=[your ACR name]
    ACR_REGISTRY=[your ACR registry url, without http schema]
    ACR_CRED_ID=[your credential id of your ACR account]
    AKS_RES_GROUP=[your AKS resource group]
    AKS_NAME=[your AKS name]
    IMAGE_NAME=[name of Docker image you will push to ACR, without registry prefix]
    KUBE_CONFIG_ID=[your kubeconfig id]
    PARENT_DEV_SPACE=[shared dev space name]
    TEST_ENDPOINT=[your web frontend end point for testing. Should be webfrontend.XXXXXXXXXXXXXXXXXXX.xxxxxx.aksapp.io]
    ```

    Az előző szakaszokban megadott mintaadatok használatával a környezeti változók listáját a következőhöz hasonló módon kell kinéznie:

    ![Jenkins-folyamat környezeti változói](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. Válassza ki **a folyamat parancsfájlját az SCM-ből** a **folyamat > definíciójában**.
8. Az **SCM**-ben válassza a **git** lehetőséget, majd adja meg a tárház URL-címét.
9. Az **ág**megadása területen adja meg a `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`.
10. Töltse ki az SCM-tárház URL-címét és a "Jenkinsfile" parancsfájl elérési útját.
11. Az **egyszerűsített pénztárat** ellenőrizni kell.

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>Lekéréses kérelem létrehozása a folyamat indításához

A jelen szakasz 3. lépésének végrehajtásához meg kell adnia a Jenkinsfile egy részét, ellenkező esetben 404-as hibaüzenet jelenik meg, amikor megpróbálja megtekinteni az új és a régi verziókat egymás mellett. Alapértelmezés szerint, ha úgy dönt, hogy egyesíteni kívánja a PR-t, a mywebapi előző megosztott verziója el lesz távolítva, és az új verzió váltja fel. Az 1. lépés végrehajtása előtt végezze el a következő módosítást a Jenkinsfile:

```Groovy
    if (userInput == true) {
        stage('deploy') {
            // Apply the deployment to shared namespace in AKS using acsDeploy, Helm or kubectl   
        }
        
        stage('Verify') {
            // verify the staging environment is working properly
        }
        
        /* Comment the cleanup stage to allow side by side comparison with the new child dev space

        stage('cleanup') {
            devSpacesCleanup aksName: env.AKS_NAME, 
                azureCredentialsId: env.AZURE_CRED_ID, 
                devSpaceName: devSpaceNamespace, 
                kubeConfigId: env.KUBE_CONFIG_ID, 
                resourceGroupName: env.AKS_RES_GROUP,
                helmReleaseName: releaseName
        }
        */

    } else {
        // Send a notification
    }
```

1. Módosítsa `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java`, majd hozzon létre egy lekéréses kérelmet. Például:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Jelentkezzen be a Jenkins-be, válassza ki a folyamat nevét, majd válassza a **Build most**lehetőséget. 

    Beállíthat egy *webhookot* is a Jenkins-folyamat automatikus elindításához. A lekéréses kérelem megadásakor a GitHub a Jenkins-be KÜLDi a folyamatot, és elindítja a folyamatot. A webhook beállításával kapcsolatos további információkért lásd: a [Jenkins és a GitHub összekapcsolása](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github).

3. A jelenlegi megosztott verzió változásainak összehasonlítása:

    1. Nyissa meg a böngészőt, és navigáljon a megosztott verzió `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. TEST_ENDPOINT tartalmazza az URL-címet.

    2. Nyisson meg egy másik fület, majd adja meg a PR-fejlesztői terület URL-címét. A `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`hasonló lesz. A > < Build History (létrehozási előzmények) elemre mutató hivatkozást a Jenkins-feladatokhoz tartozó **> > konzol kimenetében** találja. Keresse meg a `aksapp`oldalon, vagy csak az előtagot, keresse meg a `azdsprefix`.

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>Az URL-cím létrehozása a gyermek fejlesztői területhez

Ha lekéréses kérelmet küld, a Jenkins létrehoz egy gyermek-fejlesztési helyet a csapat megosztott fejlesztői területe alapján, és futtatja a kódot a lekéréses kérelemből az adott gyermek-fejlesztési térben. A gyermek fejlesztői területhez tartozó URL-cím a `http://$env.azdsprefix.<test_endpoint>`űrlapot veszi fel. 

**$env. azdsprefix** az Azure dev Spaces beépülő modullal **devSpacesCreate**:

```Groovy
stage('create dev space') {
    devSpacesCreate aksName: env.AKS_NAME,
        azureCredentialsId: env.AZURE_CRED_ID,
        kubeconfigId: env.KUBE_CONFIG_ID,
        resourceGroupName: env.AKS_RES_GROUP,
        sharedSpaceName: env.PARENT_DEV_SPACE,
        spaceName: devSpaceNamespace
}
```

A `test_endpoint` a korábban üzembe `azds up`helyezett webfrontend-alkalmazás URL-címe a minta- [alkalmazások üzembe helyezése az AK-fürtön, 7. lépés](#test_endpoint). `$env.TEST_ENDPOINT` értékének beállítása a folyamat konfigurációjában történik. 

A következő kódrészlet azt mutatja be, hogyan használja a gyermek fejlesztői terület URL-címét a `smoketest` fázisban. A kód ellenőrzi, hogy a gyermek fejlesztői terület TEST_ENDPOINT elérhető-e, és ha igen, letölti a megszólítás szövegét az stdout-ra:

```Groovy
stage('smoketest') {
    // CI testing against http://$env.azdsprefix.$env.TEST_ENDPOINT" 
    SLEEP_TIME = 30
    SITE_UP = false
    for (int i = 0; i < 10; i++) {
        sh "sleep ${SLEEP_TIME}"
        code = "0"
        try {
            code = sh returnStdout: true, script: "curl -sL -w '%{http_code}' 'http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting' -o /dev/null"
        } catch (Exception e){
            // ignore
        }
        if (code == "200") {
            sh "curl http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting"
            SITE_UP = true
            break
        }
    }
    if(!SITE_UP) {
        echo "The site has not been up after five minutes"
    }
}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a minta alkalmazás használatával, törölje az Azure-erőforrásokat az erőforráscsoport törlésével:

```bash
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan használhatja a Jenkins-hez készült Azure dev Spaces beépülő modult és a Azure Container Registry beépülő modult a kód összeállításához és a fejlesztői területekre való üzembe helyezéshez.

Az alábbi források további információkat nyújtanak az Azure fejlesztői tárhelyekről, az ACR-feladatokról és a CI/CD-ről a Jenkins segítségével.

Azure dev-helyek:
* [Az Azure Dev Spaces működése és konfigurálása](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works)

ACR-feladatok:
* [Operációs rendszer és keretrendszer javításának automatizálása az ACR Tasksszal](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)
* [Automatikus Build kód véglegesítve](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)

CI/CD az Azure-beli Jenkins-szel:
* [Jenkins folyamatos üzembe helyezése](https://docs.microsoft.com/azure/aks/jenkins-continuous-deployment)
