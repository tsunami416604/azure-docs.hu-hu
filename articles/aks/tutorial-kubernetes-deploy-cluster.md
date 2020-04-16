---
title: Azure-on futó Kubernetes oktatóanyag – Fürtök üzembe helyezése
description: Az Azure Kubernetes Service (AKS) ezen oktatóanyagában egy AKS-fürtöt fog létrehozni, és kapcsolódni fog a Kubernetes-főcsomóponthoz a kubectl használatával.
services: container-service
ms.topic: tutorial
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 609ac66ca27d5cad7dd2fb295c3a2a721a1cda16
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392698"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Oktatóanyag: Azure Kubernetes Service- (AKS-) fürt üzembe helyezése

A Kubernetes tárolóalapú alkalmazásokhoz kínál elosztott platformot. Az AKS segítségével gyorsan létrehozhat egy termelésre kész Kubernetes-fürtöt. Ebben az oktatóanyagban, amely egy hétrészes sorozat harmadik része, egy Kubernetes-fürtöt helyezünk üzembe az AKS-ben. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Kubernetes AKS-fürt telepítése, amely hitelesíthető egy Azure-tároló beállításjegyzékében
> * A Kubernetes parancssori felület (kubectl) telepítése
> * A kubectl konfigurálása az AKS-fürthöz való csatlakozásra

További oktatóanyagok, az Azure Vote alkalmazás telepítve van a fürt, méretezett és frissített.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban létrehoztunk egy tárolórendszerképet, és feltöltöttük egy Azure Container Registry-példányra. Ha még nem végezte el ezeket a lépéseket, és szeretné követni a mentén, kezdje [az 1.][aks-tutorial-prepare-app]

Ez az oktatóanyag megköveteli, hogy az Azure CLI 2.0.53-as vagy újabb verzióját futtassa. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-a-kubernetes-cluster"></a>Kubernetes-fürt létrehozása

Az AKS-fürtök képesek a Kubernetes szerepköralapú hozzáférés-vezérlők (RBAC) használatára. Ezekkel a vezérlőkkel a felhasználókhoz rendelt szerepkörök alapján definiálható az erőforrásokhoz való hozzáférés. Az engedélyek akkor lesznek kombinálva, ha egy felhasználóhoz több szerepkör van rendelve, és az engedélyek egyetlen névtérre vagy a teljes fürtre is hatókörözhetők. Alapértelmezés szerint az Azure CLI automatikusan engedélyezi az RBAC-t az AKS-fürtök létrehozásakor.

AKS-fürtöket az [az aks create][] paranccsal hozhat létre. A következő példában létrehozunk egy *myAKSCluster* nevű fürtöt a *myResourceGroup* nevű erőforráscsoportban. Ezt az erőforráscsoportot [az előző oktatóanyagban][aks-tutorial-prepare-acr] hoztuk létre. Annak érdekében, hogy egy AKS-fürt más Azure-erőforrásokkal kommunikáljon, automatikusan létrejön egy Azure Active Directory egyszerű szolgáltatás, mivel nem adott meg egyet. Itt ez a szolgáltatásegyszerű [jogosult lekérése lemezképek][container-registry-integration] az Azure Container Registry (ACR) az előző oktatóanyagban létrehozott példányból. Vegye figyelembe, hogy a szolgáltatásnév helyett [felügyelt identitást](use-managed-identity.md) is használhat a könnyebb kezelés érdekében.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

Manuálisan is konfigurálhat egy egyszerű szolgáltatást az ACR-ből a lemezképek lekérése érdekében. További információ: [ACR-hitelesítés egyszerű szolgáltatásokkal](../container-registry/container-registry-auth-service-principal.md) vagy [Hitelesítés a Kubernetes-ből lekéréses titkos adatokkal.](../container-registry/container-registry-auth-kubernetes.md)

Néhány perc múlva a központi telepítés befejeződik, és json-formázott információkat ad vissza az AKS-telepítésről.

> [!NOTE]
> Annak érdekében, hogy a fürt megbízhatóan működjön, legalább 2 (két) csomópontot kell futtatnia.

## <a name="install-the-kubernetes-cli"></a>A Kubernetes parancssori felület telepítése

Ahhoz, hogy csatlakozni tudjon a Kubernetes-fürthöz a helyi számítógépről, használja a Kubernetes [kubectl][kubectl] nevű parancssori ügyfelét.

Ha az Azure Cloud Shellt használja, a `kubectl` már telepítve van. Helyben is telepítheti az [az aks install-cli][] paranccsal:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Csatlakozás fürthöz a kubectl használatával

Az [aks get-credentials][] paranccsal konfigurálható`kubectl` a Kubernetes-fürthöz való csatlakozásra. A következő példa a *myResourceGroup*ban *myResourceGroup* nevű AKS-fürt hitelesítő adatait kapja meg:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez futtassa a [kubectl get nodes parancsot][kubectl-get] a fürtcsomópontok listájának visszaadására:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-12345678-0   Ready    agent   32m   v1.14.8
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Kubernetes-fürtöt helyezett üzembe az AKS-ben, és úgy konfigurálta a `kubectl` elemet, hogy a fürthöz csatlakozzon. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Kubernetes AKS-fürt telepítése, amely hitelesíthető egy Azure-tároló beállításjegyzékében
> * A Kubernetes parancssori felület (kubectl) telepítése
> * A kubectl konfigurálása az AKS-fürthöz való csatlakozásra

A következő oktatóanyag azt mutatja be, hogyan helyezhet üzembe egy alkalmazást a fürtben.

> [!div class="nextstepaction"]
> [Alkalmazások üzembe helyezése a Kubernetesben][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
