---
title: Azure-on futó Kubernetes oktatóanyag – Alkalmazás frissítése
description: Ebben az Azure Kubernetes Service-hez (AKS-hez) tartozó oktatóanyagban megismerheti, hogyan frissítheti a meglévő alkalmazástelepítéseket az AKS-ben az alkalmazáskód új verziójával.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e795c275b832fcd59799a4d4d1107b76f6e489b6
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856842"
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>Oktatóanyag: Az Azure Kubernetes Service (AKS) alkalmazás frissítése

A Kubernetesben való telepítésüket követően az alkalmazások egy új tárolórendszerkép- vagy rendszerképverzió megadásával frissíthetőek. Frissítés van ütemezve, hogy a csak a központi telepítést része egy időben frissül. Ennek az eltolásos frissítésnek köszönhetően az alkalmazás a frissítés során is tovább fut. Ezen kívül visszaállítási mechanizmust is biztosít az üzembe helyezés során fellépő hibák esetére.

Ebben az oktatóanyagban, amely egy hétrészes sorozat hatodik része, az Azure Vote mintaalkalmazást frissítjük. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Az előtér-alkalmazás kódjának frissítése
> * Frissített tárolórendszerkép létrehozása
> * A tárolórendszerkép leküldése az Azure Container Registrybe
> * A frissített tárolórendszerkép üzembe helyezése

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazást csomagoltunk egy tárolórendszerképbe. A rendszerképet feltöltöttük az Azure Container Registrybe, és létrehozott egy AKS-fürtöt. Az AKS-fürtöt, majd helyeztünk üzembe az alkalmazást.

Emellett klónoztunk egy alkalmazás-adattárat, amely tartalmazza az alkalmazás forráskódját, valamint a jelen oktatóanyagban használt, előre létrehozott Docker Compose-fájlt. Győződjön meg arról, hogy már az adattár klónja valóban létrejött, és könyvtárakat átállította a klónozott könyvtárra. Ha még nem hajtotta végre ezeket a lépéseket, és szeretné követni, kezdje [1. oktatóanyag – tárolórendszerképek létrehozása][aks-tutorial-prepare-app].

Ehhez az oktatóanyaghoz, hogy futtat-e az Azure CLI 2.0.53 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="update-an-application"></a>Alkalmazás frissítése

Módosítsuk a mintaalkalmazást, majd frissítsük az AKS-fürtben már üzembe helyezett verziót. Győződjön meg arról, hogy használja-e a klónozott *azure-szavazóalkalmazás – alkalmazás-redis* könyvtár. A mintaalkalmazás forráskódja majd belül található a *azure-vote* könyvtár. Nyissa meg a *config_file.cfg* fájlt egy szerkesztővel (például `vi`):

```console
vi azure-vote/azure-vote/config_file.cfg
```

Módosítsa az értékeket a *VOTE1VALUE* és *VOTE2VALUE* különböző értékeket, például a színeket. Az alábbi példa bemutatja a frissített értékekkel:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Mentse és zárja be a fájlt. A `vi`, használjon `:wq`.

## <a name="update-the-container-image"></a>A tárolórendszerkép frissítése

Az előtéri rendszerkép újbóli létrehozásához és a frissített alkalmazás teszteléséhez használja a [docker-compose][docker-compose] parancsot. A `--build` argumentummal lehet utasítani a Docker Compose-t, hogy hozza újra létre az alkalmazás rendszerképét:

```console
docker-compose up --build -d
```

## <a name="test-the-application-locally"></a>Az alkalmazás helyi tesztelése

Ha ellenőrizni szeretné, hogy a frissített tárolórendszerkép megjeleníti-e a módosításokat, nyissa meg a http://localhost:8080 címet egy helyi böngészőben.

![Egy Azure-beli Kubernetes-fürt képe](media/container-service-kubernetes-tutorials/vote-app-updated.png)

A megadott frissített értékeket az *config_file.cfg* fájlt a futó alkalmazás jelennek meg.

## <a name="tag-and-push-the-image"></a>A rendszerkép címkézése és leküldése

A frissített rendszerkép megfelelő használatához címkézze fel az *azure-vote-front* rendszerképet az ACR-beállításjegyzék bejelentkezési kiszolgálójának nevével. Kérje le a bejelentkezési kiszolgáló nevét az [az acr list](/cli/azure/acr#az_acr_list) paranccsal:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

A [docker tag][docker-tag] paranccsal címkézze fel a rendszerképet. Az `<acrLoginServer>` helyére az ACR bejelentkezési kiszolgáló nevét vagy a nyilvános beállításjegyzék gazdanevét írja, és frissítse a rendszerkép verzióját a *:v2* értékre a következőképpen:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v2
```

Most a [docker push][docker-push] paranccsal töltse fel a rendszerképet a regisztrációs adatbázisba. Az `<acrLoginServer>` helyére az ACR bejelentkezési kiszolgálójának nevét írja be. Ha az ACR-beállításjegyzékbe való leküldés problémákat tapasztal, győződjön meg arról, hogy futtatta a [az acr bejelentkezési] [ az-acr-login] parancsot.

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-the-updated-application"></a>A frissített alkalmazás üzembe helyezése

A rendelkezésre állás biztosítása érdekében az alkalmazáspodot több példányban kell futnia. Ellenőrizze a futó előtérbeli példányok számát a [kubectl get pods][kubectl-get] paranccsal:

```
$ kubectl get pods

NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Ha nem rendelkezik több előtér-podok, méretezhető a *azure-vote-front* üzembe helyezés az alábbiak szerint:

```console
kubectl scale --replicas=3 deployment/azure-vote-front
```

Az alkalmazás frissítéséhez használja a [kubectl set][kubectl-set] parancsot. Az `<acrLoginServer>` helyére a tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának nevét vagy gazdanevét írja, és adja meg a *v2* alkalmazásverziót:

```console
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

Az üzemelő példány monitorozásához használja a [kubectl get pod][kubectl-get] parancsot. A frissített alkalmazás üzembe helyezése során a rendszer a podokat megszünteti, majd az új tárolórendszerképpel újból létrehozza.

```console
kubectl get pods
```

A következő példa megszűnő podokat és új futó példányokat mutat be az üzembe helyezés előrehaladtával:

```
$ kubectl get pods

NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-the-updated-application"></a>A frissített alkalmazás tesztelése

A frissített alkalmazás megtekintéséhez először kérje le az `azure-vote-front` szolgáltatás külső IP-címét:

```console
kubectl get service azure-vote-front
```

Most nyissa meg a helyi webes böngészőben a szolgáltatás IP-címe:

![Egy Azure-beli Kubernetes-fürt képe](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy alkalmazást, és jelennek meg a frissítés, az AKS-fürt. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az előtér-alkalmazás kódjának frissítése
> * Frissített tárolórendszerkép létrehozása
> * A tárolórendszerkép leküldése az Azure Container Registrybe
> * A frissített tárolórendszerkép üzembe helyezése

Folytassa a következő oktatóanyaggal, amely az AKS-fürtök új Kubernetes-verzióra történő frissítését ismerteti.

> [!div class="nextstepaction"]
> [Kubernetes frissítése][aks-tutorial-upgrade]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[az-acr-login]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
