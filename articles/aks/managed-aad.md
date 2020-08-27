---
title: Az Azure AD használata az Azure Kubernetes szolgáltatásban
description: Ismerje meg, hogyan használhatja az Azure AD-t az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: article
ms.date: 08/26/2020
ms.author: thomasge
ms.openlocfilehash: 32273bbb14e6cee73f03bd83b84be77299186370
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936996"
---
# <a name="aks-managed-azure-active-directory-integration"></a>AK által felügyelt Azure Active Directory integráció

Az AK által felügyelt Azure AD-integráció az Azure AD integrációs felületének egyszerűsítésére szolgál, ahol a felhasználóknak korábban egy ügyfélalkalmazás, egy kiszolgálói alkalmazás létrehozása és az Azure AD-bérlő szükséges a címtár-olvasási engedélyek megadásához. Az új verzióban az AK erőforrás-szolgáltató kezeli az ügyfél-és kiszolgálói alkalmazásokat.

## <a name="azure-ad-authentication-overview"></a>Az Azure AD-hitelesítés áttekintése

A Kubernetes szerepköralapú hozzáférés-vezérlést (RBAC) állíthat be a felhasználó identitása vagy a címtár csoporttagság alapján. Az Azure AD-hitelesítés az OpenID-kapcsolattal rendelkező AK-fürtökhöz van megadva. Az OpenID Connect egy OAuth 2,0 protokollra épülő identitási réteg. Az OpenID Connecttel kapcsolatos további információkért tekintse meg az [ID Connect dokumentációját][open-id-connect].

További információ az Azure AD integrációs folyamatáról a [Azure Active Directory Integration Concepts dokumentációjában](concepts-identity.md#azure-active-directory-integration).

## <a name="region-availability"></a>Régiónkénti elérhetőség

Az AK által felügyelt Azure Active Directory integráció olyan nyilvános régiókban érhető el, ahol az [AK támogatott](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).

* A Azure Government jelenleg nem támogatott.
* Az Azure China 21Vianet jelenleg nem támogatott.

## <a name="limitations"></a>Korlátozások 

* Az AK által felügyelt Azure AD-integráció nem tiltható le.
* a nem RBAC-kompatibilis fürtök nem támogatottak az AK által felügyelt Azue AD-integrációhoz
* Az AK által felügyelt Azure AD-integrációhoz társított Azure AD-bérlő módosítása nem támogatott

## <a name="prerequisites"></a>Előfeltételek

* Az Azure CLI verziója 2.11.0 vagy újabb verzió
* Kubectl a [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) vagy a [kubelogin](https://github.com/Azure/kubelogin) minimális verziójával
* Ha a [helmot](https://github.com/helm/helm)használja, a Helm 3,3 minimális verzióját kell használnia.

> [!Important]
> A Kubectl-t a 1.18.1 vagy a kubelogin minimális verziójával kell használni. Ha nem a megfelelő verziót használja, a rendszer a hitelesítési problémákat észleli.

A kubectl és a kubelogin telepítéséhez használja a következő parancsokat:

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

[Ezeket az utasításokat](https://kubernetes.io/docs/tasks/tools/install-kubectl/) más operációs rendszerekhez használhatja.


## <a name="before-you-begin"></a>Előkészületek

A fürthöz Azure AD-csoportra van szükség. Erre a csoportra rendszergazdai csoportként van szükség a fürt rendszergazdai engedélyeinek biztosításához. Használhat meglévő Azure AD-csoportot, vagy létrehozhat egy újat. Jegyezze fel az Azure AD-csoport objektum-AZONOSÍTÓját.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

Ha új Azure AD-csoportot szeretne létrehozni a fürt rendszergazdájához, használja a következő parancsot:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>AK-fürt létrehozása az Azure AD-vel engedélyezve

Hozzon létre egy AK-fürtöt az alábbi CLI-parancsok használatával.

Azure-erőforráscsoport létrehozása:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

AK-fürt létrehozása és rendszergazdai hozzáférés engedélyezése az Azure AD-csoport számára

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Az AK által felügyelt Azure AD-fürtök sikeres létrehozása a válasz törzsének következő szakasza.
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

A fürt létrehozása után megkezdheti a hozzáférését.

## <a name="access-an-azure-ad-enabled-cluster"></a>Hozzáférés egy Azure AD-kompatibilis fürthöz

A következő lépések elvégzéséhez szüksége lesz az [Azure Kubernetes Service cluster felhasználói](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) beépített szerepkörre.

A fürt eléréséhez szükséges felhasználói hitelesítő adatok beszerzése:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
A bejelentkezéshez kövesse az utasításokat.

A fürt csomópontjainak megtekintéséhez használja a kubectl Get Nodes parancsot:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Konfigurálja az [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](./azure-ad-rbac.md) további biztonsági csoportok konfigurálásához a fürtökhöz.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Az Azure AD hozzáférési problémáinak elhárítása

> [!Important]
> Az alábbiakban ismertetett lépések megkerülik a normál Azure AD-csoport hitelesítését. Csak vészhelyzetben használható.

Ha véglegesen letiltja azt, hogy nem fér hozzá a fürthöz hozzáféréssel rendelkező érvényes Azure AD-csoporthoz, akkor továbbra is beszerezheti a rendszergazdai hitelesítő adatokat a fürt közvetlen eléréséhez.

A lépések végrehajtásához hozzá kell férnie az [Azure Kubernetes szolgáltatás-fürt rendszergazdai](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) beépített szerepköréhez.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>Az AK által felügyelt Azure AD-integráció engedélyezése a meglévő fürtön

Engedélyezheti az AK által felügyelt Azure AD-integrációt a meglévő RBAC-kompatibilis fürtön. Győződjön meg arról, hogy a felügyeleti csoport megtartja a hozzáférést a fürtön.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

Az AK által felügyelt Azure AD-fürt sikeres aktiválása a következő szakaszt tartalmazza a válasz törzsében:

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Töltse le újra a felhasználói hitelesítő adatokat a fürt eléréséhez a [következő lépések][access-cluster]végrehajtásával.

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>Frissítés az AK által felügyelt Azure AD-integrációra

Ha a fürt örökölt Azure AD-integrációt használ, frissíthet az AK által felügyelt Azure AD-integrációra.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Egy AK által felügyelt Azure AD-fürt sikeres áttelepítése a következő szakaszt tartalmazza a válasz törzsében:

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Ha el szeretné érni a fürtöt, kövesse az alábbi [lépéseket.][access-cluster]

## <a name="non-interactive-sign-in-with-kubelogin"></a>Nem interaktív bejelentkezés a kubelogin

Vannak olyan nem interaktív forgatókönyvek, mint például a folyamatos integrációs folyamatok, amelyek jelenleg nem érhetők el a kubectl. [`kubelogin`](https://github.com/Azure/kubelogin)A paranccsal hozzáférhet a fürthöz nem interaktív egyszerű szolgáltatás-bejelentkezéssel.

## <a name="next-steps"></a>További lépések

* Tudnivalók az [Azure RBAC-integrációról a Kubernetes-hitelesítéshez][azure-rbac-integration]
* Ismerje meg az [Azure ad-integrációt a KUBERNETES RBAC][azure-ad-rbac].
* A [kubelogin](https://github.com/Azure/kubelogin) használatával férhet hozzá az Azure-hitelesítés olyan szolgáltatásaihoz, amelyek nem érhetők el a kubectl-ben.
* További információ az [AK-ról és a Kubernetes-identitással kapcsolatos fogalmakról][aks-concepts-identity].
* A [Azure Resource Manager-(ARM-) Sablonok ][aks-arm-template] használatával hozzon létre AK által felügyelt Azure ad-kompatibilis fürtöket.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
