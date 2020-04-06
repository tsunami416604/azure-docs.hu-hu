---
title: Üzembe helyezés a GitHubról az AKS-re jenkins-szel
titleSuffix: Azure Kubernetes Service
description: Jenkins beállítása folyamatos integrációra (CI) a GitHubról és a folyamatos üzembe helyezés (CD) az Azure Kubernetes szolgáltatásba (AKS)
services: container-service
author: zr-msft
ms.author: zarhoads
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: a93cfc77178ff7638217663c2ceda500aae4cfd7
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668643"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>Oktatóanyag: Üzembe helyezés a GitHubról az Azure Kubernetes-szolgáltatásba (AKS) a Jenkins folyamatos integrációval és üzembe helyezéssel

Ez az oktatóanyag egy mintaalkalmazást telepít a GitHubról egy [Azure Kubernetes-szolgáltatás (AKS)](/azure/aks/intro-kubernetes) fürtre a folyamatos integráció (CI) és a folyamatos üzembe helyezés (CD) jenkins-i beállításával. Ily módon, amikor frissíti az alkalmazást a GitHubra való véglegesítésleésével, a Jenkins automatikusan futtat egy új tárolóbuildet, lelöki a tárolórendszerképeket az Azure Container Registry (ACR) rendszerbe, majd futtatja az alkalmazást az AKS-ben. 

Ebben az oktatóanyagban az alábbi feladatokat hajthatja végre:

> [!div class="checklist"]
> * Üzembe helyezhet egy minta Azure szavazási alkalmazást egy AKS-fürtben.
> * Hozzon létre egy alapvető Jenkins-projektet.
> * Állítsa be a hitelesítő adatokat a Jenkins számára az ACR-rel való interakcióhoz.
> * Hozzon létre egy Jenkins build feladatot és GitHub webhook automatikus buildek.
> * Tesztelje a CI/CD-folyamatot egy alkalmazás frissítésére az AKS-ben a GitHub-kód véglegesítése alapján.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez az alábbi elemekre van szükség:

- A Kubernetes, a Git, a CI/CD és a tárolóképek alapvető megértése

- [AKS-fürt,](../aks/kubernetes-walkthrough.md) amely `kubectl` az [AKS-fürt hitelesítő adataival](/cli/azure/aks#az-aks-get-credentials)van konfigurálva.

- Az [Azure Container Registry (ACR) rendszerleíró adatbázisa](../container-registry/container-registry-get-started-azure-cli.md), az ACR bejelentkezési kiszolgáló neve és az [ACR-beállításjegyzékkel való hitelesítésre](../aks/cluster-container-registry-integration.md)konfigurált AKS-fürt.

- Az Azure CLI 2.0.46-os vagy újabb verziója telepítve és konfigurálva. Futtassa `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése](/cli/azure/install-azure-cli)című témakört.

- [A Docker telepítve](https://docs.docker.com/install/) van a fejlesztői rendszerre

- A fejlesztői rendszerre telepített GitHub-fiók, [GitHub-személyes hozzáférési jogkivonat](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)és Git-ügyfél

- Ha a jenkins-i minta helyett a saját Jenkins-példányt adja meg, a Jenkins-példánynak telepítenie kell a [Docker-t, és](https://docs.docker.com/install/) [kubectl-et kell telepítenie.](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="prepare-your-app"></a>Az alkalmazás előkészítése

Ebben a cikkben egy minta Azure szavazási alkalmazást használ, amely egy vagy több podban üzemeltetett webes felületet tartalmaz, és egy második podot, amely redis-t üzemeltet ideiglenes adattároláshoz. Mielőtt integrálná a Jenkinst és az AKS-t az automatizált központi telepítésekhez, először manuálisan készítse elő és telepítse az Azure szavazási alkalmazást az AKS-fürtre. Ez a manuális központi telepítés az alkalmazás első verziója, és lehetővé teszi az alkalmazás működés közbeni megtekintését.

> [!NOTE]
> A minta Azure szavazási alkalmazás egy Linux pod, amely a tervek szerint egy Linux-csomóponton fut. A cikkben ismertetett folyamat a Windows Server-csomóponton ütemezett Windows Server podok esetében is működik.

Elágazás a következő GitHub-tárház a mintaalkalmazás - [https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis). Ahhoz, hogy elágaztassa a példatárt a saját GitHub-fiókja felé, kattintson a **Fork** (Elágaztatás) gombra a jobb felső sarokban.

Klónozza a villát a fejlesztési rendszerbe. Győződjön meg róla, hogy a tárlat klónozásakor használja a villa URL-címét:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Váltás a klónozott elágazás könyvtárára:

```console
cd azure-voting-app-redis
```

A mintaalkalmazáshoz szükséges tárolórendszerképek létrehozásához használja a *docker-compose.yaml* fájlt: `docker-compose`

```console
docker-compose up -d
```

A szükséges alaplemezképek lekérése és az alkalmazástárolók megépítése. Ezután a [docker-rendszerképek](https://docs.docker.com/engine/reference/commandline/images/) paranccsal megtekintheti a létrehozott lemezképet. Három rendszerkép lett letöltve vagy jött létre. Az `azure-vote-front` rendszerkép tartalmazza az alkalmazást, és a `nginx-flask` rendszerképet használja alapként. A `redis` rendszerkép redis-példány indítására szolgál:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Mielőtt leküldéses az *azure-vote-front* tároló rendszerképet ACR, az ACR bejelentkezési kiszolgáló az [az acr lista](/cli/azure/acr#az-acr-list) paranccsal. A következő példa beszerzi a myResourceGroup nevű erőforráscsoport egyik rendszerleíró adatbázisának ACR bejelentkezési *kiszolgálójának*címét:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

A [docker-címke](https://docs.docker.com/engine/reference/commandline/tag/) paranccsal címkézheti a lemezképet az ACR `v1`bejelentkezési kiszolgáló nevével és a verziószámával. Adja meg `<acrLoginServer>` saját nevét az előző lépésben:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Végül az *azure-vote-front-image* az ACR-beállításjegyzékbe. Ismét cserélje `<acrLoginServer>` le a bejelentkezési kiszolgáló nevét a saját `myacrregistry.azurecr.io`ACR rendszerleíró adatbázis, mint például:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>A mintaalkalmazás telepítése az AKS-be

A mintaalkalmazás üzembe helyezéséhez az AKS-fürt, használhatja a Kubernetes jegyzékfájl az Azure szavazási tárház tárház tárház tárház. Nyissa meg az *azure-vote-all-in-one-redis.yaml* jegyzékfájlt `vi`egy szerkesztővel, például . Az `microsoft` helyére az ACR bejelentkezési kiszolgálójának nevét írja be. Ez az érték a jegyzékfájl **47.**

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Ezután használja a [kubectl apply parancsot](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) az alkalmazás AKS-fürtre való üzembe helyezéséhez:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

A Kubernetes terheléselosztó szolgáltatás jön létre, hogy az alkalmazás az interneten. Ez eltarthat pár percig. A terheléselosztó központi telepítésének folyamatának figyeléséhez használja a [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) command az `--watch` argumentumot. Miután az *EXTERNAL-IP-cím* *függőben lévőről* `Control + C` *IP-címre*változott, a kubectl figyelési folyamat leállításához használja.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Az alkalmazás működés közbeni megtekintéséhez nyisson meg egy webböngészőt a szolgáltatás külső IP-címére. Az Azure szavazási alkalmazás jelenik meg, ahogy az a következő példában látható:

![Az AKS-ben futó Azure-mintaszavazási alkalmazás](media/jenkins-continuous-deployment/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Jenkins üzembe helyezése egy Azure virtuális gépre

A Jenkins gyors üzembe helyezéséhez ebben a cikkben való használatra, a következő parancsfájl segítségével telepíthet egy Azure virtuális gépet, konfigurálhatja a hálózati hozzáférést, és elvégezheti a Jenkins alapvető telepítését. A Jenkins és az AKS-fürt közötti hitelesítéshez a parancsfájl a Kubernetes konfigurációs fájlt másolja a fejlesztői rendszerből a Jenkins-rendszerbe.

> [!WARNING]
> Ez a mintaparancsfájl demó célokra egy Azure-beli virtuális gépen futó Jenkins-környezet gyors kiépítése. Az Azure egyéni parancsfájl-bővítmény t használja a virtuális gép konfigurálásához, majd megjeleníti a szükséges hitelesítő adatokat. A *~/.kube/config* a Jenkins virtuális gépre kerül.

Futtassa a következő parancsokat a parancsfájl letöltéséhez és futtatásához. A futtatás előtt át kell tekintenie [https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh)bármely parancsfájl tartalmát - .

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Néhány percet vesz igénybe a virtuális gép létrehozása és a Docker és a Jenkins szükséges összetevőinek üzembe helyezése. Amikor a parancsfájl befejeződött, egy címet ad ki a Jenkins-kiszolgálónak és egy kulcsot az irányítópult feloldásához, ahogy az a következő példa kimenetben látható:

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Nyisson meg egy webböngészőt a megjelenített URL-címre, és adja meg a feloldási kulcsot. A Jenkins-konfiguráció befejezéséhez kövesse a képernyőn megjelenő utasításokat:

- Válassza **a Javasolt bővítmények telepítése lehetőséget**
- Hozza létre az első rendszergazdai felhasználót. Adjon meg egy felhasználónevet, például *az azureusert,* majd adja meg saját biztonságos jelszavát. Végül gépelje be teljes nevét és e-mail-címét.
- Válassza a **Save and Finish** (Mentés és befejezés) elemet
- Amint Jenkins készen áll, kattintson a **Start using Jenkins** (Jenkins használatának megkezdése) elemre
    - Ha a webböngésző Jenkins használatának megkezdésekor egy üres lapot jelenít meg, indítsa újra a Jenkins szolgáltatást. A szolgáltatás újraindításához az SSH-t a Jenkins-példány nyilvános IP-címére kell írnia, és írja be a következőt: `sudo service jenkins restart`. A szolgáltatás újraindítása után frissítse a webböngészőt.
- Jelentkezzen be a Jenkins be a telepítési folyamat során létrehozott felhasználónévvel és jelszóval.

## <a name="create-a-jenkins-environment-variable"></a>Jenkins-környezeti változó létrehozása

A Jenkins-környezet változó az ACR bejelentkezési kiszolgáló nevének tárolására szolgál. Erre a változóra hivatkozik a Jenkins-összeállítási feladat során. A környezeti változó létrehozásához hajtsa végre az alábbi lépéseket:

- A Jenkins portál bal oldalán válassza a > **Jenkins-konfigurálási rendszer** **kezelése**lehetőséget.
- A **Globális tulajdonságok csoportban**válassza a **Környezeti változók**lehetőséget. Adjon hozzá egy `ACR_LOGINSERVER` változót az ACR bejelentkezési kiszolgáló nevével és értékével.

    ![Jenkins környezeti változók](media/jenkins-continuous-deployment/env-variables.png)

- Ha elkészült, kattintson a **Mentés** gombra a Jenkins-konfigurációs lap alján.

## <a name="create-a-jenkins-credential-for-acr"></a>Jenkins-hitelesítő adatok létrehozása az ACR-hez

Ahhoz, hogy a Jenkins létre, majd leküldéses frissített tárolórendszerképek ACR, meg kell adnia hitelesítő adatokat az ACR. Ez a hitelesítés használhatja az Azure Active Directory egyszerű szolgáltatásait. Az elő-előfeltételekben konfigurálta az AKS-fürt egyszerű szolgáltatását *reader* engedélyekkel az ACR-beállításjegyzékhez. Ezek az engedélyek lehetővé teszik, hogy az AKS-fürt *lekéri* a képeket az ACR rendszerleíró adatbázisból. A CI/CD-folyamat során a Jenkins új tárolórendszerképeket hoz létre az alkalmazásfrissítések alapján, és ezután le kell *adnia* ezeket a rendszerképeket az ACR-beállításjegyzékbe. A szerepkörök és engedélyek elkülönítéséhez most konfiguráljon egy egyszerű szolgáltatást a Jenkins *hez, amely közreműködői* engedélyekkel rendelkezik az ACR-beállításjegyzékhez.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>Az ACR használatához hozzon létre egy egyszerű szolgáltatást a Jenkins számára

Először hozzon létre egy egyszerű szolgáltatást az [ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) paranccsal:

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

Jegyezze fel a kimenetben látható *alkalmazásazonosítót* és *jelszót.* Ezeket az értékeket a következő lépésekben konfigurálja a hitelesítő adatok erőforrás Jenkins.

Az ACR-beállításjegyzék erőforrás-azonosítójának bekérése az [az acr show](/cli/azure/acr#az-acr-show) parancs használatával, és változóként tárolható. Adja meg az erőforráscsoport nevét és az ACR-nevet:

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

Most hozzon létre egy szerepkör-hozzárendelést a szolgáltatás fő *közreműködői* jogok hozzárendeléséhez az ACR-beállításjegyzékhez. A következő példában adja meg a saját *appId* jelenik meg a kimeneten egy korábbi parancsot, hogy hozza létre a szolgáltatás egyszerű:

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Hitelesítő adatok forrása a Jenkinsben az ACR-szolgáltatás egyszerű szolgáltatásához

Az Azure-ban létrehozott szerepkör-hozzárendeléssel most tárolja az ACR-hitelesítő adatait egy Jenkins hitelesítő adatok objektumban. Ezek a hitelesítő adatok a Jenkins-összeállítási feladat során hivatkoznak.

A Jenkins-portál bal oldalán kattintson a Hitelesítő > adatok > **hozadékának a Jenkins**globális**hitelesítő adatai (korlátlan)** > **Hitelesítő adatok hozzáadása elemre.** **Credentials**

Győződjön meg arról, hogy a hitelesítő adat név jelszóval van **megnevezve,** és írja be a következő elemeket:

- **Felhasználónév** – Az ACR-beállításjegyzékkel való hitelesítéshez létrehozott egyszerű szolgáltatás *alkalmazásazonosítója.*
- **Jelszó** – Az ACR-beállításjegyzékkel való hitelesítéshez létrehozott egyszerű szolgáltatás *jelszava.*
- **Azonosító** – hitelesítő adatok, például *acr-hitelesítő adatok*

Ha elkészült, a hitelesítő adatok űrlapja a következő példához hasonlóan néz ki:

![Jenkins hitelesítő adatobjektum létrehozása az egyszerű szolgáltatásadatokkal](media/jenkins-continuous-deployment/acr-credentials.png)

Kattintson **az OK gombra,** és térjen vissza a Jenkins portálra.

## <a name="create-a-jenkins-project"></a>Jenkins-projekt létrehozása

A Jenkins portál kezdőlapján válassza az **Új elem** lehetőséget a bal oldalon:

1. Adja meg *az azure-vote-ot* feladatnévként. Válassza **a Freestyle projekt**lehetőséget, majd az **OK** gombot.
1. Az **Általános** szakaszban válassza a **GitHub-projektet,** és adja meg a villás tárterület URL-címét, például *https:\//github.com/\<your-github-account/azure-voting-app-redis\>*
1. A **Forráskód kezelése** szakaszban válassza a **Git**lehetőséget, írja be a villás tárterület *.git* URL-címét, például *\/https:\</github.com/ your-github-account\>/azure-voting-app-redis.git*

1. A **Build-eseményindítók** csoportban válassza a **GitHub-hook-itriggert a GITscm-lekérdezéshez**
1. A **Környezet összeállítása csoportban**válassza **a Titkos szövegek vagy fájlok használata lehetőséget.**
1. A **Kötések csoportban**válassza **a Felhasználónév** > és jelszó hozzáadása **(külön) lehetőséget.**
   - Írja `ACR_ID` be a **Felhasználónév változót**és `ACR_PASSWORD` a **Jelszóváltozót**

     ![Jenkins-kötések](media/jenkins-continuous-deployment/bindings.png)

1. Válassza a Build Step típusú **Build Step** típusú **Végrehajtási rendszerhéj at,** és használja a következő szöveget. Ez a parancsfájl egy új tárolórendszerképet hoz létre, és leküldéses, hogy az ACR registry.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Adjon hozzá egy másik **Build Step** típusú **Parancshéj végrehajtása,** és használja a következő szöveget. Ez a parancsfájl frissíti az alkalmazás központi telepítését az AKS-ben az ACR új tárolórendszerképével.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. Miután elkészült, kattintson a **Mentés gombra.**

## <a name="test-the-jenkins-build"></a>A Jenkins-build tesztelése

Mielőtt a GitHub véglegesítése alapján automatizálna a feladat, először manuálisan tesztelje a Jenkins-buildet. Ez a manuális build ellenőrzi, hogy a feladat megfelelően van-e konfigurálva, a megfelelő Kubernetes hitelesítési fájl a helyén van-e, és hogy az ACR-rel való hitelesítés működik-e.

A projekt bal oldali menüjében válassza a **Build Now**lehetőséget.

![Jenkins tesztbuild](media/jenkins-continuous-deployment/test-build.png)

Az első build egy-két percet vesz igénybe, mivel a Docker-lemezképrétegek lehívása a Jenkins-kiszolgálóra. A későbbi buildek a gyorsítótárazott képrétegek használatával javíthatják a létrehozási időt.

A létrehozási folyamat során a GitHub-tárház klónozott a Jenkins buildelőkiszolgálóra. Egy új tárolórendszerkép jön, és az ACR-beállításjegyzékbe kerül. Végül az AKS-fürtön futó Azure szavazási alkalmazás frissül az új lemezkép használatával. Mivel az alkalmazáskód nem módosult, az alkalmazás nem változik, ha a mintaalkalmazást webböngészőben tekinti meg.

Miután a build feladat befejeződött, kattintson **a build #1** alatt épít történelem. Válassza a **Konzolkimenet lehetőséget,** és tekintse meg a buildelési folyamat kimenetét. Az utolsó sornak sikeres buildet kell jeleznie.

## <a name="create-a-github-webhook"></a>GitHub-webhook létrehozása

A sikeres manuális build befejeződésével most integrálja a GitHubot a Jenkins buildbe. A webhook használható a Jenkins buildelési feladat futtatásához minden alkalommal, amikor egy kód véglegesítése történik a GitHubon. A GitHub webhook létrehozásához hajtsa végre az alábbi lépéseket:

1. Tallózással keresse meg a forked GitHub-tárházat egy webböngészőben.
1. Válassza **a Beállítások**lehetőséget, majd a bal oldalon válassza a **Webhooks** lehetőséget.
1. Válassza a **Webhook hozzáadása lehetőséget.** A *hasznos adat URL-címéhez*adja meg, `http://<publicIp:8080>/github-webhook/`hogy hol `<publicIp>` található a Jenkins-kiszolgáló IP-címe. Győződjön meg róla, hogy tartalmazza a záró /. Hagyja meg a tartalomtípus többi alapértelmezett beállítását, és indítsa el a *leküldéses* eseményeket.
1. Válassza **a Webhook hozzáadása**lehetőséget.

    ![GitHub-webhook létrehozása Jenkins számára](media/jenkins-continuous-deployment/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>A teljes CI/CD-folyamat tesztelése

Most tesztelheti a teljes CI/CD-folyamatot. Amikor leküldéses egy kódot véglegesítése a GitHubra, a következő lépések történnek:

1. A GitHub webhook eléri a Jenkins.
1. Jenkins elindítja a buildelési feladatot, és lekéri a legújabb kódvéglegesítést a GitHubról.
1. A Docker-build a frissített kód használatával indul el, és az új tárolórendszerkép a legújabb buildszámmal van címkézve.
1. Ez az új tárolórendszerkép az Azure Container Registry.
1. Az Azure Kubernetes-szolgáltatásra telepített alkalmazás frissíti az Azure Container Registry beállításjegyzékből származó legújabb tárolórendszerképet.

A fejlesztői gépen nyissa meg a klónozott alkalmazást egy kódszerkesztővel. Az */azure-vote/azure-vote* könyvtárban nyissa meg a **config_file.cfg**. Frissítse a fájlban szereplő szavazási értékeket a macskáktól és kutyáktól eltérő szintre, amint az a következő példában látható:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Frissítéskor mentse a fájlt, véglegesítse a módosításokat, és ezeket a GitHub-tárház elágazására tolja. A GitHub webhook egy új build feladatot indít el a Jenkinsben. A Jenkins webes irányítópultján figyelje a létrehozási folyamatot. Néhány másodpercet vesz igénybe a legújabb kód lekérése, a frissített lemezkép létrehozása és leküldése, valamint a frissített alkalmazás a KS-ben való üzembe helyezése.

Miután a build befejeződött, frissítse a minta Azure szavazási alkalmazás webböngészőjét. A módosítások a következő példában látható módon jelennek meg:

![Minta Azure-szavazás az AKS-ben a Jenkins build feladat által frissített](media/jenkins-continuous-deployment/azure-vote-updated.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Jenkins az Azure-on](/azure/jenkins)