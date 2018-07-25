---
title: Az Azure Kubernetes Service (AKS) használatával a Jenkins és a kék és zöld üzembe helyezés a minta üzembe helyezése
description: Ismerje meg, hogyan helyezhet üzembe az Azure Kubernetes Service (AKS) használatával a Jenkins és a kék és zöld üzembe helyezés minta
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
ms.openlocfilehash: 472622f78303593b7a4d5d5136aa47f34a1f44b1
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39228099"
---
# <a name="deploy-to-azure-kubernetes-service-aks-using-jenkins-and-bluegreen-deployment-pattern"></a>Az Azure Kubernetes Service (AKS) használatával a Jenkins és a kék és zöld üzembe helyezés a minta üzembe helyezése

Az Azure Kubernetes Service (AKS) felügyeli az üzemeltetett Kubernetes környezetet, lehetővé téve a tárolóalapú alkalmazások gyors és egyszerű üzembe helyezését és felügyeletét tárolóvezénylési szakértelem nélkül is. Az AKS használata esetén nem folyamatos üzemeltetési és karbantartási terhe üzembe helyezésével, frissítésével és méretezési erőforrásokkal igény szerint, az alkalmazásokat offline állapotba helyezése nélkül. Aks-sel kapcsolatos további információkért tekintse meg a [AKS dokumentációját](/azure/aks/).

Kék vagy zöld üzembe helyezés egy fejlesztési és üzemeltetési folyamatos kézbesítési (CD) minta, amely gondoskodik a meglévő (kék) verziója élő közben van (zöld) egy új támaszkodik. Általában ezt a mintát alkalmaz a terheléselosztási közvetlen egyre növekvő mennyiségű forgalmat a zöld üzembe helyezés. Incidens figyelési deríti fel, ha a forgalom visszairányítható a továbbra is fut a kék központi. A folyamatos teljesítés kapcsolatos további információkért lásd a cikket: [Mi a folyamatos teljesítés](/azure/devops/what-is-continuous-delivery).

Ebben az oktatóanyagban elsajátíthatja, hogy a Jenkins és a kék és zöld üzembe helyezés minta használatával az aks üzembe helyezéséhez a következő feladatokat:

> [!div class="checklist"]
> * További információ a kék és zöld üzembe helyezés minta
> * Hozzon létre egy felügyelt Kubernetes-fürtöt.
> * Konfigurálja a Kubernetes-fürt minta parancsfájl futtatása
> * Kubernetes-fürtök manuális konfigurálása
> * Hozzon létre, és a egy Jenkins-feladat futtatása

## <a name="prerequisites"></a>Előfeltételek
- [GitHub-fiók](https://github.com) : a minta tárház klónozásához egy GitHub-fiókra van szükség.
- [Az Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) : az Azure CLI 2.0-t a Kubernetes-fürt létrehozására szolgál.
- [Chocolatey](https://chocolatey.org) -kubectl telepítéséhez használt Csomagkezelő.
- [a kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) : parancssori felület parancsok futtatása a Kubernetes-fürtök ellen.
- [jq](https://stedolan.github.io/jq/download/) : egy könnyen használható parancssori JSON feldolgozó.

## <a name="clone-the-sample-app-from-github"></a>Klónozza a mintaalkalmazást a Githubról

A Microsoft-adattárat a Githubon be van kapcsolva egy mintaalkalmazást, amely bemutatja, hogyan helyezhet üzembe az aks-ben a Jenkins és a kék és zöld minta használatával. Ebben a szakaszban létrehoz egy elágazást, adattár, a github, és az alkalmazás klónozása a helyi rendszerről.

1. A GitHub-adattárat a tallózással keresse meg a [teendőlista-alkalmazás – java-az-Azure-beli](https://github.com/microsoft/todo-app-java-on-azure.git) mintaalkalmazást.

    ![A Microsoft GitHub-adattárat a mintaalkalmazást.](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. A tárház elágaztatását kiválasztásával **elágazás** , az oldal jobb felső és kövesse az utasításokat, hogy elágaztassa a példatárt a GitHub-fiókjában.

    ![Az elágazásában a mintaalkalmazást, ahhoz a GitHub-fiókhoz.](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. Miután elágaztassa a példatárt, láthatja, hogy a fiók nevét, a fiók neve megváltozik, és a egy Megjegyzés: azt jelzi, ahol a tárház elágaztatott volt (Microsoft).

    ![Folyamatban van egy másik GitHub-fiókhoz elágaztatott után mintaalkalmazást.](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. Kiválasztásával **Klónozás vagy letöltés**.

    ![GitHub gyors klónozza, vagy töltse le a tárház teszi lehetővé.](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. Az a **Klónozás a HTTPS** ablakban kattintson a másolási ikonra.

    ![A klón URL-cím másolása a vágólapra.](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. Nyisson meg egy terminált vagy a Bash-ablakot.

1. Módosítsa a könyvtárakat a kívánt helyre, ahol a tárház (Klónozás) helyi másolatot tárolni szeretné.

1. Használatával a `git clone` parancshoz, klónozza a korábban kimásolt URL-CÍMÉT.

    ![Írja be a "git-klón" és a klónozhatja a tárházat a clone URL-cím.](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. Válassza ki a &lt;Enter > billentyűt a Klónozás megkezdéséhez.

    ![A "git-klón" parancsot a tárházban, ahol tesztelheti személyes másolatát hozza létre.](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. Módosítsa a könyvtárakat az újonnan létrehozott könyvtárba, amely tartalmazza az alkalmazás forrás klónja.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>Létrehozhat és konfigurálhat egy felügyelt Kubernetes-fürt

Ebben a szakaszban a következő lépéseket fogja végrehajtani:

- Az Azure CLI 2.0 használatával hozzon létre egy felügyelt Kubernetes-fürtöt.
- Ismerje meg, hogyan állítható be a telepítési parancsfájl használatával vagy egy fürtöt, vagy manuálisan.
- Az Azure Container Registry létrehozása.

> [!NOTE]   
> Az AKS jelenleg előzetes verzióban érhető el. További információ a Azure-előfizetéséhez az előzetes engedélyezéséről. Lásd: [a rövid útmutató: Azure Kubernetes Service (AKS)-fürt üzembe helyezése ](/azure/aks/kubernetes-walkthrough#enabling-aks-preview-for-your-azure-subscription) további részletekért.

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Felügyelt Kubernetes-fürt létrehozása az Azure CLI 2.0 használatával
Annak érdekében, hogy hozzon létre egy felügyelt Kubernetes-fürtöt a [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest), győződjön meg arról, hogy használ-e az Azure CLI 2.0.25-ös vagy újabb.

1. Jelentkezzen be az Azure-fiókjával. Miután, írja be a következő `az login` utasítások itt találhatók, amelyek bemutatják, hogyan végezze el a bejelentkezési parancsot. 
    
    ```bash
    az login
    ```

1. Ha futtatja a `az login` parancs az előző lépésben, az összes Azure-előfizetések listáját jeleníti meg (együtt az előfizetés azonosítókat). Ebben a lépésben beállíthatja az alapértelmezett Azure-előfizetést. Cserélje le a &lt;your-subscription-id > helyőrzőt a kívánt Azure-előfizetés azonosítóját. 

    ```bash
    az account set -s <your-subscription-id>
    ```

1. Hozzon létre egy erőforráscsoportot. Cserélje le a &lt;az erőforrás-csoport-neve > helyőrzőt nevét az új erőforráscsoportot, és cserélje le a &lt;a hely > helyőrzőt helyére. A parancs `az account list-locations` jeleníti meg minden Azure-helyen. Az AKS előzetes verzióban nem minden hely érhető el. Ha megad egy helyet, amely nem érvényes jelenleg, a hibaüzenet felsorolja az elérhető helyek.

    ```bash
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. A Kubernetes-fürt létrehozása. Cserélje le a &lt;az erőforrás-csoport-neve > az előző lépésben létrehozott az erőforráscsoport nevét, és cserélje le a &lt;,-kubernetes-fürt-neve > az új fürt nevére. (A folyamat eltarthat néhány percig.)

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>A Kubernetes-fürt beállítása

Egy kék vagy zöld üzembe helyezés az aks-ben beállítása teheti meg a telepítési parancsfájl a minta klónozása korábban vagy manuálisan megadott. Ebben a szakaszban bemutatjuk, hogyan mindkét.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>A telepítő a példaszkript keresztül a Kubernetes-fürt beállítása
1. Szerkessze a **deploy/aks/setup/setup.sh** fájlt, és cserélje le a helyőrzőket a környezetének megfelelő értékekkel: 

    - **&lt;az erőforrás-csoport-neve >**
    - **&lt;az kubernetes-fürt-neve->**
    - **&lt;a hely >**
    - **&lt;a-dns-neve-utótag >**

    ![A setup.sh parancsfájl tartalmazza, amelyek esetében a környezet módosítható több helyőrzőt.](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

1. Futtassa a beállítási szkriptet.

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>Manuálisan állítsa be a Kubernetes-fürt 
1. Töltse le a Kubernetes konfigurációs profil mappájába.

    ```bash
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. Módosítsa a könyvtárat a **üzembe helyezése/aks/telepítés** könyvtár. 

1. Futtassa a következő **kubectl** állítsa be a nyilvános végponthoz és a két teszt végpontok a szolgáltatás-parancsokat.

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. Frissítés a nyilvános DNS-nevét és a teszt végpontok. Kubernetes-fürt létrehozásakor egy [további erőforráscsoport](https://github.com/Azure/AKS/issues/3) jön létre, az elnevezési patter **MC_&lt;az erőforrás-csoport-neve > _&lt; az kubernetes-fürt-neve->_&lt;a hely >**.

    Keresse meg a nyilvános IP-cím az erőforráscsoportban

    ![Az erőforráscsoportban lévő nyilvános IP-cím](./media/jenkins-aks-blue-green-deployment/publicip.png)

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

    A DNS-nevének egyedinek kell lennie az előfizetésben. `<your-dns-name-suffix>` Az egyediség biztosításához használható.

### <a name="create-azure-container-registry"></a>Azure Container Registry létrehozása

1. Futtassa a `az acr create` paranccsal hozzon létre egy Azure Container Registrybe. Az Azure Container Registry létrehozása után használja `login server` , a Docker registry URL-cím a következő szakaszban.

    ```bash
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. Futtassa a `az acr credential` parancsot az Azure Container Registry hitelesítő adatok megjelenítéséhez. Megjegyzés: a Docker registry felhasználónevet és jelszót használják a következő szakaszban.

    ```bash
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>A Jenkins-kiszolgáló előkészítése

Ebben a szakaszban bemutatjuk, hogyan készítse elő a Jenkins-kiszolgáló egy build, amely nem okoz gondot az tesztelés futtatásához. Azonban, amint azt a Jenkins-cikk a a [kialakításához a fő biztonsági vonatkozásai](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master), használata javasolt egy [Azure Virtuálisgép-ügynök](https://plugins.jenkins.io/azure-vm-agents) vagy [Azure Tárolóregisztrációs ügynök](https://plugins.jenkins.io/azure-container-agents) , az Azure-ban való futtatásához a buildek ügynök üzembe helyezése. 

1. Üzembe helyezése egy [az Azure-ban a Jenkins-főkiszolgáló](https://aka.ms/jenkins-on-azure).

1. Csatlakozás a kiszolgálóhoz, SSH-n keresztül, és a build tools telepítése a kiszolgálón, amelyen futtatja a build.
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Telepítheti a Dockert](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce). Győződjön meg, hogy a felhasználó `jenkins` futtatásához engedéllyel rendelkezik a `docker` parancsokat.

1. [A kubectl telepítése](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

1. [Töltse le a jq](https://stedolan.github.io/jq/download/).

1. A következő paranccsal telepítse a jq:

   ```bash
   sudo apt-get install jq
   ```
   
1. A beépülő modulok telepítése a jenkins kifejezést a Jenkins irányítópulton a következő lépések végrehajtásával:

    1. Válassza ki **Jenkins kezelése > beépülő modulok kezelése > elérhető**.
    1. Keresse meg, és telepítse az Azure Container Service beépülő modul.

1. Adja hozzá az Azure-erőforrások kezeléséhez használt hitelesítő adatokkal kell. Ha a beépülő modul még nem rendelkezik, telepítse a **Azure Credential** beépülő modult.

1. Adja hozzá az Azure egyszerű szolgáltatás hitelesítő adat típusa/típusú **a Microsoft Azure egyszerű szolgáltatás**.

1. Adja hozzá a Docker-beállításjegyzék Azure-felhasználónevét és jelszavát (amikor lekérte a szakaszban **Azure Container Registry létrehozása**), típus vagy egyedülálló **felhasználónév, jelszó**.

## <a name="edit-the-jenkinsfile"></a>A Jenkinsfile szerkesztése

1. A saját adattárat, navigáljon a `/deploy/aks/` és megnyitása `Jenkinsfile`

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
    
    És frissítse az ACR credential azonosítója:
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>A feladat létrehozása
1. Új feladat hozzáadása a típus **folyamat**.

1. Válassza ki **folyamat > definíció > SCM parancsfájlt folyamat**.

1. Az SCM adattár URL-címét adja meg a &lt;az elágaztatott tárház >

1. Adja meg a parancsfájl elérési útján, `deploy/aks/Jenkinsfile`.

## <a name="run-the-job"></a>A feladat futtatása

1. Győződjön meg arról, hogy futtatni lehessen a projekt sikeres a helyi környezetben. [Projekt futtatása helyi gépen](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it)

1. A Jenkins-feladat futtatása. A Jenkins-feladatot az első alkalommal fut, amikor a Jenkins a teendőlista alkalmazás a kék környezetre, amely az alapértelmezett inaktív környezetben telepíti. 

1. Annak ellenőrzéséhez, hogy a feladat futott, keresse meg az URL-címek:
    - Nyilvános végpontja: `http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Kék végpont- `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Zöld végpont- `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

A nyilvános és a kék teszt végpontok kell számolnia ugyanazt a frissítést, amíg a zöld-végpontot a tomcat alapértelmezett képet jeleníti meg. 

Ha a build csak egyszer futtatta, kék és zöld üzemelő példányok között léptet. Más szóval ha az aktuális környezet kék, a feladat fog üzembe helyezése tesztelési a zöld környezetet és frissítse az alkalmazás nyilvános végpontot irányíthatja a forgalmat a zöld környezetet, ha minden rendben, a tesztelés.

## <a name="additional-information"></a>További információ

További információ az üzemszünet nélküli üzembe helyezés, tekintse meg ezt [gyorsindítási sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment). 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az ebben az oktatóanyagban létrehozott Azure-erőforrások.

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>Hibaelhárítás

Ha bármely hibái a Jenkins beépülő modulok, a fájl be a problémát a [Jenkins JIRA](https://issues.jenkins-ci.org/) az adott összetevő.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan helyezhet üzembe az Azure Kubernetes Service (AKS) használatával a Jenkins és a kék és zöld üzembe helyezés mintát. Az Azure a Jenkins-szolgáltató kapcsolatos további információkért lásd: a Jenkins Azure webhelyen.

> [!div class="nextstepaction"]
> [Jenkins az Azure-ban](/azure/jenkins/)