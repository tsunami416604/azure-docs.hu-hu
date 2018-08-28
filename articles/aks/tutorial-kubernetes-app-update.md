---
title: Azure-on futó Kubernetes oktatóanyag – Alkalmazás frissítése
description: Ebben az Azure Kubernetes Service-hez (AKS-hez) tartozó oktatóanyagban megismerheti, hogyan frissítheti a meglévő alkalmazástelepítéseket az AKS-ben az alkalmazáskód új verziójával.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b2dd52fec112b879e072d3ac5598dd7978e68cbc
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2018
ms.locfileid: "41918649"
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>Oktatóanyag: Alkalmazások frissítése az Azure Kubernetes Service-ben (AKS)

A Kubernetesben való telepítésüket követően az alkalmazások egy új tárolórendszerkép- vagy rendszerképverzió megadásával frissíthetőek. Ilyen esetben a frissítés úgy lesz ütemezve, hogy egy adott pillanatban az üzemelő példánynak csak egy része legyen frissítve. Ennek az eltolásos frissítésnek köszönhetően az alkalmazás a frissítés során is tovább fut. Ezen kívül visszaállítási mechanizmust is biztosít az üzembe helyezés során fellépő hibák esetére.

Ebben az oktatóanyagban, amely egy hétrészes sorozat hatodik része, az Azure Vote mintaalkalmazást frissítjük. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Az előtér-alkalmazás kódjának frissítése
> * Frissített tárolórendszerkép létrehozása
> * A tárolórendszerkép leküldése az Azure Container Registrybe
> * A frissített tárolórendszerkép üzembe helyezése

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazást csomagoltunk egy tárolórendszerképbe, a rendszerképet feltöltöttük az Azure Container Registrybe (ACR), és létrehoztunk egy Kubernetes-fürtöt. Az alkalmazást ezután a Kubernetes-fürtön futtattuk.

Emellett klónoztunk egy alkalmazás-adattárat, amely tartalmazza az alkalmazás forráskódját, valamint a jelen oktatóanyagban használt, előre létrehozott Docker Compose-fájlt. Bizonyosodjon meg róla, hogy az adattár klónja valóban létrejött, és hogy a könyvtárakat átállította a klónozott könyvtárra. Ha ezeket a lépéseket még nem hajtotta végre, és szeretné követni az oktatóanyagot, lépjen vissza az [1. oktatóanyag – Tárolórendszerképek létrehozása][aks-tutorial-prepare-app] részhez.

Az oktatóanyag elvégzéséhez az Azure CLI 2.0.44-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="update-an-application"></a>Alkalmazás frissítése

Módosítsuk a mintaalkalmazást, majd frissítsük az AKS-fürtben már üzembe helyezett verziót. A mintaalkalmazás forráskódja az *azure-vote* könyvtárban található. Nyissa meg a *config_file.cfg* fájlt egy szerkesztővel (például `vi`):

```console
vi azure-vote/azure-vote/config_file.cfg
```

Módosítsa más színekre a *VOTE1VALUE* és a *VOTE2VALUE* értékeit. Az alábbi példában a frissített színértékek láthatók:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Mentse és zárja be a fájlt.

## <a name="update-the-container-image"></a>A tárolórendszerkép frissítése

Az előtéri rendszerkép újbóli létrehozásához és a frissített alkalmazás teszteléséhez használja a [docker-compose][docker-compose] parancsot. A `--build` argumentummal lehet utasítani a Docker Compose-t, hogy hozza újra létre az alkalmazás rendszerképét:

```console
docker-compose up --build -d
```

## <a name="test-the-application-locally"></a>Az alkalmazás helyi tesztelése

Ha ellenőrizni szeretné, hogy a frissített tárolórendszerkép megjeleníti-e a módosításokat, nyissa meg a http://localhost:8080 címet egy helyi böngészőben.

![Egy Azure-beli Kubernetes-fürt képe](media/container-service-kubernetes-tutorials/vote-app-updated.png)

A *config_file.cfg* fájlban megadott frissített színértékek megjelennek a futó alkalmazásban.

## <a name="tag-and-push-the-image"></a>A rendszerkép címkézése és leküldése

A frissített rendszerkép megfelelő használatához címkézze fel az *azure-vote-front* rendszerképet az ACR-beállításjegyzék bejelentkezési kiszolgálójának nevével. Kérje le a bejelentkezési kiszolgáló nevét az [az acr list](/cli/azure/acr#az_acr_list) paranccsal:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

A [docker tag][docker-tag] paranccsal címkézze fel a rendszerképet. Az `<acrLoginServer>` helyére az ACR bejelentkezési kiszolgáló nevét vagy a nyilvános beállításjegyzék gazdanevét írja, és frissítse a rendszerkép verzióját a *:v2* értékre a következőképpen:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v2
```

Most a [docker push][docker-push] paranccsal töltse fel a rendszerképet a regisztrációs adatbázisba. Az `<acrLoginServer>` helyére az ACR bejelentkezési kiszolgálójának nevét írja be. Ha problémákat tapasztal az ACR-beállításjegyzékbe való leküldés során, győződjön meg arról, hogy futtatta az [az acr login][az-acr-login] parancsot.

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-the-updated-application"></a>A frissített alkalmazás üzembe helyezése

A rendelkezésre állás maximalizálása érdekében az alkalmazáspodot több példányban kell futtatni. Ellenőrizze a futó előtérbeli példányok számát a [kubectl get pods][kubectl-get] paranccsal:

```
$ kubectl get pods

NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Amennyiben nem rendelkezik több előtérbeli poddal, skálázza az *azure-vote-front* üzemelő példányt a következőképpen:

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

Most nyissa meg az IP-címet egy helyi böngészőben.

![Egy Azure-beli Kubernetes-fürt képe](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>További lépések

Az oktatóanyagban frissítettünk egy alkalmazást, és a frissítést kivezettük egy Kubernetes-fürtre. Megismerte, hogyan végezheti el az alábbi műveleteket:

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
[az-acr-login]: /cli/azure/acr#az_acr_login
[azure-cli-install]: /cli/azure/install-azure-cli
