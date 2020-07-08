---
title: Fürt hitelesítő adatainak alaphelyzetbe állítása
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan frissítheti vagy állíthatja alaphelyzetbe az Azure Kubernetes Service (ak) fürthöz tartozó egyszerű szolgáltatásnév vagy HRE-alkalmazás hitelesítő adatait
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 7dcbd91063d4f36c4d78023b6548db0c968eda74
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86077694"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service (ak) hitelesítő adatainak frissítése vagy elforgatása

Alapértelmezés szerint az AK-fürtök olyan egyszerű szolgáltatással jönnek létre, amely egy éves lejárati idővel rendelkezik. A lejárati dátum közelében visszaállíthatja a hitelesítő adatokat, hogy a szolgáltatásnév további ideig is kiterjeszthető legyen. Előfordulhat, hogy a hitelesítő adatokat egy meghatározott biztonsági szabályzat részeként szeretné frissíteni vagy elforgatni. Ez a cikk részletesen ismerteti, hogyan frissítheti ezeket a hitelesítő adatokat egy AK-fürthöz.

Előfordulhat, hogy az [AK-fürtöt Azure Active Directory-mel integrálta][aad-integration], és a fürthöz hitelesítő szolgáltatóként használja. Ebben az esetben a fürthöz, a HRE Server-alkalmazáshoz és a HRE-ügyfélalkalmazáshoz két további identitást kell létrehoznia, ezeket a hitelesítő adatokat is visszaállíthatja.

Azt is megteheti, hogy egy egyszerű szolgáltatásnév helyett felügyelt identitást is használhat az engedélyekhez. A felügyelt identitások könnyebben kezelhetők, mint az egyszerű szolgáltatások, és nem igényelnek frissítést vagy rotációt. További információ: [felügyelt identitások használata](use-managed-identity.md).

## <a name="before-you-begin"></a>Előkészületek

Szüksége lesz az Azure CLI-verzió 2.0.65 vagy újabb verziójára, és konfigurálva van.  `az --version`A verzió megkereséséhez futtassa a parancsot. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését][install-azure-cli]ismertető témakört.

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Új egyszerű szolgáltatás frissítése vagy létrehozása az AK-fürthöz

Ha egy AK-fürt hitelesítő adatait szeretné frissíteni, a következőket teheti:

* frissítse a fürt által használt meglévő egyszerű szolgáltatás hitelesítő adatait, vagy
* hozzon létre egy egyszerű szolgáltatásnevet, és frissítse a fürtöt az új hitelesítő adatok használatára.

### <a name="check-the-expiration-date-of-your-service-principal"></a>Az egyszerű szolgáltatás lejárati idejének megtekintése

Az egyszerű szolgáltatás lejárati idejének ellenőrzéséhez használja az az [ad SP hitelesítőadat-lista][az-ad-sp-credential-list] parancsot. A következő példa beolvassa a *myAKSCluster* nevű fürt egyszerű SZOLGÁLTATÁSának azonosítóját a *myResourceGroup* -erőforráscsoporthoz az az [AK show][az-aks-show] parancs használatával. Az egyszerű szolgáltatás azonosítója *SP_ID* nevű változóként van beállítva az az [ad SP hitelesítőadat-lista][az-ad-sp-credential-list] paranccsal való használatra.

```azurecli
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id $SP_ID --query "[].endDate" -o tsv
```

### <a name="reset-existing-service-principal-credential"></a>Meglévő egyszerű szolgáltatásnév hitelesítő adatainak alaphelyzetbe állítása

A meglévő szolgáltatásnév hitelesítő adatainak frissítéséhez szerezze be a fürt egyszerű szolgáltatásnév-AZONOSÍTÓját az az az [AK show][az-aks-show] paranccsal. A következő példa lekéri a *myAKSCluster* nevű fürt azonosítóját a *myResourceGroup* erőforráscsoporthoz. Az egyszerű szolgáltatásnév AZONOSÍTÓját *SP_ID* nevű változóként kell beállítani a további parancsokban való használathoz. Ezek a parancsok a bash szintaxisát használják.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Az egyszerű szolgáltatás AZONOSÍTÓját tartalmazó változóval most állítsa alaphelyzetbe a hitelesítő adatokat az [az ad SP hitelesítőadat-visszaállítás][az-ad-sp-credential-reset]használatával. Az alábbi példa lehetővé teszi, hogy az Azure platform új biztonságos titkos kulcsot állítson be az egyszerű szolgáltatás számára. Ez az új biztonsági titok is változóként van tárolva.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Most folytassa az [AK-fürt frissítését az új egyszerű szolgáltatás hitelesítő adataival](#update-aks-cluster-with-new-service-principal-credentials). Ez a lépés szükséges ahhoz, hogy az egyszerű szolgáltatásnév az AK-fürtön tükrözze.

### <a name="create-a-new-service-principal"></a>Új egyszerű szolgáltatás létrehozása

Ha úgy döntött, hogy frissíti a meglévő szolgáltatásnév hitelesítő adatait az előző szakaszban, hagyja ki ezt a lépést. Folytassa az [AK-fürt frissítését az új egyszerű szolgáltatás hitelesítő adataival](#update-aks-cluster-with-new-service-principal-credentials).

Egyszerű szolgáltatásnév létrehozásához, majd az AK-fürt frissítéséhez az új hitelesítő adatok használatára használja az az [ad SP Create-for-RBAC][az-ad-sp-create] parancsot. A következő példában a `--skip-assignment` paraméter megakadályozza bármilyen további alapértelmezett hozzárendelés használatát:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A kimenet a következő példához hasonló. Jegyezze fel a saját `appId` és `password` adatait. Ezek az értékek a következő lépésben használatosak.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Most adja meg az egyszerű szolgáltatásnév és az ügyfél titkos KÓDJÁnak változóit az az [ad SP Create-for-RBAC][az-ad-sp-create] parancs kimenete alapján, az alábbi példában látható módon. A *SP_ID* a *AppID*, és a *SP_SECRET* a *jelszava*:

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Most folytassa az [AK-fürt frissítését az új egyszerű szolgáltatás hitelesítő adataival](#update-aks-cluster-with-new-service-principal-credentials). Ez a lépés szükséges ahhoz, hogy az egyszerű szolgáltatásnév az AK-fürtön tükrözze.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>AK-fürt frissítése az új egyszerű szolgáltatás hitelesítő adataival

Függetlenül attól, hogy frissíteni kívánja-e a meglévő szolgáltatásnév hitelesítő adatait, vagy hozzon létre egy egyszerű szolgáltatásnevet, az az [AK Update-hitelesítő adatok][az-aks-update-credentials] paranccsal frissítheti az AK-fürtöt az új hitelesítő adatokkal. A *--Service-Principal* és *--Client-Secret* változók a következők:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret "$SP_SECRET"
```

Néhány percet vesz igénybe, hogy az egyszerű szolgáltatás hitelesítő adatai frissítve legyenek az AK-ban.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>AK-fürt frissítése új HRE-alkalmazás hitelesítő adataival

A [HRE-integráció lépéseinek][create-aad-app]követésével új HRE-kiszolgálót és-ügyfélalkalmazások hozhatók létre. Vagy állítsa alaphelyzetbe a meglévő HRE-alkalmazásait az [egyszerű szolgáltatás alaphelyzetbe állítására szolgáló metódust](#reset-existing-service-principal-credential)követve. Ezt követően csak frissítenie kell a fürt HRE-alkalmazásának hitelesítő adatait ugyanazzal az [AK Update-hitelesítő adatokkal][az-aks-update-credentials] , de a *--reset-HRE* változók használatával.

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>További lépések

Ebben a cikkben a saját AK-fürthöz tartozó egyszerű szolgáltatás és a HRE integrációs alkalmazásai is frissültek. A fürtön belüli számítási feladatok identitásának kezelésével kapcsolatos további információkért lásd: [ajánlott eljárások a hitelesítéshez és engedélyezéshez az AK-ban][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: azure-ad-integration.md
[create-aad-app]: azure-ad-integration.md#create-the-server-application
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-list]: /cli/azure/ad/sp/credential#az-ad-sp-credential-list
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
