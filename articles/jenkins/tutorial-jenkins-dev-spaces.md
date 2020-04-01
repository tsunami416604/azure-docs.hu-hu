---
title: Az Azure Dev Spaces beépülő modul használata a Jenkins számára az Azure Kubernetes szolgáltatással
description: Ismerje meg, hogyan használhatja az Azure Dev Spaces beépülő modult egy folyamatos integrációs folyamatban.
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 281565cec5ee947781ab8ee9f62a00e01f9ababb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79037023"
---
# <a name="tutorial-using-the-azure-dev-spaces-plug-in-for-jenkins-with-azure-kubernetes-service"></a>Oktatóanyag: Az Azure Dev Spaces beépülő modul használata a Jenkins és az Azure Kubernetes szolgáltatás számára 

Az Azure Dev Spaces lehetővé teszi, hogy tesztelje és iteratív módon fejlessze az Azure Kubernetes-szolgáltatásban (AKS) futó mikroszolgáltatás-alkalmazást anélkül, hogy replikálnia vagy szimulálnia kellene a függőségeket. Az Azure Dev Spaces beépülő modul a Jenkins számára segít a fejlesztői terek használatában a folyamatos integrációs és kézbesítési (CI/CD) folyamatban.

Ez az oktatóanyag az Azure Container Registry (ACR) szolgáltatást is használja. Az ACR tárolja a képeket, és egy ACR-feladat létrehozza a Docker és a Helm összetevőket. Az ACR és az ACR-feladat műtermék-generáláshoz való használata szükségtelent jelent további szoftverek, például a Docker telepítésének a Jenkins-kiszolgálón. 

Ebben az oktatóanyagban az alábbi feladatokat hajthatja végre:

> [!div class="checklist"]
> * Azure dev spaces-kompatibilis AKS-fürt létrehozása
> * Többszolgáltatásos alkalmazás telepítése az AKS-be
> * A Jenkins-kiszolgáló előkészítése
> * Az Azure Dev Spaces beépülő modul használata a Jenkins-folyamatban a kódmódosítások előnézetének megtekintéséhez a projektbe való egyesítés előtt

Ez az oktatóanyag az alapvető Azure-szolgáltatások, az AKS, az ACR, az Azure Dev Spaces, [a Jenkins-folyamatok](https://jenkins.io/doc/book/pipeline/) és a beépülő modulok köztes ismereteit feltételezi. Alapvető ismerete támogató eszközök, mint a kubectl és Helm hasznos.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

* Egy GitHub-fiók. Ha nem rendelkezik GitHub-fiókkal, hozzon létre egy [ingyenes fiókot,](https://github.com/) mielőtt elkezdené.

* [Visual Studio-kód](https://code.visualstudio.com/download) az [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) bővítmény telepítve.

* [Az Azure CLI telepítve](/cli/azure/install-azure-cli?view=azure-cli-latest)van, 2.0.43-as vagy újabb verzió.

* Egy Jenkins-főkiszolgáló. Ha még nem rendelkezik Jenkins-főkiszolgálóval, telepítse a [Jenkinst](https://aka.ms/jenkins-on-azure) az Azure-on az ebben a rövid útmutatóban leírt lépések [végrehajtásával.](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template) 

* A Jenkins-kiszolgálónak a Helm és a kubectl is telepítve kell lennie, és elérhetőnek kell lennie a Jenkins-fiók számára, amint azt az oktatóanyag későbbi részében ismertetik.

* VS-kód, a VS code terminál vagy WSL, és bash. 


## <a name="create-azure-resources"></a>Azure-erőforrások létrehozása

Ebben a szakaszban azure-erőforrásokat hoz létre:

* Az oktatóanyag összes Azure-erőforrását tartalmazó erőforráscsoport.
* Egy [Azure Kubernetes-szolgáltatás](https://docs.microsoft.com/azure/aks/) (AKS) fürt.
* Egy [Azure-tároló beállításjegyzék](https://docs.microsoft.com/azure/container-registry/) (ACR) létrehozásához (ACR-feladatok használatával) és Docker-rendszerképek tárolására.

1. Hozzon létre egy erőforráscsoportot.

    ```azurecli
    az group create --name MyResourceGroup --location westus2
    ```

2. Hozzon létre egy AKS-fürtöt. Hozza létre az AKS-fürtöt egy olyan [régióban, amely támogatja a fejlesztői tereket.](../dev-spaces/about.md#supported-regions-and-configurations)

    ```azurecli
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Konfigurálja az AKS-t a fejlesztői szóközök használatára.

    ```azurecli
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    Ez a lépés `azds` telepíti a CLI-bővítményt.

4. Hozzon létre egy tároló beállításjegyzéket.

    ```azurecli
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Mintaalkalmazások telepítése az AKS-fürtre

Ebben a szakaszban beállíthat egy fejlesztői területet, és üzembe helyez egy mintaalkalmazást az utolsó szakaszban létrehozott AKS-fürtre. Az alkalmazás két részből áll, *webfrontend* és *mywebapi*. Mindkét összetevő egy fejlesztői térben van telepítve. Az oktatóanyag későbbi részében lekéréses kérelmet nyújt be a mywebapi ellen a CI-folyamat jenkins-i aktiválásához.

Az Azure Dev Spaces használatával és a többszolgáltatásos fejlesztéssel kapcsolatos további információkért olvassa [el az Azure Dev Spaces használatának első lépéseit java val](https://docs.microsoft.com/azure/dev-spaces/get-started-java)és a [többszolgáltatásos fejlesztést az Azure Dev Spaces használatával című témakörben.](https://docs.microsoft.com/azure/dev-spaces/multi-service-java) Ezek az oktatóanyagok további háttér-információkat tartalmaznak, amelyek nem szerepelnek itt.

1. Töltse https://github.com/Azure/dev-spaces le a tárta a GitHubról.

2. Nyissa `samples/java/getting-started/webfrontend` meg a mappát a VS-kódban. (Figyelmen kívül hagyhat minden olyan alapértelmezett kérést, amely az objektumok hibakeresésére vagy a projekt visszaállítására vonatkozik.)

3. Frissítsen `/src/main/java/com/ms/sample/webfrontend/Application.java` úgy, hogy a következőkre hasonlítson:

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

4. Kattintson **a Nézet,** majd a **Terminál** parancsra az integrált terminál VS-kódban való megnyitásához.

5. Futtassa a `azds prep` parancsot, hogy előkészítse az alkalmazást egy fejlesztői térben való futtatásra. Az alkalmazás megfelelő `dev-spaces/samples/java/getting-started/webfrontend` előkészítéséhez a parancsból kell futtatni:

    ```bash
    azds prep --public
    ```

    A fejlesztői tereket `azds prep` CLI parancs a Docker és a Kubernetes eszközöket hozza létre az alapértelmezett beállításokkal. Ezek a fájlok a projekt teljes élettartama alatt megmaradnak, és testre szabhatók:

    * `./Dockerfile`és `./Dockerfile.develop` írja le az alkalmazás tárolólemezképét, valamint azt, hogy hogyan épül fel és hogyan fut a forráskód a tárolón belül.
    * A `./charts/webfrontend` alatt található [Helm-diagram](https://helm.sh/docs/topics/charts/) ismerteti a konténer Kubernetesben történő üzembe helyezését.
    * `./azds.yaml`az Azure Dev Spaces konfigurációs fájl.

    További információ: [Hogyan működik és van konfigurálva az Azure dev spaces.](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works)

6. Az alkalmazás létrehozása és futtatása `azds up` az AKS-ben a következő paranccsal:

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>A `up` parancs által létrehozott URL-címmel kapcsolatos információk a konzol kimenetén. Ez az alábbi formátumban lesz:

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Nyissa meg ezt az URL-címet egy böngészőablakban, és meg kell jelennie a webalkalmazásnak. Ahogy a tároló futni kezd, a rendszer `stdout` és `stderr` kimenetet streamel a terminálablakba.

8. Ezután állítsa be és telepítse *a mywebapi-t:*

    1. Könyvtár módosítása`dev-spaces/samples/java/getting-started/mywebapi`

    2. Futtassa a következőt:

        ```bash
        azds prep
        ```

    3. Futtassa a következőt:

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Jenkins-kiszolgáló előkészítése

Ebben a szakaszban előkészíti a Jenkins-kiszolgálót a minta CI-folyamat futtatására.

* Beépülő modulok telepítése
* Telepítse a Helm és a Kubernetes CLI-t
* Hitelesítő adatok hozzáadása

### <a name="install-plug-ins"></a>Beépülő modulok telepítése

1. Jelentkezzen be a Jenkins-kiszolgálóra. Válassza **a Jenkins kezelése > a Beépülő modulok kezelése lehetőséget.**
2. Az **Elérhető** lapon válassza ki a következő beépülő modulokat:
    * [Azure Dev Spaces](https://plugins.jenkins.io/azure-dev-spaces)
    * [Azure Container Registry Tasks](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Környezeti injektor](https://plugins.jenkins.io/envinject)
    * [GitHub-integráció](https://plugins.jenkins.io/github-pullrequest)

    Ha ezek a beépülő modulok nem jelennek meg a listában, a **Telepített** lapon ellenőrizze, hogy már telepítve vannak-e.

3. A beépülő modulok telepítéséhez válassza a **Letöltés és az újraindítás után a telepítés lehetőséget.**

4. A telepítés befejezéséhez indítsa újra a Jenkins-kiszolgálót.

### <a name="install-helm-and-kubectl"></a>Helm és kubectl telepítése

A mintafolyamat Helm és kubectl segítségével telepíti a fejlesztési területen. A Jenkins telepítésekor létrehoz egy *jenkins*nevű rendszergazdai fiókot. A Helm nek és a kubectlnek is elérhetőnek kell lennie a jenkins-felhasználó számára.

1. SSH-kapcsolatot létesítsen a Jenkins-főkiszolgálóval. 

2. Váltás a `jenkins` felhasználóra:
    ```bash
    sudo su jenkins
    ```

3. Telepítse a Helm CLI-t. További információ: [Telepítése Helm](https://helm.sh/docs/using_helm/#installing-helm).

4. Telepítse a kubectl. További információ: [**az acs kubernetes install-cli**](https://helm.sh/docs/using_helm/#installing-helm).

### <a name="add-credentials-to-jenkins"></a>Hitelesítő adatok hozzáadása a Jenkinshez

1. A Jenkinsnek egyszerű Azure-szolgáltatásra van szüksége az Azure-erőforrások hitelesítéséhez és eléréséhez. A szolgáltatásegyszerű létrehozása, tekintse meg a [szolgáltatás egyszerű](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) szolgáltatás létrehozása szakaszban az Azure App Service oktatóanyag üzembe helyezése. Ügyeljen arra, hogy mentse a `create-for-rbac` másolatot a kimenet, mert szükség van, hogy az információ a következő lépés végrehajtásához. A kimenet a következőhöz hasonlóan fog kinézni:

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Adjon hozzá egy *Microsoft Azure service Principal* credential típust a Jenkinsben az előző lépés egyszerű szolgáltatásadatainak használatával. Az alábbi képernyőképen szereplő nevek `create-for-rbac`megfelelnek a kimenetének.

    Az **azonosító** mező az egyszerű szolgáltatás Jenkins hitelesítő adatainak neve. A példa a `displayName` (ebben a `xxxxxxxjenkinssp`példában ) értékét használja, de tetszőleges szöveget használhat. Ez a hitelesítő adatok neve a következő szakaszban szereplő AZURE_CRED_ID környezeti változó értéke.

    ![Egyszerű szolgáltatáshitelesítő adatok hozzáadása a Jenkinshez](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    A **leírás** megadása nem kötelező. További részletes utasításokért tekintse meg az egyszerű szolgáltatás hozzáadása a [Jenkinshez](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) című témakört az Azure App Service-oktatóanyag üzembe helyezése című témakörben. 



3. Az ACR-hitelesítő adatok megjelenítéséhez futtassa a következő parancsot:

    ```azurecli
    az acr credential show -n <yourRegistryName>
    ```

    Készítsen másolatot a JSON kimenetről, amely a következővel fog kinézni:

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

4. Adjon hozzá egy *jelszótípusú felhasználónevet* a Jenkins ben. A **felhasználónév** az utolsó lépés felhasználóneve ebben `acr01`a példában. A **jelszó** az első jelszó értéke ebben `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`a példában. A hitelesítő adatok **azonosítója** a ACR_CRED_ID értéke.

5. Állítsa be az AKS hitelesítő adatait. Adjon hozzá egy *Kubernetes-konfigurációt (kubeconfig)* hitelesítő adattípust a Jenkins ben (használja az "Enter directly" lehetőséget). Az AKS-fürt hozzáférési hitelesítő adatainak lekérnie, futtassa a következő parancsot:

    ```azurecli
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   A hitelesítő adatok **azonosítója** a következő szakaszban KUBE_CONFIG_ID értéke.

## <a name="create-a-pipeline"></a>Folyamat létrehozása

A példafolyamathoz kiválasztott forgatókönyv valós mintán alapul: A lekéréses kérelem elindítja a CI-folyamatot, amely létrehozza, majd üzembe helyezi az Azure fejlesztési terület javasolt módosításait tesztelésre és felülvizsgálatra. A felülvizsgálat eredményétől függően a módosítások at egyesítve és az AKS-ben való üzembe helyezve vagy elvetve. Végül a fejlesztői terület törlődik.

A Jenkins-folyamat konfigurációja és a Jenkinsfile határozza meg a CI-folyamat szakaszait. Ez a folyamatábra a Jenkins-fájl által meghatározott folyamatszakaszokat és döntési pontokat jeleníti meg:

![Jenkins-folyamat folyamata](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. Töltse le a *mywebapi* projekt [https://github.com/azure-devops/mywebapi](https://github.com/azure-devops/mywebapi)módosított verzióját a alkalmazásból. Ez a projekt több, a folyamat létrehozásához szükséges fájlt tartalmaz, beleértve a *Jenkinsfile*, *Dockerfiles*és Helm diagramot.

2. Jelentkezzen be Jenkinsbe. A bal oldali menüben válassza az **Elem hozzáadása parancsot.**

3. Válassza **a Folyamat**lehetőséget, majd írjon be egy nevet az **Elemnév megadása** mezőbe. Válassza **az OK**gombot, majd a folyamat konfigurációs képernyője automatikusan megnyílik.

4. Az **Általános** lapon jelölje be **A környezet előkészítése a futtatáshoz**jelölőnégyzetet. 

5. Jelölje be **a Jenkins-környezet változóinak megtartása** és **a Jenkins buildváltozók megtartása jelölőnégyzetet.**

6. A **Tulajdonságok tartalma** mezőbe írja be a következő környezeti változókat:

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

    Az előző szakaszokban megadott mintaértékek használatával a környezeti változók listájának a következővel kell kinéznie:

    ![Jenkins-folyamat környezeti változói](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. Válassza **a Pipeline parancsfájlt az SCM >** Definition **területen.**
8. Az **SCM alkalmazásban**válassza a **Git** lehetőséget, majd adja meg a tártár URL-címét.
9. A **Branch Specifier**mezőbe írja be a értéket. `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`
10. Töltse ki az SCM-tárlaterény URL-címét és a "Jenkinsfile" parancsfájl elérési útját.
11. **A könnyű fizetést** ellenőrizni kell.

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>Lekéréses kérelem létrehozása a folyamat aktiválásához

Ebben a szakaszban a 3. Alapértelmezés szerint, ha úgy dönt, hogy egyesíti a PR, a korábbi megosztott változata mywebapi el kell távolítani, és helyébe az új verzió. Az 1.

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

1. Módosítsa a `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java`hívást, majd hozzon létre egy lekéréses kérelmet. Példa:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Jelentkezzen be a Jenkinsbe, és válassza ki a folyamat nevét, majd válassza a **Build Now**lehetőséget. 

    Beállíthat egy *webhookot* is, amely automatikusan elindítja a Jenkins-folyamatot. Lekéréses kérelem beírásakor a GitHub postai úton küldi el a folyamatot. A webhook beállításáról a [Jenkins csatlakoztatása a GitHubhoz című](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github)témakörben talál további információt.

3. Az aktuális megosztott verzió módosításaiösszehasonlítása:

    1. Nyissa meg a böngészőt, `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`és keresse meg a megosztott verziót. TEST_ENDPOINT tartalmazza az URL-t.

    2. Nyisson meg egy másik lapot, majd adja meg a PR-fejlesztési terület URL-címét. Ez hasonló lesz `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`a . A **buildelési előzmények > <a Jenkins-feladathoz a build#> > console output** című részben találja a hivatkozást. Keressen rá `aksapp`a lapon a ( mező) `azdsprefix`kifejezésre, vagy ha csak az előtagot szeretné látni, keresse meg a mezőt.

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>A gyermekfejlesztési terület URL-címének összeállítása

Amikor lekéréses kérelmet nyújt be, a Jenkins létrehoz egy gyermekfejlesztői területet a csapat megosztott fejlesztői tárhelye alapján, és futtatja a kódot a lekéréses kérelemből az adott gyermekfejlesztői térben. A gyermekfejlesztési terület URL-címe `http://$env.azdsprefix.<test_endpoint>`a . 

**$env.azdsprefix** van beállítva a folyamat végrehajtása során az Azure Dev Spaces beépülő modul **által devSpacesCreate:**

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

Az `test_endpoint` a korábban telepített `azds up`webfrontend alkalmazás URL-címe a [mintaalkalmazások központi telepítése az AKS-fürt7.](#test_endpoint) Az érték `$env.TEST_ENDPOINT` a csővezeték konfigurációjában van beállítva. 

A következő kódrészlet bemutatja, hogyan használja a `smoketest` gyermekfejlesztői terület URL-címét a fázisban. A kód ellenőrzi, hogy a gyermekfejlesztői terület rendelkezésre áll-e TEST_ENDPOINT, és ha igen, letölti az üdvözlőszöveget:

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

Ha befejezte a mintaalkalmazás használatát, törölje az Azure-erőforrásokat az erőforráscsoport törlésével:

```azurecli
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [CI/CD a Jenkinsszel az Azure-ban](jenkins-continuous-deployment.md)
