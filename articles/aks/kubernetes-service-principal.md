---
title: Az Azure Kubernetes Services (AKS) szolgáltatásnevei
description: Az Azure Kubernetes Service-ben található fürthöz tartozó Azure Active Directory szolgáltatásnév létrehozása és felügyelete
services: container-service
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 523f08ddbf22e175af5b0604b04d4a2460ffd634
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259421"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Szolgáltatásnevek és az Azure Kubernetes Service (AKS)

Az Azure API-kkal való kommunikációhoz egy AK-fürthöz [Azure Active Directory (ad) egyszerű szolgáltatásnév][aad-service-principal]szükséges. A szolgáltatásnévvel dinamikusan hozhat létre és kezelhet olyan egyéb Azure-erőforrásokat, mint az Azure Load Balancer vagy a tárolóregisztrációs adatbázis (ACR).

Ez a cikk azt mutatja be, hogyan lehet létrehozni és használni egy szolgáltatásnevet az AKS-fürtökhöz.

## <a name="before-you-begin"></a>Előkészületek

Azure AD szolgáltatásnév létrehozásához rendelkeznie kell alkalmazásregisztrációs engedéllyel az Azure AD-bérlőben és alkalmazások szerepkörhöz rendeléséhez az előfizetésben. Ha nem rendelkezik a szükséges engedélyekkel, lehet, hogy meg kell kérnie az Azure AD vagy az előfizetés rendszergazdáját, hogy biztosítsa a szükséges engedélyeket, vagy hogy hozzon létre előzetesen egy szolgáltatásnevet, hogy használhassa az AKS-fürthöz.

Ha egy másik Azure AD-bérlőből származó szolgáltatásnevet használ, további szempontokat is figyelembe kell vennie a fürt telepítésekor elérhető engedélyek köré. Előfordulhat, hogy nem rendelkezik a megfelelő engedélyekkel a címtáradatok olvasásához és írásához. További információ: [Mi az alapértelmezett felhasználói engedély a Azure Active Directory-ben?][azure-ad-permissions]

Szüksége lesz az Azure CLI 2.0.59 vagy újabb verziójára is, valamint a telepítésre és konfigurálásra. A verzió megkereséséhez futtassa a `az --version`. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését][install-azure-cli]ismertető témakört.

## <a name="automatically-create-and-use-a-service-principal"></a>Szolgáltatásnév automatikus létrehozása és használata

Amikor egy AK-fürtöt hoz létre a Azure Portal vagy az az [AK Create][az-aks-create] paranccsal, az Azure automatikusan létrehoz egy egyszerű szolgáltatást.

Az alábbi Azure CLI-példaparancsban nincs megadva szolgáltatásnév. Ebben a forgatókönyvben az Azure CLI létrehoz egy szolgáltatásnevet az AKS-fürthöz. A művelet sikeres végrehajtásához az Azure-fiókjának rendelkeznie kell a szükséges jogosultságokkal a szolgáltatásnév létrehozásához.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Szolgáltatásnév manuális létrehozása

Ha manuálisan szeretne létrehozni egy egyszerű szolgáltatást az Azure CLI-vel, használja az az [ad SP Create-for-RBAC][az-ad-sp-create] parancsot. A következő példában a `--skip-assignment` paraméter megakadályozza bármilyen további alapértelmezett hozzárendelés használatát:

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

Ha egy meglévő szolgáltatásnevet szeretne használni, amikor AK-fürtöt hoz létre az az [AK Create][az-aks-create] paranccsal, használja a `--service-principal` és a `--client-secret` paramétereket az az [ad SP Create-RBAC][az-ad-sp-create] parancs kimenetében lévő `appId` és `password` megadásához:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

> [!NOTE]
> Ha olyan meglévő szolgáltatásnevet használ, amelynek testreszabott titka van, ügyeljen arra, hogy a titok ne legyen 190 bájtnál hosszabb.

Ha az Azure Portal használatával helyez üzembe egy AKS-fürtöt, akkor a *Kubernetes-fürt létrehozása* párbeszédablak **Hitelesítés** oldalán válassza a **Szolgáltatásnév konfigurálása** lehetőséget. Válassza a **Meglévő használata** lehetőséget, majd adja meg a következő értékeket:

- A **szolgáltatásnév ügyfél-azonosítója** az *appId*
- A **szolgáltatásnév titkos ügyfélkulcsa** pedig *jelszó* értéke

![Az Azure Vote keresését ábrázoló kép](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Más Azure-erőforrásokhoz való hozzáférés delegálása

Az AK-fürthöz tartozó egyszerű szolgáltatás használható más erőforrások elérésére. Ha például az AK-fürtöt egy meglévő Azure-beli virtuális hálózati alhálózatba kívánja telepíteni, vagy a Azure Container Registryhoz (ACR-hez) szeretne csatlakozni, akkor az erőforrásokhoz való hozzáférést delegálnia kell az egyszerű szolgáltatáshoz.

Az engedélyek delegálásához hozzon létre egy szerepkör-hozzárendelést az az [role hozzárendelés Create][az-role-assignment-create] paranccsal. Rendelje hozzá a `appId` egy adott hatókörhöz, például egy erőforráscsoport vagy egy virtuális hálózati erőforráshoz. A szerepkör ezután meghatározza, hogy az egyszerű szolgáltatásnév milyen engedélyekkel rendelkezik az erőforráson, ahogy az az alábbi példában is látható:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

Egy erőforrás `--scope` teljes erőforrás-AZONOSÍTÓnak kell lennie, például */subscriptions/\<guid\>/resourceGroups/myResourceGroup* vagy */subscriptions/\<GUID\>/resourceGroups/myResourceGroupVnet/Providers/Microsoft.Network/virtualNetworks/myVnet*

A következő részek részletesen ismertetik a közös delegálásokat, amelyeket érdemes lehet megtennie.

### <a name="azure-container-registry"></a>Azure Container Registry

Ha Azure Container Registry (ACR) tároló lemezkép-tárolót használ, engedélyeket kell adnia az AK-fürthöz tartozó egyszerű szolgáltatásnév számára a képek olvasásához és lekéréséhez. Jelenleg az ajánlott konfiguráció az az [AK Create][az-aks-create] vagy [az Kaba Update][az-aks-update] parancs használata a beállításjegyzékbe való integráláshoz és az egyszerű szolgáltatásnév megfelelő szerepkörének hozzárendeléséhez. A részletes lépésekért lásd: [hitelesítés a Azure Container Registry az Azure Kubernetes szolgáltatással][aks-to-acr].

### <a name="networking"></a>Hálózat

Használhat olyan speciális hálózatkezelést, ahol a virtuális hálózat és az alhálózat vagy a nyilvános IP-címek egy másik erőforráscsoporthoz tartoznak. Rendelje hozzá a következő szerepkör-engedélyek egyikét:

- Hozzon létre egy [Egyéni szerepkört][rbac-custom-role] , és adja meg a következő szerepkör-engedélyeket:
  - *Microsoft. Network/virtualNetworks/alhálózatok/csatlakozás/művelet*
  - *Microsoft. Network/virtualNetworks/alhálózatok/olvasás*
  - *Microsoft. Network/virtualNetworks/alhálózatok/írás*
  - *Microsoft. Network/nyilvános IP/csatlakozás/művelet*
  - *Microsoft. Network/nyilvános IP/READ*
  - *Microsoft. Network/nyilvános IP/Write*
- Vagy rendelje hozzá a [hálózati közreműködő][rbac-network-contributor] beépített szerepkört a virtuális hálózaton belüli alhálózathoz.

### <a name="storage"></a>Tárterület

Előfordulhat, hogy egy másik erőforráscsoport meglévő lemez-erőforrásaihoz is hozzá kell férnie. Rendelje hozzá a következő szerepkör-engedélyek egyikét:

- Hozzon létre egy [Egyéni szerepkört][rbac-custom-role] , és adja meg a következő szerepkör-engedélyeket:
  - *Microsoft. számítás/lemezek/olvasás*
  - *Microsoft. számítás/lemezek/írás*
- Vagy rendelje hozzá a [Storage-fiók közreműködője][rbac-storage-contributor] beépített szerepkört az erőforráscsoporthoz.

### <a name="azure-container-instances"></a>Azure Container Instances

Ha virtuális Kubelet használ az AK-val való integrációhoz, és úgy dönt, hogy a (z) Azure Container Instances (ACI)-t futtatja egy, az AK -fürttől független erőforráscsoporthoz

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

AKS és Azure AD szolgáltatásnevek használata esetén vegye figyelembe a következőket.

- A Kubernetes egyszerű szolgáltatása része a fürtkonfigurációnak. Azonban nem ajánlott az identitást használni a fürt üzembe helyezésére.
- Alapértelmezés szerint az egyszerű szolgáltatás hitelesítő adatai egy évig érvényesek. Bármikor [frissítheti vagy elforgathatja a szolgáltatás egyszerű hitelesítő adatait][update-credentials] .
- Minden egyszerű szolgáltatás társítva van egy Azure AD-alkalmazáshoz. A Kubernetes-fürt szolgáltatásneve társítható bármilyen érvényes Azure AD-alkalmazásnévhez (például: *https://www.contoso.org/example* ). Az alkalmazás URL-címének nem szükséges valódi végpontnak lennie.
- Amikor megadja a szolgáltatásnév **Client ID-ját** (Ügyfél-azonosítóját), használja az `appId` értékét.
- A Kubernetes-fürt ügynök csomópontjának virtuális gépei esetében az egyszerű szolgáltatás hitelesítő adatait a rendszer a fájlban tárolja `/etc/kubernetes/azure.json`
- Ha az az [AK Create][az-aks-create] parancsot használja az egyszerű szolgáltatás automatikus létrehozásához, az egyszerű szolgáltatás hitelesítő adatait a rendszer a parancs futtatásához használt számítógépen `~/.azure/aksServicePrincipal.json` fájlba írja.
- Ha nem adott meg konkrét szolgáltatásnevet a további AK CLI-parancsokban, a rendszer az alapértelmezett egyszerű szolgáltatásnevet használja `~/.azure/aksServicePrincipal.json`.  
- Szükség esetén eltávolíthatja a aksServicePrincipal. JSON fájlt is, és az AK létrehoz egy új szolgáltatásnevet.
- Amikor töröl egy olyan AK-fürtöt, amelyet az [az AK Create][az-aks-create], az automatikusan létrehozott egyszerű szolgáltatás nem törlődik.
    - Az egyszerű szolgáltatásnév törléséhez kérdezze le a *servicePrincipalProfile. clientId* , majd törölje az az [AD App delete][az-ad-app-delete]paranccsal. Cserélje le a következő erőforráscsoportot és a fürtök nevét a saját értékeire:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Hibaelhárítás

Az AK-fürtökhöz tartozó egyszerű hitelesítő adatokat az Azure CLI gyorsítótárazza. Ha ezek a hitelesítő adatok lejártak, az AK-fürtök üzembe helyezésekor hibákba ütközik. A következő hibaüzenet jelenik meg, amikor futtatja az [az AK Create][az-aks-create] hibát jelez a gyorsítótárazott szolgáltatásnév hitelesítő adataival:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

A következő parancs használatával keresse meg a hitelesítő adatok fájljának korát:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

Az egyszerű szolgáltatás hitelesítő adatainak alapértelmezett lejárati ideje egy év. Ha a *aksServicePrincipal. JSON* fájl egy évnél régebbi, törölje a fájlt, és próbálkozzon újra egy AK-fürt üzembe helyezésével.

## <a name="next-steps"></a>Következő lépések

Az Azure Active Directory egyszerű szolgáltatásaival kapcsolatos további információkért lásd: [alkalmazás-és egyszerű szolgáltatások objektumai][service-principal].

A hitelesítő adatok frissítésével kapcsolatos információkért lásd: [az egyszerű szolgáltatásnév hitelesítő adatainak frissítése vagy elforgatása az AK-ban][update-credentials].

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
