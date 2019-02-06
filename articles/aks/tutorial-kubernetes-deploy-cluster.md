---
title: Azure-on futó Kubernetes oktatóanyag – Fürtök üzembe helyezése
description: Az Azure Kubernetes Service (AKS) ezen oktatóanyagában egy AKS-fürtöt fog létrehozni, és kapcsolódni fog a Kubernetes-főcsomóponthoz a kubectl használatával.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 54872a1c5a40cdb3f51c17362daed93c3892001e
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754557"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Oktatóanyag: Az Azure Kubernetes Service (AKS)-fürt üzembe helyezése

A Kubernetes tárolóalapú alkalmazásokhoz kínál elosztott platformot. Az aks-sel gyorsan létrehozhat egy üzemkész Kubernetes-fürt. Ebben az oktatóanyagban, amely egy hétrészes sorozat harmadik része, egy Kubernetes-fürtöt helyezünk üzembe az AKS-ben. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Szolgáltatásnév létrehozása az erőforrás-interakciókhoz
> * Kubernetes AKS-fürt üzembe helyezése
> * A Kubernetes parancssori felület (kubectl) telepítése
> * A kubectl konfigurálása az AKS-fürthöz való csatlakozásra

További oktatóanyagok az Azure Vote alkalmazást a fürtön üzembe helyezett, van ellátva, és frissíteni.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban létrehoztunk egy tárolórendszerképet, és feltöltöttük egy Azure Container Registry-példányra. Ha még nem végezte el ezeket a lépéseket, és szeretné követni, kezdőpont [1. oktatóanyag – tárolórendszerképek létrehozása][aks-tutorial-prepare-app].

Ehhez az oktatóanyaghoz, hogy futtat-e az Azure CLI 2.0.53 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Ahhoz, hogy egy AKS-fürt kommunikálhasson más Azure-erőforrásokkal, Azure Active Directory-szolgáltatásnevet kell használnia. A szolgáltatásnév automatikusan létrehozható az Azure CLI-vel vagy a portállal, vagy létrehozhat egyet előre, és hozzárendelhet további engedélyeket. Ebben az oktatóanyagban létrehoz egy szolgáltatásnevet, hozzáférést biztosít az előző oktatóanyagban létrehozott Azure Container Registry (ACR)-példányhoz, majd létrehoz egy AKS-fürtöt.

Hozzon létre egy szolgáltatásnevet az [az ad sp create-for-rbac][] paranccsal. A `--skip-assignment` paraméter korlátozza a további engedélyek hozzárendelését. Alapértelmezés szerint a szolgáltatásnévnek a érvényes egy év.

```azurecli
az ad sp create-for-rbac --skip-assignment
```

A kimenet a következő példához hasonló:

```
{
  "appId": "e7596ae3-6864-4cb8-94fc-20164b1588a9",
  "displayName": "azure-cli-2018-06-29-19-14-37",
  "name": "http://azure-cli-2018-06-29-19-14-37",
  "password": "52c95f25-bd1e-4314-bd31-d8112b293521",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Jegyezze fel az *appID* és a *password* értékét. A következő lépésekben szükség lesz ezekre az értékekre.

## <a name="configure-acr-authentication"></a>Az ACR-hitelesítés konfigurálása

Az ACR-ben tárolt rendszerképek eléréséhez biztosítania kell az AKS szolgáltatásnév számára a megfelelő jogosultságokat a rendszerképek ACR-ből való lekéréséhez.

Először kérje le az ACR-erőforrás azonosítóját az [az acr show][] paranccsal. Frissítse az `<acrName>` beállításjegyzék-nevet az ACR-példánya nevére, és az ACR-példányt tároló erőforráscsoport nevére.

```azurecli
az acr show --resource-group myResourceGroup --name <acrName> --query "id" --output tsv
```

Az AKS-fürt lekéréses rendszerképeket az ACR tárolt megfelelő hozzáférést, rendelje hozzá a `AcrPull` szerepkör a [az szerepkör-hozzárendelés létrehozása][] parancsot. Cserélje le az `<appId`> és `<acrId>` helyőrzőket az előző két lépésben beszerzett értékekre.

```azurecli
az role assignment create --assignee <appId> --scope <acrId> --role acrpull
```

## <a name="create-a-kubernetes-cluster"></a>Kubernetes-fürt létrehozása

Az AKS-fürtök képesek a Kubernetes szerepköralapú hozzáférés-vezérlők (RBAC) használatára. Ezekkel a vezérlőkkel a felhasználókhoz rendelt szerepkörök alapján definiálható az erőforrásokhoz való hozzáférés. Engedélyek mostantól Ha egy felhasználóhoz több szerepkört, és az engedélyek hatóköre beállítható egyetlen névtér vagy az egész fürt között. Alapértelmezés szerint az Azure CLI automatikusan engedélyezi az RBAC-t az AKS-fürtök létrehozásakor.

AKS-fürtöket az [az aks create][] paranccsal hozhat létre. A következő példában létrehozunk egy *myAKSCluster* nevű fürtöt a *myResourceGroup* nevű erőforráscsoportban. Ezt az erőforráscsoportot [az előző oktatóanyagban][aks-tutorial-prepare-acr] hoztuk létre. Adja meg a saját `<appId>` és `<password>` értékét az előző lépésből, ahol a szolgáltatásnevet létrehozta.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --service-principal <appId> \
    --client-secret <password> \
    --generate-ssh-keys
```

Néhány perc múlva a telepítés befejeződik, és az AKS üzembe helyezéssel kapcsolatos adatokat JSON formátumban adja vissza.

## <a name="install-the-kubernetes-cli"></a>A Kubernetes parancssori felület telepítése

Ahhoz, hogy csatlakozni tudjon a Kubernetes-fürthöz a helyi számítógépről, használja a Kubernetes [kubectl][kubectl] nevű parancssori ügyfelét.

Ha az Azure Cloud Shellt használja, a `kubectl` már telepítve van. Helyben is telepítheti az [az aks install-cli][] paranccsal:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Csatlakozás fürthöz a kubectl használatával

Konfigurálása `kubectl` a Kubernetes-fürt csatlakozni, használja a [az aks get-credentials][] parancsot. Az alábbi példa lekéri az AKS-fürtöt nevű hitelesítő adatok *myAKSCluster* a a *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez futtassa a [kubectl get nodes][kubectl-get] parancsot:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-28993262-0   Ready    agent   3m18s   v1.9.11
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Kubernetes-fürtöt helyezett üzembe az AKS-ben, és úgy konfigurálta a `kubectl` elemet, hogy a fürthöz csatlakozzon. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Szolgáltatásnév létrehozása az erőforrás-interakciókhoz
> * Kubernetes AKS-fürt üzembe helyezése
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
[az szerepkör-hozzárendelés létrehozása]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
