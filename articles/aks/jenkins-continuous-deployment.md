---
title: Oktatóanyag – Azure Kubernetes Service (AKS) a jenkins-szel való üzembe helyezés a Githubról
description: Állítsa be a Jenkins folyamatos integrációs (CI) a GitHub és a folyamatos készregyártás (CD) az Azure Kubernetes Service (AKS)
services: container-service
ms.service: container-service
author: iainfoulds
ms.author: iainfou
ms.topic: article
ms.date: 09/27/2018
ms.openlocfilehash: d252e275280ed2a5c2129f6b228e9989a33b37fd
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853625"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>Oktatóanyag: Üzembe helyezés a Githubról, az Azure Kubernetes Service (AKS) a Jenkins folyamatos integrációs és üzembe helyezés

Ebben az oktatóanyagban egy mintaalkalmazást a Githubról történő üzembe helyez egy [Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes) fürt folyamatos integrációs (CI) és a Jenkins folyamatos készregyártás (CD) beállítása. Így amikor frissítse az alkalmazást a Githubról, hogy a véglegesítéseket Jenkins automatikusan fut egy új tárolót hozhat létre, leküldi a tárolórendszerképek az Azure Container Registry (ACR), és ezután futtatja az alkalmazást az aks-ben. 

Ez az oktatóanyag ezen feladatok fogja végrehajtani:

> [!div class="checklist"]
> * Az AKS-fürt üzembe helyezése az Azure vote mintaalkalmazást.
> * Hozzon létre egy alapszintű Jenkins-projektet.
> * Állítsa be a Jenkins használatához az ACR-REL és hitelesítő adatait.
> * Hozzon létre egy Jenkins létrehozási feladatot és a GitHub-webhook az automatizált buildekig.
> * Tesztelje a CI/CD-folyamat az aks-ben a GitHub code véglegesítéseket alapján egy alkalmazás frissítéséhez.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége ezeket az elemeket:

- Kubernetes, a Git, a CI/CD-ről és a tároló lemezképek alapvető ismeretekkel

- Egy [AKS-fürt] [ aks-quickstart] és `kubectl` konfigurálva a [AKS-fürt hitelesítő adatait][aks-credentials]

- Egy [Azure Container Registry (ACR) beállításjegyzék][acr-quickstart], az ACR bejelentkezési kiszolgáló nevét, és az AKS-fürtöt úgy [hitelesítés az ACR-beállításjegyzékbe][acr-authentication]

- Az Azure CLI 2.0.46 verzió vagy újabb telepítése és konfigurálása. Futtatás `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni, tekintse meg kell [Azure CLI telepítése][install-azure-cli].

- [A docker telepítve] [ docker-install] a fejlesztői rendszerhez

- Egy GitHub-fiók [Githubhoz használt személyes hozzáférési tokent][git-access-token], és a Git-ügyfél telepítve van a fejlesztői rendszerhez

- A saját Jenkins-példány helyett ezt a mintát a Jenkins üzembe helyezésére parancsfájlalapú ad meg, ha a Jenkins-példány igények [Docker telepített és konfigurált] [ docker-install] és [kubectl][kubectl-install].

## <a name="prepare-your-app"></a>Az alkalmazás előkészítése

Ebben a cikkben egy minta Azure vote alkalmazást, amely tartalmazza az egy vagy több podok és a egy második pod Redis üzemeltető ideiglenes adattárolásra üzemeltetett webes felületet használhat. Mielőtt automatikus központi telepítés integrálása a Jenkins és az AKS először manuális előkészítése és központi telepítése az Azure vote alkalmazást, az AKS-fürt. A manuális üzembe helyezés az alkalmazás első verziója, és lehetővé teszi, hogy az alkalmazást működés közben láthatja.

A mintaalkalmazás - a következő GitHub-tárház elágaztatása [ https://github.com/Azure-Samples/azure-voting-app-redis ](https://github.com/Azure-Samples/azure-voting-app-redis). Ahhoz, hogy elágaztassa a példatárt a saját GitHub-fiókja felé, kattintson a **Fork** (Elágaztatás) gombra a jobb felső sarokban.

A fejlesztői rendszerhez a elágazás klónozása. Győződjön meg arról, hogy az URL-címét az elágazásában használhatja, ha a tárház klónozása:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Módosítsa a könyvtárat a saját klónozott elágazásában:

```console
cd azure-voting-app-redis
```

A mintaalkalmazás szükséges tárolórendszerképeket létrehozásához használja a *docker-compose.yaml* fájlt `docker-compose`:

```console
docker-compose up -d
```

A szükséges alaplemezképek lekért vannak, és az alkalmazástárolók készítettük. Ezután a [docker-rendszerképek] [ docker-images] paranccsal megtekintheti a létrehozott rendszerképet. Három rendszerkép lett letöltve vagy jött létre. Az `azure-vote-front` rendszerkép tartalmazza az alkalmazást, és a `nginx-flask` rendszerképet használja alapként. A `redis` lemezképet egy Redis-példány indításához használja:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Mielőtt a *azure-vote-front* tárolórendszerképet az ACR-be, az ACR bejelentkezési kiszolgálójának az első a [az acr list] [ az-acr-list] parancsot. Az alábbi példa lekéri az ACR bejelentkezési kiszolgálójának címe nevű erőforráscsoportot a beállításjegyzék *myResourceGroup*:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Használja a [docker címke] [ docker-tag] fel a rendszerképet az ACR bejelentkezési kiszolgáló nevét és a egy verziószáma parancs `v1`. Adja meg a saját `<acrLoginServer>` az előző lépésben lekért neve:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Végül küldje le a *azure-vote-front* rendszerképet az ACR-beállításjegyzékbe. Ezúttal is helyettesítse be `<acrLoginServer>` a bejelentkezési kiszolgálójának nevét a saját ACR-beállításjegyzékbe való például `myacrregistry.azurecr.io`:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>A mintaalkalmazás az aks üzembe helyezése

A mintaalkalmazás az AKS-fürt üzembe helyezéséhez használhatja az Azure vote tárház tárház gyökérkönyvtárában található a Kubernetes-jegyzékfájl. Nyissa meg a *azure-vote-all-in-one-redis.yaml* például manifest fájlt egy szerkesztővel `vi`. Az `microsoft` helyére az ACR bejelentkezési kiszolgálójának nevét írja be. Ez az érték sorában található **47** a jegyzékfájl:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Ezután a [a kubectl a alkalmazni] [ kubectl-apply] parancsot az alkalmazás az AKS-fürt üzembe helyezéséhez:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Egy terheléselosztó Kubernetes szolgáltatás jön létre az alkalmazást az interneten elérhetővé. Ez eltarthat pár percig. A load balancer központi telepítés állapotának monitorozásához használja a [kubectl get service] [ kubectl-get] parancsot a `--watch` argumentum. Miután az *EXTERNAL-IP* cím *pending* állapotról egy *IP-címre* változik, a `Control + C` billentyűparanccsal állítsa le a kubectl figyelési folyamatát.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Az alkalmazás működésének megtekintéséhez nyissa meg a szolgáltatás külső IP-címét egy webböngészőben. Az Azure vote alkalmazás jelenik meg, az alábbi példában látható módon:

![Az Azure vote mintaalkalmazást az aks-ben futó](media/aks-jenkins/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Az Azure virtuális géphez a Jenkins üzembe helyezése

Gyorsan üzembe helyezése a Jenkins használható ebben a cikkben, az alábbi parancsfájlt használhatja üzembe helyezése az Azure virtuális gép, hálózati hozzáférés konfigurálása és egy alapszintű Jenkins-telepítés befejezéséhez. A Jenkins és az AKS-fürt közötti hitelesítéshez a szkript átmásolja a Kubernetes konfigurációs fájl a fejlesztői rendszerhez a Jenkins rendszer.

> [!WARNING]
> Ez a példaszkript bemutató céljából a gyors üzembe helyezése a Jenkins-környezet egy Azure-beli virtuális gépen futó van. Az Azure egyéni szkriptek futtatására szolgáló bővítmény használatával konfiguráljon egy virtuális Gépet, és jelenítheti meg a szükséges hitelesítő adatokat. A *~/.kube/config* a Jenkins virtuális gép másolja.

Töltse le és futtassa a parancsfájlt a következő parancsok futtatásával. Tekintse át, futtatása előtt minden parancsfájl tartalmát,- [ https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh ](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh).

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

A virtuális gép létrehozása és a szükséges összetevők a Docker és a Jenkins üzembe helyezése néhány percet vesz igénybe. A szkript befejezése után azt kimenete egy címet a Jenkins-kiszolgáló és a egy kulcs zárolásának feloldása az irányítópulton, az alábbi példa kimenetében látható módon:

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Nyisson meg egy webböngészőt a URL-cím jelenik meg, és adja meg a feloldási kulcsot. Kövesse a képernyőn megjelenő utasításokat követve fejezze be a Jenkins konfigurációja:

- Válasszon **javasolt beépülő modulok telepítése**
- Hozza létre az első rendszergazdai felhasználót. Írjon be egy felhasználónevet, például *azureuser*, majd adja meg a saját biztonságos jelszavát. Végül gépelje be teljes nevét és e-mail-címét.
- Válassza a **Save and Finish** (Mentés és befejezés) elemet
- Amint Jenkins készen áll, kattintson a **Start using Jenkins** (Jenkins használatának megkezdése) elemre
    - Ha a webböngésző Jenkins használatának megkezdésekor egy üres lapot jelenít meg, indítsa újra a Jenkins szolgáltatást. A szolgáltatás újraindításához SSH nyilvános IP-címet a Jenkins-példány és a típus `sudo service jenkins restart`. A szolgáltatás újraindulása után frissítse webböngésző.
- Jelentkezzen be a Jenkins-a felhasználónevet és jelszót, amelyet a telepítési folyamat során.

## <a name="create-a-jenkins-environment-variable"></a>Hozzon létre egy Jenkins környezeti változó

A Jenkins környezeti változó segítségével tartsa az ACR bejelentkezési kiszolgáló nevét. Ez a változó a Jenkins létrehozási feladatot során hivatkozik. Ez a környezeti változó létrehozásához hajtsa végre az alábbi lépéseket:

- A Jenkins portálon bal oldalán, válassza ki a **Jenkins kezelése** > **rendszer konfigurálása**
- A **globális tulajdonságok**válassza **környezeti változók**. Adja hozzá a nevű változó `ACR_LOGINSERVER` és az ACR bejelentkezési kiszolgálójának értékét.

    ![A Jenkins környezeti változók](media/aks-jenkins/env-variables.png)

- Amikor végzett, kattintson a **mentése** a Jenkins-konfiguráció lap alján.

## <a name="create-a-jenkins-credential-for-acr"></a>Hozzon létre egy Jenkins-hitelesítő adatot az ACR-hez

Ahhoz, hogy a Jenkins és a build, majd továbbítsa a frissített tárolórendszerkép az ACR-be, meg kell adja meg a hitelesítő adatokat az ACR-hez. A hitelesítés használhatja az Azure Active Directory egyszerű szolgáltatásaira. Az Előfeltételek konfigurálta az egyszerű szolgáltatás számára az AKS fürt *olvasó* engedélyeket az ACR-beállításjegyzékbe. Ezek az engedélyek lehetővé teszik az AKS-fürt *lekéréses* rendszerképeket az ACR-beállításjegyzékből. A CI/CD-folyamat során a Jenkins létrehozza az új tárolórendszerképek alkalmazásfrissítéseknél alapján, és kell majd *leküldéses* ezeket rendszerképeket az ACR-beállításjegyzékbe. El a szerepköröket és engedélyeket, mostantól konfigurálhatja az egyszerű szolgáltatás a jenkins *közreműködői* engedélyeket az ACR-beállításjegyzékbe.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>A Jenkins használata ACR egyszerű szolgáltatás létrehozása

Először is hozzon létre egy szolgáltatás egyszerű a [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] parancsot:

```azurecli
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "626dd8ea-042d-4043-a8df-4ef56273670f",
  "displayName": "azure-cli-2018-09-28-22-19-34",
  "name": "http://azure-cli-2018-09-28-22-19-34",
  "password": "1ceb4df3-c567-4fb6-955e-f95ac9460297",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Jegyezze fel a *appId* és *jelszó* szöveget a kimenetben látható. Ezek az értékek szolgálnak a következő lépéseket a hitelesítőadat-erőforrás konfigurálása a Jenkinsben.

Az ACR beállításjegyzék használata az erőforrás-azonosító beszerzése a [az acr show] [ az-acr-show] parancsot, és a egy változóként tárolja. Adja meg az erőforráscsoport nevét és az ACR-név:

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

Most hozzon létre egy szerepkör-hozzárendelés az egyszerű szolgáltatás hozzárendelése *közreműködői* jogok az ACR-beállításjegyzékbe. A következő példában adja meg a saját *appId* egy előző parancsot az egyszerű szolgáltatás létrehozása a kimenet látható:

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Hitelesítő adatok erőforrás létrehozása a Jenkinsben az ACR-szolgáltatás egyszerű

A szerepkör-hozzárendelés létrehozása az Azure-ban, most már az ACR hitelesítő adatok tárolása egy Jenkins hitelesítő objektumot. A Jenkins létrehozási feladatot során hivatkozott ezeket a hitelesítő adatokat.

Azon a Jenkins portálon bal oldalán kattintson **hitelesítő adatok** > **Jenkins** > **globális hitelesítő adatokat (korlátlan)**  >  **Hitelesítő adatainak hozzáadása**

Győződjön meg arról, hogy a hitelesítő adatok típusa **felhasználónév, jelszó** , és adja meg a következő elemek:

- **Felhasználónév** – a *appId* a hitelesítés az ACR-beállításjegyzékbe a létrehozott szolgáltatásnév.
- **Jelszó** – a *jelszó* a hitelesítés az ACR-beállításjegyzékbe a létrehozott szolgáltatásnév.
- **ID** -Hitelesítőadat-azonosító például *acr-hitelesítő adatok*

Amikor végzett, a hitelesítő adatok képernyőn a következő példához hasonlóan néz ki:

![Hozzon létre egy Jenkins-hitelesítő objektumot a szolgáltatásnév adatait](media/aks-jenkins/acr-credentials.png)

Kattintson a **OK** és térjen vissza a Jenkins portálon.

## <a name="create-a-jenkins-project"></a>A Jenkins projekt létrehozása

A Jenkins portálra a kezdőlapon, válassza ki a **új elem** a bal oldalon:

1. Adja meg *azure-vote* feladat neveként. Válasszon **Freestyle project**, majd **OK**
1. A **General** (Általános) szakaszban válassza ki a **GitHub project** (GitHub-projekt) lehetőséget, majd adja meg az elágaztatott adattár URL-címét, például: *https://github.com/\<your-github-account\>/azure-voting-app-redis*
1. A **Source code management** (Forráskódkezelés) szakaszban válassza a **Git** elemet, majd adja meg az elágaztatott *.git*-adattár URL-címét, például: *https://github.com/\<your-github-account\>/azure-voting-app-redis.git*
    - A hitelesítő adatait, kattintson a és **Hozzáadás** > **Jenkins**
    - Alatt **típusú**válassza **titkos szöveg** , és adja meg a [Githubhoz használt személyes hozzáférési tokent] [ git-access-token] , a titkos kulcsot.
    - Válassza ki **Hozzáadás** végeztével.

    ![GitHub hitelesítő adatok](media/aks-jenkins/github-creds.png)

1. Alatt a **hozhat létre eseményindítókat** szakaszban jelölje be **GitHub hook trigger for GITscm lekérdezés**
1. Alatt **környezetet hozhat létre**válassza **titkos szövegek vagy fájlok használata**
1. Alatt **kötések**, jelölje be **Hozzáadás** > **felhasználónév és jelszó (elválasztva)**
    - Adja meg `ACR_ID` számára a **felhasználónév-változó**, és `ACR_PASSWORD` számára a **Jelszóváltozó**

    ![A Jenkins-kötések](media/aks-jenkins/bindings.png)

1. Hozzáadhat egy **összeállítása lépés** típusú **hajtsa végre a rendszerhéj** és használja a következő szöveget. Ez a szkript összeállít egy új tárolórendszerképet, majd leküldi azt az ACR-beállításjegyzékbe.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Adjon hozzá egy másik **összeállítása lépés** típusú **hajtsa végre a rendszerhéj** és használja a következő szöveget. Ez a szkript frissíti az új tárolórendszerképet az ACR-ből az alkalmazás központi telepítése az aks-ben.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. Ha befejeződött, kattintson a **mentése**.

## <a name="test-the-jenkins-build"></a>A Jenkins-buildelés tesztelése

Automatizálja a feladat a GitHub-véglegesítéseket alapján, mielőtt először manuálisan tesztelje a Jenkins-buildelés. A manuális build ellenőrzi, hogy a feladat megfelelően van konfigurálva, a megfelelő Kubernetes hitelesítési fájl van beállítva, és, hogy működik-e a hitelesítés az ACR-REL.

Válassza a bal oldali menüben a projekt, **hozhat létre most**.

![A Jenkins létrehozási tesztelése](media/aks-jenkins/test-build.png)

Az első hozhat létre egy percet vesz igénybe, vagy két, mint a Docker-rendszerképek rétegeit vannak lekért a Jenkins-kiszolgáló. Ezt követő buildek a gyorsítótárazott rendszerképek rétegeit használatával javíthatja a fejlesztési idő.

Az összeállítási folyamat során a GitHub-adattárat a Jenkins-buildkiszolgáló van klónozni. Egy új tárolórendszerkép összeállítása és leküldött az ACR-beállításjegyzékbe. Végül az Azure vote alkalmazást, az AKS-fürtön futó frissül, és az új rendszerképet használja. Az alkalmazás kódja nem módosultak, mivel az alkalmazás nem módosul, ha megtekinti a mintaalkalmazást egy webböngészőben.

A fordítási feladatot befejeződése után kattintson a **összeállítása #1** alatt hozhat létre előzmények. Válassza ki **konzolkimenet** és a létrehozási folyamat eredményének megtekintéséhez. A végső vonal jelzi a build sikeres létrehozása.

## <a name="create-a-github-webhook"></a>Egy GitHub-webhook létrehozása

A build sikeres manuális létrehozása befejeződött, az mostantól integrálhatja az GitHub a Jenkins-buildelés. A Jenkins létrehozási feladatot futtatni minden alkalommal, amikor egy kód véglegesítésére jön létre a GitHub webhook használható. A GitHub-webhook létrehozásához hajtsa végre az alábbi lépéseket:

1. Keresse meg az elágaztatott GitHub-adattár egy webböngészőben.
1. Válassza ki **beállítások**, majd **Webhookok** a bal oldalon.
1. Válassza ki a **webhook hozzáadása**. Az a *hasznos adat URL-cím*, adja meg `http://<publicIp:8080>/github-webhook/`, ahol `<publicIp>` a Jenkins-kiszolgáló IP-címe. Győződjön meg arról, hogy tartalmazza a záró /. Az alapértelmezett tartalom típusa és az eseményindító meghagyása *leküldéses* eseményeket.
1. Válassza ki **webhook hozzáadása**.

    ![A Jenkins-GitHub-webhook létrehozása](media/aks-jenkins/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>A teljes CI/CD-folyamat tesztelése

Most tesztelheti a teljes CI/CD-folyamat. Amikor leküld egy kód véglegesítésére, GitHub, a következő lépések fordulhat elő:

1. A GitHub-webhook véglegesítéskor a Jenkins.
1. A Jenkins elindítja a fordítási feladatot, és a Githubról hívja le a legújabb kód véglegesítésére.
1. A Docker összeállítási a frissített kóddal végzett elindult, és az új tárolórendszerképet a a legújabb buildszám van megjelölve.
1. Az új tárolórendszerképet az Azure Container Registrybe leküldésekor.
1. Az alkalmazás a legújabb tárolórendszerképet az Azure Kubernetes Service-frissítések telepítve az Azure Container Registry-beállításjegyzékből.

A fejlesztői gépén nyissa meg a klónozott alkalmazás egy Kódszerkesztő. Alatt a */azure-vote/azure-vote* , nyissa meg a fájlt nevű könyvtárat **config_file.cfg**. Frissítse a szavazási ebben a fájlban egy eltérő macskák és kutyájával lenni, az alábbi példában látható módon:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Frissítésekor az mentse a fájlt, a módosítások véglegesítéséhez és leküldéses ezeket a GitHub-tárház elágazása. A GitHub-webhook aktiválása a Jenkinsben új build feladat. A Jenkins webes irányítópultján a létrehozási folyamat figyelése Kérje le a legújabb kódot, létrehozása és a frissített rendszerkép leküldése és az aks-ben a frissített alkalmazás üzembe helyezése néhány másodpercet vesz igénybe.

A létrehozás befejezése után a frissítés az Azure vote mintaalkalmazást böngészőjében. A módosítások megjelenését, az alábbi példában látható módon:

![Minta Azure szavazhat az aks-ben frissítette a Jenkins létrehozási feladatot:](media/aks-jenkins/azure-vote-updated.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan használható a Jenkins CI/CD megoldás részeként. Az AKS integrálható más CI/CD-megoldások és automatizálási eszközeivel, mint például a [Azure DevOps Project] [ azure-devops] vagy [AKS-fürt létrehozása az ansible segítségével] [ aks-ansible].

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[docker-install]: https://docs.docker.com/install/
[kubectl-install]: https://kubernetes.io/docs/tasks/tools/install-kubectl/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az-acr-list
[acr-authentication]: ../container-registry/container-registry-auth-aks.md#grant-aks-access-to-acr
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[azure-devops]: ../devops-project/azure-devops-project-aks.md
[aks-ansible]: ../ansible/ansible-create-configure-aks.md
