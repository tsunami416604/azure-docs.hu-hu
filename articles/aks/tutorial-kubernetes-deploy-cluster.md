---
title: Azure-on futó Kubernetes oktatóanyag – Fürtök üzembe helyezése
description: Az Azure Kubernetes Service (AKS) ezen oktatóanyagában egy AKS-fürtöt fog létrehozni, és kapcsolódni fog a Kubernetes-főcsomóponthoz a kubectl használatával.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: e0dea5fcc7860e8653d542653e279010f8ede200
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69898854"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Oktatóanyag: Azure Kubernetes Service (ak) fürt üzembe helyezése

A Kubernetes tárolóalapú alkalmazásokhoz kínál elosztott platformot. Az AK használatával gyorsan létrehozhat egy éles használatra kész Kubernetes-fürtöt. Ebben az oktatóanyagban, amely egy hétrészes sorozat harmadik része, egy Kubernetes-fürtöt helyezünk üzembe az AKS-ben. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Szolgáltatásnév létrehozása az erőforrás-interakciókhoz
> * Kubernetes AKS-fürt üzembe helyezése
> * A Kubernetes parancssori felület (kubectl) telepítése
> * A kubectl konfigurálása az AKS-fürthöz való csatlakozásra

A további oktatóanyagokban az Azure vote-alkalmazás üzembe helyezése a fürtön történik, méretezhető és frissítve.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban létrehoztunk egy tárolórendszerképet, és feltöltöttük egy Azure Container Registry-példányra. Ha még nem tette meg ezeket a lépéseket, és szeretné követni, kezdje az [1. oktatóanyag – tároló lemezképek létrehozása][aks-tutorial-prepare-app]című témakörben.

Ehhez az oktatóanyaghoz az Azure CLI 2.0.53 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Ahhoz, hogy egy AKS-fürt kommunikálhasson más Azure-erőforrásokkal, Azure Active Directory-szolgáltatásnevet kell használnia. A szolgáltatásnév automatikusan létrehozható az Azure CLI-vel vagy a portállal, vagy létrehozhat egyet előre, és hozzárendelhet további engedélyeket. Ebben az oktatóanyagban létrehoz egy szolgáltatásnevet, hozzáférést biztosít az előző oktatóanyagban létrehozott Azure Container Registry (ACR)-példányhoz, majd létrehoz egy AKS-fürtöt.

Hozzon létre egy szolgáltatásnevet az [az ad sp create-for-rbac][] paranccsal. A `--skip-assignment` paraméter korlátozza a további engedélyek hozzárendelését. Alapértelmezés szerint ez az egyszerű szolgáltatás egy évig érvényes.

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

Ahhoz, hogy a megfelelő hozzáférést biztosítsa az AK-fürt számára az ACR-ben tárolt rendszerképek lekéréséhez, rendelje hozzá a `AcrPull` szerepkört az az szerepkör- [hozzárendelés létrehozási][] parancs használatával. Cserélje le az `<appId`> és `<acrId>` helyőrzőket az előző két lépésben beszerzett értékekre.

```azurecli
az role assignment create --assignee <appId> --scope <acrId> --role acrpull
```

## <a name="create-a-kubernetes-cluster"></a>Kubernetes-fürt létrehozása

Az AKS-fürtök képesek a Kubernetes szerepköralapú hozzáférés-vezérlők (RBAC) használatára. Ezekkel a vezérlőkkel a felhasználókhoz rendelt szerepkörök alapján definiálható az erőforrásokhoz való hozzáférés. Az engedélyek kombinálva vannak, ha egy felhasználó több szerepkörhöz van rendelve, és az engedélyek hatóköre egyetlen névtérre vagy a teljes fürtre is kiterjed. Alapértelmezés szerint az Azure CLI automatikusan engedélyezi az RBAC-t az AKS-fürtök létrehozásakor.

AKS-fürtöket az [az aks create][] paranccsal hozhat létre. A következő példában létrehozunk egy *myAKSCluster* nevű fürtöt a *myResourceGroup* nevű erőforráscsoportban. Ez az erőforráscsoport az [előző oktatóanyagban][aks-tutorial-prepare-acr]lett létrehozva. Adja meg a saját `<appId>` és `<password>` értékét az előző lépésből, ahol a szolgáltatásnevet létrehozta.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --service-principal <appId> \
    --client-secret <password> \
    --generate-ssh-keys
```

Néhány perc elteltével az üzembe helyezés befejeződik, és visszaadja a JSON-formátumú adatokat az AK-beli telepítésről.

## <a name="install-the-kubernetes-cli"></a>A Kubernetes parancssori felület telepítése

Ha a helyi számítógépről kíván csatlakozni a Kubernetes-fürthöz, a [kubectl][kubectl], a Kubernetes parancssori ügyfelet kell használnia.

Ha az Azure Cloud Shellt használja, a `kubectl` már telepítve van. Helyben is telepítheti az [az aks install-cli][] paranccsal:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Csatlakozás fürthöz a kubectl használatával

A Kubernetes `kubectl` -fürthöz való kapcsolódás konfigurálásához használja az az az [AK Get-hitelesítőadats][] parancsot. A következő példa a *myAKSCluster* nevű AK-fürt hitelesítő adatait kéri le a *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való kapcsolódás ellenőrzéséhez futtassa a [kubectl Get Nodes][kubectl-get] parancsot:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-12345678-0   Ready    agent   32m   v1.13.10
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
[hozzárendelés létrehozási]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[AK Get-hitelesítőadats]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
