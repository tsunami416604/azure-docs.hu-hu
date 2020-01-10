---
title: Alkalmazások üzembe helyezése az Azure Spring Cloud-on a Jenkins és az Azure CLI használatával
description: Ismerje meg, hogyan használhatja az Azure CLI-t folyamatos integrációs és üzembe helyezési folyamatokban a Service-szolgáltatások Azure Spring Cloud Service-ben való üzembe helyezéséhez
ms.topic: tutorial
ms.date: 01/07/2020
ms.openlocfilehash: 67ad97bb762ed302ef52c404d47c5755ea4b245b
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75734977"
---
# <a name="tutorial-deploy-apps-to-azure-spring-cloud-using-jenkins-and-the-azure-cli"></a>Oktatóanyag: alkalmazások telepítése az Azure Spring Cloud-ba a Jenkins és az Azure CLI használatával

Az [Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-overview) egy teljes körűen felügyelt, beépített szolgáltatás-felderítési és-konfigurációs felügyelettel rendelkező Service-fejlesztés. A szolgáltatás megkönnyíti a Spring boot-alapú Service-alkalmazások üzembe helyezését az Azure-ban. Ez az oktatóanyag azt mutatja be, hogyan használható az Azure CLI a Jenkins-ben a folyamatos integráció és a kézbesítés (CI/CD) automatizálására az Azure Spring Cloud számára.

Ebben az oktatóanyagban a következő feladatokat hajtja végre:

> [!div class="checklist"]
> * Szolgáltatási példány kiépítése és Java Spring-alkalmazás elindítása
> * A Jenkins-kiszolgáló előkészítése
> * Az Azure CLI használata a Jenkins-folyamatokban a Service-alkalmazások felépítéséhez és üzembe helyezéséhez 

Ez az oktatóanyag az alapszintű Azure-szolgáltatások, az Azure Spring Cloud, a Jenkins- [folyamatok](https://jenkins.io/doc/book/pipeline/) , a beépülő modulok és a GitHub közbenső ismeretét feltételezi.

## <a name="prerequisites"></a>Előfeltételek

>[!Note]
> Az Azure Spring Cloud jelenleg nyilvános előzetes verzióként érhető el. A nyilvános előzetes ajánlatok lehetővé teszik, hogy az ügyfelek a hivatalos kiadásuk előtt új funkciókkal kísérletezzenek.  A nyilvános előzetes verzió funkcióit és szolgáltatásait nem éles használatra szánták.  Az előzetes verziókra vonatkozó támogatással kapcsolatos további információkért tekintse meg a [gyakori kérdéseket](https://azure.microsoft.com/support/faq/) , vagy a [support Request](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) .

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

* Egy GitHub-fiók. Ha nem rendelkezik GitHub-fiókkal, hozzon létre egy [ingyenes fiókot](https://github.com/) a Kezdés előtt.

* Egy Jenkins-főkiszolgáló. Ha még nem rendelkezik Jenkins-főkiszolgálóval [, az ebben](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template)a rövid útmutatóban ismertetett lépéseket követve telepítse a [Jenkins](https://aka.ms/jenkins-on-azure) az Azure-ban. A Jenkins-csomóponton/-ügynökön (például:) a következők szükségesek. kiszolgáló létrehozása):

    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3,0 vagy újabb](https://maven.apache.org/download.cgi)
    * [Azure CLI telepítve](/cli/azure/install-azure-cli?view=azure-cli-latest), 2.0.67 vagy újabb verzió

    >[!TIP]
    > Az Azure Marketplace [Microsoft Jenkins](https://aka.ms/jenkins-on-azure) megoldási sablonja alapértelmezés szerint tartalmazza a git, a JDK, az az CLI és az Azure plug-ius eszközöket.
    
* [Feliratkozás Azure-előfizetésre](https://azure.microsoft.com/free/)
 
## <a name="provision-a-service-instance-and-launch-a-java-spring-application"></a>Szolgáltatási példány kiépítése és Java Spring-alkalmazás elindítása

A [Piggy mérőszámokat](https://github.com/Azure-Samples/piggymetrics) a Microsoft szolgáltatásalkalmazás mintaként használjuk, és a rövid útmutatóban ismertetett lépéseket követve hajtsa végre a [Java Spring-alkalmazás elindítása az Azure CLI használatával](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) a szolgáltatási példány kiépítéséhez és az alkalmazások beállításához. Ha már elvégezte ugyanezt a folyamatot, ugorjon a következő szakaszra. Ellenkező esetben az alábbi Azure CLI-parancsokat tartalmazza. Tekintse meg a rövid útmutató [: Java Spring-alkalmazás elindítása az Azure CLI használatával](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) további háttér-információk eléréséhez.

A helyi gépnek meg kell felelnie a Jenkins Build-kiszolgálóval megegyező előfeltételeknek. Győződjön meg arról, hogy a következők telepítve vannak a Service-alkalmazások felépítéséhez és üzembe helyezéséhez:
    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3,0 vagy újabb](https://maven.apache.org/download.cgi)
    * [Azure CLI telepítve](/cli/azure/install-azure-cli?view=azure-cli-latest), 2.0.67 vagy újabb verzió

1. Az Azure Spring Cloud bővítmény telepítése:

    ```Azure CLI
        az extension add --name spring-cloud
    ```

2. Hozzon létre egy erőforráscsoportot, amely tartalmazza az Azure Spring Cloud Service-t:

    ```Azure CLI
        az group create --location eastus --name <resource group name>
    ```

3. Azure Spring Cloud-példány kiépítése:

    ```Azure CLI
        az spring-cloud create -n <service name> -g <resource group name>
    ```

4. A [Piggy-metrikák](https://github.com/Azure-Samples/piggymetrics) tárházát a saját GitHub-fiókjába. A helyi gépen a tárház klónozása `source-code`nevű könyvtárban:

    ```bash
        mkdir source-code
        git clone https://github.com/<your GitHub id>/piggymetrics
    ```

5. Állítsa be a konfigurációs kiszolgálót. Győződjön meg arról, hogy lecserélte a GitHub-azonosító &lt;&gt; a megfelelő értékre.

    ```Azure CLI
        az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/<your GitHub id>/piggymetrics --label config
    ```

6. Hozza létre a projektet:

    ```bash
        cd piggymetrics
        mvn clean package -D skipTests
    ```

7. Hozza létre a három szolgáltatást: **átjáró**, **Auth-szolgáltatás**és **szolgáltatásfiók**:

    ```Azure CLI
        az spring-cloud app create --n gateway -s <service name> -g <resource group name>
        az spring-cloud app create --n auth-service -s <service name> -g <resource group name>
        az spring-cloud app create --n account-service -s <service name> -g <resource group name>
    ```

8. Alkalmazások üzembe helyezése: 

    ```Azure CLI
        az spring-cloud app deploy -n gateway -s <service name> -g <resource group name> --jar-path ./gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service -s <service name> -g <resource group name> --jar-path ./account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service -s <service name> -g <resource group name> --jar-path ./auth-service/target/auth-service.jar
    ```

9. Nyilvános végpont kiosztása az átjáróhoz:

    ```Azure CLI
        az spring-cloud app update -n gateway -s <service name> -g <resource group name> --is-public true
    ```

10. Kérdezze le az átjáró alkalmazást az URL-cím lekéréséhez, így ellenőrizheti, hogy fut-e az alkalmazás.

    ```Azure CLI
    az spring-cloud app show --name gateway | grep url
    ```
    
    Navigáljon az előző parancs által megadott URL-címhez a PiggyMetrics alkalmazás futtatásához. 

## <a name="prepare-jenkins-server"></a>Jenkins-kiszolgáló előkészítése

Ebben a szakaszban előkészíti a Jenkins-kiszolgálót egy olyan Build futtatására, amely kiválóan használható a teszteléshez. A biztonsági következmények miatt azonban Azure-beli virtuálisgép- [ügynök](https://plugins.jenkins.io/azure-vm-agents) vagy [Azure Container Agent](https://plugins.jenkins.io/azure-container-agents) használatával kell felkészítenie egy ügynököt az Azure-ban a buildek futtatásához. További információkért lásd a Jenkins-cikket a [főkiszolgálón végzett összeállítás biztonsági vonatkozásairól](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

### <a name="install-plug-ins"></a>Beépülő modulok telepítése

1. Jelentkezzen be a Jenkins-kiszolgálóra. Válassza a **Jenkins kezelése > a beépülő modulok kezelése**lehetőséget.
2. A **rendelkezésre álló** lapon válassza ki a következő beépülő modulokat:
    * [GitHub-integráció](https://plugins.jenkins.io/github-pullrequest)
    * [Azure-beli hitelesítő adatok](https://plugins.jenkins.io/azure-credentials)

    Ha ezek a beépülő modulok nem jelennek meg a listában, tekintse meg a **telepített** lapot, és ellenőrizze, hogy telepítve vannak-e már.

3. A beépülő modulok telepítéséhez válassza a **Letöltés most lehetőséget, majd az újraindítás után telepítse a telepítést**.

4. A telepítés befejezéséhez indítsa újra a Jenkins-kiszolgálót.

### <a name="add-your-azure-service-principal-credential-in-jenkins-credential-store"></a>Az Azure-szolgáltatás egyszerű hitelesítő adatainak hozzáadása a Jenkins hitelesítőadat-tárolóban

1. Szüksége lesz egy Azure-szolgáltatásra az Azure-ban való üzembe helyezéshez. További információ: [egyszerű szolgáltatásnév létrehozása](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) című rész a Deploy to Azure app Service oktatóanyagban. A `az ad sp create-for-rbac` kimenete a következőképpen néz ki:

    ```
    {
        "appId": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "displayName": "xxxxxxxjenkinssp",
        "name": "http://xxxxxxxjenkinssp",
        "password": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "tenant": "xxxxxx--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. A Jenkins irányítópultján válassza a **Credentials** > **System** (Hitelesítő adatok, Rendszer) lehetőséget. Ezután válassza a **Global credentials (unrestricted)** (Globális hitelesítő adatok (korlátlan)) elemet.

3. Válassza a **hitelesítő adatok hozzáadása**lehetőséget. 

4. Válassza ki **Microsoft Azure egyszerű szolgáltatásnevet** .

5. Adja meg a következő értékeket: * előfizetés azonosítója: használja az Azure-előfizetés AZONOSÍTÓját * ügyfél-azonosító: `appId` * ügyfél titkának használata: használja a `password` * bérlői azonosítót: használja az `tenant` * Azure-környezetet: válasszon egy előre beállított értéket. Például használja az **Azure** -t az Azure globális * azonosítóhoz: beállítás **azure_service_principalként**. Ezt az azonosítót a cikk későbbi részében használjuk * Leírás: egy választható mező. Javasoljuk, hogy adjon meg egy értelmes értéket.

### <a name="install-maven-and-az-cli-spring-cloud-extension"></a>A Maven és az a CLI Spring-Cloud Extension telepítése

A mintavételi folyamat a Maven használatával épít és az az CLI-t használja a szolgáltatási példányba való üzembe helyezéshez. A Jenkins telepítésekor létrejön egy *Jenkins*nevű rendszergazdai fiók. Győződjön meg arról, hogy a felhasználó *Jenkins* jogosult a Spring-Cloud bővítmény futtatására.

1. Kapcsolódjon a Jenkins-főkiszolgálóhoz SSH-n keresztül. 

2. Maven telepítése

    ```bash
        sudo apt-get install maven 
    ```

3. Telepítse az Azure CLI-t. További információ: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Ha az Azure-beli [Jenkins-főkiszolgálót](https://aka.ms/jenkins-on-azure)használja, a rendszer alapértelmezés szerint telepíti az Azure CLI-t.

4. Váltson a `jenkins` felhasználóra:

    ```bash
        sudo su jenkins
    ```

5. A **Spring-Cloud** bővítmény hozzáadása:

    ```bash
        az extension add --name spring-cloud
    ```

## <a name="create-a-jenkinsfile"></a>Jenkinsfile létrehozása
1. A saját tárházában (https://github.com/&lt ; a GitHub-azonosító&gt; /piggymetrics) hozzon létre egy **Jenkinsfile** a gyökérkönyvtárban.

2. Frissítse a fájlt az alábbiak szerint. Győződjön meg arról, hogy lecserélte **\<erőforráscsoport neve >** és a **\<szolgáltatásnév >** értékét. Ha más értéket használ a Jenkins-ben, cserélje le a **azure_service_principalt** a megfelelő azonosítóra. 

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

1. A Jenkins-irányítópulton kattintson az **új elem**lehetőségre.

2. Adjon meg egy nevet, a *PiggyMetrics* a feladatokhoz, és válassza a **folyamat**lehetőséget. Kattintson az OK gombra.

3. Ezután kattintson a **Pipeline (Folyamat)** lapra.

4. A **Definition (Definíció)** értékeként válassza a **Pipeline script from SCM (Folyamatszkript SCM-ből)** lehetőséget.

5. Az **SCM** értékeként válassza a **Git** lehetőséget.

6. Adja meg az elágazó adattárhoz tartozó GitHub URL-címet: **https://github.com/&lt ; a GitHub-azonosító&gt; /piggymetrics.git**

7. Győződjön meg arról, hogy az **ág megadása (Black for "any")** * **/Azure**

8. **Parancsfájl elérési útjának** megtartása **Jenkinsfile**

7. Kattintson a **Mentés** gombra.

## <a name="validate-and-run-the-job"></a>A feladatok ellenőrzése és futtatása

A művelet futtatása előtt frissítse a bejelentkezés beviteli mezőjében szereplő szöveget a **bejelentkezési azonosító megadásához**.

1. A saját tárházában nyissa meg `index.html` a **/Gateway/src/Main/Resources/static/**

2. Keressen rá a "bejelentkezési azonosító megadása" kifejezésre, és frissítse a

    ```HTML
        <input class="frontforms" id="frontloginform" name="username" placeholder="enter login ID" type="text" autocomplete="off"/>
    ```

3. A módosítások véglegesítve

4. Futtassa manuálisan a feladatot a Jenkinsben. A Jenkins Irányítópultján kattintson a PiggyMetrics, majd a **Build**( *üzembe helyezés* ) lehetőségre.

A feladatok befejezése után navigáljon az **átjáró** alkalmazás nyilvános IP-címére, és ellenőrizze, hogy az alkalmazás frissítve lett-e. 

![Frissített Piggy-metrikák](media/tutorial-jenkins-deploy-cli-spring-cloud/piggymetrics.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat:

```bash
az group delete -y --no-wait -n <resource group name>
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan használhatja az Azure CLI-t a Jenkins-ben a folyamatos integráció és a kézbesítés (CI/CD) automatizálására az Azure Spring Cloud számára.

Ha többet szeretne megtudni az Azure Jenkins-szolgáltatóról, tekintse meg a Jenkins az Azure-webhelyen című témakört.

> [!div class="nextstepaction"]
> [A Jenkins az Azure-on](/azure/jenkins/)
