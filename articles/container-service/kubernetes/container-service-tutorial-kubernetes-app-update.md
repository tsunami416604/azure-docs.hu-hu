---
title: "Azure Tárolószolgáltatás útmutató - alkalmazása"
description: "Azure Tárolószolgáltatás útmutató - alkalmazása"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 5ecaa3a79270e29ac002e91065f7df4f7e8914e7
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="update-an-application-in-kubernetes"></a>Kubernetes tartozó alkalmazás frissítése

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Után az alkalmazás telepítve lett Kubernetes, akkor egy új tároló kép vagy lemezkép verziója lehet frissíteni. Annak során, a frissítés tartalma, hogy a központi telepítés egy részének egyidejűleg frissül. A két lépésben frissítés lehetővé teszi, hogy az alkalmazás tovább futnak a frissítés során. Azt is lehetővé teszi a visszaállítási központi telepítési hiba esetén. 

Ebben az oktatóanyagban hét, hat része a mintaalkalmazás Azure szavazattal frissül. Feladatokat, a következők:

> [!div class="checklist"]
> * Az előtér-alkalmazás kódja frissítése
> * Frissített tároló lemezkép létrehozása
> * A tároló kép küldését Azure tároló beállításjegyzék
> * A frissített tároló rendszerkép központi telepítése

A következő útmutatókból Operations Management Suite a Kubernetes fürt figyelésére van konfigurálva.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagok egy tároló lemezképet, az Azure-tároló beállításjegyzék feltöltött lemezkép és a létrehozott Kubernetes fürt alkalmazás lett csomagolva. Az alkalmazás ezután futtatták a Kubernetes fürtön. 

Egy alkalmazás tárház lett is klónozott ide tartozik az alkalmazás forráskódjához, és ebben az oktatóanyagban használt előre létrehozott Docker Compose fájl. Győződjön meg arról, hogy létrehozta a tárház másolat, és hogy módosult-könyvtárak a klónozott könyvtárba. Belső út egy könyvtár nevű `azure-vote` és nevű fájlt `docker-compose.yml`.

Ha még nem fejeződött be az alábbi lépéseket, és követéséhez, térjen vissza [oktatóanyag 1 – létrehozás tároló képek](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-application"></a>Alkalmazás frissítése

Ebben az oktatóanyagban egy módosításakor az alkalmazást, és a Kubernetes fürt központi telepítése a frissített alkalmazás. 

Az alkalmazás forráskódjához található található a `azure-vote` könyvtár. Nyissa meg a `config_file.cfg` bármely kód vagy szöveges szerkesztővel fájlt. Ebben a példában `vi` szolgál.

```bash
vi azure-vote/azure-vote/config_file.cfg
```

Megváltoztatni az `VOTE1VALUE` és `VOTE2VALUE`, majd mentse a fájlt.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Mentse és zárja be a fájlt.

## <a name="update-container-image"></a>Tároló lemezképek

Használjon [docker compose](https://docs.docker.com/compose/) hozza létre az előtér-kép és a frissített alkalmazás futtatásához. A `--build` argumentum használata az utasítási kiadása a Docker Compose újra létre kell hoznia az alkalmazás-lemezképet.

```bash
docker-compose up --build -d
```

## <a name="test-application-locally"></a>Alkalmazás helyi teszteléséhez

Tallózással keresse meg az frissített alkalmazást 8080.

![Egy Azure-beli Kubernetes-fürt képe](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Címke és leküldéses lemezképek

Címke a `azure-vote-front` a tároló beállításjegyzék loginServer lemezkép. 

A bejelentkezési kiszolgáló nevét, a [az acr lista](/cli/azure/acr#list) parancsot.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Használjon [docker címke](https://docs.docker.com/engine/reference/commandline/tag/) használatával címkézhesse a lemezképet. Cserélje le `<acrLoginServer>` az Azure-tároló beállításjegyzék bejelentkezési kiszolgáló neve vagy a nyilvános beállításjegyzék állomásnév. Is figyelje meg, hogy a kép verzióra frissül, és `redis-v2`.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

Használjon [docker leküldéses](https://docs.docker.com/engine/reference/commandline/push/) való feltölti a lemezképet a beállításjegyzékhez. Cserélje le `<acrLoginServer>` az Azure-tároló beállításjegyzék bejelentkezési kiszolgáló nevével.

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## <a name="deploy-update-application"></a>Frissítés alkalmazás központi telepítése

Maximális hasznos üzemidő biztosítása érdekében az alkalmazás fogyasztanak több példánya fut. Ez a konfiguráció ellenőrzése a [kubectl beolvasása pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) parancsot.

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

Ha nem rendelkezik több három munkaállomás-csoporttal fut az azure-szavazat-első kép, méretezni a `azure-vote-front` központi telepítés.


```azurecli-interactive
kubectl scale --replicas=3 deployment/azure-vote-front
```

Az alkalmazás frissítésére, használja a [kubectl set](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#set) parancsot. Frissítés `<acrLoginServer>` a tároló beállításjegyzék bejelentkezési kiszolgáló vagy a gazdagép nevével.

```azurecli-interactive
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

Az üzemelő példány figyelése a [kubectl beolvasása pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) parancsot. A frissített alkalmazás lett telepítve, mint a három munkaállomás-csoporttal lezárva, és újból létrehozza az új tároló lemezképpel.

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

## <a name="test-updated-application"></a>Frissített alkalmazás tesztelése

A külső IP-címet az beszerzése a `azure-vote-front` szolgáltatás.

```azurecli-interactive
kubectl get service azure-vote-front
```

Tallózással keresse meg az IP-címet a frissített alkalmazás megtekintéséhez.

![Egy Azure-beli Kubernetes-fürt képe](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy alkalmazás frissítése, és a frissítés Kubernetes fürtre megkezdődött. Befejeződtek a következő feladatokat:

> [!div class="checklist"]
> * Frissítve az előtér-alkalmazás kódja
> * Frissített tároló lemezkép létrehozása
> * A tároló kép leküldött Azure tároló beállításjegyzék
> * A frissített alkalmazás telepítve

Továbblépés figyelése az Operations Management Suite Kubernetes olvashat a következő oktatóanyag.

> [!div class="nextstepaction"]
> [A Kubernetes monitorozása az OMS használatával](./container-service-tutorial-kubernetes-monitor.md)