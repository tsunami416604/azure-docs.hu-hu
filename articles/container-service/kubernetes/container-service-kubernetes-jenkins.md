---
title: "Az Azure Tárolószolgáltatásban Kubernetes Jenkins CI/CD"
description: "Egy telepítésének és frissítésének a tárolóalapú alkalmazást az Azure Tárolószolgáltatásban Kubernetes Jenkins CI/CD folyamat automatizálása"
services: container-service
author: chzbrgr71
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/23/2017
ms.author: briar
ms.custom: mvc
ms.openlocfilehash: cd8f7ae584b6b1afd357cc585df28dedd3c1f70e
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="jenkins-integration-with-azure-container-service-and-kubernetes"></a>Az Azure Tárolószolgáltatás és Kubernetes Jenkins integráció 

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Az oktatóanyag azt ismerteti a folyamatot, az Azure-tároló szolgáltatás Kubernetes a Jenkins platform használatával több tároló alkalmazás folyamatos integráció beállítása. A munkafolyamat Docker Hub tároló kép frissíti, és frissíti a Kubernetes három munkaállomás-csoporttal egy központi telepítési bevezetés használatával. 

## <a name="high-level-process"></a>Magas szintű folyamata
Ebben a cikkben ismertetett lépéseit a következők: 
- A Tárolószolgáltatás Kubernetes fürt telepítése
- Állítson be Jenkins és Tárolószolgáltatás való hozzáférés konfigurálása
- Jenkins munkafolyamat létrehozása
- A CI/CD folyamat teljes körű tesztelése

## <a name="install-a-kubernetes-cluster"></a>Kubernetes fürt telepítése
    
Az Azure Tárolószolgáltatásban, az alábbi lépéseket követve a Kubernetes fürt központi telepítése. Teljes dokumentációját helyezkedik [Itt](container-service-kubernetes-walkthrough.md).

### <a name="step-1-create-a-resource-group"></a>1. lépés:, Hozzon létre egy erőforráscsoportot
```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus

az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="step-2-deploy-the-cluster"></a>2. lépés: A fürt központi telepítése
> [!NOTE]
> Az alábbi lépéseket a ~/.ssh mappájában tárolt, helyi SSH nyilvános kulcs szükséges.
>

```azurecli
RESOURCE_GROUP=my-resource-group
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name

az acs create \
--orchestrator-type=kubernetes \
--resource-group $RESOURCE_GROUP \
--name=$CLUSTER_NAME \
--dns-prefix=$DNS_PREFIX \
--ssh-key-value ~/.ssh/id_rsa.pub \
--admin-username=azureuser \
--master-count=1 \
--agent-count=5 \
--agent-vm-size=Standard_D1_v2
```

## <a name="set-up-jenkins-and-configure-access-to-container-service"></a>Állítson be Jenkins és Tárolószolgáltatás való hozzáférés konfigurálása

### <a name="step-1-install-jenkins"></a>1. lépés: Jenkins telepítése
1. Hozzon létre egy Azure virtuális gép Ubuntu 16.04 LTS.  Később a lépésben kell csatlakozni a virtuális gép bash használatával a helyi számítógépen, mert a "hitelesítési típus" a "Nyilvános SSH-kulcs", és illessze be a ~/.ssh mappában található helyileg tárolt nyilvános SSH-kulcs.  Továbbá jegyezze fel a "felhasználónév", amely, azóta a Jenkins irányítópult megtekintéséhez szükséges ezt a felhasználónevet és a későbbi lépésekben Jenkins VM való kapcsolódáshoz.
2. Ezek keresztül Jenkins telepítése [utasításokat](https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins+on+Ubuntu). Egy részletes oktatóanyag jelenleg [howtoforge.com](https://www.howtoforge.com/tutorial/how-to-install-jenkins-with-apache-on-ubuntu-16-04).
3. Jenkins irányítópultjának megjelenítése a helyi számítógépen, adja hozzá egy bejövő szabályt, amely lehetővé teszi a 8080-as port elérését engedélyezi a 8080-as porton az Azure hálózati biztonsági csoport frissítése.  Port továbbítás azt is megteheti, előfordulhat, hogy beállítása a parancs futtatásával:`ssh -i ~/.ssh/id_rsa -L 8080:localhost:8080 <your_jenkins_user>@<your_jenkins_public_ip`
4. Csatlakozás az Jenkins kiszolgálóhoz a böngészővel nyissa meg a nyilvános IP-cím (http:// < your_jenkins_public_ip >: 8080) és a kezdő adminisztrációs jelszóval először a Jenkins irányítópult feloldásához.  A rendszergazdai jelszó a virtuális Gépre Jenkins /var/lib/jenkins/secrets/initialAdminPassword tárolódik.  Ezt a jelszót egy egyszerű módja, hogy SSH-ból a Jenkins VM: `ssh <your_jenkins_user>@<your_jenkins_public_ip>`.  Ezt követően: `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.
5. Docker telepítse ezeket a Jenkins gép [utasításokat](https://docs.docker.com/cs-engine/1.13/#install-on-ubuntu-1404-lts-or-1604-lts). Ez lehetővé teszi a Docker parancsok Jenkins feladatok futtatásához.
6. Konfigurálja a Docker engedélyt, hogy Jenkins a Docker-végpont elérésére.

    ```bash
    sudo chmod 777 /run/docker.sock
    ```
8. Telepítés `kubectl` Jenkins a parancssori felület. További részletekért erővel [telepítése és beállítása a kubectl](https://kubernetes.io/docs/tasks/kubectl/install/).  Jenkins feladatok "kubectl" használatával kezelni és telepíteni a Kubernetes fürthöz.

    ```bash
    curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

    chmod +x ./kubectl

    sudo mv ./kubectl /usr/local/bin/kubectl
    ```

### <a name="step-2-set-up-access-to-the-kubernetes-cluster"></a>2. lépés: A Kubernetes fürthöz való hozzáférés beállítása

> [!NOTE]
> Nincsenek több módszer parancsokról, az alábbi lépéseket. A legegyszerűbb, módszert használja.
>

1. Másolás a `kubectl` konfigurációs fájlja a Jenkins géphez, hogy Jenkins feladatok van a Kubernetes fürt eléréséhez. Ezek az utasítások azt feltételezik, hogy használja a Jenkins VM mint egy másik gépről bash, és egy helyi nyilvános SSH-kulcs a gép ~/.ssh mappában van tárolva.

```bash
export KUBE_MASTER=<your_cluster_master_fqdn>
export JENKINS_USER=<your_jenkins_user>
export JENKINS_SERVER=<your_jenkins_public_ip>
sudo ssh $JENKINS_USER@$JENKINS_SERVER sudo mkdir -m 777 /home/$JENKINS_USER/.kube/ \
&& sudo ssh $JENKINS_USER@$JENKINS_SERVER sudo mkdir /var/lib/jenkins/.kube/ \
&& sudo scp -3 -i ~/.ssh/id_rsa azureuser@$KUBE_MASTER:.kube/config $JENKINS_USER@$JENKINS_SERVER:~/.kube/config \
&& sudo ssh -i ~/.ssh/id_rsa $JENKINS_USER@$JENKINS_SERVER sudo cp /home/$JENKINS_USER/.kube/config /var/lib/jenkins/.kube/config \
```
        
2. Ellenőrizheti a Jenkins, hogy a Kubernetes fürt érhető el.  Ehhez a SSH azokat a Jenkins VM: `ssh <your_jenkins_user>@<your_jenkins_public_ip>`.  Ezután a Jenkins sikeresen csatlakozott-e a fürt ellenőrzése: `kubectl cluster-info`.
    

## <a name="create-a-jenkins-workflow"></a>Jenkins munkafolyamat létrehozása

### <a name="prerequisites"></a>Előfeltételek

- A kód tárház GitHub-fiók.
- Docker Hub fiókot tárolja, és a lemezképek frissítéséhez.
- Indexelése alkalmazás újbóli létrehozása és frissítése. Ez a minta tároló alkalmazással Golang írt: https://github.com/chzbrgr71/go-web 

> [!NOTE]
> Az alábbi lépéseket kell végrehajtani a saját GitHub-fiók. Úgy, hogy a fenti tárház klónozása, de a webhookok konfigurálása egy saját fiókot kell használnia, és Jenkins eléréséhez.
>

### <a name="step-1-deploy-initial-v1-of-application"></a>1. lépés: Kezdeti v1 alkalmazás központi telepítése
1. A fejlesztői gépen az alábbi parancsokkal az alkalmazás elkészítésére. Cserélje le `myrepo` az Ön által.
    
    ```bash
    git clone https://github.com/chzbrgr71/go-web.git
    cd go-web
    docker build -t myrepo/go-web .
    ```

2. Docker Hub leküldéses lemezképet.

    ```bash
    docker login
    docker push myrepo/go-web
    ```

3. Telepítse a Kubernetes fürthöz.
    
    > [!NOTE] 
    > Szerkessze a `go-web.yaml` fájl frissítése a tároló kép és a tárházban.
    >
        
    ```bash
    kubectl create -f ./go-web.yaml --record
    ```
### <a name="step-2-configure-jenkins-system"></a>2. lépés: Jenkins rendszer konfigurálása
1. Kattintson a **Jenkins kezelése** > **rendszer konfigurálása**.
2. A **GitHub**, jelölje be **GitHub-kiszolgáló hozzáadása**.
3. Hagyja **API URL-címe** alapértelmezettként.
4. A **hitelesítő adatok**, vegye fel a Jenkins hitelesítő adatok használatával **titkos szöveg**. Azt javasoljuk, GitHub személyes hozzáférési jogkivonatok, amelyek a GitHub felhasználói fiók beállítások vannak konfigurálva. Ezzel kapcsolatban további részletek [itt.](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)
5. Kattintson a **tesztkapcsolat** annak érdekében, hogy ez megfelelően van-e konfigurálva.
6. A **globális tulajdonságok**, adjon hozzá egy környezeti változó `DOCKER_HUB` , és adja meg a Docker Hub jelszavát. (Ez akkor hasznos, ebben a bemutatóban szereplő, de egy éles telepítési forgatókönyvhöz egy biztonságosabb módszert igényel.)
7. Mentse.

![Jenkins GitHub-hozzáférés](./media/container-service-kubernetes-jenkins/jenkins-github-access.png)

### <a name="step-3-create-the-jenkins-workflow"></a>3. lépés: A Jenkins munkafolyamat létrehozása
1. Hozzon létre egy Jenkins elemet.
2. Adjon meg egy nevet (például "Ugrás-web"), majd **Freestyle projekt**. 
3. Ellenőrizze **GitHub-projekt** , és adja meg a GitHub-tárház URL-CÍMÉT.
4. A **forrás kód felügyeleti**, adja meg a GitHub-tárház URL-CÍMÉT és hitelesítő adatait. 
5. Adja hozzá a **összeállítása lépés** típusú **hajtható végre a rendszerhéj** és használja a következő szöveget:

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME .
    docker login -u <your-dockerhub-username> -p ${DOCKER_HUB}
    docker push $WEB_IMAGE_NAME
    ```

6. Adja hozzá egy másik **összeállítása lépés** típusú **hajtható végre a rendszerhéj** és használja a következő szöveget:

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    kubectl set image deployment/go-web go-web=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

![Jenkins build lépései](./media/container-service-kubernetes-jenkins/jenkins-build-steps.png)
    
7. Mentse a Jenkins elemet, és tesztelés **Build most**.

### <a name="step-4-connect-github-webhook"></a>4. lépés: Csatlakozás GitHub webhook
1. Kattintson a létrehozott Jenkins elem, **konfigurálása**.
2. A **Build eseményindítók**, jelölje be **GitHub hook eseményindítója a következőnek: GITScm lekérdezési** és **mentése**. Ez automatikusan konfigurálja a GitHub webhook.
3. Kattintson a webes nyissa meg a GitHub-tárház, **beállítások > Webhookok**.
4. Győződjön meg arról, hogy a Jenkins webhook URL-cím felvétele sikeresen megtörtént. Az URL-címet a "github webhook" kell végződnie.

![Jenkins webhook konfiguráció](./media/container-service-kubernetes-jenkins/jenkins-webhook.png)

## <a name="test-the-cicd-process-end-to-end"></a>A CI/CD folyamat teljes körű tesztelése

1. Frissítse a tárházban és leküldéses/szinkronizálási kódját a GitHub-tárházban.
2. A Jenkins konzolról, ellenőrizze a **Build előzmények** , és ellenőrizze, hogy a feladat futott. Az részletes nézet a konzol kimeneti.
3. A Kubernetes a frissített központi telepítés részleteinek megtekintéséhez:

    ```bash
    kubectl rollout history deployment/go-web
    ```

## <a name="next-steps"></a>Következő lépések

- Azure tároló beállításjegyzék telepítése, és egy biztonságos tárházban lemezképeket menteni. Lásd: [Azure tároló beállításjegyzék docs](https://docs.microsoft.com/azure/container-registry).
- Egy összetettebb munkafolyamat tartalmaz egymás melletti központi telepítés és automatikus tesztek a Jenkins felépítéséhez.
- CI/CD Jenkins és Kubernetes kapcsolatos további információkért tekintse meg a [Jenkins blog](https://jenkins.io/blog/2015/07/24/integrating-kubernetes-and-jenkins/).
