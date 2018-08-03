---
title: A Jenkins folyamatos üzembe helyezés a Kubernetes Azure Kubernetes Service-ben
description: A jenkins üzembe helyezése és frissítése az Azure Kubernetes Service-ben a Kubernetes tárolóalapú alkalmazás folyamatos üzembe helyezés folyamat automatizálása
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: a1a6799bc049fea829f8e32d12705e26e3a41dc0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425758"
---
# <a name="continuous-deployment-with-jenkins-and-azure-kubernetes-service"></a>Folyamatos üzembe helyezés a Jenkins és az Azure Kubernetes Service-ben

Ez a dokumentum bemutatja, hogyan állíthat be egy alapszintű folyamatos üzembe helyezést megvalósító munkafolyamat a Jenkins és a egy Azure Kubernetes Service (AKS)-fürt.

Példa-munkafolyamat a következő lépésekből áll:

> [!div class="checklist"]
> * Kubernetes-fürthöz az Azure vote-alkalmazás üzembe helyezése.
> * Az Azure vote alkalmazás kódjának frissítése, és leküldése egy GitHub-adattár, amely elindítja a folyamatos üzembe helyezési folyamatot.
> * Jenkins klónozza az adattárat, és a egy új tárolórendszerképet a frissített kóddal épít.
> * Ez a rendszerkép Azure Container Registry (ACR) leküldésekor.
> * Az alkalmazás fut az AKS-fürtöt a frissül az új tárolórendszerképet.

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben szereplő lépések végrehajtásához a következő elemeket kell.

- Kubernetes, a Git, a CI/CD és az Azure Container Registry (ACR) alapszintű ismerete.
- Egy [Azure Kubernetes Service (AKS)-fürt] [ aks-quickstart] és [konfigurálni az AKS hitelesítő adatok] [ aks-credentials] a fejlesztői rendszeren.
- Egy [Azure Container Registry (ACR) beállításjegyzék][acr-quickstart], az ACR bejelentkezési kiszolgáló nevét, és [ACR hitelesítő adatok] [ acr-authentication] lekérést és a hozzáférést.
- Az Azure CLI telepítve van a fejlesztői rendszeren.
- A docker telepítve van a fejlesztői rendszeren.
- GitHub-fiók [Githubhoz használt személyes hozzáférési tokent][git-access-token], és a Git-ügyfél telepítve van a fejlesztői rendszeren.

## <a name="prepare-application"></a>Alkalmazás előkészítése

Az Azure vote alkalmazást a dokumentumban az egy vagy több podok és a egy második pod Redis üzemeltető ideiglenes adattárolásra üzemeltetett webes felületet tartalmaz.

A Jenkins építése előtt / AKS-integráció, előkészítése és üzembe helyezése az Azure vote alkalmazást, az AKS-fürt. Gondoljon erre úgy, mint az alkalmazás első verziója.

A következő GitHub-tárház elágaztatása.

```
https://github.com/Azure-Samples/azure-voting-app-redis
```

Miután létrejött az elágazást, klónozza a fejlesztői rendszerhez. Győződjön meg arról, hogy az URL-címét az elágazásában használunk, ha a tárház klónozása.

```bash
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Módosítsa a könyvtárakat, hogy a klónozott könyvtárból dolgozzon.

```bash
cd azure-voting-app-redis
```

Futtassa a `docker-compose.yaml` fájlt, hogy létrehozza a `azure-vote-front` tárolórendszerképet, és az alkalmazás indítása.

```bash
docker-compose up -d
```

Amikor elkészült, a [docker-rendszerképek] [ docker-images] paranccsal megtekintheti a létrehozott rendszerképet.

Figyelje meg, hogy három rendszerkép lett letöltve vagy jött létre. Az `azure-vote-front` rendszerkép tartalmazza az alkalmazást, és a `nginx-flask` rendszerképet használja alapként. A `redis` rendszerkép használatával indítható el egy Redis-példány.

```console
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Az ACR bejelentkezési kiszolgáló az első a [az acr list] [ az-acr-list] parancsot. Ellenőrizze, hogy frissítse az erőforráscsoport nevét az erőforráscsoportot az ACR regisztrációs adatbázis üzemeltetéséhez.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Használja a [docker címke] [ docker-tag] fel a rendszerképet a bejelentkezési kiszolgáló nevét és a egy verziószáma parancs `v1`.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Frissítse az ACR bejelentkezési kiszolgáló értéke az ACR bejelentkezési kiszolgálójának nevét, és leküldéses a `azure-vote-front` rendszerképet a regisztrációs adatbázisba.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-application-to-kubernetes"></a>Kubernetes-alkalmazás üzembe helyezése

Egy Kubernetes-jegyzékfájl lehet az Azure vote-tárház gyökérkönyvtárában található, és helyezze üzembe az alkalmazást a Kubernetes-fürt segítségével.

Először frissítse a **azure-vote-all-in-one-redis.yaml** Alkalmazásjegyzék-fájl helyét, az ACR-beállításjegyzékbe. Nyissa meg a fájlt bármilyen szövegszerkesztőben, és cserélje le `microsoft` az ACR bejelentkezési kiszolgálójának nevét. Ez az érték a jegyzékfájl **47**. sorában található.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Ezután a [a kubectl a alkalmazni] [ kubectl-apply] parancsot az alkalmazás futtatásához. A parancs elemzi jegyzékfájlt, és létrehozza a meghatározott Kubernetes-objektumokat.

```bash
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

A [Kubernetes-szolgáltatást] [ kubernetes-service] hoz létre, tegye elérhetővé az alkalmazást az interneten. Ez eltarthat pár percig.

A folyamat állapotának monitorozásához használja [kubectl get service][kubectl-get] parancsot a `--watch` argumentummal.

```bash
kubectl get service azure-vote-front --watch
```

Kezdetben az *azure-vote-front* szolgáltatás *EXTERNAL-IP* értéke *pending* állapotú.

```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Miután az *EXTERNAL-IP* cím *pending* állapotról egy *IP-címre* változik, a `control+c` billentyűparanccsal állítsa le a kubectl figyelési folyamatát.

```
azure-vote-front   10.0.34.242   13.90.150.118   80:30676/TCP   2m
```

Az alkalmazás megtekintéséhez navigáljon a külső IP-címhez.

![Egy Azure-beli Kubernetes-fürt képe](media/aks-jenkins/azure-vote-safari.png)

## <a name="deploy-jenkins-to-vm"></a>A Jenkins virtuális gép üzembe helyezése

A parancsfájl lett előre létrehozott virtuális gép üzembe helyezése, a hálózati hozzáférés konfigurálása és a egy alapszintű Jenkins-telepítés befejezéséhez. Ezenkívül a szkript átmásolja a Kubernetes konfigurációs fájl a fejlesztői rendszerhez a Jenkins rendszer. Ezt a fájlt használja a Jenkins és az AKS-fürt közötti hitelesítéshez.

Töltse le és futtassa a parancsfájlt a következő parancsok futtatásával. Az alábbi URL-címet is használható a parancsfájl tartalmát.

> [!WARNING]
> Ez a példaszkript bemutató céljából a gyors üzembe helyezése a Jenkins-környezet egy Azure-beli virtuális gépen futó van. Az Azure egyéni szkriptek futtatására szolgáló bővítmény használatával konfiguráljon egy virtuális Gépet, és jelenítheti meg a szükséges hitelesítő adatokat. A *~/.kube/config* a Jenkins virtuális gép másolja.

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

A szkript befejezése után azt jelenít meg a Jenkins-kiszolgáló címének kulcsként jól Jenkins zárolásának feloldásához. Tallózással keresse meg az URL-címet, adja meg a kulcsot, és a következő a képernyőn megjelenő utasításokat követve fejezze be a Jenkins konfigurációja.

```console
Open a browser to http://52.166.118.64:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

## <a name="jenkins-environment-variables"></a>A Jenkins környezeti változók

A Jenkins környezeti változó segítségével tárolásához az Azure Container Registry (ACR) bejelentkezési kiszolgálójának nevét. Ez a változó hivatkozik a Jenkins folyamatos üzembe helyezés során.

A Jenkins-felügyeleti portálon, a kattintson **Jenkins kezelése** > **rendszer konfigurálása**.

Alatt **globális tulajdonságok**, jelölje be **környezeti változók**, és adja hozzá a nevű változó `ACR_LOGINSERVER` és a egy értéke az ACR bejelentkezési kiszolgálójának.

![A Jenkins környezeti változók](media/aks-jenkins/env-variables.png)

Amikor végzett, kattintson a **mentése** a Jenkins lapon.

## <a name="jenkins-credentials"></a>A Jenkins hitelesítő adatok

Most már az ACR hitelesítő adatok tárolása a Jenkins hitelesítő objektumot. A Jenkins létrehozási feladatot során hivatkozott ezeket a hitelesítő adatokat.

A Jenkins-felügyeleti portálon, azon kattintson **hitelesítő adatok** > **Jenkins** > **globális hitelesítő adatokat (korlátlan)**  >   **Hitelesítő adatok hozzáadása**.

Győződjön meg arról, hogy a hitelesítő adatok típusa **felhasználónév, jelszó** , és adja meg a következő elemek:

- **Felhasználónév** -szolgáltatás egyszerű használjanak a hitelesítéshez, az ACR-beállításjegyzék azonosítója.
- **Jelszó** -ügyfélkulcsot hitelesítés az ACR-beállításjegyzék egyszerű szolgáltatás használatával.
- **ID** -Hitelesítőadat-azonosító például `acr-credentials`.

Amikor végzett, a hitelesítő adatok képernyőn ábrán láthatóhoz hasonlónak kell kinéznie:

![ACR hitelesítő adatok](media/aks-jenkins/acr-credentials.png)

Kattintson a **OK** és térjen vissza a Jenkins-felügyeleti portálon.

## <a name="create-jenkins-project"></a>A Jenkins-projekt létrehozása

A Jenkins-felügyeleti portálon kattintson a **új elem**.

Adja meg a projekt nevét, például `azure-vote`válassza **Freestyle Project**, és kattintson a **OK**.

![Jenkins-projekt](media/aks-jenkins/jenkins-project.png)

A **általános**válassza **GitHub-projekt** , és adja meg az Azure vote GitHub-projekt elágazása URL-CÍMÉT.

![GitHub-projekt](media/aks-jenkins/github-project.png)

A **Source Code Management**válassza **Git**, adja meg az Azure Vote GitHub-tárház elágazása URL.

A hitelesítő adatait, kattintson a és **Hozzáadás** > **Jenkins**. Alatt **típusú**válassza **titkos szöveg** , és adja meg a [Githubhoz használt személyes hozzáférési tokent] [ git-access-token] , a titkos kulcsot.

Válassza ki **Hozzáadás** végeztével.

![GitHub hitelesítő adatok](media/aks-jenkins/github-creds.png)

A **hozhat létre eseményindítókat**válassza **GitHub hook trigger for GITScm lekérdezés**.

![A Jenkins triggerek létrehozását.](media/aks-jenkins/build-triggers.png)

A **környezetet hozhat létre**válassza **használjon titkos szövegek vagy**.

![A Jenkins-összeállító környezetet](media/aks-jenkins/build-environment.png)

Alatt **kötések**, jelölje be **Hozzáadás** > **felhasználónév és jelszó (vesszővel)**.

Adja meg `ACR_ID` számára a **felhasználónév-változó**, és `ACR_PASSWORD` számára a **Jelszóváltozó**.

![A Jenkins-kötések](media/aks-jenkins/bindings.png)

Adjon hozzá egy **összeállítása lépés** típusú **hajtsa végre a rendszerhéj** és használja a következő szöveget. Ez a szkript összeállít egy új tárolórendszerképet, majd leküldi azt az ACR-beállításjegyzékbe.

```bash
# Build new image and push to ACR.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
docker build -t $WEB_IMAGE_NAME ./azure-vote
docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
docker push $WEB_IMAGE_NAME
```

Adjon hozzá egy másik **összeállítása lépés** típusú **hajtsa végre a rendszerhéj** és használja a következő szöveget. Ez a szkript frissíti a Kubernetes üzemelő példányt.

```bash
# Update kubernetes deployment with new image.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
```

Ha befejeződött, kattintson a **mentése**.

## <a name="test-the-jenkins-build"></a>A Jenkins-buildelés tesztelése

A folytatás előtt tesztelje a Jenkins-buildelés. Ez igazolja, hogy a fordítási feladatot megfelelően van konfigurálva, a megfelelő Kubernetes hitelesítési fájl van beállítva, és hogy a megfelelő ACR hitelesítő adatokat adtak meg.

Kattintson a **hozhat létre most** a projekt a bal oldali menüben.

![A Jenkins létrehozási tesztelése](media/aks-jenkins/test-build.png)

Ez a folyamat során a GitHub-adattárat a Jenkins-buildkiszolgáló van klónozni. Egy új tárolórendszerkép összeállítása és leküldött az ACR-beállításjegyzékbe. Végül az Azure vote alkalmazást, az AKS-fürtön futó frissül, és az új rendszerképet használja. Az alkalmazás kódja nem módosultak, mivel az alkalmazás nem változik.

A folyamat befejeződése után kattintson a **összeállítása #1** alatt hozhat létre előzmények, és válassza ki **konzolkimenet** az összeállítási folyamat során az összes kimenetének megtekintése. A végső vonal jelzi a build sikeres létrehozása.

## <a name="create-github-webhook"></a>GitHub-webhook létrehozása

Ezután környezet igénybe vételét az alkalmazás-adattárat a Jenkins-build kiszolgálóhoz, hogy minden véglegesítés az új build aktiválódik.

1. Keresse meg az elágaztatott GitHub-adattárában.
2. Válassza ki **beállítások**, majd **Webhookok** a bal oldalon.
3. Válassza ki a **webhook hozzáadása**. Az a *hasznos adat URL-cím*, adja meg `http://<publicIp:8080>/github-webhook/` ahol `publicIp` a Jenkins-kiszolgáló IP-címe. Győződjön meg arról, hogy tartalmazza a záró /. Az alapértelmezett tartalom típusa és az eseményindító meghagyása *leküldéses* eseményeket.
4. Válassza ki **webhook hozzáadása**.

    ![GitHub-webhook](media/aks-jenkins/webhook.png)

## <a name="test-cicd-process-end-to-end"></a>Teljes körű CI/CD-folyamat tesztelése

A fejlesztői gépén nyissa meg a klónozott alkalmazás egy Kódszerkesztő.

Alatt a **/azure-vote/azure-vote** könyvtárban keresse meg a fájlnevet nevű **config_file.cfg**. Frissítse a szavazási ebben a fájlban valami eltérő macskák és kutyájával lenni.

Az alábbi példa bemutatja, és a frissített **config_file.cfg** fájlt.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Amikor végzett, mentse a fájlt, a módosítások véglegesítéséhez és azokat a GitHub-adattár az elágazásában leküldéses... A véglegesítés befejezése után a GitHub-webhook eseményindítók új Jenkins-buildelés, amely frissíti a tárolórendszerképet, és az AKS üzembe helyezés. A létrehozási folyamat a Jenkins felügyeleti konzolon figyelheti.

A létrehozás befejezése után újra keresse meg a kérelem végpontot, hogy figyelje meg a változásokat.

![Az Azure vote frissítve](media/aks-jenkins/azure-vote-updated-safari.png)

Ezen a ponton egy egyszerű folyamatos üzembe helyezési folyamat befejeződött. A lépéseket, és ebben a példában bemutatott konfigurációk használható egy megbízhatóbb és éles használatra kész buildelési automation fejlesztéséhez.

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az-acr-list
[acr-authentication]: ../container-registry/container-registry-auth-aks.md
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli