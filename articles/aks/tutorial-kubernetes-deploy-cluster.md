---
title: "Kubernetes az Azure útmutató - fürt központi telepítése"
description: "AKS oktatóanyag - fürt központi telepítése"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2c2318d9a5f72800f9cfbd430dca448fd1e5746f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="deploy-an-azure-container-service-aks-cluster"></a>Az Azure-tároló szolgáltatás (AKS) fürt központi telepítése

A Kubernetes tárolóalapú alkalmazásokhoz kínál elosztott platformot. Teljes AKS készen áll a Kubernetes éles fürt üzembe, akkor egyszerűen és gyorsan. Ebben az oktatóanyagban nyolc, három része a Kubernetes fürt AKS van telepítve. Befejeződött a lépések az alábbiak:

> [!div class="checklist"]
> * Egy Kubernetes AKS fürt telepítése
> * A Kubernetes CLI (kubectl) telepítése
> * Kubectl konfigurálása

A következő útmutatókból az Azure szavazattal alkalmazás központi telepítése a fürt, méretezhető, frissítése, és az Operations Management Suite a Kubernetes fürt figyelésére van beállítva.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokat a tároló-lemezkép létrejött, de feltöltött egy Azure-tároló beállításjegyzék-példányon. Ha nem volna ezeket a lépéseket, és szeretné követéséhez, vissza [oktatóanyag 1 – létrehozás tároló képek][aks-tutorial-prepare-app].

## <a name="enabling-aks-preview-for-your-azure-subscription"></a>Azure-előfizetése AKS előzetes engedélyezése
AKS jelenleg előzetes verzióban érhető, amíg a szolgáltatás jelzőt előfizetéséhez új fürtök létrehozása szükséges. Ez a szolgáltatás tetszőleges számú előfizetések, amelyek használni szeretné a kérheti. Használja a `az provider register` parancs futtatásával regisztrálja a AKS szolgáltatót:

```azurecli-interactive
az provider register -n Microsoft.ContainerService
```

A regisztrálás után most már készen áll a AKS Kubernetes fürt létrehozása.

## <a name="create-kubernetes-cluster"></a>Kubernetes-fürt létrehozása

Az alábbi példakód létrehozza a fürt nevű `myK8sCluster` nevű erőforráscsoportban `myResourceGroup`. Ez az erőforráscsoport jött létre a [az oktatóanyag előző][aks-tutorial-prepare-acr].

```azurecli
az aks create --resource-group myResourceGroup --name myK8sCluster --node-count 1 --generate-ssh-keys
```

Pár perc múlva a telepítés befejeződik, és értéket ad vissza json formátumú AKS nyújt tájékoztatást.

## <a name="install-the-kubectl-cli"></a>A kubectl parancssori felület telepítése

Az Kubernetes fürthöz csatlakoztatja az ügyfélszámítógépről, használja a [kubectl][kubectl], a Kubernetes parancssori ügyfél.

Ha az Azure CloudShellt használja, a kubectl már telepítve van. Ha szeretné helyileg telepíteni, futtassa a következő parancsot:

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>Kapcsolódás a kubectl parancssori ügyfélhez

A Kubernetes fürthöz való kapcsolódás kubectl konfigurálásához futtassa a következő parancsot:

```azurecli
az aks get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Ellenőrizze a kapcsolatot a fürthöz, futtassa a [kubectl beolvasása csomópontok] [ kubectl-get] parancsot.

```azurecli
kubectl get nodes
```

Kimenet:

```
NAME                          STATUS    AGE       VERSION
k8s-myk8scluster-36346190-0   Ready     49m       v1.7.7
```

Oktatóanyag befejezése után következő rendelkezik készen áll a munkaterhelések AKS fürttel. A következő útmutatókból egy több tároló alkalmazás üzemel a fürthöz, horizontálisan, frissítése, és figyeli.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban Kubernetes fürt AKS lett telepítve. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * Kubernetes AKS fürt telepítése
> * A Kubernetes CLI (kubectl) telepítése
> * Konfigurált kubectl

Továbblépés a következő oktatóanyag fürtben futó alkalmazás megismeréséhez.

> [!div class="nextstepaction"]
> [Kubernetes az alkalmazás központi telepítése][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md