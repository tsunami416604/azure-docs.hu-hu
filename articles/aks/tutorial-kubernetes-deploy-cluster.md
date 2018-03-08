---
title: "Azure-on futó Kubernetes oktatóanyag – Fürtök üzembe helyezése"
description: "AKS-oktatóanyag – Fürtök üzembe helyezése"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 975069dbe9283c98482d7d0d5741a595ef323b35
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="deploy-an-azure-container-service-aks-cluster"></a>Azure Container Service- (AKS-) fürt üzembe helyezése

A Kubernetes tárolóalapú alkalmazásokhoz kínál elosztott platformot. Az AKS-sel egyszerűen és gyorsan építhető ki egy éles üzemre kész Kubernetes-fürt. Ebben az oktatóanyagban, amely egy nyolcrészes sorozat harmadik része, egy Kubernetes-fürtöt helyezünk üzembe az AKS-ben. Ennek lépései az alábbiak:

> [!div class="checklist"]
> * Kubernetes AKS-fürt üzembe helyezése
> * A Kubernetes parancssori felület (kubectl) telepítése
> * A kubectl konfigurálása

Az ezt követő oktatóanyagokban üzembe helyezzük majd az Azure Vote alkalmazást a fürtön, méretezzük, frissítjük, majd konfiguráljuk az Operations Management Suite szolgáltatást a Kubernetes-fürt monitorozására.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban létrehoztunk egy tárolórendszerképet, és feltöltöttük egy Azure Container Registry-példányra. Ha ezeket a lépéseket még nem hajtotta végre, és szeretné követni az oktatóanyagot, lépjen vissza az [1. oktatóanyag – Tárolórendszerképek létrehozása][aks-tutorial-prepare-app] részhez.

## <a name="enable-aks-preview"></a>Az AKS előzetes verziójának engedélyezése

Amíg az AKS előzetes verziójú, az új fürtök létrehozásához szolgáltatásjelzőre van szükség az előfizetésén. Ezt a szolgáltatást annyi előfizetésen kérheti, amennyin használni szeretné. Az `az provider register` paranccsal regisztrálja az AKS-szolgáltatót:

```azurecli
az provider register -n Microsoft.ContainerService
```

A regisztrálás után készen áll egy Kubernetes-fürt létrehozására az AKS-sel.

## <a name="create-kubernetes-cluster"></a>Kubernetes-fürt létrehozása

A következő példában létrehozunk egy `myAKSCluster` nevű fürtöt egy `myResourceGroup` nevű erőforráscsoportban. Az erőforráscsoportot [az előző oktatóanyagban][aks-tutorial-prepare-acr] hoztuk létre.

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

Néhány perc múlva befejeződik az üzembe helyezés, és a rendszer visszaadja az AKS-beli üzembe helyezéssel kapcsolatos adatokat JSON formátumban.

## <a name="install-the-kubectl-cli"></a>A kubectl parancssori felület telepítése

Ahhoz, hogy csatlakozni tudjon a Kubernetes-fürthöz az ügyfélszámítógépről, használja a Kubernetes [kubectl][kubectl] nevű parancssori ügyfelét.

Ha az Azure CloudShellt használja, a kubectl már telepítve van. A helyi telepítéséhez futtassa a következő parancsot:

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>Kapcsolódás a kubectl parancssori ügyfélhez

A kubectl a Kubernetes-fürthöz való csatlakozásra konfigurálásához futtassa a következő parancsot:

```azurecli
az aks get-credentials --resource-group=myResourceGroup --name=myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez futtassa a [kubectl get nodes][kubectl-get] parancsot.

```azurecli
kubectl get nodes
```

Kimenet:

```
NAME                          STATUS    AGE       VERSION
k8s-myAKSCluster-36346190-0   Ready     49m       v1.7.9
```

Az oktatóanyag elvégzésével rendelkezésére áll majd egy számítási feladatok végrehajtására kész AKS-fürt. Az ezt követő oktatóanyagokban egy többtárolós alkalmazást helyezünk üzembe a fürtön, majd elvégezzük annak horizontális skálázását, frissítését és monitorozását.

## <a name="configure-acr-authentication"></a>Az ACR-hitelesítés konfigurálása

Hitelesítést kell konfigurálni az AKS-fürt és az ACR-beállításjegyzék között. Ennek részeként megfelelő jogosultságokat kell biztosítania az ACS-identitásnak, hogy rendszerképeket tudjon lekérni az ACR-beállításjegyzékből.

Először szerezze be az AKS-hez konfigurált szolgáltatásnév azonosítóját. Frissítse az erőforráscsoport és az AKS-fürt nevét, hogy megfeleljenek az aktuális környezetnek.

```azurecli
CLIENT_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId" --output tsv)
```

Szerezze be az ACR-beállításjegyzék erőforrás-azonosítóját. Frissítse a beállításjegyzék nevét az ACR-beállításjegyzék nevére, és az erőforráscsoportot arra az erőforráscsoportra, ahol az ACR-beállításjegyzék található.

```azurecli
ACR_ID=$(az acr show --name myACRRegistry --resource-group myResourceGroup --query "id" --output tsv)
```

Hozza létre a megfelelő hozzáférést biztosító szerepkör-hozzárendelést.

```azurecli
az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Kubernetes-fürtöt helyeztünk üzembe az AKS-ben. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
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