---
title: Azure-on futó Kubernetes oktatóanyag – Fürtök üzembe helyezése
description: Az Azure Kubernetes Service (AKS) ezen oktatóanyagában egy AKS-fürtöt fog létrehozni, és kapcsolódni fog a Kubernetes-főcsomóponthoz a kubectl használatával.
services: container-service
ms.topic: tutorial
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 72c2a664b3994d53fdd3602b432df1cabdaeb3ef
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "88002969"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Oktatóanyag: Azure Kubernetes Service- (AKS-) fürt üzembe helyezése

A Kubernetes tárolóalapú alkalmazásokhoz kínál elosztott platformot. Az AK használatával gyorsan létrehozhat egy éles használatra kész Kubernetes-fürtöt. Ebben az oktatóanyagban, amely egy hétrészes sorozat harmadik része, egy Kubernetes-fürtöt helyezünk üzembe az AKS-ben. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Az Azure Container registryben hitelesítésre képes Kubernetes AK-fürt üzembe helyezése
> * A Kubernetes parancssori felület (kubectl) telepítése
> * A kubectl konfigurálása az AKS-fürthöz való csatlakozásra

A további oktatóanyagokban az Azure vote-alkalmazás üzembe helyezése a fürtön történik, méretezhető és frissítve.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban létrehoztunk egy tárolórendszerképet, és feltöltöttük egy Azure Container Registry-példányra. Ha még nem tette meg ezeket a lépéseket, és szeretné követni, kezdje az [1. oktatóanyag – tároló lemezképek létrehozása][aks-tutorial-prepare-app]című témakörben.

Ehhez az oktatóanyaghoz az Azure CLI 2.0.53 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-a-kubernetes-cluster"></a>Kubernetes-fürt létrehozása

Az AK-fürtök használhatják a Kubernetes szerepköralapú hozzáférés-vezérlést (RBAC). Ezekkel a vezérlőkkel a felhasználókhoz rendelt szerepkörök alapján definiálható az erőforrásokhoz való hozzáférés. Az engedélyek kombinálva vannak, ha egy felhasználó több szerepkörhöz van rendelve, és az engedélyek hatóköre egyetlen névtérre vagy a teljes fürtre is kiterjed. Alapértelmezés szerint az Azure CLI automatikusan engedélyezi az RBAC-t az AKS-fürtök létrehozásakor.

AKS-fürtöket az [az aks create][] paranccsal hozhat létre. A következő példában létrehozunk egy *myAKSCluster* nevű fürtöt a *myResourceGroup* nevű erőforráscsoportban. Ez az erőforráscsoport az [előző oktatóanyagban][aks-tutorial-prepare-acr] , a *eastus* régióban lett létrehozva. A következő példa nem ad meg régiót, így az AK-fürt is létrejön az *eastus* régióban. Az AK-ra vonatkozó erőforrás-korlátokkal és a régió rendelkezésre állásával kapcsolatos további információkért lásd: [kvóták, virtuálisgép-méretre vonatkozó korlátozások és régiók rendelkezésre állása az Azure Kubernetes szolgáltatásban (ak)][quotas-skus-regions] .

Annak engedélyezéséhez, hogy egy AK-fürt más Azure-erőforrásokkal is működjön, automatikusan létrejön egy Azure Active Directory egyszerű szolgáltatás, mert nem adott meg ilyet. Ebben az egyszerű szolgáltatásban [megkapta a jogot arra, hogy][container-registry-integration] az előző oktatóanyagban létrehozott Azure Container Registry (ACR) példányból lekérje a lemezképeket. Vegye figyelembe, hogy egy egyszerű szolgáltatásnév helyett [felügyelt identitást](use-managed-identity.md) használhat a könnyebb kezelés érdekében.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

Manuálisan is konfigurálhat egy egyszerű szolgáltatást a képek ACR-ből való lekéréséhez. További információkért tekintse meg az [ACR-hitelesítés egyszerű szolgáltatásokkal](../container-registry/container-registry-auth-service-principal.md) vagy a [Kubernetes egy lekéréses titokkal történő hitelesítését](../container-registry/container-registry-auth-kubernetes.md)ismertető témakört.

Néhány perc elteltével az üzembe helyezés befejeződik, és visszaadja a JSON-formátumú adatokat az AK-beli telepítésről.

> [!NOTE]
> Annak biztosítása érdekében, hogy a fürt megbízhatóan működjön, legalább 2 (két) csomópontot kell futtatnia.

## <a name="install-the-kubernetes-cli"></a>A Kubernetes parancssori felület telepítése

Ahhoz, hogy csatlakozni tudjon a Kubernetes-fürthöz a helyi számítógépről, használja a Kubernetes [kubectl][kubectl] nevű parancssori ügyfelét.

Ha az Azure Cloud Shellt használja, a `kubectl` már telepítve van. Helyben is telepítheti az [az aks install-cli][] paranccsal:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Csatlakozás fürthöz a kubectl használatával

Az [aks get-credentials][] paranccsal konfigurálható`kubectl` a Kubernetes-fürthöz való csatlakozásra. A következő példa a *myAKSCluster* nevű AK-fürt hitelesítő adatait kéri le a *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való kapcsolódás ellenőrzéséhez futtassa a [kubectl Get Nodes][kubectl-get] parancsot a fürtcsomópontok listájának visszaküldéséhez:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-12345678-0   Ready    agent   32m   v1.14.8
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Kubernetes-fürtöt helyezett üzembe az AKS-ben, és úgy konfigurálta a `kubectl` elemet, hogy a fürthöz csatlakozzon. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Az Azure Container registryben hitelesítésre képes Kubernetes AK-fürt üzembe helyezése
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
[quotas-skus-regions]: quotas-skus-regions.md
