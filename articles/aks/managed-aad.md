---
title: Az Azure AD használata az Azure Kubernetes szolgáltatásban
description: Ismerje meg, hogyan használhatja az Azure AD-t az Azure Kubernetes szolgáltatásban (ak)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 05/11/2020
ms.openlocfilehash: 67f5f707ad2971551e3c9623dd5c07ad880afcf2
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211144"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Az Azure AD integrálása az Azure Kubernetes szolgáltatásban (előzetes verzió)

> [!Note]
> A HRE (Azure Active Directory) integrációt tartalmazó meglévő AK-fürtöket nem érinti az új AK által felügyelt HRE-élmény.

Az AK által felügyelt HRE való Azure AD-integráció lehetővé teszi az Azure AD integrációs felületének egyszerűsítését, ahol a felhasználóknak korábban egy ügyfélalkalmazás, egy kiszolgálói alkalmazás létrehozása és az Azure AD-bérlő szükséges a címtár-olvasási engedélyek megadásához. Az új verzióban az AK erőforrás-szolgáltató kezeli az ügyfél-és kiszolgálói alkalmazásokat.

## <a name="limitations"></a>Korlátozások

* Jelenleg nem frissíthet egy meglévő AK-HRE integrált fürtöt az új AK által felügyelt HRE-élményre.

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói az önkiszolgáló, a választható lehetőségek alapján érhetők el. Az előzetes verziók az "adott állapotban" és "elérhetőként" jelennek meg, és ki vannak zárva a szolgáltatói szerződésekből és a korlátozott jótállásból. A (z) és az ügyfél-támogatási szolgálatok a lehető leghatékonyabban fedezik az előzetes verziókat. Ezért ezeket a funkciókat nem éles használatra szánták. További információkért lásd a következő támogatási cikkeket:
>
> - [AK-támogatási szabályzatok](support-policies.md)
> - [Azure-támogatás – gyakori kérdések](faq.md)

## <a name="before-you-begin"></a>Előkészületek

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

A kubectl telepítéséhez használja a következőt:

```azurecli
sudo az aks install-cli
kubectl version --client
```

[Ezeket az utasításokat](https://kubernetes.io/docs/tasks/tools/install-kubectl/) más operációs rendszerekhez használhatja.

> [!CAUTION]
> Miután regisztrált egy szolgáltatást egy előfizetéshez, jelenleg nem tudja törölni a szolgáltatást. Az előzetes verziójú funkciók engedélyezésekor az alapértelmezett beállítások az előfizetésben később létrehozott AK-fürtökhöz is használhatók. Ne engedélyezze az előzetes verziójú funkciókat az éles előfizetésekben. Ehelyett használjon külön előfizetést az előzetes verziójú funkciók tesztelésére és visszajelzések gyűjtésére.

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

Most létrehozhat egy AK-fürtöt az alábbi parancssori felületi parancsokkal.

Először hozzon létre egy Azure-erőforráscsoportot:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Ezután hozzon létre egy AK-fürtöt:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
A fenti parancs létrehoz egy három csomópontos AK-fürtöt, de a fürtöt létrehozó felhasználó alapértelmezés szerint nem tagja egy olyan csoportnak, amely hozzáfér ehhez a fürthöz. A felhasználónak létre kell hoznia egy Azure AD-csoportot, fel kell vennie magát a csoport tagjaként, majd frissítenie kell a fürtöt az alábbiak szerint. Kövesse az [alábbi utasításokat](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)

Miután létrehozott egy csoportot, és hozzáadta saját magát (és másokat) tagként, a következő parancs használatával frissítheti a fürtöt az Azure AD-csoporttal.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```
Ha először létrehoz egy csoportot, és tagokat ad hozzá, akkor az alábbi parancs használatával engedélyezheti az Azure AD-csoportot a létrehozás ideje alatt.

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

Az AK által felügyelt HRE-fürtök sikeres létrehozása a válasz törzsének következő szakasza.
```
"Azure ADProfile": {
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
A rendszergazdai hitelesítő adatok beszerzése a fürt eléréséhez:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
Most használja a kubectl Get Nodes parancsot a fürt csomópontjainak megtekintéséhez:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

A fürt eléréséhez szükséges felhasználói hitelesítő adatok beszerzése:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
A bejelentkezéshez kövesse az utasításokat.

A következőt kapja: be **kell jelentkeznie a kiszolgálóra (nem engedélyezett)**

A fenti felhasználó hibaüzenetet kap, mert a felhasználó nem része egy olyan csoportnak, amely hozzáfér a fürthöz.

## <a name="next-steps"></a>További lépések

* Az [Azure ad szerepköralapú Access Control][azure-ad-rbac]megismerése.
* [Kubelogin](https://github.com/Azure/kubelogin) használata az Azure-hitelesítés olyan szolgáltatásainak eléréséhez, amelyek nem érhetők el a kubectl-ben.

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
