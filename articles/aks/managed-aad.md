---
title: Az Azure AD használata az Azure Kubernetes szolgáltatásban
description: Ismerje meg, hogyan használhatja az Azure AD-t az Azure Kubernetes szolgáltatásban (ak)
services: container-service
manager: gwallace
author: mlearned
ms.topic: article
ms.date: 06/25/2020
ms.author: mlearned
ms.openlocfilehash: f22b79cb8a730fb9c28dd1a208ab672473218b79
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86105948"
---
# <a name="aks-managed-azure-active-directory-integration-preview"></a>AK által felügyelt Azure Active Directory integráció (előzetes verzió)

> [!NOTE]
> Az új AK által felügyelt Azure AD-felület nem érinti a meglévő AK (Azure Kubernetes Service) fürtök Azure Active Directory (Azure AD) integrációját.

Az AK által felügyelt Azure AD-integráció az Azure AD integrációs felületének egyszerűsítésére szolgál, ahol a felhasználóknak korábban egy ügyfélalkalmazás, egy kiszolgálói alkalmazás létrehozása és az Azure AD-bérlő szükséges a címtár-olvasási engedélyek megadásához. Az új verzióban az AK erőforrás-szolgáltató kezeli az ügyfél-és kiszolgálói alkalmazásokat.

## <a name="azure-ad-authentication-overview"></a>Az Azure AD-hitelesítés áttekintése

A Kubernetes szerepköralapú hozzáférés-vezérlést (RBAC) állíthat be a felhasználó identitása vagy a címtár csoporttagság alapján. Az Azure AD-hitelesítés az OpenID-kapcsolattal rendelkező AK-fürtökhöz van megadva. Az OpenID Connect egy OAuth 2,0 protokollra épülő identitási réteg. Az OpenID Connecttel kapcsolatos további információkért tekintse meg az [ID Connect dokumentációját][open-id-connect].

A HRE integrációs folyamatáról a [Azure Active Directory Integration Concepts dokumentációjában](concepts-identity.md#azure-active-directory-integration)olvashat bővebben.

## <a name="limitations"></a>Korlátozások

* Jelenleg nem frissíthet egy meglévő AK Azure AD-integrált fürtöt az új AK által felügyelt Azure AD-élményre.

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói az önkiszolgáló, a választható lehetőségek alapján érhetők el. Az előzetes verziók az "adott állapotban" és "elérhetőként" jelennek meg, és ki vannak zárva a szolgáltatói szerződésekből és a korlátozott jótállásból. A (z) és az ügyfél-támogatási szolgálatok a lehető leghatékonyabban fedezik az előzetes verziókat. Ezért ezeket a funkciókat nem éles használatra szánták. További információkért lásd a következő támogatási cikkeket:
>
> - [AK-támogatási szabályzatok](support-policies.md)
> - [Azure-támogatás – gyakori kérdések](faq.md)

## <a name="before-you-begin"></a>Előkészületek

* Keresse meg az Azure-fiók bérlői AZONOSÍTÓját a Azure Portal navigálva, és válassza ki Azure Active Directory > tulajdonságok > könyvtár-azonosító

> [!Important]
> A Kubectl-et legalább 1,18-es verzióval kell használni

A következő erőforrásokat kell telepítenie:

- Az Azure CLI, 2.5.1-es vagy újabb verzió
- Az AK – előzetes verziójú 0.4.38 bővítmény
- Kubectl legalább [1,18](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1180) -es verzióval

A következő Azure CLI-parancsokkal telepítheti/frissítheti az AK-előnézet bővítményt vagy újabb verziót:

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

A kubectl telepítéséhez használja a következő parancsokat:

```azurecli
sudo az aks install-cli
kubectl version --client
```

[Ezeket az utasításokat](https://kubernetes.io/docs/tasks/tools/install-kubectl/) más operációs rendszerekhez használhatja.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Több percet is igénybe vehet, amíg az állapot **regisztrálva**jelenik meg. A regisztrációs állapotot az az [Feature List](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) parancs használatával tekintheti meg:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Ha az állapot regisztrálva értékre van állítva, frissítse az `Microsoft.ContainerService` erőforrás-szolgáltató regisztrációját az az [Provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) paranccsal:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>AK-fürt létrehozása az Azure AD-vel engedélyezve

Hozzon létre egy AK-fürtöt az alábbi CLI-parancsok használatával.

Azure-erőforráscsoport létrehozása:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Használhat meglévő Azure AD-csoportot, vagy létrehozhat egy újat. Szüksége lesz az Azure AD-csoport objektumazonosító-AZONOSÍTÓJÁRA.

```azurecli-interactive
# List existing groups in the directory
az ad group list
```

Ha új Azure AD-csoportot szeretne létrehozni a fürt rendszergazdájához, használja a következő parancsot:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name MyDisplay --mail-nickname MyDisplay
```

AK-fürt létrehozása és rendszergazdai hozzáférés engedélyezése az Azure AD-csoport számára

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

Az AK által felügyelt Azure AD-fürtök sikeres létrehozása a válasz törzsének következő szakasza.
```
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

A fürt néhány percen belül létrejön.

## <a name="access-an-azure-ad-enabled-cluster"></a>Hozzáférés egy Azure AD-kompatibilis fürthöz

A következő lépések elvégzéséhez szüksége lesz az [Azure Kubernetes Service cluster felhasználói](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-user-role) beépített szerepkörre.

A fürt eléréséhez szükséges felhasználói hitelesítő adatok beszerzése:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
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
[Szerepköralapú Access Control (RBAC)](https://docs.microsoft.com/azure/aks/azure-ad-rbac) konfigurálása a fürtökhöz tartozó további biztonsági csoportok konfigurálásához.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Az Azure AD hozzáférési problémáinak elhárítása

> [!Important]
> Az alábbiakban ismertetett lépések megkerülik a normál Azure AD-csoport hitelesítését. Csak vészhelyzetben használható.

Ha véglegesen letiltja azt, hogy nem fér hozzá a fürthöz hozzáféréssel rendelkező érvényes Azure AD-csoporthoz, akkor továbbra is beszerezheti a rendszergazdai hitelesítő adatokat a fürt közvetlen eléréséhez.

A lépések végrehajtásához hozzá kell férnie az [Azure Kubernetes szolgáltatás-fürt rendszergazdai](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-admin-role) beépített szerepköréhez.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```

## <a name="non-interactive-sign-in-with-kubelogin"></a>Nem interaktív bejelentkezés a kubelogin

Vannak olyan nem interaktív forgatókönyvek, mint például a folyamatos integrációs folyamatok, amelyek jelenleg nem érhetők el a kubectl. [`kubelogin`](https://github.com/Azure/kubelogin)A paranccsal hozzáférhet a fürthöz nem interaktív egyszerű szolgáltatás-bejelentkezéssel.

## <a name="next-steps"></a>Következő lépések

* Tudnivalók az [Azure RBAC-integrációról a Kubernetes-hitelesítéshez][azure-rbac-integration]
* Ismerje meg az [Azure ad-integrációt a KUBERNETES RBAC][azure-ad-rbac].
* A [kubelogin](https://github.com/Azure/kubelogin) használatával férhet hozzá az Azure-hitelesítés olyan szolgáltatásaihoz, amelyek nem érhetők el a kubectl-ben.
* További információ az [AK-ról és a Kubernetes-identitással kapcsolatos fogalmakról][aks-concepts-identity].
* A [Azure Resource Manager-(ARM-) Sablonok][aks-arm-template] használatával hozzon létre AK által felügyelt Azure ad-kompatibilis fürtöket.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: https://docs.microsoft.com/azure/templates/microsoft.containerservice/managedclusters

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

