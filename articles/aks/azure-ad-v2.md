---
title: Az Azure AD használata az Azure Kubernetes szolgáltatásban
description: Ismerje meg, hogyan használhatja az Azure AD-t az Azure Kubernetes-szolgáltatásban (AKS)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 03/24/2020
ms.openlocfilehash: 430df504c677b005f5ff5e7fdd9346aed3e168af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294456"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Az Azure AD integrálása az Azure Kubernetes szolgáltatásban (előzetes verzió)

> [!Note]
> Az aKS v1-es integrációval rendelkező meglévő AKS-fürtöket nem érinti az új AKS v2-élmény.

Az Azure AD-integráció az AKS v2-vel úgy lett kialakítva, hogy egyszerűsítse az Azure AD-integrációt az AKS v1-es felhasználói felülettel, ahol a felhasználóknak ügyfélalkalmazást, kiszolgálóalkalmazást kellett létrehozniuk, és az Azure AD-bérlőnek címtárolvasási engedélyeket kellett adniuk. Az új verzióban az AKS-erőforrás-szolgáltató kezeli az ügyfél- és kiszolgálóalkalmazásokat.

## <a name="limitations"></a>Korlátozások

* Jelenleg nem frissíthet egy meglévő Azure AD-kompatibilis AKS-v1-fürtöt a v2-es élményre.

> [!IMPORTANT]
> Az AKS előzetes funkciók önkiszolgáló, opt-in alapon érhetők el. Az előzetes verziók "adott verzióban" és "ahogy elérhetők", és nem tartoznak a szolgáltatásiszint-szerződések és a korlátozott jótállás hatálya alá. Az AKS-előzeteseket részben az ügyfélszolgálat fedezi a legjobb erőfeszítés alapján. Mint ilyen, ezek a funkciók nem célja a termelés használatra. További információt az alábbi támogatási cikkekben talál:
>
> - [Az AKS támogatási irányelvei](support-policies.md)
> - [Azure-támogatás – gyakori kérdések](faq.md)

## <a name="before-you-begin"></a>Előkészületek

A következő erőforrásokat kell telepítenie:

- Az Azure CLI 2.2.0-s vagy újabb verziója
- Az aks-preview 0.4.38 kiterjesztés
- Kubectl egy minimális változata [1,18 béta](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#client-binaries)

Az aks-preview bővítmény vagy újabb verzió telepítéséhez/frissítéséhez használja a következő Azure CLI-parancsokat:

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

A kubectl telepítéséhez használja az alábbi
```azurecli
curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.18.0-beta.2/bin/darwin/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version --client
```

Ezeket az [utasításokat](https://kubernetes.io/docs/tasks/tools/install-kubectl/) más operációs rendszerekhez is használhatja.

> [!CAUTION]
> Miután regisztrált egy szolgáltatást egy előfizetésen, jelenleg nem függeszheti le a szolgáltatást. Ha engedélyezi az előzetes verzió egyes szolgáltatásait, előfordulhat, hogy az előfizetésben később létrehozott összes AKS-fürthöz alapértelmezett értékeket használ. Az éles előfizetések előzetes funkcióinak engedélyezése nem lehetséges. Ehelyett használjon külön előfizetést az előzetes verzió funkcióinak teszteléséhez és a visszajelzések összegyűjtéséhez.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Az állapot **regisztrált**állapotként való megjelenítése több percig is eltarthat. A regisztrációs állapotot az [az szolgáltatáslista](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) paranccsal ellenőrizheti:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Ha az állapot regisztráltként jelenik `Microsoft.ContainerService` meg, frissítse az erőforrás-szolgáltató regisztrációját az [az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) paranccsal:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>AKS-fürt létrehozása engedélyezve az Azure AD-vel

Most már létrehozhat egy AKS-fürtöt a következő CLI-parancsokkal.

Először hozzon létre egy Azure-erőforráscsoportot:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Ezután hozzon létre egy AKS-fürtöt:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
A fenti parancs három csomópontos AKS-fürtöt hoz létre, de a fürtet létrehozó felhasználó alapértelmezés szerint nem tagja annak a csoportnak, amely hozzáfér ehhez a fürthöz. Ennek a felhasználónak létre kell hoznia egy Azure AD-csoportot, hozzá kell adnia magát a csoport tagjaként, majd frissítenie kell a fürtöt az alábbiak szerint. Kövesse az utasításokat [itt](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)

Miután létrehozott egy csoportot, és felvette magát (és másokat) tagként, frissítheti a fürtöt az Azure AD csoporttal a következő paranccsal

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id1,id2>] [--aad-tenant-id <id>]
```
Másik lehetőségként, ha először hoz létre egy csoportot, és tagokat ad hozzá, engedélyezheti az Azure AD csoportot a létrehozási időben a következő paranccsal,

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id1,id2>] [--aad-tenant-id <id>]
```

Egy Azure AD v2-fürt sikeres létrehozása a következő szakaszt rendelkezik a választörzsben
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
A rendszergazda hitelesítő adatainak beszereznie a fürt eléréséhez:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
Most használja a kubectl get csomópontok parancsot a fürt csomópontjainak megtekintéséhez:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

A fürt eléréséhez szükséges felhasználói hitelesítő adatok beszerezni:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
A bejelentkezéshez kövesse az utasításokat.

Ön kap: **Be kell jelentkeznie a szerverre (jogosulatlan)**

A fenti felhasználó hibaüzenetet kap, mert a felhasználó nem része a fürthöz hozzáféréssel rendelkező csoportnak.

## <a name="next-steps"></a>További lépések

További információ az [Azure AD szerepköralapú hozzáférés-vezérlésről.][azure-ad-rbac]

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md