---
title: Oktatóanyag – üzembe helyezés a GitHubról az Azure Kubernetes szolgáltatásba (ak) a Jenkins használatával
description: A a GitHub és a folyamatos üzembe helyezés (CD) és az Azure Kubernetes szolgáltatás (ak) közötti folyamatos integráció beállítása (CI)
services: container-service
ms.service: container-service
author: zr-msft
ms.author: zarhoads
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: e46e2c2933ee9afda860b68b10c135ac75a5d247
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263927"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>Oktatóanyag: üzembe helyezés a GitHubról az Azure Kubernetes szolgáltatásba (ak) a Jenkins folyamatos integrációja és üzembe helyezése révén

Ez az oktatóanyag üzembe helyez egy minta alkalmazást a GitHubról egy [Azure Kubernetes Service (ak)](/azure/aks/intro-kubernetes) fürtre a folyamatos integráció (CI) és a folyamatos üzembe helyezés (CD) a Jenkins-ben való beállításával. Így amikor a véglegesítések a GitHubba való küldésével frissíti az alkalmazást, a Jenkins automatikusan futtat egy új tárolót, leküldi a tároló lemezképeit Azure Container Registry (ACR), majd futtatja az alkalmazást az AK-ban. 

Ebben az oktatóanyagban a következő feladatokat hajtja végre:

> [!div class="checklist"]
> * Helyezzen üzembe egy minta Azure vote-alkalmazást egy AK-fürtön.
> * Hozzon létre egy alapszintű Jenkins-projektet.
> * Állítsa be a Jenkins hitelesítő adatait az ACR-sel való interakcióhoz.
> * Hozzon létre egy Jenkins Build-feladatot és egy GitHub-webhookot az automatizált buildekhez.
> * Tesztelje a CI/CD-folyamatot, hogy a GitHub-kód véglegesíte alapján frissítsen egy alkalmazást az AK-ban.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz ezekre az elemekre:

- A Kubernetes, a git, a CI/CD és a Container images alapvető ismerete

- [AK-fürt][aks-quickstart] és `kubectl` az AK- [fürt hitelesítő adataival][aks-credentials] konfigurálva

- Egy [Azure Container Registry (ACR) beállításjegyzék][acr-quickstart], az ACR bejelentkezési kiszolgáló neve, valamint az [ACR-beállításjegyzékkel való HITELESÍTÉSre][acr-authentication] konfigurált AK-fürt

- Az Azure CLI 2.0.46 vagy újabb verziója telepítve és konfigurálva van. A verzió megkereséséhez futtassa a @ no__t-0 parancsot. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését][install-azure-cli]ismertető témakört.

- A [Docker telepítve van][docker-install] a fejlesztői rendszeren

- Egy GitHub-fiók, a [GitHub személyes hozzáférési jogkivonata][git-access-token], és a git-ügyfél telepítve van a fejlesztői rendszeren

- Ha a Jenkins üzembe helyezése helyett a saját Jenkins-példányát adja meg, akkor a Jenkins-példánynak [telepítve és konfigurálva][docker-install] kell lennie a Docker-nek, és konfigurálnia kell a [kubectl][kubectl-install].

## <a name="prepare-your-app"></a>Az alkalmazás előkészítése

Ebben a cikkben egy minta Azure vote-alkalmazást használunk, amely egy vagy több hüvelyben üzemeltetett webes felületet, valamint egy második Pod üzemeltetési Redis tartalmaz az ideiglenes adattároláshoz. A Jenkins és az AK automatikus üzembe helyezése előtt először manuálisan készítse elő és telepítse az Azure vote alkalmazást az AK-fürtre. Ez a manuális üzembe helyezés az alkalmazás egyik verziója, és lehetővé teszi az alkalmazás működés közbeni megtekintését.

> [!NOTE]
> A minta Azure vote-alkalmazás egy Linux-Pod-t használ, amely egy Linux-csomóponton fut. A cikkben leírt folyamat a Windows Server-csomóponton ütemezett Windows Server Pod-ra is működik.

A következő GitHub-tárházat a minta alkalmazáshoz – [https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis). Ahhoz, hogy elágaztassa a példatárt a saját GitHub-fiókja felé, kattintson a **Fork** (Elágaztatás) gombra a jobb felső sarokban.

Az elágazás klónozása a fejlesztői rendszeren. Győződjön meg arról, hogy a tárház klónozásakor a elágazás URL-címét használja:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Váltson a klónozott elágazás könyvtárára:

```console
cd azure-voting-app-redis
```

A minta alkalmazáshoz szükséges tároló-lemezképek létrehozásához használja a *Docker-levélírás. YAML* fájlt `docker-compose` értékkel:

```console
docker-compose up -d
```

A rendszer lehúzta a szükséges alapképeket, és az alkalmazás tárolóit is létrehozta. Ezután a [Docker images][docker-images] paranccsal megtekintheti a létrehozott rendszerképet. Három rendszerkép lett letöltve vagy jött létre. Az `azure-vote-front` rendszerkép tartalmazza az alkalmazást, és a `nginx-flask` rendszerképet használja alapként. A `redis` rendszerkép a Redis-példány elindítására szolgál:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Az *Azure-vote-elülső* tároló rendszerképének az ACR-be való leküldéséhez kérje le az ACR bejelentkezési kiszolgálóját az az [ACR List][az-acr-list] paranccsal. A következő példa beolvassa az ACR bejelentkezési kiszolgálójának címeit az *myResourceGroup*nevű erőforráscsoport beállításjegyzékében:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

A [Docker tag][docker-tag] paranccsal címkézze fel a rendszerképet az ACR bejelentkezési kiszolgálójának nevével és `v1` verziószámával. Adja meg saját `<acrLoginServer>` nevet az előző lépésben:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Végül küldje le az *Azure-vote-elülső* rendszerképet az ACR-beállításjegyzékbe. Újra cserélje le a `<acrLoginServer>` értéket a saját ACR-beállításjegyzék bejelentkezési kiszolgálójának nevére, például `myacrregistry.azurecr.io`:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>A minta alkalmazás üzembe helyezése az AK-ban

A minta alkalmazás az AK-fürtön való üzembe helyezéséhez használhatja az Azure vote adattár-tárház gyökerében található Kubernetes manifest-fájlt. Nyissa meg az *Azure-vote-all-in-One-Redis. YAML* jegyzékfájlt egy szerkesztővel, például `vi`. Az `microsoft` helyére az ACR bejelentkezési kiszolgálójának nevét írja be. Ez az érték a manifest-fájl **47** -es sorában található:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Ezután a [kubectl Apply][kubectl-apply] parancs használatával telepítse az alkalmazást az AK-fürtre:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Létrejön egy Kubernetes terheléselosztó szolgáltatás, amellyel elérhetővé teheti az alkalmazást az interneten. Ez eltarthat pár percig. A terheléselosztó telepítésének előrehaladásának figyeléséhez használja a [kubectl Get Service][kubectl-get] parancsot a `--watch` argumentummal. Miután az *EXTERNAL-IP* cím *pending* állapotról egy *IP-címre* változik, a `Control + C` billentyűparanccsal állítsa le a kubectl figyelési folyamatát.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Az alkalmazás működés közbeni megtekintéséhez nyisson meg egy webböngészőt a szolgáltatás külső IP-címére. Megjelenik az Azure vote alkalmazás az alábbi példában látható módon:

![Azure-beli szavazási alkalmazás fut az AK-ban](media/aks-jenkins/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>A Jenkins üzembe helyezése Azure-beli virtuális gépen

A Jenkins a cikkben való használatának gyors üzembe helyezéséhez a következő parancsfájllal telepítheti az Azure-beli virtuális gépeket, konfigurálhatja a hálózati hozzáférést, és elvégezheti a Jenkins alapszintű telepítését. A Jenkins és az AK-fürt közötti hitelesítéshez a szkript átmásolja a Kubernetes konfigurációs fájlját a fejlesztői rendszerből a Jenkins rendszerbe.

> [!WARNING]
> Ez a példa a bemutató céljára szolgál, amellyel gyorsan kiépíthető egy Azure-beli virtuális gépen futó Jenkins-környezet. Az Azure Custom script bővítmény használatával konfigurálja a virtuális gépet, majd megjeleníti a szükséges hitelesítő adatokat. A *~/.Kube/config* a Jenkins virtuális gépre másolódik.

Futtassa a következő parancsokat a szkript letöltéséhez és futtatásához. Tekintse át az összes parancsfájl tartalmát a futtatása előtt – [https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh).

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

A virtuális gép létrehozása néhány percet vesz igénybe, és telepíti a szükséges összetevőket a Docker és a Jenkins számára. A parancsfájl befejezését követően a a Jenkins-kiszolgáló és az irányítópult zárolásának feloldására szolgáló kulcsot ad vissza, ahogy az a következő példában látható:

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Nyisson meg egy webböngészőt a megjelenő URL-címen, és adja meg a feloldási kulcsot. A Jenkins-konfiguráció befejezéséhez kövesse a képernyőn megjelenő utasításokat:

- Válassza a **javasolt beépülő modulok telepítése** lehetőséget.
- Hozza létre az első rendszergazdai felhasználót. Adjon meg egy felhasználónevet, például az *azureuser*nevet, majd adja meg a saját biztonságos jelszavát. Végül gépelje be teljes nevét és e-mail-címét.
- Válassza a **Save and Finish** (Mentés és befejezés) elemet
- Amint Jenkins készen áll, kattintson a **Start using Jenkins** (Jenkins használatának megkezdése) elemre
    - Ha a webböngésző Jenkins használatának megkezdésekor egy üres lapot jelenít meg, indítsa újra a Jenkins szolgáltatást. A szolgáltatás újraindításához az SSH-t a Jenkins-példány nyilvános IP-címére, és írja be a következőt: `sudo service jenkins restart`. A szolgáltatás újraindítása után frissítse a webböngészőt.
- Jelentkezzen be a Jenkins-be a telepítési folyamat során létrehozott felhasználónévvel és jelszóval.

## <a name="create-a-jenkins-environment-variable"></a>Jenkins környezeti változó létrehozása

A Jenkins környezeti változó az ACR bejelentkezési kiszolgáló nevének tárolására szolgál. Ez a változó a Jenkins-Build feladataiban hivatkozik. A környezeti változó létrehozásához hajtsa végre a következő lépéseket:

- A Jenkins-portál bal oldalán válassza a **Jenkins kezelése** > **rendszer konfigurálása** lehetőséget.
- A **globális tulajdonságok**területen válassza a **környezeti változók**elemet. Adjon hozzá egy `ACR_LOGINSERVER` nevű változót és az ACR bejelentkezési kiszolgáló értékét.

    ![Jenkins környezeti változók](media/aks-jenkins/env-variables.png)

- Ha elkészült, kattintson a **Save (Mentés** ) gombra a Jenkins konfigurációs oldalának alján.

## <a name="create-a-jenkins-credential-for-acr"></a>Jenkins-hitelesítő adatok létrehozása az ACR-hez

Annak engedélyezéséhez, hogy a Jenkins felépítse, majd leküldi a tárolók frissített adatait az ACR-be, meg kell adnia az ACR hitelesítő adatait. Ez a hitelesítés Azure Active Directory egyszerű szolgáltatásokat használhatja. Az előfeltételekben konfigurálta a szolgáltatást az AK-fürthöz tartozó egyszerű szolgáltatáshoz az ACR-beállításjegyzék *olvasói* engedélyeivel. Ezek az engedélyek lehetővé teszik az AK-fürt számára a rendszerképek *lekérését* az ACR-beállításjegyzékből. A CI/CD folyamat során a Jenkins az alkalmazások frissítései alapján új tároló lemezképeket hoz létre, *és ezeket a* képeket az ACR-beállításjegyzékbe kell küldenie. A szerepkörök és engedélyek elkülönítéséhez most konfigurálja a Jenkins szolgáltatáshoz tartozó egyszerű szolgáltatásnevet az ACR-beállításjegyzék *közreműködői* engedélyeivel.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>Egyszerű szolgáltatásnév létrehozása a Jenkins számára az ACR használatához

Először hozzon létre egy egyszerű szolgáltatásnevet az az [ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] parancs használatával:

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

Jegyezze fel a kimenetében megjelenő *AppID* és *jelszót* . Ezeket az értékeket a következő lépésekkel állíthatja be a Jenkins-ben a hitelesítőadat-erőforrás konfigurálásához.

Szerezze be az ACR-beállításjegyzék erőforrás-AZONOSÍTÓját az az [ACR show][az-acr-show] parancs használatával, és tárolja változóként. Adja meg az erőforráscsoport nevét és az ACR nevét:

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

Most hozzon létre egy szerepkör-hozzárendelést, hogy hozzárendelje az egyszerű szolgáltatás *közreműködői* jogosultságait az ACR-beállításjegyzékhez. Az alábbi példában adja meg saját *AppID* az előző parancs kimenetében az egyszerű szolgáltatásnév létrehozásához:

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Hitelesítőadat-erőforrás létrehozása a Jenkinsben az ACR egyszerű szolgáltatásához

Az Azure-ban létrehozott szerepkör-hozzárendeléssel mostantól egy Jenkins hitelesítőadat-objektumában tárolja az ACR hitelesítő adatait. Ezek a hitelesítő adatok a Jenkins-felépítési feladatban vannak hivatkozva.

A Jenkins-portál bal oldalán kattintson a **hitelesítő adatok**elemre  > **Jenkins** > **globális hitelesítő adatok (korlátlan)**  > **hitelesítő adatok hozzáadása**

Győződjön meg arról, hogy a hitelesítő adat a **username jelszóval** , és adja meg a következő elemeket:

- **Username (Felhasználónév** ) – az a *AppID* , amelyet a rendszer az ACR-beállításjegyzékkel való hitelesítésre hozott létre.
- **Password (jelszó** ) – az az egyszerű szolgáltatásnév *jelszava* , amely az ACR-beállításjegyzékkel való hitelesítésre lett létrehozva.
- **Azonosító** – hitelesítő adatok azonosítója, például *ACR – hitelesítő adatok*

Ha elkészült, a hitelesítő adatok űrlapja a következő példához hasonlóan néz ki:

![Jenkins hitelesítőadat-objektum létrehozása az egyszerű szolgáltatásnév adataival](media/aks-jenkins/acr-credentials.png)

Kattintson **az OK** gombra, és térjen vissza a Jenkins-portálra.

## <a name="create-a-jenkins-project"></a>Jenkins-projekt létrehozása

A Jenkins-portál kezdőlapján válassza az **új elem** lehetőséget a bal oldali oldalon:

1. Adja meg az *Azure-vote* nevet a feladattípusként. Válassza a **Freestyle projekt**elemet, majd kattintson **az OK gombra** .
1. Az **általános** szakaszban válassza a **GitHub-projekt** elemet, és adja meg az elágazó tárház URL-címét (például *https: \//GitHub. com/\<your-GitHub-Account @ no__t-5/Azure-szavazás-app-Redis*
1. A **forráskód-kezelés** szakaszban válassza a **git**lehetőséget, írja be az elágazó tárházat *. git* URL-cím, például *https: \//GitHub. com/\<your-GitHub-Account @ no__t-6/Azure-voting-app-Redis. git*

1. Az **Eseményindítók létrehozása** szakaszban válassza ki a **GitHub Hook-eseményindítót a gitscm Polling lekérdezéshez**
1. A **létrehozási környezet**területen válassza a **titkos szövegek vagy fájlok használata** lehetőséget.
1. A **kötések**területen válassza a **Hozzáadás**@no__t – 2**Felhasználónév és jelszó (elválasztva) lehetőséget.**
   - Adja meg `ACR_ID` értéket a **Felhasználónév változóhoz**, és `ACR_PASSWORD` értéket a **jelszó változóhoz** .

     ![Jenkins-kötések](media/aks-jenkins/bindings.png)

1. Válassza ki a következő típusú **Build** -lépést **, és használja** az alábbi szöveget:. Ez a szkript létrehoz egy új tároló-rendszerképet, és leküldi az ACR-beállításjegyzékbe.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Adjon hozzá egy újabb **Build-lépést** a következő típushoz: **Execute Shell** , és használja az alábbi szöveget. Ez a szkript frissíti az alkalmazás üzembe helyezését az AK-ban az ACR új tárolójának képével.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. Ha elkészült, kattintson a **Mentés**gombra.

## <a name="test-the-jenkins-build"></a>A Jenkins Build tesztelése

Mielőtt automatizálja a feladatot a GitHub-elkövetések alapján, először manuálisan kell tesztelni a Jenkins-buildet. Ez a manuális Build ellenőrzi, hogy a feladatot helyesen konfigurálták-e, a megfelelő Kubernetes-hitelesítési fájl van-e érvényben, és hogy a hitelesítés az ACR-sel működik-e.

A projekt bal oldali menüjében válassza a **Létrehozás most**lehetőséget.

![Jenkins-teszt Build](media/aks-jenkins/test-build.png)

Az első összeállítás egy-két percet vesz igénybe, mivel a Docker-képrétegek le vannak húzva a Jenkins-kiszolgálóra. A későbbi buildek a gyorsítótárazott képrétegek segítségével javítják a létrehozási időt.

A létrehozási folyamat során a GitHub-adattár klónozott a Jenkins Build-kiszolgálóra. A rendszer létrehoz egy új tárolót, és leküldi az ACR-beállításjegyzékbe. Végül az AK-fürtön futó Azure vote alkalmazás frissül az új rendszerkép használatára. Mivel az alkalmazás kódjának módosítása nem történt meg, az alkalmazás nem változik meg, ha a minta alkalmazást egy böngészőben tekinti meg.

A létrehozási feladatok befejezését követően kattintson a Build **#1** elemre a létrehozási előzmények szakaszban. Válassza ki a **konzol kimenetét** , és tekintse meg a kiépítési folyamat kimenetét. Az utolsó sorban a sikeres buildnek kell szerepelnie.

## <a name="create-a-github-webhook"></a>GitHub-webhook létrehozása

A sikeres manuális Build befejezésével mostantól integrálhatja a GitHubot a Jenkins buildbe. Egy webhook a Jenkins-Build feladatainak futtatására szolgál minden alkalommal, amikor kódot véglegesítenek a GitHubon. A GitHub-webhook létrehozásához hajtsa végre a következő lépéseket:

1. Tallózással keresse meg az elágazó GitHub-tárházat egy böngészőben.
1. Válassza a **Beállítások**, majd a bal oldali **webhookok** lehetőséget.
1. Válassza a **webhook hozzáadását**. A *hasznos adatok URL-címéhez*írja be a következőt: `http://<publicIp:8080>/github-webhook/`, ahol a `<publicIp>` a Jenkins-kiszolgáló IP-címe. Ügyeljen arra, hogy tartalmazza a záró/. Hagyja meg a többi alapértelmezett értéket a tartalomtípusnál, és aktiválja a *leküldéses* eseményeket.
1. Válassza a **webhook hozzáadása**elemet.

    ![GitHub-webhook létrehozása a Jenkinshez](media/aks-jenkins/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>A teljes CI/CD-folyamat tesztelése

Most tesztelheti a teljes CI/CD-folyamatot. Amikor elküld egy kódot a GitHubra, a következő lépések történnek:

1. A GitHub-webhook eléri a Jenkinst.
1. A Jenkins elindítja a felépítési feladatot, és lekéri a legújabb kódot a GitHubról.
1. A Docker-Build a frissített kóddal lett elindítva, és az új tároló képe a legújabb Build-számmal van megjelölve.
1. Az új tároló rendszerképét a rendszer leküldi Azure Container Registry.
1. Az alkalmazás üzembe helyezése az Azure Kubernetes szolgáltatásban a legújabb tároló lemezképével történik a Azure Container Registry beállításjegyzékből.

A fejlesztői gépen nyissa meg a klónozott alkalmazást egy Kódszerkesztő használatával. A */Azure-vote/Azure-vote* könyvtárban nyissa meg a **config_file. cfg**nevű fájlt. Az alábbi példában látható módon frissítse a fájlban szereplő szavazati értékeket a macskák és kutyák számára.

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Ha frissítve van, mentse a fájlt, véglegesítse a módosításokat, majd küldje le ezeket a GitHub-tárház villájának. A GitHub-webhook új Build-feladatot indít a Jenkinsben. A Jenkins webes irányítópultján figyelje a létrehozási folyamatot. Eltarthat néhány másodpercig, hogy lekérje a legújabb kódot, létrehozza és leküldi a frissített lemezképet, és üzembe helyezi a frissített alkalmazást az AK-ban.

A Build befejezése után frissítse a minta Azure vote-alkalmazás webböngészőjét. A módosítások megjelennek az alábbi példában látható módon:

![A Jenkins Build feladata által frissített Azure-beli szavazás](media/aks-jenkins/azure-vote-updated.png)

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan használhatja a Jenkins-t egy CI/CD-megoldás részeként. Az AK integrálható más CI/CD-megoldásokkal és Automation-eszközökkel, például az [Azure DevOps-projekttel][azure-devops] , vagy [egy AK-fürt létrehozásával a Ansible][aks-ansible]használatával.

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
[acr-authentication]: cluster-container-registry-integration.md
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[azure-devops]: ../devops-project/azure-devops-project-aks.md
[aks-ansible]: ../ansible/ansible-create-configure-aks.md
