---
title: Az Azure Dev tárolóhelyek beépülő modul használatával a Jenkins Azure Kubenetes szolgáltatással
description: Ismerje meg, hogyan használható az Azure fejlesztési tárolóhelyek beépülő modul egy folyamatos integrációs folyamat.
author: tomarchermsft
ms.author: tarcher
ms.service: jenkins
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/18/2019
ms.openlocfilehash: f5f74ebeb803a5c493f1dbedb6501adf3a88c215
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785675"
---
<!-- GMinchAQ, 06/18/19 -->

# <a name="tutorial-using-the-azure-dev-spaces-plugin-for-jenkins-with-azure-kubenetes-service"></a>Oktatóanyag: Az Azure Dev tárolóhelyek beépülő modul használatával a Jenkins Azure Kubenetes szolgáltatással 

Az Azure fejlesztési tárolóhelyek tesztelése és iteratív fejlesztés az Azure Kubernetes Service (AKS) futtató replikálása vagy a függőségek utánzása nélkül mikroszolgáltatás alkalmazás lehetővé teszi. Az Azure fejlesztési tárolóhelyek beépülő modul a Jenkins segít a folyamatos integráció és teljesítés (CI/CD) a fejlesztési szóköz használatára.

Ez az oktatóanyag az Azure Container Registry (ACR) is használja. ACR tárolja a lemezképeket, és a egy ACR-tevékenység létrehozza a Docker és a Helm-összetevők. ACR-REL és az ACR-tevékenység használatával az összetevő létrehozásához nincs szükség további szoftverek, például a Dockert, a Jenkins-kiszolgáló telepítését. 

Ez az oktatóanyag ezen feladatok fogja végrehajtani:

> [!div class="checklist"]
> * Azure fejlesztői szóközt engedélyezni AKS-fürt létrehozása
> * Több szolgáltatást az AKS-alkalmazás üzembe helyezése
> * A Jenkins-kiszolgáló előkészítése
> * Az Azure fejlesztési tárolóhelyek beépülő moduljának használata a Jenkins-folyamatokban őket a projektbe egyesítés előtt kód módosítások előnézetének megtekintése

Ez az oktatóanyag feltételezi, hogy mag köztes ismerete Azure-szolgáltatások, az AKS, ACR, az Azure fejlesztési tárolóhelyek, a Jenkins [folyamatok](https://jenkins.io/doc/book/pipeline/) és beépülő modulok és a GitHub. Hasznos eszközök, például a kubectl és a Helm támogató alapszintű ismeretét.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

* Egy GitHub-fiók. Ha nem rendelkezik GitHub-fiókkal, hozzon létre egy [ingyenes fiókot](https://github.com/) megkezdése előtt.

* [A Visual Studio Code](https://code.visualstudio.com/download) együtt a [Azure fejlesztési tárolóhelyek](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) kiterjesztés telepítése.

* [Az Azure CLI telepítve](/cli/azure/install-azure-cli?view=azure-cli-latest), 2.0.43 verzió vagy újabb verziója.

* Egy Jenkins-főkiszolgáló. Ha még nem rendelkezik Jenkins-főkiszolgáló, üzembe helyezése [Jenkins](https://aka.ms/jenkins-on-azure) az Azure-ban, a jelen szakasz lépéseit követve [rövid](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* A Jenkins-kiszolgáló kell rendelkeznie a Helm és a telepített és elérhető a Jenkins-fiókjába, a kubectl, ebben az oktatóanyagban leírtak szerint.

* A VS Code, VS Code Terminálszolgáltatások vagy a WSL és a Bash. 


## <a name="create-azure-resources"></a>Azure-erőforrások létrehozása

Ebben a szakaszban az Azure-erőforrásokat hoz létre:

* Egy erőforráscsoport tartalmazó összes bevezetésének erőforrást ehhez az oktatóanyaghoz.
* Egy [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) (AKS-) fürt.
* Egy [az Azure container registry](https://docs.microsoft.com/azure/container-registry/) (ACR) (ACR-feladatok használatával), és a Docker-rendszerképek tárolásához.

1. Hozzon létre egy erőforráscsoportot.

    ```bash
    az group create --name MyResourceGroup --location westus2
    ```

2. AKS-fürt létrehozása. Az AKS-fürt létrehozása egy [régióban, amely támogatja a fejlesztői, szóközök](../dev-spaces/about.md#supported-regions-and-configurations).

    ```bash
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Aks-ben: fejlesztési szóköz használatára konfigurálja.

    ```bash
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    Ez a lépés telepíti a `azds` CLI-bővítményt.

4. Tárolóregisztrációs adatbázis létrehozása.

    ```bash
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Mintaalkalmazások az AKS-fürt üzembe helyezése

Ebben a szakaszban állítsa be a fejlesztési szóközzel, és üzembe helyezünk egy mintaalkalmazást, hogy az AKS-fürtöt az előző szakaszban létrehozott. Az alkalmazás két részből áll *webfrontend* és *mywebapi*. Mindkét összetevő fejlesztési szóközzel vannak üzembe helyezve. Az oktatóanyag későbbi részében meg fogjuk beküldhető egy tartalomjavítási kérelem mywebapi ellen a Jenkins CI folyamat indításához.

Azure fejlesztői szóközöket és több szolgáltatást fejlesztési használatáról az Azure fejlesztési tárolóhelyek további információkért lásd: [használatának első lépései a Java Azure fejlesztési tárolóhelyek](https://docs.microsoft.com/azure/dev-spaces/get-started-java), és [az Azure fejlesztési tárolóhelyek több szolgáltatásos fejlesztői](https://docs.microsoft.com/azure/dev-spaces/multi-service-java). Ezek az oktatóanyagok itt nem szereplő további háttér-információkat biztosít.

1. Töltse le a https://github.com/Azure/dev-spaces GitHub-tárházban.

2. Nyissa meg a `samples/java/getting-started/webfrontend` mappát a VS Code-ban. (Figyelmen kívül hagyhat minden olyan alapértelmezett kérést, amely az objektumok hibakeresésére vagy a projekt visszaállítására vonatkozik.)

3. Frissítés `/src/main/java/com/ms/sample/webfrontend/Application.java` a következő a következő módon:

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

4. Kattintson a **nézet** majd **terminálon** az integrált terminálon megnyitásához a VS Code-ban.

5. Futtassa a `azds prep` paranccsal készítse elő az alkalmazás futtatásához fejlesztési szóközzel. Ezt a parancsot kell futtatni a `dev-spaces/samples/java/getting-started/webfrontend` készíti elő az alkalmazás megfelelően:

    ```bash
    azds prep --public
    ```

    A fejlesztési tárolóhelyek CLI `azds prep` parancs létrehozza a Docker és a Kubernetes eszközök alapértelmezett beállításokkal. Ezek a fájlok megőrzéséhez a projekt teljes élettartama, és testre szabható:

    * `./Dockerfile` és `./Dockerfile.develop` leírják, az alkalmazás tárolórendszerképet, és hogyan forráskódját épül, és a tárolón belül futtatja.
    * A `./charts/webfrontend` alatt található [Helm-diagram](https://helm.sh/docs/developing_charts/) ismerteti a konténer Kubernetesben történő üzembe helyezését.
    * `./azds.yaml` az Azure fejlesztési tárolóhelyek konfigurációs fájl van.

    További információkért lásd: [hogyan Azure fejlesztési tárolóhelyek működik, és konfigurált](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works).

6. Alkalmazás összeállítása és futtatása az AKS használatával a `azds up` parancsot:

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>A konzol kimenete az URL-cím által létrehozott kapcsolatos információk beolvasása a `up` parancsot. Ez az alábbi formátumban lesz:

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Nyissa meg az URL-cím egy böngészőablakban, és a web app kell megjelennie. Ahogy a tároló futni kezd, a rendszer `stdout` és `stderr` kimenetet streamel a terminálablakba.

8. Következő lépésként állítsa be, és üzembe helyezése *mywebapi*:

    1. Módosítsa a könyvtárat `dev-spaces/samples/java/getting-started/mywebapi`

    2. Futtassa a következőt:

        ```bash
        azds prep
        ```

    3. Futtassa a következőt:

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Jenkins-kiszolgáló előkészítése

Ebben a szakaszban készítse elő a Jenkins-kiszolgáló a minta a CI folyamat futtatása.

* Beépülő modulok telepítése
* Helm és a Kubernetes parancssori felület telepítése
* Hitelesítő adatok hozzáadása

### <a name="install-plugins"></a>Beépülő modulok telepítése

1. Jelentkezzen be a Jenkins-kiszolgáló. Válasszon **Jenkins kezelése > beépülő modulok kezelése**.
2. Az a **elérhető** lapra, válassza ki a következő beépülő modulok:
    * [Az Azure fejlesztői, szóközök](https://plugins.jenkins.io/azure-dev-spaces)
    * [Az Azure Container Registry-feladatok](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Környezet Injector](https://plugins.jenkins.io/envinject)
    * [GitHub-integráció](https://plugins.jenkins.io/github-pullrequest)

    Ha a beépülő modulok nem jelennek meg a listában, ellenőrizze a **telepített** lapján megtekintheti, ha azok már telepítésekor.

3. A beépülő modulok telepítéséhez válassza **letöltése és telepítése az újraindítást követően**.

4. A Jenkins-kiszolgáló, a telepítés befejezéséhez indítsa újra.

### <a name="install-helm-and-kubectl"></a>Helm és a kubectl telepítése

A mintául szolgáló folyamat Helm és a kubectl használatával helyezze üzembe a fejlesztési területet. Jenkins telepítésekor létrehoz egy rendszergazdai fiók nevű *jenkins*. Helm és a kubectl kell lennie a jenkins-felhasználó számára elérhető.

1. Létesítsen SSH-kapcsolatot, a Jenkins-főkiszolgáló. 

2. Váltson a `jenkins` felhasználói:
    ```bash
    sudo su jenkins
    ```

3. A Helm CLI telepítéséhez. További információkért lásd: [telepítése Helm](https://helm.sh/docs/using_helm/#installing-helm).

4. A kubectl telepítése. További információkért lásd: [ **az acs kubernetes install-cli**](https://helm.sh/docs/using_helm/#installing-helm).

### <a name="add-credentials-to-jenkins"></a>Hitelesítő adatok hozzáadása a Jenkinsben

1. A Jenkins Azure-beli szolgáltatásnév hitelesítése és Azure-erőforrások eléréséhez szükség van. A szolgáltatásnév létrehozásához, tekintse meg a [egyszerű szolgáltatás létrehozása](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) üzembe helyezés az Azure App Service-oktatóanyag szakaszát. Ne felejtse el menteni a kimenet egy másolatát `create-for-rbac` , mivel ezt az információt a következő lépés elvégzéséhez szüksége. A kimenet következőhöz hasonlóan fog kinézni:

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Adjon hozzá egy *a Microsoft Azure egyszerű szolgáltatás* hitelesítőadat-típus a jenkins kifejezést, használja a szolgáltatásnév adatait az előző lépésben. A neve, az alábbi képernyőképen kimenete megfelel `create-for-rbac`.

    A **azonosító** a Jenkins-hitelesítő adat neve, a szolgáltatásnévhez tartozó. A példában értékét használja `displayName` (ebben a példányban `xxxxxxxjenkinssp`), de használhat bármilyen szöveget. A hitelesítő adat neve a következő szakaszban AZURE_CRED_ID környezeti változó értéke.

    ![Add service principal credentials to Jenkins](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    A **leírás** nem kötelező. További részletes utasítások: [szolgáltatásnév hozzáadása Jenkins](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) szakaszt az üzembe helyezés az Azure App Service-oktatóanyag. 



3. Az ACR-hitelesítő adatok megjelenítéséhez a következő parancs futtatásával:

    ```bash
    az acr credential show -n <yourRegistryName>
    ```

    Készítsen másolatot a JSON-kimenetet, amely kell kinéznie:

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

4. Adjon hozzá egy *felhasználónév, jelszó* hitelesítőadat-típus a Jenkinsben. A **felhasználónév** az utolsó lépésben, ebben a példában a felhasználónév `acr01`. A **jelszó** az érték az első jelszót, és ebben a példában `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`. A **azonosító** ACR_CRED_ID értékét az ezeket a hitelesítő adatokat.

5. Az AKS hitelesítő adatok beállítása. Adjon hozzá egy *Kubernetes konfigurációs (kubeconfig)* hitelesítőadat-típus a Jenkinsben (használja az "Enter közvetlenül" beállítást). Az AKS-fürt esetében a hozzáférési hitelesítő adatok lekéréséhez futtassa a következő parancsot:

    ```cmd
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   A **azonosító** ezeket a hitelesítő adatokat a következő szakaszban KUBE_CONFIG_ID értéke.

## <a name="create-a-pipeline"></a>Folyamat létrehozása

A forgatókönyv a példa folyamat kiválasztva egy való életből vett minta alapján: Eseményindítók CI folyamat, amely lekéréses kérelmek állít össze, és ezután telepíti a javasolt változtatások egy Azure dev terület tesztelése, és tekintse át. Attól függően, a felülvizsgálat eredményének a módosítások vannak egyesítve és az aks-ben üzembe helyezett vagy elvetett. Végül a fejlesztési szóközöket eltávolítja.

A Jenkins folyamat konfigurálása és a Jenkinsfile határozza meg a szakasz a CI folyamat. Ez a folyamatábra szakaszain és határozzák meg a Jenkinsfile döntési pontokat mutatja:

![A Jenkins folyamat folyamat](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. Töltse le a módosított változatát a *mywebapi* -projektet https://github.com/azure-devops/mywebapi. Ez a projekt számos fájlt létrehoz egy folyamatot, szükség tartalmazza többek között a *Jenkinsfile*, *docker-fájlok*, és a Helm-diagramot.

2. Jelentkezzen be a Jenkins. A bal oldali menüben válassza ki a **elem hozzáadása**.

3. Válassza ki **folyamat**, majd adja meg egy nevet a a **adjon nevet az elemnek** mezőbe. Válassza ki **OK**, és ezután automatikusan megnyílik a folyamat következő konfigurációs képernyőjén.

4. Az a **általános** lapon jelölje **készítse elő a környezetet a Futtatás**. 

5. Ellenőrizze **tartsa a Jenkins környezeti változók** és **tartsa a Jenkins-Buildelés változók**.

6. Az a **tulajdonságok tartalom** mezőbe írja be az alábbi környezeti változókat:

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

    Az előző szakaszok megadott minta értéket használja, a környezeti változók listájának hasonlóan kell kinéznie ezt:

    ![A Jenkins folyamat környezeti változók](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. Válasszon **SCM parancsfájlt folyamat** a **folyamat > definíció**.
8. A **SCM**, válassza a **Git** és írja be az adattár URL-CÍMÉT.
9. A **ág specifikátor**, adja meg `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`.
10. Adja meg az SCM adattár URL-CÍMÉT és a parancsfájl elérési út "Jenkinsfile".
11. **Egyszerűsített kivétele** ellenőrizni kell.

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>A folyamat aktiválása a lekéréses kérelem létrehozása

Ebben a szakaszban 3 befejezése lépésnél hozzá megjegyzéseket a Jenkinsfile része kell, ellenkező esetben a 404-es hiba jelenik meg a régi és új verziókat egymás mellett megtekintéséhez. Alapértelmezés szerint ha úgy dönt, hogy a lekéréses kérelem egyesítése mywebapi megosztott előző verziója lesznek távolítva, és váltotta fel az új verzióra. 1\. lépés elvégzése előtt hajtsa végre a Jenkinsfile a következő módosítást:

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

1. Győződjön meg arról, a módosítás `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java`, majd hozza létre a lekéréses kérelmet. Példa:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Jelentkezzen be a Jenkins, és válassza ki a folyamat nevét, és válassza **hozhat létre most**. 

    Emellett beállíthat egy *webhook* aktiválják automatikusan a Jenkins-folyamat. Amikor egy lekéréses kérelmet is meg kell adni, GitHub problémák az hozzászólás Jenkins, a folyamat elindítása. Webhook beállításával kapcsolatos további információkért lásd: [Jenkins csatlakozás a Githubhoz](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github).

3. Hasonlítsa össze az aktuális megosztott verzióra:

    1. Nyissa meg a böngészőt, és keresse meg a megosztott változatot `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. TEST_ENDPOINT az URL-címet tartalmaz.

    2. Nyisson meg egy másik lapra, és adja meg a lekéréses kérelem fejlesztési terület URL-CÍMÉT. Hasonló lesz `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. A hivatkozás találja **hozhat létre Előzmények >< build # >> konzolkimenet** a Jenkins-feladat. Az oldal a Keresés `aksapp`, vagy csak az előtagját, keressen `azdsprefix`.

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>Hozhat létre, a gyermek fejlesztési terület URL-címe

Szövegfájl lekéréses kérelmek, a Jenkins létrehoz egy gyermek fejlesztési tárolóhelyet a csapat megosztott fejlesztési hely alapján, és futtatja a kódot a lekéréses kérelem a gyermek fejlesztési terület. A gyermek fejlesztési területre az URL-cím formájában adható `http://$env.azdsprefix.<test_endpoint>`. 

**$env.azdsprefix** folyamat-végrehajtás során be az Azure fejlesztési tárolóhelyek beépülő modul által **devSpacesCreate**:

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

A `test_endpoint` a korábban telepített használatával webfrontend alkalmazás URL-cím `azds up`a [mintaalkalmazások üzembe az AKS-fürtöt, a 7. lépés](#test_endpoint). Az érték `$env.TEST_ENDPOINT` a folyamat konfigurációs van beállítva. 

A következő kódrészlet azt mutatja be, hogyan a gyermek fejlesztési terület URL-t a `smoketest` szakaszban. A kód ellenőrzi, hogy a gyermek fejlesztési terület TEST_ENDPOINT érhető el, és ha igen, letölti az üdvözlő szöveg stdout:

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

Ha befejezte a mintaalkalmazás használja, az erőforráscsoport törlésével az Azure erőforrások eltávolítása:

```bash
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan használható az Azure fejlesztési tárolóhelyek beépülő modul a Jenkins és az Azure Container Registry beépülő modul kód buildelése és üzembe helyezése fejlesztési adhatja.

Az erőforrások az alábbi lista további információkat biztosít az Azure fejlesztési tárolóhelyek, ACR feladatok és a CI/CD a jenkins-szel.

Az Azure fejlesztési tárolóhelyek:
* [Az Azure Dev Spaces működése és konfigurálása](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works)

ACR-tevékenységek:
* [Operációs rendszer és keretrendszer javításának automatizálása az ACR Tasksszal](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)
* [A kód véglegesítésére automatikus build](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)

CI/CD a Jenkins az Azure-ban:
* [A Jenkins folyamatos üzembe helyezés](https://docs.microsoft.com/azure/aks/jenkins-continuous-deployment)
