---
title: Alkalmazások üzembe helyezése az Azure Spring Cloudban a Jenkins és az Azure parancssori felületének használatával
description: Ismerje meg, hogyan használhatja az Azure CLI-t folyamatos integrációs és üzembe helyezési folyamatban a mikroszolgáltatások Azure Spring Cloud szolgáltatásba való üzembe helyezéséhez
ms.topic: tutorial
ms.date: 01/07/2020
ms.openlocfilehash: bc48eecc9e73b95fd9112d645135409c24369e10
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810221"
---
# <a name="tutorial-deploy-apps-to-azure-spring-cloud-using-jenkins-and-the-azure-cli"></a>Oktatóanyag: Alkalmazások üzembe helyezése az Azure Spring Cloud szolgáltatásba a Jenkins és az Azure CLI használatával

[Az Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-overview) egy teljes körűen felügyelt mikroszolgáltatás-fejlesztés beépített szolgáltatásfelderítéssel és konfigurációkezeléssel. A szolgáltatás megkönnyíti a tavaszi rendszerindítás-alapú mikroszolgáltatási alkalmazások üzembe helyezését az Azure-ba. Ez az oktatóanyag bemutatja, hogyan használhatja az Azure CLI-t a Jenkinsben az Azure Spring Cloud folyamatos integrációjának és kézbesítésének (CI/CD) automatizálásához.

Ebben az oktatóanyagban az alábbi feladatokat hajthatja végre:

> [!div class="checklist"]
> * Szolgáltatáspéldány kiépítése és Java Spring alkalmazás elindítása
> * A Jenkins-kiszolgáló előkészítése
> * Az Azure CLI használata a Jenkins-folyamatban a mikroszolgáltatási alkalmazások létrehozásához és üzembe helyezéséhez 

Ez az oktatóanyag az alapvető Azure-szolgáltatások, az Azure Spring Cloud, [a Jenkins-folyamatok](https://jenkins.io/doc/book/pipeline/) és beépülő modulok, valamint a GitHub köztes ismereteit feltételezi.

## <a name="prerequisites"></a>Előfeltételek

>[!Note]
> Az Azure Spring Cloud jelenleg nyilvános előzetes verzióként érhető el. A nyilvános előzetes verziólehetővé teszi az ügyfelek számára, hogy a hivatalos kiadás előtt kísérletezzenek az új funkciókkal.  A nyilvános előzetes verziójú funkciók és szolgáltatások nem éles környezetben való használatra valók.  Az előzetes verziók során nyújtott támogatással kapcsolatos további információkért tekintse át [a gyakori kérdéseket,](https://azure.microsoft.com/support/faq/) vagy nyújtson be [támogatási kérelmet](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) további információkért.

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* Egy GitHub-fiók. Ha nem rendelkezik GitHub-fiókkal, hozzon létre egy [ingyenes fiókot,](https://github.com/) mielőtt elkezdené.

* Egy Jenkins-főkiszolgáló. Ha még nem rendelkezik Jenkins-főkiszolgálóval, telepítse a [Jenkinst](https://aka.ms/jenkins-on-azure) az Azure-on az ebben a rövid útmutatóban leírt lépések [végrehajtásával.](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template) A jenkins-csomóponton/ügynökön a következők szükségesek (például. build szerver):

    * [Git](https://git-scm.com/)
    * [JDK, 8.](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3.0 vagy újabb](https://maven.apache.org/download.cgi)
    * [Azure CLI telepítve](/cli/azure/install-azure-cli?view=azure-cli-latest), 2.0.67-es vagy újabb verzió

    >[!TIP]
    > Az olyan eszközök, mint a Git, a JDK, az Az CLI és az Azure plug-ius alapértelmezés szerint az Azure Marketplace Microsoft Jenkins-megoldássablonban [Microsoft Jenkins](https://aka.ms/jenkins-on-azure) találhatók.
    
* [Regisztráció Azure-előfizetésre](https://azure.microsoft.com/free/)
 
## <a name="provision-a-service-instance-and-launch-a-java-spring-application"></a>Szolgáltatáspéldány kiépítése és Java Spring alkalmazás elindítása

A [Piggy metrikákat használjuk](https://github.com/Azure-Samples/piggymetrics) minta Microsoft-szolgáltatásalkalmazásként, és ugyanazokat a lépéseket követjük a [rövid útmutatóban: Indítsa el a Java Spring alkalmazást az Azure CLI használatával a](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) szolgáltatáspéldány kiépítése és az alkalmazások beállítása érdekében. Ha már átment ugyanazon a folyamaton, ugorhat a következő szakaszra. Ellenkező esetben az alábbi az Azure CLI-parancsok at tartalmazza. Tekintse meg [a rövid útmutató: Indítson el egy Java Spring alkalmazást az Azure CLI használatával](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) további háttér-információk beszerezéséhez.

A helyi számítógépnek meg kell felelnie a Jenkins buildkiszolgálóval azonos előfeltételnek. Győződjön meg arról, hogy a mikroszolgáltatási alkalmazások létrehozásához és telepítéséhez telepítve vannak a következők:
    * [Git](https://git-scm.com/)
    * [JDK, 8.](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3.0 vagy újabb](https://maven.apache.org/download.cgi)
    * [Azure CLI telepítve](/cli/azure/install-azure-cli?view=azure-cli-latest), 2.0.67-es vagy újabb verzió

1. Telepítse az Azure Spring Cloud bővítményt:

    ```Azure CLI
        az extension add --name spring-cloud
    ```

2. Hozzon létre egy erőforráscsoportot az Azure Spring Cloud szolgáltatás ának tárolására:

    ```Azure CLI
        az group create --location eastus --name <resource group name>
    ```

3. Az Azure Spring Cloud egy példányának kiépítése:

    ```Azure CLI
        az spring-cloud create -n <service name> -g <resource group name>
    ```

4. Elágazás a [Piggy metrikák](https://github.com/Azure-Samples/piggymetrics) tártára a saját GitHub-fiók. A helyi számítógépen klónozza a tártárat egy könyvtárban, amelynek neve: `source-code`

    ```bash
        mkdir source-code
        git clone https://github.com/<your GitHub id>/piggymetrics
    ```

5. Állítsa be a konfigurációs kiszolgálót. Győződjön meg &lt;arról, hogy&gt; lecseréli a GitHub-azonosítót a megfelelő értékre.

    ```Azure CLI
        az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/<your GitHub id>/piggymetrics --label config
    ```

6. A projekt megépítése:

    ```bash
        cd piggymetrics
        mvn clean package -D skipTests
    ```

7. A három mikroszolgáltatás létrehozása: **átjáró**, **hitelesítési szolgáltatás**és **fiókszolgáltatás:**

    ```Azure CLI
        az spring-cloud app create --n gateway -s <service name> -g <resource group name>
        az spring-cloud app create --n auth-service -s <service name> -g <resource group name>
        az spring-cloud app create --n account-service -s <service name> -g <resource group name>
    ```

8. Az alkalmazások telepítése: 

    ```Azure CLI
        az spring-cloud app deploy -n gateway -s <service name> -g <resource group name> --jar-path ./gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service -s <service name> -g <resource group name> --jar-path ./account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service -s <service name> -g <resource group name> --jar-path ./auth-service/target/auth-service.jar
    ```

9. Nyilvános végpont hozzárendelése az átjáróhoz:

    ```Azure CLI
        az spring-cloud app update -n gateway -s <service name> -g <resource group name> --is-public true
    ```

10. Az átjáróalkalmazás lekérdezésével lekell kérnünk az URL-címet, így ellenőrizheti, hogy fut-e az alkalmazás.

    ```Azure CLI
    az spring-cloud app show --name gateway | grep url
    ```
    
    Keresse meg az előző parancs által biztosított URL-címet a PiggyMetrics alkalmazás futtatásához. 

## <a name="prepare-jenkins-server"></a>Jenkins-kiszolgáló előkészítése

Ebben a szakaszban előkészíti a Jenkins-kiszolgálót egy build futtatására, amely tesztelésre alkalmas. Azonban a biztonsági beleöntés miatt egy [Azure virtuálisgép-ügynök](https://plugins.jenkins.io/azure-vm-agents) vagy az [Azure Container-ügynök](https://plugins.jenkins.io/azure-container-agents) segítségével egy ügynök az Azure-ban a buildek futtatásához. További információkért lásd a Jenkins-cikket a [főkiszolgálón végzett összeállítás biztonsági vonatkozásairól](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

### <a name="install-plug-ins"></a>Beépülő modulok telepítése

1. Jelentkezzen be a Jenkins-kiszolgálóra. Válassza **a Jenkins kezelése > a Beépülő modulok kezelése lehetőséget.**
2. Az **Elérhető** lapon válassza ki a következő beépülő modulokat:
    * [GitHub-integráció](https://plugins.jenkins.io/github-pullrequest)
    * [Azure-hitelesítő adatok](https://plugins.jenkins.io/azure-credentials)

    Ha ezek a beépülő modulok nem jelennek meg a listában, a **Telepített** lapon ellenőrizze, hogy már telepítve vannak-e.

3. A beépülő modulok telepítéséhez válassza a **Letöltés és az újraindítás után a telepítés lehetőséget.**

4. A telepítés befejezéséhez indítsa újra a Jenkins-kiszolgálót.

### <a name="add-your-azure-service-principal-credential-in-jenkins-credential-store"></a>Az Azure Service Principal hitelesítő adatainak hozzáadása a Jenkins-hitelesítő adatok tárolójában

1. Az Azure-ban üzembe helyezéshez egy Egyszerű Azure-szolgáltatásra van szükség. További információkért tekintse [meg](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) az egyszerű szolgáltatás létrehozása szakaszban az Azure App Service-oktatóanyag üzembe helyezése. A kimenet `az ad sp create-for-rbac` valahogy így néz ki:

    ```
    {
        "appId": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "displayName": "xxxxxxxjenkinssp",
        "name": "http://xxxxxxxjenkinssp",
        "password": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "tenant": "xxxxxx--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. A Jenkins irányítópulton válassza a **Hitelesítő adatok** > **rendszere lehetőséget.** Ezután válassza a **Global credentials (unrestricted)** (Globális hitelesítő adatok (korlátlan)) elemet.

3. Válassza **a Hitelesítő adatok hozzáadása lehetőséget.** 

4. Válassza ki a **Microsoft Azure egyszerű szolgáltatást.**

5. Adja meg a következő értékeket: * Előfizetés-azonosító: használja `appId` az Azure-előfizetés `password` azonosítóját * `tenant` Ügyfélazonosító: használja * Ügyféltitok: használata * Bérlőazonosító: használata * Azure-környezet: válasszon ki egy előre beállított értéket. Például használja az **Azure** for Azure Global * ID azonosítóját: **azure_service_principal.** Ezt az azonosítót a cikk egy későbbi lépésében használjuk * Leírás: egy választható mező. Itt azt javasoljuk, hogy adjon érdemi értéket.

### <a name="install-maven-and-az-cli-spring-cloud-extension"></a>Telepítse a Maven és az Az CLI tavaszi felhőbővítményt

A mintafolyamat a Maven-t használja a létrehozáshoz, az Az CLI pedig a szolgáltatáspéldányra való üzembe helyezéshez. A Jenkins telepítésekor létrehoz egy *jenkins*nevű rendszergazdai fiókot. Győződjön meg arról, hogy a felhasználó *jenkins* rendelkezik engedéllyel a tavaszi felhő bővítmény futtatásához.

1. Csatlakozzon a Jenkins-főkiszolgálóhoz SSH-n keresztül. 

2. A Maven telepítése

    ```bash
        sudo apt-get install maven 
    ```

3. Telepítse az Azure CLI-t. További információ: [Az Azure CLI telepítése.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Az Azure CLI alapértelmezés szerint telepítve van, ha a Jenkins Mastert használja [az Azure-ban.](https://aka.ms/jenkins-on-azure)

4. Váltás a `jenkins` felhasználóra:

    ```bash
        sudo su jenkins
    ```

5. Adja hozzá a **tavaszi felhő** bővítményt:

    ```bash
        az extension add --name spring-cloud
    ```

## <a name="create-a-jenkinsfile"></a>Jenkins-fájl létrehozása
1. A saját tárházbanhttps://github.com/&lt( a GitHub-azonosító&gt;/piggymetrics) hozzon létre egy **Jenkinsfile** fájlt a gyökérben.

2. Frissítse a fájlt az alábbiak szerint. Győződjön meg arról, hogy lecseréli az ** \<erőforráscsoport nevének**>és ** \<a szolgáltatásnév>** értékét. Cserélje le **azure_service_principal** a megfelelő azonosítóra, ha más értéket használ, amikor hozzáadja a hitelesítő adatokat a Jenkinsben. 

```groovy
    node {
      stage('init') {
        checkout scm
      }
      stage('build') {
        sh 'mvn clean package'
      }
      stage('deploy') {
        withCredentials([azureServicePrincipal('azure_service_principal')]) {
          // login to Azure
          sh '''
            az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
            az account set -s $AZURE_SUBSCRIPTION_ID
          '''  
          // Set default resource group name and service name. Replace <resource group name> and <service name> with the right values
          sh 'az configure --defaults group=<resource group name>'
          sh 'az configure --defaults spring-cloud=<service name>'
          // Deploy applications
          sh 'az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar'
          sh 'az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar'
          sh 'az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar'
          sh 'az logout'
        }
      }
    }
```

3. Mentse és véglegesítse a módosítást.

## <a name="create-the-job"></a>A feladat létrehozása

1. A Jenkins irányítópulton kattintson az **Új elem gombra.**

2. Adjon meg egy nevet, *deploy-PiggyMetrics* a feladathoz, és válassza **a Folyamat lehetőséget.** Kattintson az OK gombra.

3. Ezután kattintson a **Pipeline (Folyamat)** lapra.

4. A **Definition (Definíció)** értékeként válassza a **Pipeline script from SCM (Folyamatszkript SCM-ből)** lehetőséget.

5. Az **SCM** értékeként válassza a **Git** lehetőséget.

6. Adja meg a Forkforált tárterület GitHub-URL-címét: ** https://github.com/&lt;A GitHub-azonosító&gt;/piggymetrics.git**

7. Győződjön meg arról, hogy **a Branch Specifier (fekete az "any")-hoz** ***/Azure**

8. **Parancsfájl elérési útjának megtartása** **Jenkinsfile néven**

7. Kattintson a **Mentés gombra**

## <a name="validate-and-run-the-job"></a>A feladat ellenőrzése és futtatása

A feladat futtatása előtt frissítsük a bejelentkezési beviteli mezőben lévő szöveget a **bejelentkezési azonosító megadásához.**

1. A saját tárházában `index.html` nyissa meg a **/gateway/src/main/resources/static/**

2. Keressen rá a "Adja meg a bejelentkezést" kifejezésre, és frissítse a "bejelentkezési azonosító megadása" kifejezésre

    ```HTML
        <input class="frontforms" id="frontloginform" name="username" placeholder="enter login ID" type="text" autocomplete="off"/>
    ```

3. A módosítások véglegesítése

4. Futtassa a feladatot manuálisan a Jenkinsben. A Jenkins irányítópulton kattintson a *Deploy-PiggyMetrics* feladatra, majd **a Build Now parancsra.**

A feladat befejezése után keresse meg az **átjáróalkalmazás** nyilvános IP-címét, és ellenőrizze, hogy az alkalmazás frissült-e. 

![Frissítve Malacka metrikák](media/tutorial-jenkins-deploy-cli-spring-cloud/piggymetrics.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat:

```bash
az group delete -y --no-wait -n <resource group name>
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan használhatja az Azure CLI-t a Jenkinsben a folyamatos integráció és kézbesítés (CI/CD) automatizálására az Azure Spring Cloud számára.

Ha többet szeretne megtudni az Azure Jenkins-szolgáltatóról, tekintse meg a Jenkins az Azure-webhelyen című témakört.

> [!div class="nextstepaction"]
> [A Jenkins az Azure-on](/azure/jenkins/)
