---
title: Azure-on futó Kubernetes oktatóanyag – Alkalmazás frissítése
description: AKS-oktatóanyag – Alkalmazás frissítése
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 622cd17a93bf1b9fa9d3c138d385ca1d29426f3b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33934056"
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>Oktatóanyag: Alkalmazások frissítése az Azure Kubernetes Service-ben (AKS)

A Kubernetesben való telepítésüket követően az alkalmazások egy új tárolórendszerkép- vagy rendszerképverzió megadásával frissíthetőek. Ilyen esetben a frissítés úgy lesz ütemezve, hogy egy adott pillanatban az üzemelő példánynak csak egy része legyen frissítve. Ennek az eltolásos frissítésnek köszönhetően az alkalmazás a frissítés során is tovább fut. Ezen kívül visszaállítási mechanizmust is biztosít az üzembe helyezés során fellépő hibák esetére.

Ebben az oktatóanyagban, amely egy nyolcrészes sorozat hatodik része, az Azure Vote alkalmazást frissítjük. A következő feladatokat fogjuk végrehajtani:

> [!div class="checklist"]
> * Az előtér-alkalmazás kódjának frissítése
> * Frissített tárolórendszerkép létrehozása
> * A tárolórendszerkép leküldése az Azure Container Registrybe
> * A frissített tárolórendszerkép üzembe helyezése

Az ezt követő oktatóanyagokban a Log Analytics szolgáltatást fogjuk konfigurálni a Kubernetes-fürt monitorozására.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazást csomagoltunk egy tárolórendszerképbe, a rendszerképet feltöltöttük az Azure Container Registrybe, és létrehoztunk egy Kubernetes-fürtöt. Az alkalmazást ezután a Kubernetes-fürtön futtattuk.

Emellett klónoztunk egy alkalmazás-adattárat, amely tartalmazza az alkalmazás forráskódját, valamint a jelen oktatóanyagban használt, előre létrehozott Docker Compose-fájlt. Bizonyosodjon meg róla, hogy az adattár klónja valóban létrejött, és hogy a könyvtárakat átállította a klónozott könyvtárra. Itt egy `azure-vote` nevű könyvtár és egy `docker-compose.yaml` nevű fájl található.

Ha ezeket a lépéseket még nem hajtotta végre, és szeretné követni az oktatóanyagot, lépjen vissza az [1. oktatóanyag – Tárolórendszerképek létrehozása][aks-tutorial-prepare-app] részhez.

## <a name="update-application"></a>Az alkalmazás frissítése

Ebben az oktatóanyagban módosítjuk az alkalmazást, és a frissített alkalmazást üzembe helyezzük a Kubernetes-fürtön.

Az alkalmazás forráskódja az `azure-vote` könyvtárban található. Nyissa meg a `config_file.cfg` fájlt bármilyen kód- vagy szövegszerkesztővel. Ebben a példában a `vi` eszközt használjuk.

```console
vi azure-vote/azure-vote/config_file.cfg
```

Módosítsa a `VOTE1VALUE` és a `VOTE2VALUE` értékét, majd mentse a fájlt.

```console
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Mentse és zárja be a fájlt.

## <a name="update-container-image"></a>A tárolórendszerkép frissítése

A [docker-compose][docker-compose] használatával hozza újból létre az előtérrendszerképet, és futtassa a frissített alkalmazást. A `--build` argumentummal lehet utasítani a Docker Compose-t, hogy hozza újra létre az alkalmazás rendszerképét.

```console
docker-compose up --build -d
```

## <a name="test-application-locally"></a>Az alkalmazás helyi tesztelése

A böngészőben nyissa meg a http://localhost:8080 címet a frissített alkalmazás megtekintéséhez.

![Egy Azure-beli Kubernetes-fürt képe](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Rendszerképek címkézése és leküldése

Címkézze fel az `azure-vote-front` rendszerképet a tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának nevével.

Kérje le a bejelentkezési kiszolgáló nevét az [az acr list](/cli/azure/acr#az_acr_list) paranccsal.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

A [docker tag][docker-tag] paranccsal címkézze fel a rendszerképet. Az `<acrLoginServer>` helyére az Azure Container Registry bejelentkezési kiszolgáló nevét vagy a nyilvános beállításjegyzék gazdanevét írja. Figyelje meg, hogy a rendszerkép verziója a `v2` verzióra frissült.

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v2
```

A [docker push][docker-push] paranccsal töltse fel a rendszerképet a beállításjegyzékbe. Az `<acrLoginServer>` helyére az Azure Container Registry bejelentkezési kiszolgálójának nevét írja be. Ha problémákat tapasztal az ACR-beállításjegyzékbe való leküldés során, győződjön meg arról, hogy futtatta az [az acr login][az-acr-login] parancsot.

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-update-application"></a>A frissített alkalmazás üzembe helyezése

A rendelkezésre állás maximalizálása érdekében az alkalmazáspodot több példányban kell futtatni. Ezt a konfigurációt a [kubectl get pod][kubectl-get] paranccsal ellenőrizheti.

```
kubectl get pod
```

Kimenet:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Amennyiben az azure-vote-front rendszerkép nem fut egyszerre több podon, méretezze át az `azure-vote-front` üzemi környezetet.


```azurecli
kubectl scale --replicas=3 deployment/azure-vote-front
```

Az alkalmazás frissítéséhez használja a [kubectl set][kubectl-set] parancsot. Az `<acrLoginServer>` helyére a tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának nevét vagy gazdanevét írja.

```azurecli
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

Az üzemelő példány monitorozásához használja a [kubectl get pod][kubectl-get] parancsot. A frissített alkalmazás üzembe helyezése során a rendszer a podokat megszünteti, majd az új tárolórendszerképpel újból létrehozza.

```azurecli
kubectl get pod
```

Kimenet:

```
NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-updated-application"></a>A frissített alkalmazás tesztelése

Kérje le az `azure-vote-front` szolgáltatás külső IP-címét.

```azurecli
kubectl get service azure-vote-front
```

A böngészőben nyissa meg ezt az IP-címet a frissített alkalmazás megtekintéséhez.

![Egy Azure-beli Kubernetes-fürt képe](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>További lépések

Az oktatóanyagban frissítettünk egy alkalmazást, és a frissítést kivezettük egy Kubernetes-fürtre. A következő feladatokat hajtottuk végre:

> [!div class="checklist"]
> * Frissítettük az előtér-alkalmazás kódját
> * Létrehoztunk egy frissített tárolórendszerképet
> * Leküldtük a tárolórendszerképet az Azure Container Registrybe
> * Üzembe helyeztük a frissített alkalmazást

Folytassa a következő oktatóanyaggal, amely a Kurbernetes új verzióra történő frissítését ismerteti.

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
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az_acr_login