---
title: Azure Container Service-oktatóanyag – Alkalmazás frissítése
description: Azure Container Service-oktatóanyag – Alkalmazás frissítése
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 81f2302df5740b482f03a4a724d2899734579949
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096859"
---
# <a name="update-an-application-in-kubernetes"></a>Alkalmazások frissítése a Kubernetesben

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

A Kubernetesben való telepítésüket követően az alkalmazások egy új tárolórendszerkép- vagy rendszerképverzió megadásával frissíthetőek. Ilyen esetben a frissítés úgy lesz ütemezve, hogy egy adott pillanatban az üzemelő példánynak csak egy része legyen frissítve. Ennek az eltolásos frissítésnek köszönhetően az alkalmazás a frissítés során is tovább fut. Ezen kívül visszaállítási mechanizmust is biztosít az üzembe helyezés során fellépő hibák esetére. 

Ebben az oktatóanyagban, amely egy hétrészes sorozat hatodik része, az Azure Vote mintaalkalmazást frissítjük. A következő feladatokat fogjuk végrehajtani:

> [!div class="checklist"]
> * Az előtér-alkalmazás kódjának frissítése
> * Frissített tárolórendszerkép létrehozása
> * A tárolórendszerkép leküldése az Azure Container Registrybe
> * A frissített tárolórendszerkép üzembe helyezése

Az ezt követő oktatóanyagokban a Log Analytics szolgáltatást fogjuk konfigurálni a Kubernetes-fürt monitorozására.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazást csomagoltunk egy tárolórendszerképbe, a rendszerképet feltöltöttük az Azure Container Registrybe, és létrehoztunk egy Kubernetes-fürtöt. Az alkalmazást ezután a Kubernetes-fürtön futtattuk. 

Emellett klónoztunk egy alkalmazás-adattárat, amely tartalmazza az alkalmazás forráskódját, valamint a jelen oktatóanyagban használt, előre létrehozott Docker Compose-fájlt. Bizonyosodjon meg róla, hogy az adattár klónja valóban létrejött, és hogy a könyvtárakat átállította a klónozott könyvtárra. Itt egy `azure-vote` nevű könyvtár és egy `docker-compose.yml` nevű fájl található.

Ha ezeket a lépéseket még nem hajtotta végre, de szeretne velünk tartani, lépjen vissza az [1. oktatóanyag – Tárolórendszerképek létrehozása](./container-service-tutorial-kubernetes-prepare-app.md) részhez. 

## <a name="update-application"></a>Az alkalmazás frissítése

Ebben az oktatóanyagban módosítjuk az alkalmazást, és a frissített alkalmazást üzembe helyezzük a Kubernetes-fürtön. 

Az alkalmazás forráskódja az `azure-vote` könyvtárban található. Nyissa meg a `config_file.cfg` fájlt bármilyen kód- vagy szövegszerkesztővel. Ebben a példában a `vi` eszközt használjuk.

```bash
vi azure-vote/azure-vote/config_file.cfg
```

Módosítsa a `VOTE1VALUE` és a `VOTE2VALUE` értékét, majd mentse a fájlt.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Mentse és zárja be a fájlt.

## <a name="update-container-image"></a>A tárolórendszerkép frissítése

A [docker-compose](https://docs.docker.com/compose/) használatával hozza újra létre az előtéri rendszerképet, és futtassa a frissített alkalmazást. A `--build` argumentummal lehet utasítani a Docker Compose-t, hogy hozza újra létre az alkalmazás rendszerképét.

```bash
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

A [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) paranccsal címkézze fel a rendszerképet. Az `<acrLoginServer>` helyére az Azure Container Registry bejelentkezési kiszolgáló nevét vagy a nyilvános beállításjegyzék gazdanevét írja. Figyelje meg, hogy a rendszerkép verziója a `redis-v2` verzióra frissült.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

A [docker push](https://docs.docker.com/engine/reference/commandline/push/) paranccsal töltse fel a rendszerképet a regisztrációs adatbázisba. Az `<acrLoginServer>` helyére az Azure Container Registry bejelentkezési kiszolgálójának nevét írja be.

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## <a name="deploy-update-application"></a>A frissített alkalmazás üzembe helyezése

A rendelkezésre állás maximalizálása érdekében az alkalmazáspodot több példányban kell futtatni. Ezt a konfigurációt a [kubectl get pod](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) paranccsal ellenőrizheti.

```bash
kubectl get pod
```

Kimenet:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Amennyiben az azure-vote-front rendszerkép nem fut egyszerre több podon, méretezze át az `azure-vote-front` üzemi környezetet.


```azurecli-interactive
kubectl scale --replicas=3 deployment/azure-vote-front
```

Az alkalmazás frissítéséhez használja a [kubectl set](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set) parancsot. Az `<acrLoginServer>` helyére a tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának nevét vagy gazdanevét írja.

```azurecli-interactive
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

Az üzemelő példány monitorozásához használja a [kubectl get pod](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) parancsot. A frissített alkalmazás üzembe helyezése során a rendszer a podokat megszünteti, majd az új tárolórendszerképpel újból létrehozza.

```azurecli-interactive
kubectl get pod
```

Kimenet:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>A frissített alkalmazás tesztelése

Kérje le az `azure-vote-front` szolgáltatás külső IP-címét.

```azurecli-interactive
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

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan monitorozható a Kubernetes a Log Analytics segítségével.

> [!div class="nextstepaction"]
> [A Kubernetes monitorozása a Log Analytics használatával](./container-service-tutorial-kubernetes-monitor.md)