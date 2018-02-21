---
title: "Az az Azure Tárolószolgáltatásban Kubernetes Jenkins folyamatos üzembe helyezés"
description: "A folyamatos üzembe helyezés folyamat telepítésének és frissítésének a tárolóalapú alkalmazást az Azure Tárolószolgáltatásban Kubernetes Jenkins automatizálása"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 1293fda45602203570a0f7f75481f67bdcb6edf3
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2018
---
# <a name="continuous-deployment-with-jenkins-and-azure-container-service"></a>Azure Tárolószolgáltatás és Jenkins folyamatos üzembe helyezés

Ez a dokumentum bemutatja, hogyan állíthat be egy alapszintű folyamatos üzembe helyezés munkafolyamat Jenkins és az Azure-tároló szolgáltatás (AKS) fürt között. 

A példa-munkafolyamat a következő lépésekből áll:

> [!div class="checklist"]
> * Az Azure szavazattal alkalmazás Kubernetes fürthöz való telepítéséhez.
> * Az Azure szavazattal alkalmazáskód frissítése, és küldje le a GitHub-adattár, amely a folyamatos üzembe helyezési folyamat elindítja.
> * Jenkins a tárház klónokat, és a frissített kód új tároló képet alkot.
> * Ez a rendszerkép fejlesztőre Azure tároló beállításjegyzék (ACR).
> * A AKS fürtben futó alkalmazás frissül az új tároló lemezkép.

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben szereplő lépések végrehajtásához a következőkre van szüksége.

- Alapvető ismeretekkel Kubernetes, Git, CI/CD és Azure tároló beállításjegyzék (ACR).
- Egy [Azure tároló szolgáltatás (AKS) fürt] [ aks-quickstart] és [AKS hitelesítő adatok a konfigurált] [ aks-credentials] a fejlesztői rendszeren.
- Egy [Azure tároló beállításjegyzék (ACR) beállításjegyzékbeli][acr-quickstart], a ACR bejelentkezési kiszolgáló nevét, és [ACR hitelesítő adatok] [ acr-authentication] leküldéses és lekéréses hozzáféréssel.
- Az Azure CLI-t a fejlesztői rendszeren telepítve.
- A docker a fejlesztői rendszeren telepítve.
- GitHub-fiók [GitHub személyes hozzáférési jogkivonat][git-access-token], és a fejlesztői rendszeren Git-ügyféllel.

## <a name="prepare-application"></a>Alkalmazás előkészítése

Ez a dokumentum használt Azure szavazat-alkalmazás egy vagy több három munkaállomás-csoporttal, és egy második pod Redis üzemeltető ideiglenes tárolására tárolt webes felületet tartalmazza. 

A Jenkins létrehozása előtt / AKS integráció, előkészítése, és az Azure szavazattal alkalmazás AKS fürthöz való telepítéséhez. Használjon egy, az alkalmazás verziója.

Elágazás a következő GitHub-tárházban.

```
https://github.com/Azure-Samples/azure-voting-app-redis
```

Miután létrejött a elágazás, klónozza a fejlesztői rendszerhez. Győződjön meg arról, hogy az URL-CÍMÉT az elágazáshoz használunk, ha a tárház klónozása.

```bash
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Módosítsa a könyvtárat, hogy a klónozott könyvtárból dolgozik.

```bash
cd azure-voting-app-redis
```

Futtassa a `docker-compose.yaml` fájlt, hogy létrehozza a `azure-vote-front` tároló lemezképet és az alkalmazás elindítása.

```bash
docker-compose up -d
```

Befejezése után használja a [docker képek] [ docker-images] parancs a létrehozott kép megtekintéséhez.

Figyelje meg, hogy három képek letöltése vagy létrehozni. A `azure-vote-front` lemezképet tartalmazza az alkalmazás, és használja a `nginx-flask` kép alapjaként. A `redis` lemezkép használatával indítsa el a Redis-példányt.

```console
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Az ACR bejelentkezési kiszolgáló és a [az acr lista] [ az-acr-list] parancsot. Ügyeljen arra, hogy frissítse az erőforráscsoport neve az erőforráscsoportot, a ACR rendszerleíró adatbázis üzemeltetéséhez.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Használja a [docker címke] [ docker-tag] parancs használatával címkézhesse a kép és a kiszolgálói bejelentkezési nevet és egy verziószáma `v1`.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Frissítse az ACR bejelentkezési kiszolgáló értéket a ACR bejelentkezési kiszolgáló nevét, és leküldéses a `azure-vote-front` kép a beállításjegyzékhez. 

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-application-to-kubernetes"></a>Kubernetes alkalmazás telepítése

A jegyzékfájl lehet Kubernetes az Azure szavazattal tárház gyökérkönyvtárában található, és az alkalmazás Kubernetes fürthöz való telepítéséhez használható.

Először frissítse a **azure-vote-all-in-one-redis.yaml** jegyzékfájl a ACR beállításjegyzék helyét. Szövegszerkesztőben nyissa meg a fájlt, és cserélje le `microsoft` az ACR bejelentkezési kiszolgáló nevével. Ez az érték a jegyzékfájl **47**. sorában található.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Ezután használhatja a [kubectl létrehozása] [ kubectl-create] parancsot kell futtatni az alkalmazást. A parancs elemzi jegyzékfájlt, és létrehozza a meghatározott Kubernetes-objektumokat.

```bash
kubectl create -f azure-vote-all-in-one-redis.yaml
```

A [Kubernetes szolgáltatás] [ kubernetes-service] teszi közzé az alkalmazást az interneten jön létre. Ez eltarthat pár percig. 

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

## <a name="deploy-jenkins-to-vm"></a>Virtuális gép Jenkins telepítése

Egy parancsfájl volt előre létrehozott, a virtuális gép telepítése, konfigurálja a hálózati hozzáférést, fejezze be a Jenkins alapszintű telepítését. Emellett a parancsfájl másolja át a Kubernetes konfigurációs fájl a fejlesztői rendszerhez a Jenkins rendszerhez. Ez a fájl Jenkins és a AKS fürt közötti hitelesítéshez használt.

Töltse le és futtassa a parancsfájlt a következő parancsok futtatásával. Az alábbi URL-cím is használható tekintse át a parancsfájl tartalmát.

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

A parancsfájl befejezése után kulcsként jól Jenkins feloldásához kiírja a Jenkins kiszolgáló címét. Keresse meg az URL-címet, adja meg a kulcsot, és a következő a képernyőn megjelenő kérni fogja a Jenkins konfigurálásának befejezéséhez.

```console
Open a browser to http://52.166.118.64:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

## <a name="jenkins-environment-variables"></a>Jenkins környezeti változók

Egy Jenkins környezeti változó Azure tároló beállításjegyzék (ACR) bejelentkezési kiszolgálónév tárolására szolgál. Ez a változó a Jenkins folyamatos üzembe helyezés feladat során hivatkozik.

Kattintson a Jenkins felügyeleti portálon, a **kezelése Jenkins** > **rendszer konfigurálása**.

A **globális tulajdonságok**, jelölje be **környezeti változók**, és adja hozzá a nevű változó `ACR_LOGINSERVER` érték esetén a ACR bejelentkezési kiszolgáló.

![Jenkins környezeti változók](media/aks-jenkins/env-variables.png)

Amikor végzett, kattintson **mentése** az Jenkins-konfiguráció lapon.

## <a name="jenkins-credentials"></a>Jenkins hitelesítő adatok

Most tárolja egy Jenkins hitelesítő objektum ACR hitelesítő adatait. Ezeket a hitelesítő adatokat a Jenkins összeállítási feladat során hivatkozott.

Vissza a Jenkins felügyeleti portálon, kattintson **hitelesítő adatok** > **Jenkins** > **globális hitelesítő adatok (korlátlan)**  >   **Adja hozzá a hitelesítő adatok**.

Győződjön meg arról, hogy a hitelesítő adat típusú **felhasználónév és jelszó** , és írja be a következő elemek:

- **Felhasználónév** – a szolgáltatás egyszerű használjanak a hitelesítéshez a ACR rendszerleíró azonosítója.
- **Jelszó** -ügyfélkulcs a szolgáltatás egyszerű használat a ACR beállításjegyzék-val történő hitelesítéshez.
- **Azonosító** -azonosító például a hitelesítőadat- `acr-credentials`.

Amikor végzett, a hitelesítő adatok űrlap a lemezkép hasonlóan kell kinéznie:

![ACR hitelesítő adatok](media/aks-jenkins/acr-credentials.png)

Kattintson a **OK** és térjen vissza a Jenkins felügyeleti portálra.

## <a name="create-jenkins-project"></a>Jenkins projekt létrehozása

A Jenkins felügyeleti portálon, kattintson **új elem**.

Nevezze el a projekt, például `azure-vote`, jelölje be **Freestyle projekt**, és kattintson a **OK**. 

![Jenkins projekt](media/aks-jenkins/jenkins-project.png)

A **általános**, jelölje be **GitHub-projekt** , és írja be az URL-címet az elágazáshoz, az Azure szavazattal GitHub-projekthez tartozó.

![GitHub-projekt](media/aks-jenkins/github-project.png)

A **forrás kód felügyeleti**, jelölje be **Git**, adja meg az URL-címet az elágazáshoz, az Azure szavazattal GitHub-tárház. 

A hitelesítő adatokat, kattintson az és **Hozzáadás** > **Jenkins**. A **jellegű**, jelölje be **titkos szöveg** , és írja be a [GitHub személyes hozzáférési jogkivonat] [ git-access-token] , a titkos kulcsot. 

Válassza ki **Hozzáadás** végzett.

![GitHub hitelesítő adatok](media/aks-jenkins/github-creds.png)

A **Build eseményindítók**, jelölje be **GitHub hook eseményindítója a következőnek: GITScm lekérdezési**.

![Jenkins eseményindítók létrehozása](media/aks-jenkins/build-triggers.png)

A **telepítési környezet**, jelölje be **titkos szövegek vagy fájlok**.

![Jenkins összeállító környezetet](media/aks-jenkins/build-environment.png)

A **kötések**, jelölje be **Hozzáadás** > **felhasználónév és jelszó (elválasztott)**. 

Adja meg `ACR_ID` a a **tartozó felhasználónév változójának**, és `ACR_PASSWORD` a a **jelszó változót**.

![Jenkins kötések](media/aks-jenkins/bindings.png)

Adja hozzá a **összeállítása lépés** típusú **hajtható végre a rendszerhéj** és használja a következő szöveget. Ezt a parancsfájlt hoz létre egy új tároló lemezképet, és leküldéses értesítések azt a ACR beállításjegyzék.

```bash
# Build new image and push to ACR.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
docker build -t $WEB_IMAGE_NAME ./azure-vote
docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
docker push $WEB_IMAGE_NAME
```

Adja hozzá egy másik **összeállítása lépés** típusú **hajtható végre a rendszerhéj** és használja a következő szöveget. A parancsfájl a Kubernetes központi telepítést frissíti.

```bash
# Update kubernetes deployment with new image.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
```

Ezt követően kattintson **mentése**.

## <a name="test-the-jenkins-build"></a>Tesztelje a Jenkins összeállítása

A folytatás előtt tesztelje a Jenkins összeállítása. Ez azt ellenőrzi, hogy az összeállítási feladat megfelelően van konfigurálva, illetve a megfelelő Kubernetes hitelesítési fájlt helyben, és, hogy vannak-e megadva ACR szükséges hitelesítő adatokat.

Kattintson a **Build most** a projekt a bal oldali menüben. 

![Jenkins build tesztelése](media/aks-jenkins/test-build.png)

A folyamat során a GitHub-tárházban klónozták a Jenkins build kiszolgálóra. Új tároló-lemezkép összeállítása és a ACR beállításjegyzék leküldve. Végül az Azure szavazattal alkalmazás AKS fürtben futó frissül az új rendszerképet használja. Az alkalmazás kódja nem módosult, mert az alkalmazás nem változik.

Ha a folyamat befejeződött, kattintson a **#1 build** az előzmények építsenek, és válassza ki **konzol kimeneti** a felépítési folyamat összes eredményének megtekintéséhez. Utolsó sorának sikeres build jelzi. 

## <a name="create-github-webhook"></a>GitHub-webhook létrehozása

A következő környezet igénybe vételét az alkalmazás tárház a Jenkins build kiszolgálóra, hogy bármely-érvényesítés: az új buildverziót elindul.

1. Tallózással keresse meg a villás GitHub-tárházban.
2. Válassza a **Settings** (Beállítások) lehetőséget, majd az **Integrations & services** (Integrációk és szolgáltatások) lehetőséget a bal oldalon.
3. Válasszon **hozzáadása szolgáltatás**, adja meg `Jenkins (GitHub plugin)` a Szűrő mezőbe, és válasszon a beépülő modul.
4. Az a Jenkins hook URL-CÍMÉT, írja be a `http://<publicIp:8080>/github-webhook/` ahol `publicIp` Jenkins kiszolgáló IP-címét. Győződjön meg arról, hogy tartalmazzák a záró /.
5. Válassza ki a bővítmény szolgáltatást.
  
![GitHub-webhook](media/aks-jenkins/webhook.png)

## <a name="test-cicd-process-end-to-end"></a>CI/CD folyamat teljes körű tesztelése

A fejlesztői számítógépén nyissa meg a klónozott bejelentést a kód szerkesztése. 

Az a **/azure-vote/azure-vote** könyvtárban található nevű fájl **config_file.cfg**. Módosítsa szavazás a fájl egy eltérő macska és kutya. 

Az alábbi példában látható, és frissítve **config_file.cfg** fájlt.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Amikor végzett, mentse a fájlt, a változtatások véglegesítése a határidő, és ezek az elágazáshoz, a GitHub-tárház leküldéses... A véglegesítési befejezése után a GitHub webhook új Jenkins build, frissül a tároló lemezképet, és a AKS telepítési váltja ki. A Jenkins felügyeleti konzol a folyamat figyelése. 

A létrehozás befejezése után újra keresse meg az alkalmazás végpontjának és figyelje meg a módosításokat.

![Az Azure szavazattal frissítése](media/aks-jenkins/azure-vote-updated-safari.png)

Ezen a ponton egy egyszerű folyamatos üzembe helyezés folyamat befejezése. A lépéseket és a konfigurációkat az alábbi példa mutatja egy megbízhatóbb és éles használatra kész folyamatos build automation felé használható.

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az_acr_list
[acr-authentication]: ../container-registry/container-registry-auth-aks.md
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az_aks_get_credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli