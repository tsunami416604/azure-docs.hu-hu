---
title: Üzembe helyezés az Azure Kubernetes Service-be (AKS) a Jenkins és a kék/zöld üzembehelyezési minta használatával
description: Útmutató az Azure Kubernetes Service-be (AKS) való üzembe helyezéshez a Jenkins és a kék/zöld üzembehelyezési minta használatával.
ms.service: jenkins
keywords: jenkins, azure, devops, kubernetes, k8s, aks, kék zöld üzembehelyezés, folyamatos kézbesítés, cd
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/11/2018
ms.openlocfilehash: 6cd3938844d7f6977c7b0912acffbfb1679dc42e
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49387384"
---
# <a name="deploy-to-azure-kubernetes-service-aks-by-using-jenkins-and-the-bluegreen-deployment-pattern"></a>Üzembe helyezés az Azure Kubernetes Service-be (AKS) a Jenkins és a kék/zöld üzembehelyezési minta használatával

Az Azure Kubernetes Service (AKS) felügyeli az üzemeltetett Kubernetes-környezetet, lehetővé téve a tárolóalapú alkalmazások gyors és egyszerű üzembe helyezését és felügyeletét. Nincs szükség tárolóvezénylési szakértelemre. Az AKS ezenkívül a folyamatban lévő üzemeltetés és karbantartás terhét is megszünteti az erőforrások igény szerinti kiépítésével, frissítésével és skálázásával. Ehhez nem szükséges offline állapotba helyezni az alkalmazásait. További információ az AKS-ről: [az AKS dokumentációja](/azure/aks/).

A kék/zöld üzembe helyezés egy Azure DevOps folyamatos kézbesítési minta, amelynek lényege, hogy működésben tart egy meglévő (kék) verziót, miközben üzembe helyez egy új (zöld) verziót. Ez a minta általában terheléselosztást alkalmaz, hogy a megnövekedett forgalmat a zöld üzemelő példányra irányítsa. Ha a monitorozás során valamilyen incidens történik, a forgalom átirányítható a kék üzemelő példányra, amely még fut. A folyamatos kézbesítéssel kapcsolatos további információkat a [folyamatos kézbesítést](/azure/devops/what-is-continuous-delivery) ismertető cikk tartalmaz.

Ebben az oktatóanyagban a következőket sajátíthatja el:

> [!div class="checklist"]
> * A kék/zöld üzembehelyezési minta megismerése
> * Felügyelt Kubernetes-fürt létrehozása
> * Mintaszkript futtatása Kubernetes-fürt konfigurálásához
> * Kubernetes-fürt manuális konfigurálása
> * Jenkins-feladat létrehozása és futtatása

## <a name="prerequisites"></a>Előfeltételek
- [GitHub-fiók](https://github.com): A mintaadattár klónozásához szüksége lesz egy GitHub-fiókra.
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest): A Kubernetes-fürt létrehozásához az Azure CLI 2.0-t használja.
- [Chocolatey](https://chocolatey.org): A kubectl telepítéséhez használt csomagkezelő.
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/): Egy parancssori felület, amelyen a Kubernetes-fürtök parancsait futtatja.
- [jq](https://stedolan.github.io/jq/download/): Egy egyszerű, parancssori JSON-feldolgozó.

## <a name="clone-the-sample-app-from-github"></a>Klónozza a mintaalkalmazást a GitHubról.

A GitHub Microsoft-adattárában lévő mintaalkalmazás bemutatja, hogyan helyezhető üzembe az AKS a Jenkins és a kék/zöld minta használatával. Ebben a szakaszban az adattár új elágaztatását fogja létrehozni a GitHubon, és klónozza az alkalmazást a helyi rendszerre.

1. A GitHub-adattárban keresse meg a [todo-app-java-on-azure](https://github.com/microsoft/todo-app-java-on-azure.git) mintaalkalmazást.

    ![Képernyőkép a Microsoft GitHub adattárban található mintaalkalmazásról](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. Az oldal jobb felső felén található **Fork** (Elágaztatás) paranccsal ágaztassa el az adattárat, és az utasításokat követve ágaztassa el a GitHub-fiókjához tartozó adattárat.

    ![Képernyőkép az elágaztatáshoz szükséges GitHub lehetőségről](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. Miután elágaztatta az adattárat, látni fogja, hogy a fiók neve megváltozik az Ön fióknevére, és egy üzenet jelzi, hogy az adattárat honnan ágaztatták el (Microsoft).

    ![Képernyőkép a GitHub-fióknevéről és az üzenetről](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. Válassza a **Clone or download** (Klónozás vagy letöltés) lehetőséget.

    ![Képernyőkép az adattár klónozását vagy letöltését végző GitHub lehetőségről](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. A **Clone with HTTPS** (Másolás HTTPS-sel) ablakban válassza a **másolás** ikont.

    ![Képernyőkép a klónozott URL-cím vágólapra történő másolását végző GitHub lehetőségről](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. Nyisson meg egy terminált vagy Git Bash ablakot.

1. A könyvtárat módosítsa arra a helyre, ahol az adattár helyi másolatát (klónját) tárolni szeretné.

1. A `git clone` paranccsal klónozza az előzőleg lemásolt URL-címet.

    ![Képernyőkép a Git Bash git klónozás parancsról](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. A klónozási folyamat elindításához nyomja le az Enter billentyűt.

    ![Képernyőkép a Git Bash git klónozás parancs eredményeiről](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. A könyvtárat módosítsa arra az újonnan létrehozott könyvtárra, amely az alkalmazásforrás klónját tartalmazza.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>Hozzon létre és konfiguráljon egy felügyelt Kubernetes-fürtöt

Ebben a szakaszban az alábbi lépéseket fogja végrehajtani:

- Az Azure CLI 2.0 használata egy felügyelt Kubernetes-fürt létrehozásához.
- Tudnivalók egy fürt beállításáról a beállítási szkripttel vagy manuálisan.
- Az Azure Container Registry szolgáltatás egy példányának létrehozása.

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Az Azure CLI 2.0-val hozzon létre egy felügyelt Kubernetes-fürtöt
Egy felügyelt Kubernetes-fürt létrehozásához az [Azure CLI 2.0-val](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) győződjön meg róla, hogy az Azure CLI 2.0.25-ös vagy újabb verzióját használja.

1. Jelentkezzen be Azure-fiókjába. A következő parancs beírása után megkapja az utasításokat, amelyek elmagyarázzák a bejelentkezés további lépéseit. 
    
    ```bash
    az login
    ```

1. Amikor lefuttatja az `az login` parancsot az előző lépésnél, megjelenik egy lista az összes Azure-előfizetéséről (az előfizetési azonosítókkal együtt). Ebben a lépésben beállítja az alapértelmezett Azure-előfizetést. A &lt;your-subscription-id> (Azure-előfizetés azonosítója) érték helyére írja be a kívánt Azure-előfizetés azonosítóját. 

    ```bash
    az account set -s <your-subscription-id>
    ```

1. Hozzon létre egy erőforráscsoportot. A &lt;your-resource-group-name> (erőforráscsoport neve) érték helyére írja be az új erőforráscsoport nevét, a &lt;your-location> (hely) érték helyére pedig a helyét. Az `az account list-locations` parancs megjeleníti az összes Azure-helyet. Az AKS előzetes verziója során nem érhető el minden hely. Ha olyan helyet ad meg, amely jelenleg nem érhető el, a hibaüzenetben megjelennek az elérhető helyek.

    ```bash
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. Hozza létre a Kubernetes-fürtöt. A &lt;your-resource-group-name> (erőforráscsoport neve) érték helyére írja be az előző lépésben létrehozott erőforráscsoport nevét, a &lt;your-kubernetes-cluster-name> (Kubernetes-fürt neve) érték helyére pedig az új fürt nevét. (A folyamat több percig is eltarthat.)

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>A Kubernetes-fürt beállítása

Egy kék/zöld üzembe helyezést beállíthat az AKS-ben manuálisan vagy a korábban klónozott mintában található beállítási szkripttel. Ebből a szakaszból mindkét módszert megismerheti.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>A Kubernetes-fürt beállítása a minta beállítási szkripttel
1. A **deploy/aks/setup/setup.sh** fájlt szerkesztve cserélje le a következő helyőrzőket a saját környezete megfelelő értékeivel: 

    - **&lt;your-resource-group-name>** (erőforráscsoport neve)
    - **&lt;your-kubernetes-cluster-name>** (Kubernetes-fürt neve)
    - **&lt;your-location>** (hely)
    - **&lt;your-dns-name-suffix>** (DNS-név utótagja)

    ![Képernyőkép a setup.sh szkriptről a Bashben, helyőrzők kiemelve](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

1. Futtassa a beállítási szkriptet.

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>Kubernetes-fürt beállítása manuálisan 
1. Töltse le a Kubernetes konfigurációt a profilmappájába.

    ```bash
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. A könyvtárat módosítsa a **deploy/aks/setup** könyvtárra. 

1. A nyilvános végpont és a két teszt végpont beállításához futtassa a következő **kubectl** parancsokat.

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. Frissítse a DNS-nevet a nyilvános és a teszt végpontoknál. Egy Kubernetes-fürt létrehozásakor egy [további erőforráscsoport](https://github.com/Azure/AKS/issues/3) is létrehoz, amelynek elnevezési mintája **MC_&lt;your-resource-group-name>_&lt;your-kubernetes-cluster-name>_&lt;your-location>** (MC_<erőforráscsoport-neve><kubernetes-fürt-neve><hely>).

    Keresse meg a nyilvános IP-címeket az erőforráscsoportban.

    ![Képernyőkép a nyilvános IP-címekről az erőforráscsoportban](./media/jenkins-aks-blue-green-deployment/publicip.png)

    Minden szolgáltatáshoz keresse meg a külső IP-címet a következő parancs lefuttatásával:
    
    ```bash
    kubectl get service todoapp-service
    ```
    
    Az alábbi paranccsal frissítse a DNS-nevet a kapcsolódó IP-címnél:

    ```bash
    az network public-ip update --dns-name aks-todoapp --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Ismételje meg a `todoapp-test-blue` és a `todoapp-test-green` meghívását:

    ```bash
    az network public-ip update --dns-name todoapp-blue --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>

    az network public-ip update --dns-name todoapp-green --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    A DNS-névnek egyedinek kell lennie az előfizetésben. Az egyediség garantálásához használhatja a `<your-dns-name-suffix>` helyőrzőt.

### <a name="create-an-instance-of-container-registry"></a>Container Registry-példány létrehozása

1. Egy Container Registry-példány létrehozásához futtassa az `az acr create` parancsot. A következő szakaszban használhatja a `login server` beállítást a Docker regisztrációs adatbázis URL-címeként.

    ```bash
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. Futtassa az `az acr credential` parancsot a Container Registry hitelesítő adatainak megjelenítéséhez. Jegyezze meg a Docker regisztrációs adatbázishoz tartozó felhasználónevét és jelszavát, mert a következő szakaszban szükség lesz rájuk.

    ```bash
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>A Jenkins-kiszolgáló előkészítése

Ebben a szakaszban előkészíthet egy Jenkins-kiszolgálót egy összeállítás futtatására, amely alkalmas a tesztelésre. Azonban az összeállítások futtatására alkalmas ügynök indításához az Azure-ban használjon egy [Azure virtuálisgép-ügynököt](https://plugins.jenkins.io/azure-vm-agents) vagy egy [Azure-tárolóügynököt](https://plugins.jenkins.io/azure-container-agents). További információkért lásd a Jenkins-cikket a [főkiszolgálón végzett összeállítás biztonsági vonatkozásairól](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

1. Helyezzen üzembe egy [Jenkins-főkiszolgálót az Azure-on](https://aka.ms/jenkins-on-azure).

1. Kapcsolódjon a kiszolgálóhoz SSH-n keresztül, és telepítse az összeállítási eszközöket arra a kiszolgálóra, ahol az összeállítást futtatja.
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Telepítse a Dockert](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce). Győződjön meg arról, hogy a `jenkins` felhasználó rendelkezik engedéllyel a `docker` parancsok futtatására.

1. [Telepítse a kubectl-t](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

1. [Töltse le a jq-t](https://stedolan.github.io/jq/download/).

1. Telepítse a jq-t a következő paranccsal:

   ```bash
   sudo apt-get install jq
   ```
   
1. Telepítse a Jenkinsen a beépülő modulokat a Jenkins-irányítópulton végrehajtott következő lépésekkel:

    1. Válassza a **Manage Jenkins (Jenkins kezelése)> Manage Plugins (Beépülő modulok kezelése) > Available (Elérhető)** lehetőséget.
    1. Keresse meg és telepítse az Azure Container Service beépülő modult.

1. Az Azure-beli erőforrások kezeléséhez adja meg a hitelesítő adatokat. Ha még nincs telepítve a beépülő modul, telepítse az **Azure-beli hitelesítő adatok** beépülő modulját.

1. Adja meg az Azure szolgáltatásnévhez tartozó hitelesítő adatot **Microsoft Azure Service Principal** (Microsoft Azure szolgáltatásnév) típusként.

1. Adja meg az Azure Docker regisztrációs adatbázishoz tartozó felhasználónevét és jelszavát (amelyet ebben a szakaszban a „Container Registry-példány létrehozása” részben szerzett be) **Username with password** (Felhasználónév jelszóval) típusként.

## <a name="edit-the-jenkinsfile"></a>A Jenkinsfile szerkesztése

1. A saját adattárában lépjen a `/deploy/aks/` helyre, és nyissa meg a következőt: `Jenkinsfile`.

2. A következőképpen módosítsa a fájlt:

    ```groovy
    def servicePrincipalId = '<your-service-principal>'
    def resourceGroup = '<your-resource-group-name>'
    def aks = '<your-kubernetes-cluster-name>'

    def cosmosResourceGroup = '<your-cosmodb-resource-group>'
    def cosmosDbName = '<your-cosmodb-name>'
    def dbName = '<your-dbname>'

    def dockerRegistry = '<your-acr-name>.azurecr.io'
    ```
    
    Módosítsa a Container Registry hitelesítési azonosítóját:
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>A feladat létrehozása
1. Adjon hozzá egy új, **Pipeline** (Folyamat) típusú feladatot.

1. Válassza a **Pipeline (Folyamat)** > **Definition (Definíció)** > **Pipeline script from SCM (Folyamatszkript SCM-ből)** lehetőséget.

1. Adja meg az SCM-adattár URL-címét a saját &lt;your-forked-repo> értékével.

1. A szkript elérési útját a következőképpen adja meg: `deploy/aks/Jenkinsfile`.

## <a name="run-the-job"></a>A feladat futtatása

1. Ellenőrizze, hogy a projekt sikeresen fut-e a helyi környezetben. Ezzel kapcsolatos tudnivalókat [a projekt helyi gépen való futtatását](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it) ismertető cikkben olvashat.

1. Futtassa a Jenkins-feladatot. Amikor első alkalommal futtatja a feladatot, a Jenkins üzembe helyezi a todo alkalmazást a kék környezetben, amely alapértelmezés szerint az inaktív környezet. 

1. Annak ellenőrzéséhez, hogy a feladat futtatása sikeres volt-e, nyissa meg a következő URL-címeket:
    - Nyilvános végpont: `http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Kék végpont: `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Zöld végpont: `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

A nyilvános és a kék teszt végpontok ugyanazzal a frissítéssel rendelkeznek, míg a zöld végpont az alapértelmezett tomcat rendszerképet mutatja. 

Ha többször futtatja le a buildet, váltakozva használja a kék és zöld üzemelő példányt. Vagyis ha az aktuális környezet a kék, a feladat a zöld környezetben lesz üzembe helyezve és tesztelve. Ezután ha a tesztek jól sikerültek, a feladat frissíti az alkalmazás nyilvános végpontját, hogy a forgalmat a zöld környezetbe irányítsa.

## <a name="additional-information"></a>További információ

Az állásidő nélküli üzembe helyezésről további információkat ebben a [rövid útmutató sablonban](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment) találhat. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az ezen oktatóanyagban létrehozott erőforrásokra, törölheti őket.

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a Jenkins beépülő modulok használata során bármilyen hibát tapasztal, jelentse be a problémát az adott összetevő [Jenkins JIRA](https://issues.jenkins-ci.org/) felületén.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezhet üzembe helyezést az AKS-re a Jenkins és a kék/zöld üzembehelyezési minta használatával. További információt az Azure Jenkins szolgáltatóról a Jenkins az Azure-on című cikkben talál.

> [!div class="nextstepaction"]
> [A Jenkins az Azure-on](/azure/jenkins/)