---
title: Az Azure Kubernetes Service (AKS) üzembe helyezése a Jenkins és a kék és zöld üzembe helyezés minta használatával
description: Ismerje meg, hogyan helyezhet üzembe az Azure Kubernetes Service (AKS) használatával a Jenkins és a kék és zöld üzembe helyezés minta.
services: app-service\web
documentationcenter: ''
author: tomarcher
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 07/23/2018
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 384681ae0ba212b485022ac81743528f96075ec8
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39716458"
---
# <a name="deploy-to-azure-kubernetes-service-aks-by-using-jenkins-and-the-bluegreen-deployment-pattern"></a>Az Azure Kubernetes Service (AKS) üzembe helyezése a Jenkins és a kék és zöld üzembe helyezés minta használatával

Az Azure Kubernetes Service (AKS) felügyeli az üzemeltetett Kubernetes környezetet, lehetővé téve a gyors és egyszerű üzembe helyezését és kezelhet tárolóalapú alkalmazásokat. Tárolóvezénylés szakismeretek nem szükséges. Az AKS használata esetén nem a folyamatos üzemeltetési és karbantartási, kiépítés, frissítése és igény szerinti méretezés erőforrások járó terheket. Az alkalmazások kapcsolat nélküli üzemmódra nincs szükségünk. Aks-sel kapcsolatos további információkért lásd: a [AKS dokumentációját](/azure/aks/).

Kék és zöld üzembe helyezés egy folyamatos Készregyártás az Azure-fejlesztési és üzemeltetési minta, amely gondoskodik a meglévő (kék) verziója élő, míg egy újat (zöld) üzembe helyezése támaszkodik. Általában ezt a mintát alkalmaz a terheléselosztási közvetlen egyre növekvő mennyiségű forgalmat a zöld üzembe helyezés. Incidens figyelési deríti fel, ha a forgalom visszairányítható a kék központi telepítését, amelyek továbbra is fut a. A folyamatos teljesítés kapcsolatos további információkért lásd: [Mi a folyamatos teljesítés](/azure/devops/what-is-continuous-delivery).

Ebben az oktatóanyagban elsajátíthatja, hogyan hajthat végre a következő feladatokat:

> [!div class="checklist"]
> * További információ a kék és zöld üzembe helyezés minta
> * Felügyelt Kubernetes-fürt létrehozása
> * Konfigurálja a Kubernetes-fürt minta parancsfájl futtatása
> * Kubernetes-fürtök manuális konfigurálása
> * Hozzon létre, és a egy Jenkins-feladat futtatása

## <a name="prerequisites"></a>Előfeltételek
- [GitHub-fiók](https://github.com) : a minta tárház klónozásához egy GitHub-fiókra van szükség.
- [Az Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) : a Kubernetes-fürt létrehozása az Azure CLI 2.0-t használja.
- [Chocolatey](https://chocolatey.org): telepíti a kubectl Csomagkezelő.
- [a kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/): olyan parancssori felületet használhat parancsok futtatása a Kubernetes-fürtök ellen.
- [jq](https://stedolan.github.io/jq/download/): egy könnyen használható, a parancssori JSON feldolgozó.

## <a name="clone-the-sample-app-from-github"></a>Klónozza a mintaalkalmazást a Githubról

A Microsoft-adattárat a Githubon, az annak egy mintaalkalmazást, amely bemutatja, hogyan helyezhet üzembe az aks-ben a Jenkins és a kék és zöld minta használatával. Ebben a szakaszban létrehoz egy elágazást, adattár, a github, és az alkalmazás klónozása a helyi rendszerről.

1. A GitHub-adattárat a tallózással keresse meg a [teendőlista-alkalmazás – java-az-Azure-beli](https://github.com/microsoft/todo-app-java-on-azure.git) mintaalkalmazást.

    ![Képernyőkép a Microsoft GitHub-adattárat a mintaalkalmazás](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. A tárház elágaztatását kiválasztásával **elágazás** , az oldal jobb felső és kövesse az utasításokat, hogy elágaztassa a példatárt a GitHub-fiókjában.

    ![Képernyőfelvétel: a GitHub beállítás elágazásba](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. Miután elágaztassa a példatárt, láthatja, hogy a fiók nevét, a fiók neve megváltozik, és a egy Megjegyzés: azt jelzi, ahol a tárház elágaztatott volt (Microsoft).

    ![Képernyőkép a GitHub-fiók neve, és jegyezze fel](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. Válassza ki **Klónozás vagy letöltés**.

    ![Képernyőfelvétel: a GitHub beállítással Klónozás vagy letöltés adattár](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. Az a **Klónozás a HTTPS** ablakban válassza ki a **másolási** ikonra.

    ![Képernyőfelvétel: a GitHub lehetőséget a clone URL-cím másolása a vágólapra](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. Nyisson meg egy terminált vagy a Git Bash-ablakot.

1. Módosítsa a könyvtárakat a kívánt helyre, ahol a tárház (Klónozás) helyi másolatot tárolni szeretné.

1. Használatával a `git clone` parancshoz, klónozza a korábban kimásolt URL-CÍMÉT.

    ![Képernyőfelvétel: a Git Bash git clone parancs](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. Nyomja le az Enter billentyűt a Klónozás megkezdéséhez.

    ![Képernyőfelvétel: a Git Bash git clone parancs eredménye](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. Módosítsa a könyvtárakat az újonnan létrehozott könyvtárba, amely tartalmazza az alkalmazás forrás klónja.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>Létrehozhat és konfigurálhat egy felügyelt Kubernetes-fürt

Ebben a szakaszban a következő lépéseket fogja végrehajtani:

- Az Azure CLI 2.0 használatával hozzon létre egy felügyelt Kubernetes-fürtöt.
- Ismerje meg, hogyan állítható be egy fürthöz, a telepítési parancsfájl használatával vagy manuálisan.
- Hozzon létre egy példányt az Azure Container Registry szolgáltatásba.

> [!NOTE]   
> Az AKS jelenleg előzetes verzióban érhető el. Információ az Azure-előfizetéséhez az előzetes verzió engedélyezése: [a rövid útmutató: Azure Kubernetes Service (AKS)-fürt üzembe helyezése](/azure/aks/kubernetes-walkthrough#enabling-aks-preview-for-your-azure-subscription).

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Felügyelt Kubernetes-fürt létrehozása az Azure CLI 2.0 használatával
Annak érdekében, hogy az egy felügyelt Kubernetes-fürt létrehozása a [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), győződjön meg arról, hogy használja az Azure CLI 2.0.25-ös vagy újabb.

1. Jelentkezzen be az Azure-fiókjával. Miután megadta a következő parancsot, kap, amely leírja, hogyan végezheti el a bejelentkezési utasításokat. 
    
    ```bash
    az login
    ```

1. Ha futtatja a `az login` parancsot az előző lépésben az összes Azure-előfizetések listája jelenik meg (együtt az előfizetés azonosítókat). Ebben a lépésben beállíthatja az alapértelmezett Azure-előfizetést. Cserélje le a &lt;your-subscription-id > helyőrzőt a kívánt Azure-előfizetés azonosítóját. 

    ```bash
    az account set -s <your-subscription-id>
    ```

1. Hozzon létre egy erőforráscsoportot. Cserélje le a &lt;az erőforrás-csoport-neve > helyőrzőt nevét az új erőforráscsoportot, és cserélje le a &lt;a hely > helyőrzőt helyére. A parancs `az account list-locations` jeleníti meg minden Azure-helyen. Az AKS előzetes verzióban nem minden hely érhető el. Ha megad egy helyet, amely nem érvényes jelenleg, a hibaüzenet felsorolja az elérhető helyek.

    ```bash
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. A Kubernetes-fürt létrehozása. Cserélje le a &lt;az erőforrás-csoport-neve > az előző lépésben létrehozott az erőforráscsoport nevét, és cserélje le a &lt;az kubernetes-fürt-neve-> az új fürt nevére. (A folyamat eltarthat néhány percig.)

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>A Kubernetes-fürt beállítása

Beállíthat egy kék vagy zöld üzembe helyezés az aks-ben manuálisan, vagy a telepítés pedig a minta megadott szkriptet klónozható közé tartoznak korábban. Ebben a szakaszban bemutatjuk, hogyan mindkét.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>A telepítő a példaszkript keresztül a Kubernetes-fürt beállítása
1. Szerkessze a **deploy/aks/setup/setup.sh** fájlt, és cserélje le a helyőrzőket a környezetének megfelelő értékekkel: 

    - **&lt;az erőforrás-csoport-neve >**
    - **&lt;az kubernetes-fürt-neve->**
    - **&lt;a hely >**
    - **&lt;a-dns-neve-utótag >**

    ![Képernyőkép setup.sh parancsfájlt a bash, több helyőrzőkkel kiemelésével](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

1. Futtassa a beállítási szkriptet.

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>Manuálisan állítsa be a Kubernetes-fürt 
1. Töltse le a Kubernetes konfigurációs profil mappájába.

    ```bash
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. Lépjen be a **üzembe helyezése/aks/telepítés** könyvtár. 

1. Futtassa a következő **kubectl** parancsok a szolgáltatások a nyilvános végponthoz, és a két teszt végpontok beállítása.

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. Frissítés a nyilvános DNS-nevét és a teszt végpontok. Amikor egy Kubernetes-fürtöt hoz létre, akkor is létrehozhat egy [további erőforráscsoport](https://github.com/Azure/AKS/issues/3), az elnevezési minta **MC_&lt;az erőforrás-csoport-neve >_ &lt; az kubernetes-fürt-neve->_&lt;a hely >**.

    Keresse meg a nyilvános IP-címek az erőforráscsoportban.

    ![Képernyőkép az erőforráscsoportban lévő nyilvános IP-címek](./media/jenkins-aks-blue-green-deployment/publicip.png)

    Minden egyes szolgáltatások esetében keresse meg a külső IP-cím a következő parancs futtatásával:
    
    ```bash
    kubectl get service todoapp-service
    ```
    
    Frissítse a megfelelő IP-cím DNS-nevét a következő paranccsal:

    ```bash
    az network public-ip update --dns-name aks-todoapp --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Ismételje meg a hívás a `todoapp-test-blue` és `todoapp-test-green`:

    ```bash
    az network public-ip update --dns-name todoapp-blue --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>

    az network public-ip update --dns-name todoapp-green --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    A DNS-nevének egyedinek kell lennie az előfizetésben. Az egyediség biztosítása érdekében használhatja `<your-dns-name-suffix>`.

### <a name="create-an-instance-of-container-registry"></a>A Container Registry-példány létrehozása

1. Futtassa a `az acr create` parancsot a Tárolóregisztrációs adatbázis-példány létrehozása. A következő szakaszban, majd használhatja `login server` , a Docker-beállításjegyzék URL-cím.

    ```bash
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. Futtassa a `az acr credential` parancsot a Tárolóregisztrációs adatbázis hitelesítő adatok megjelenítéséhez. Megjegyzés: a Docker registry felhasználónevet és jelszót, a következő szakaszban és igény szerint.

    ```bash
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>A Jenkins-kiszolgáló előkészítése

Ebben a szakaszban bemutatjuk, hogyan készítse elő a Jenkins-kiszolgáló egy build, amely nem okoz gondot az tesztelés futtatásához. Azonban, használjon egy [Azure Virtuálisgép-ügynök](https://plugins.jenkins.io/azure-vm-agents) vagy [Azure tároló-ügynök](https://plugins.jenkins.io/azure-container-agents) az Azure-ban való futtatásához a buildek ügynök üzembe helyezése. További információkért tekintse meg a Jenkins-cikk a [kialakításához a fő biztonsági vonatkozásai](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

1. Üzembe helyezése egy [az Azure-ban a Jenkins-főkiszolgáló](https://aka.ms/jenkins-on-azure).

1. Csatlakozás a kiszolgálóhoz, SSH-n keresztül, és a build tools telepítése a kiszolgálón, amelyen futtatja a build.
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Telepítheti a Dockert](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce). Ellenőrizze, hogy a felhasználó `jenkins` futtatásához engedéllyel rendelkezik a `docker` parancsokat.

1. [A kubectl telepítése](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

1. [Töltse le a jq](https://stedolan.github.io/jq/download/).

1. A következő paranccsal telepítse a jq:

   ```bash
   sudo apt-get install jq
   ```
   
1. A beépülő modulok telepítése a jenkins kifejezést a Jenkins irányítópulton a következő lépések végrehajtásával:

    1. Válassza ki **Jenkins kezelése > beépülő modulok kezelése > elérhető**.
    1. Keresse meg, és telepítse az Azure Container Service beépülő modul.

1. Adja hozzá a hitelesítő adatokat az Azure-erőforrások kezeléséhez. Ha még nem rendelkezik a beépülő modul, telepítse a **Azure Credential** beépülő modult.

1. Az Azure egyszerű szolgáltatás hitelesítő adatainak hozzáadása a típusaként **a Microsoft Azure egyszerű szolgáltatás**.

1. Adja hozzá a docker Azure-beli beállításjegyzék felhasználónevét és jelszavát (szerint az "A Tárolóregisztrációs adatbázis-példány létrehozása" szakaszban beszerzett) típusaként **felhasználónév, jelszó**.

## <a name="edit-the-jenkinsfile"></a>A Jenkinsfile szerkesztése

1. A saját tárházban nyissa meg `/deploy/aks/`, és nyissa meg a `Jenkinsfile`.

2. A fájl a következő frissítése:

    ```groovy
    def servicePrincipalId = '<your-service-principal>'
    def resourceGroup = '<your-resource-group-name>'
    def aks = '<your-kubernetes-cluster-name>'

    def cosmosResourceGroup = '<your-cosmodb-resource-group>'
    def cosmosDbName = '<your-cosmodb-name>'
    def dbName = '<your-dbname>'

    def dockerRegistry = '<your-acr-name>.azurecr.io'
    ```
    
    Frissítse a Container Registry hitelesítőadat-Azonosítóval:
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>A feladat létrehozása
1. Új feladat hozzáadása a típus **folyamat**.

1. Válassza ki **folyamat** > **definíció** > **SCM parancsfájlt folyamat**.

1. Az SCM adattár URL-CÍMÉT adja meg a &lt;az elágaztatott tárház >.

1. Adja meg a parancsfájl elérési útján, `deploy/aks/Jenkinsfile`.

## <a name="run-the-job"></a>A feladat futtatása

1. Győződjön meg arról, hogy futtatni lehessen a projekt sikeres a helyi környezetben. Íme, miként: [projekt futtatása helyi gépen](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it).

1. A Jenkins-feladat futtatása. A feladat első futtatásakor a Jenkins a kék környezetre, amely az alapértelmezett inaktív környezetben a teendőkezelő alkalmazást helyezünk üzembe. 

1. Annak ellenőrzéséhez, hogy a feladat futott-e, nyissa meg az URL-címek:
    - Nyilvános végpontja: `http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Kék végpont- `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Zöld végpont- `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

A nyilvános és a kék teszt végpontok rendelkezik ugyanazt a frissítést, a zöld-végpontot a tomcat alapértelmezett lemezkép szerepelnek. 

Ha a build csak egyszer futtatta, zöld és kék üzemelő példányok között léptet. Más szóval ha a jelenlegi környezet kék, a feladat helyez üzembe és a zöld környezet vizsgálatok. Ezt követően tesztek jók, ha a feladat frissíti az alkalmazás nyilvános végpontot irányíthatja a forgalmat a zöld környezetet.

## <a name="additional-information"></a>További információ

Az üzemszünet nélküli üzembe helyezés kapcsolatban bővebben lásd: Ez [gyorsindítási sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment). 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az ebben az oktatóanyagban létrehozott erőforrásokat, törölheti őket.

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a Jenkins beépülő modulok használata során bármilyen hibát tapasztal, jelentse be a problémát az adott összetevő [Jenkins JIRA](https://issues.jenkins-ci.org/) felületén.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan helyezhet üzembe az aks-ben a Jenkins és a kék és zöld üzembe helyezés minta használatával. Az Azure a Jenkins-szolgáltató kapcsolatos további információkért lásd: a Jenkins Azure webhelyen.

> [!div class="nextstepaction"]
> [Jenkins az Azure-ban](/azure/jenkins/)