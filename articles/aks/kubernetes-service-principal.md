---
title: Az Azure Kubernetes Services (AKS) szolgáltatásnevei
description: Az Azure Kubernetes Service-ben található fürthöz tartozó Azure Active Directory szolgáltatásnév létrehozása és felügyelete
services: container-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 2c792eb4dc060e3f5d7fa2d8f2176bdd51538c43
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392724"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Szolgáltatásnevek és az Azure Kubernetes Service (AKS)

Az Azure API-k használatához az AKS-fürthöz egy [Azure Active Directory (AD) egyszerű szolgáltatásra][aad-service-principal] vagy egy [felügyelt identitásra](use-managed-identity.md)van szükség. Egy egyszerű szolgáltatás vagy felügyelt identitás szükséges más Azure-erőforrások, például egy Azure-terheléselosztó vagy a tároló beállításjegyzék (ACR) dinamikus létrehozásához és kezeléséhez.

Ez a cikk azt mutatja be, hogyan lehet létrehozni és használni egy szolgáltatásnevet az AKS-fürtökhöz.

## <a name="before-you-begin"></a>Előkészületek

Azure AD szolgáltatásnév létrehozásához rendelkeznie kell alkalmazásregisztrációs engedéllyel az Azure AD-bérlőben és alkalmazások szerepkörhöz rendeléséhez az előfizetésben. Ha nem rendelkezik a szükséges engedélyekkel, lehet, hogy meg kell kérnie az Azure AD vagy az előfizetés rendszergazdáját, hogy biztosítsa a szükséges engedélyeket, vagy hogy hozzon létre előzetesen egy szolgáltatásnevet, hogy használhassa az AKS-fürthöz.

Ha egy másik Azure AD-bérlőből származó egyszerű szolgáltatásszolgáltatást használ, a fürt telepítésekor elérhető engedélyek további szempontokat is figyelembe vesz. Lehet, hogy nem rendelkezik a megfelelő engedélyekkel a címtáradatok olvasásához és írásához. További információ: [Mik az alapértelmezett felhasználói engedélyek az Azure Active Directoryban?][azure-ad-permissions]

Az Azure CLI 2.0.59-es vagy újabb verziójára is szüksége van telepítve és konfigurálva. Futtassa `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése][install-azure-cli]című témakört.

## <a name="automatically-create-and-use-a-service-principal"></a>Szolgáltatásnév automatikus létrehozása és használata

Amikor az Azure Portalon vagy az [az aks create][az-aks-create] paranccsal létrehoz egy AKS-fürtöt, az Azure automatikusan létre tud hozni egy szolgáltatásnevet.

Az alábbi Azure CLI-példaparancsban nincs megadva szolgáltatásnév. Ebben a forgatókönyvben az Azure CLI létrehoz egy szolgáltatásnevet az AKS-fürthöz. A művelet sikeres végrehajtásához az Azure-fiókjának rendelkeznie kell a szükséges jogosultságokkal a szolgáltatásnév létrehozásához.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Szolgáltatásnév manuális létrehozása

Egy szolgáltatásnév Azure CLI felületen való manuális létrehozásához használja az [az ad sp create-for-rbac][az-ad-sp-create] parancsot. A következő példában a `--skip-assignment` paraméter megakadályozza bármilyen további alapértelmezett hozzárendelés használatát:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment --name myAKSClusterServicePrincipal
```

A kimenet a következő példához hasonló. Jegyezze fel a saját `appId` és `password` adatait. A rendszer a következő szakaszban ezeket az értékeket fogja használni az AKS-fürtök létrehozása során.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "myAKSClusterServicePrincipal",
  "name": "http://myAKSClusterServicePrincipal",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Szolgáltatásnév megadása AKS-fürthöz

Ha egy meglévő szolgáltatásnevet szeretne használni az AKS-fürt [az aks create][az-aks-create] paranccsal történő létrehozásakor, akkor használja az [az ad sp create-for-rbac][az-ad-sp-create] parancs kimenetéből a `--service-principal` és a `--client-secret` paramétereket az `appId` és a `password` megadásához:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

> [!NOTE]
> Ha egy testre szabott titkos titokkal rendelkező meglévő szolgáltatásnévhasználatát használja, győződjön meg arról, hogy a titkos titok nem haladja meg a 190 bájtot.

Ha az Azure Portal használatával helyez üzembe egy AKS-fürtöt, akkor a **Kubernetes-fürt létrehozása** párbeszédablak *Hitelesítés* oldalán válassza a **Szolgáltatásnév konfigurálása** lehetőséget. Válassza a **Meglévő használata** lehetőséget, majd adja meg a következő értékeket:

- A **szolgáltatásnév ügyfél-azonosítója** az *appId*
- A **szolgáltatásnév titkos ügyfélkulcsa** pedig *jelszó* értéke

![Az Azure Vote keresését ábrázoló kép](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Hozzáférés delegálása más Azure-erőforrásokhoz

Az AKS-fürt egyszerű szolgáltatása más erőforrások elérésére is használható. Ha például az AKS-fürtöt egy meglévő Azure virtuális hálózati alhálózatba szeretné telepíteni, vagy csatlakozni szeretne az Azure Container Registry (ACR) hálózathoz, delegálnia kell az erőforrásokhoz való hozzáférést az egyszerű szolgáltatásnak.

Engedélyek delegálásához hozzon létre egy szerepkör-hozzárendelést az [az szerepkör-hozzárendelés létrehozásparaval.][az-role-assignment-create] Rendelje `appId` hozzá a hatókört egy adott hatókörhöz, például egy erőforráscsoporthoz vagy egy virtuális hálózati erőforráshoz. Ezután egy szerepkör határozza meg, hogy az egyszerű szolgáltatás milyen engedélyekkel rendelkezik az erőforráshoz, ahogy az a következő példában látható:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

Az `--scope` erőforrásnak teljes erőforrás-azonosítónak kell lennie, például */subscriptions/\<\>guid /resourceGroups/myResourceGroup* vagy */subscriptions/\<guid\>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*

A következő szakaszok részletesen ismertetik azokat a gyakori küldöttségeket, amelyeket esetleg meg kell tennie.

### <a name="azure-container-registry"></a>Azure Container Registry

Ha az Azure Container Registry (ACR) a tárolórendszerkép-tároló, engedélyeket kell adnia az AKS-fürt egyszerű az AKS-fürt olvasásához és lekérése. Jelenleg az ajánlott konfiguráció az [az aks create][az-aks-create] vagy az [aks update][az-aks-update] parancs használata a beállításjegyzékbe való integráláshoz és a szolgáltatásnév megfelelő szerepkörének hozzárendeléséhez. Részletes lépéseket az [Azure Kubernetes-szolgáltatás Hitelesítése az Azure Container Registry használatával című témakörben ismer.][aks-to-acr]

### <a name="networking"></a>Hálózat

Használhat speciális hálózati hálózatot, ahol a virtuális hálózat és az alhálózat vagy a nyilvános IP-címek egy másik erőforráscsoportban találhatók. Rendeljen hozzá egyet a következő szerepkör-engedélyek közül:

- Hozzon létre [egyéni szerepkört,][rbac-custom-role] és határozza meg a következő szerepkör-engedélyeket:
  - *Microsoft.Network/virtualNetworks/alhálózatok/join/action*
  - *Microsoft.Network/virtualNetworks/alhálózatok/olvasás*
  - *Microsoft.Network/virtualNetworks/alhálózatok/írás*
  - *Microsoft.Network/publicIPAddresses/join/action*
  - *Microsoft.Network/publicIPAddresses/read*
  - *Microsoft.Network/publicIPAddresses/write*
- Vagy rendelje hozzá a [Hálózati közreműködő][rbac-network-contributor] beépített szerepkört a virtuális hálózaton belüli alhálózaton.

### <a name="storage"></a>Storage

Előfordulhat, hogy egy másik erőforráscsoport ban lévő Lemez-erőforrásokhoz kell hozzáférnie. Rendeljen hozzá egyet a következő szerepkör-engedélyek közül:

- Hozzon létre [egyéni szerepkört,][rbac-custom-role] és határozza meg a következő szerepkör-engedélyeket:
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- Vagy rendelje hozzá a [tárfiók közreműködője][rbac-storage-contributor] beépített szerepkört az erőforráscsoporthoz

### <a name="azure-container-instances"></a>Azure Container Instances

Ha a Virtual Kubelet használatával integrálható az AKS-sel, és úgy dönt, hogy az Azure Container Instances (ACI) az AKS-fürttől különálló erőforráscsoportban fut, az AKS-szolgáltatásnévnek *közreműködői* engedélyt kell adni az ACI erőforráscsoporthoz.

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

AKS és Azure AD szolgáltatásnevek használata esetén vegye figyelembe a következőket.

- A Kubernetes egyszerű szolgáltatása része a fürtkonfigurációnak. Azonban nem ajánlott az identitást használni a fürt üzembe helyezésére.
- Alapértelmezés szerint a szolgáltatásnév hitelesítő adatai egy évig érvényesek. A [szolgáltatásnév hitelesítő adatait bármikor frissítheti vagy elforgathatja.][update-credentials]
- Minden egyszerű szolgáltatás társítva van egy Azure AD-alkalmazáshoz. A Kubernetes-fürt egyszerű szolgáltatása bármely érvényes Azure AD-alkalmazásnévhez társítható (például: *https://www.contoso.org/example*). Az alkalmazás URL-címének nem szükséges valódi végpontnak lennie.
- Amikor megadja a szolgáltatásnév **Client ID-ját** (Ügyfél-azonosítóját), használja az `appId` értékét.
- A Kubernetes-fürtben lévő ügynökcsomópont virtuális gépein a szolgáltatásnév hitelesítő adatai a fájlban tárolódnak`/etc/kubernetes/azure.json`
- Ha az [az aks create][az-aks-create] parancsot használja a szolgáltatásnév automatikus létrehozásához, a szolgáltatásnév hitelesítő adatai a `~/.azure/aksServicePrincipal.json` fájlba lesznek írva azon a gépen, amelyen a parancsot futtatta.
- Ha nem adja át kifejezetten a szolgáltatásnév további AKS CLI-parancsok, az alapértelmezett szolgáltatás névszerint található a `~/.azure/aksServicePrincipal.json` rendszer.  
- Az aksServicePrincipal.json fájlt is eltávolíthatja, és az AKS új egyszerű szolgáltatást hoz létre.
- Az [az aks create][az-aks-create] használatával létrehozott AKS-fürt törlésekor az automatikusan létrehozott szolgáltatásnév nem törlődik.
    - Az egyszerű szolgáltatás törléséhez kérdezze le a *fürtservicePrincipalProfile.clientId lekérdezését,* majd törölje [az ad alkalmazás törlésével.][az-ad-app-delete] Cserélje le a következő erőforráscsoport- és fürtneveket a saját értékeire:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Hibaelhárítás

Az AKS-fürt egyszerű hitelesítő adatait az Azure CLI gyorsítótárazza. Ha ezek a hitelesítő adatok lejártak, hibákat tapasztal az AKS-fürtök telepítésekor. Az [az aks create][az-aks-create] futtatásakor a következő hibaüzenet jelezheti a gyorsítótárazott egyszerű szolgáltatás hitelesítő adataival kapcsolatos problémát:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

Ellenőrizze a hitelesítő adatok fájljának korát a következő paranccsal:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

Az egyszerű szolgáltatás hitelesítő adatainak alapértelmezett lejárati ideje egy év. Ha az *aksServicePrincipal.json* fájl egy évnél régebbi, törölje a fájlt, és próbálja meg újra telepíteni az AKS-fürtöt.

## <a name="next-steps"></a>További lépések

Az Azure Active Directory egyszerű szolgáltatásairól további információt [az Alkalmazás- és egyszerű szolgáltatásobjektumok című témakörben talál.][service-principal]

A hitelesítő adatok frissítésével kapcsolatos további tudnivalókért olvassa el [az AKS szolgáltatásnév hitelesítő adatainak frissítése vagy elforgatása című témakört.][update-credentials]

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
