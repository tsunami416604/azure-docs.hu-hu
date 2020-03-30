---
title: A kubeconfig hoz való hozzáférés korlátozása az Azure Kubernetes szolgáltatásban (AKS)
description: A Kubernetes konfigurációs fájlhoz (kubeconfig) való hozzáférés fürtrendszergazdák és fürtfelhasználók számára idotartama
services: container-service
ms.topic: article
ms.date: 01/28/2020
ms.openlocfilehash: 25c710cce2855d6af985d3f46082f47573bbc101
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259551"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Azure szerepköralapú hozzáférés-vezérléshasználatával definiálhatja a Kubernetes konfigurációs fájljához való hozzáférést az Azure Kubernetes-szolgáltatásban (AKS)

Az eszköz segítségével kommunikálhat a `kubectl` Kubernetes-fürtökkel. Az Azure CLI segítségével egyszerűen lejuthat az AKS-fürtökhöz való csatlakozáshoz `kubectl`szükséges hozzáférési hitelesítő adatok és konfigurációs információk lefelvételére. Ha korlátozni szeretné, hogy ki kaphatja le a Kubernetes-konfiguráció *(kubeconfig)* adatait, és hogy korlátozza az engedélyeket, akkor használhatja az Azure szerepköralapú hozzáférés-vezérléseket (RBAC).

Ez a cikk bemutatja, hogyan rendelhet RBAC-szerepköröket, amelyek korlátozzák, hogy ki kértem le az AKS-fürt konfigurációs adatait.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AKS-fürttel. Ha AKS-fürtre van szüksége, tekintse meg az AKS [gyorsútmutatót az Azure CLI használatával][aks-quickstart-cli] vagy az Azure Portal [használatával.][aks-quickstart-portal]

Ez a cikk azt is megköveteli, hogy az Azure CLI 2.0.65-ös vagy újabb verzióját futtassa. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Elérhető fürtszerepkörök engedélyei

Amikor az eszközzel lép kapcsolatba `kubectl` egy AKS-fürttel, a rendszer egy konfigurációs fájlt használ, amely meghatározza a fürtkapcsolat adatait. Ez a konfigurációs fájl általában *~/.kube/config*helyen van tárolva. Ebben a *kubeconfig* fájlban több fürt is definiálható. A fürtök között a [kubectl config use-context][kubectl-config-use-context] paranccsal válthat.

Az [az aks get-credentials][az-aks-get-credentials] parancs lehetővé teszi, hogy egy AKS-fürt hozzáférési hitelesítő adatait bekérhesse, és egyesíti őket a *kubeconfig* fájlba. Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) használatával szabályozhatja a hitelesítő adatokhoz való hozzáférést. Ezek az Azure RBAC-szerepkörök segítségével meghatározhatja, hogy ki kérheti le a *kubeconfig* fájlt, és milyen engedélyekkel rendelkeznek a fürtön belül.

A két beépített szerepkör a következő:

* **Azure Kubernetes szolgáltatásfürt felügyeleti szerepkör**  
  * Engedélyezi a *microsoft.ContainerService/managedClusters/listClusterAdminCredential/action* API-hívás elérését. Ez az API-hívás [felsorolja a fürt felügyeleti hitelesítő adatait.][api-cluster-admin]
  * Letölti *kubeconfig* a *clusterAdmin* szerepkörhöz.
* **Azure Kubernetes szolgáltatásfürt felhasználói szerepköre**
  * Engedélyezi a *microsoft.ContainerService/managedClusters/listClusterUserCredential/action* API-hívás elérését. Ez az API-hívás [felsorolja a fürt felhasználói hitelesítő adatait.][api-cluster-user]
  * Letölti *a kubeconfig-ot* a *clusterUser* szerepkörhöz.

Ezek az RBAC-szerepkörök egy Azure Active Directory (AD) felhasználóra vagy -csoportra alkalmazhatók.

> ! - Nem, nem, nem, nem, nem, Az Azure AD-t használó fürtökön a *fürtuseri* szerepkörrel rendelkező felhasználók egy üres *kubeconfig* fájllal rendelkeznek, amely bejelentkezést kér. Miután bejelentkezett, a felhasználók az Azure AD-felhasználó vagy csoport beállításai alapján férhetnek hozzá. A *clusterAdmin* szerepkörrel rendelkező felhasználók rendszergazdai hozzáféréssel rendelkeznek.
>
> Fürtök, amelyek nem használják az Azure AD csak a *clusterAdmin* szerepkört használja.

## <a name="assign-role-permissions-to-a-user-or-group"></a>Szerepkör-engedélyek hozzárendelése felhasználóhoz vagy csoporthoz

A rendelkezésre álló szerepkörök hozzárendeléséhez le kell szereznie az AKS-fürt erőforrás-azonosítóját és az Azure AD felhasználói fiók vagy -csoport azonosítóját. A következő példaparancsok:

* A fürterőforrás-azonosító beszereznie az [aks show][az-aks-show] parancsot a myResourceGroup erőforráscsoportban a *myResourceGroup* erőforráscsoportmyAKSCluster nevű fürthöz. *myAKSCluster* Adja meg a saját fürt- és erőforráscsoport nevét, ha szükséges.
* Használja az [az fiók show][az-account-show] és az az [ad user show][az-ad-user-show] parancsokat, hogy a felhasználói azonosítót.
* Végül rendeljen hozzá egy szerepkört az [az szerepkör-hozzárendelés létrehozási][az-role-assignment-create] parancshasználatával.

A következő példa az *Azure Kubernetes szolgáltatásfürt felügyeleti szerepkörét* rendeli hozzá egy egyéni felhasználói fiókhoz:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!TIP]
> Ha engedélyeket szeretne hozzárendelni egy Azure AD-csoporthoz, frissítse az `--assignee` előző példában látható paramétert a *csoport* objektumazonosítójával, nem pedig egy *felhasználóval.* Egy csoport objektumazonosítójának beszerzéséhez használja az [az ad group show][az-ad-group-show] parancsot. A következő példa lekéri az *alkalmazáskivés*nevű Azure AD-csoport objektumazonosítóját:`az ad group show --group appdev --query objectId -o tsv`

Az előző hozzárendelést szükség szerint módosíthatja a *fürtfelhasználói szerepkörre.*

A következő példakimenet azt mutatja, hogy a szerepkör-hozzárendelés sikeresen létrejött:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>A konfigurációs adatok be- és ellenőrzése

RBAC-szerepkörök hozzárendelésével az [az aks get-credentials][az-aks-get-credentials] paranccsal lekéri a *kubeconfig* definíciót az AKS-fürthöz. A következő példa beszerzi a *--admin* hitelesítő adatokat, amelyek megfelelően működnek, ha a felhasználó megkapta a *fürtfelügyeleti szerepkört:*

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Ezután a [kubectl konfigurációs nézet][kubectl-config-view] paranccsal ellenőrizheti, hogy a fürt *környezete* azt mutatja-e, hogy a rendszergazdai konfigurációs adatok at alkalmaztak:

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>Szerepkör-engedélyek eltávolítása

A szerepkör-hozzárendelések eltávolításához használja az [az szerepkör-hozzárendelés törlés parancsát.][az-role-assignment-delete] Adja meg a fiókazonosítót és a fürterőforrás-azonosítót az előző parancsokban kapott módon. Ha a szerepkört felhasználó helyett egy csoporthoz rendelte, adja meg a megfelelő csoportobjektum-azonosítót a `--assignee` paraméterhöz tartozó fiókobjektum-azonosító helyett:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>További lépések

Az AKS-fürtökhöz való hozzáférés fokozott biztonsága érdekében [integrálja az Azure Active Directory-hitelesítést.][aad-integration]

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: azure-ad-integration.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
