---
title: Azure-on futó Kubernetes oktatóanyag – Fürtök üzembe helyezése
description: AKS-oktatóanyag – Fürtök üzembe helyezése
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: c8698f16138e9baeb9c9c1142a5d0c8937a69d1b
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341399"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Oktatóanyag: Azure Kubernetes Service- (AKS-) fürt üzembe helyezése

A Kubernetes tárolóalapú alkalmazásokhoz kínál elosztott platformot. Az AKS-sel gyorsan kiépíthet egy éles üzemre kész Kubernetes-fürtöt. Ebben az oktatóanyagban, amely egy hétrészes sorozat harmadik része, egy Kubernetes-fürtöt helyezünk üzembe az AKS-ben. Ennek lépései az alábbiak:

> [!div class="checklist"]
> * Szolgáltatásnév létrehozása erőforrás-interakciókhoz
> * Kubernetes AKS-fürt üzembe helyezése
> * A Kubernetes parancssori felület (kubectl) telepítése
> * A kubectl konfigurálása

Az ezt követő oktatóanyagokban üzembe helyezzük az Azure Vote alkalmazást a fürtön, skálázzuk és frissítjük.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban létrehoztunk egy tárolórendszerképet, és feltöltöttük egy Azure Container Registry-példányra. Ha ezeket a lépéseket még nem hajtotta végre, és szeretné követni az oktatóanyagot, lépjen vissza az [1. oktatóanyag – Tárolórendszerképek létrehozása][aks-tutorial-prepare-app] részhez.

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Ahhoz, hogy egy AKS-fürt kommunikálhasson más Azure-erőforrásokkal, Azure Active Directory-szolgáltatásnevet kell használnia. A szolgáltatásnév automatikusan létrehozható az Azure CLI-vel vagy a portállal, vagy létrehozhat egyet előre, és hozzárendelhet további engedélyeket. Ebben az oktatóanyagban létrehoz egy szolgáltatásnevet, hozzáférést biztosít az előző oktatóanyagban létrehozott Azure Container Registry (ACR)-példányhoz, majd létrehoz egy AKS-fürtöt.

Hozzon létre egy szolgáltatásnevet az [az ad sp create-for-rbac][] paranccsal. A `--skip-assignment` paraméter korlátozza a további engedélyek hozzárendelését.

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
az acr show --name <acrName> --resource-group myResourceGroup --query "id" --output tsv
```

Ahhoz, hogy megfelelő hozzáférést adjon az AKS-fürtnek az ACR-ben tárolt rendszerképek használatához, hozzon létre egy szerepkör-kijelölést az [az role assignment create][] paranccsal. Cserélje le az `<appId`> és `<acrId>` helyőrzőket az előző két lépésben beszerzett értékekre.

```azurecli
az role assignment create --assignee <appId> --role Reader --scope <acrId>
```

## <a name="create-kubernetes-cluster"></a>Kubernetes-fürt létrehozása

Most hozzon létre egy AKS-fürtöt az [az aks create][] paranccsal. A következő példában létrehozunk egy *myAKSCluster* nevű fürtöt egy *myResourceGroup* nevű erőforráscsoportban. Ezt az erőforráscsoportot [az előző oktatóanyagban][aks-tutorial-prepare-acr] hoztuk létre. Adja meg a saját `<appId>` és `<password>` értékét az előző lépésből, ahol létrehozta a szolgáltatásnevet.

```azurecli
az aks create \
    --name myAKSCluster \
    --resource-group myResourceGroup \
    --node-count 1 \
    --generate-ssh-keys \
    --service-principal <appId> \
    --client-secret <password>
```

Néhány perc után befejeződik az üzembe helyezés, és a rendszer visszaadja az AKS-beli üzembe helyezéssel kapcsolatos adatokat JSON formátumban.

## <a name="install-the-kubectl-cli"></a>A kubectl parancssori felület telepítése

Ahhoz, hogy csatlakozni tudjon a Kubernetes-fürthöz az ügyfélszámítógépről, használja a Kubernetes [kubectl][kubectl] nevű parancssori ügyfelét.

Ha az Azure Cloud Shellt használja, a kubectl már telepítve van. Helyben is telepítheti az [az aks install-cli][] paranccsal:

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>Kapcsolódás a kubectl parancssori ügyfélhez

Az [az aks get-credentials][] paranccsal konfigurálhatja a kubectl-t a Kubernetes-fürthöz való csatlakozásra. A következő példa lekéri a *myResourceGroup* erőforrásban lévő *myAKSCluster* AKS-fürtnév hitelesítő adatait:

```azurecli
az aks get-credentials --name myAKSCluster --resource-group myResourceGroup
```

A fürthöz való csatlakozás ellenőrzéséhez futtassa a [kubectl get nodes][kubectl-get] parancsot.

```azurecli
kubectl get nodes
```

Kimenet:

```
NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-66427764-0   Ready     agent     9m        v1.9.6
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Kubernetes-fürtöt helyeztünk üzembe az AKS-ben. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * Létrehozott egy szolgáltatásnevet az erőforrás-interakciókhoz
> * Üzembe helyezett egy Kubernetes AKS-fürtöt
> * Telepítette a Kubernetes parancssori felületet (kubectl)
> * Konfigurálta a kubectl parancssori felületet

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan futtathatók alkalmazások a fürtön.

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
