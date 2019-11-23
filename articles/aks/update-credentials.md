---
title: Azure Kubernetes Service (ak) fürt hitelesítő adatainak alaphelyzetbe állítása
description: Megtudhatja, hogyan frissítheti vagy állíthatja alaphelyzetbe egy fürt egyszerű szolgáltatásnév hitelesítő adatait az Azure Kubernetes szolgáltatásban (ak)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: bda0ab50b829fa2e6d58e73b51e3a0a0f6c9e2af
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72432912"
---
# <a name="update-or-rotate-the-credentials-for-a-service-principal-in-azure-kubernetes-service-aks"></a>Egyszerű szolgáltatásnév hitelesítő adatainak frissítése vagy elforgatása az Azure Kubernetes szolgáltatásban (ak)

Alapértelmezés szerint az AK-fürtök olyan egyszerű szolgáltatással jönnek létre, amely egy éves lejárati idővel rendelkezik. A lejárati dátum közelében visszaállíthatja a hitelesítő adatokat, hogy a szolgáltatásnév további ideig is kiterjeszthető legyen. Előfordulhat, hogy a hitelesítő adatokat egy meghatározott biztonsági szabályzat részeként szeretné frissíteni vagy elforgatni. Ez a cikk részletesen ismerteti, hogyan frissítheti ezeket a hitelesítő adatokat egy AK-fürthöz.

## <a name="before-you-begin"></a>Előkészületek

Szüksége lesz az Azure CLI-verzió 2.0.65 vagy újabb verziójára, és konfigurálva van. A verzió megkereséséhez futtassa a `az --version`. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését][install-azure-cli]ismertető témakört.

## <a name="choose-to-update-or-create-a-service-principal"></a>Egy egyszerű szolgáltatás frissítésének vagy létrehozásának kiválasztása

Ha egy AK-fürt hitelesítő adatait szeretné frissíteni, a következőket teheti:

* frissítse a fürt által használt meglévő egyszerű szolgáltatás hitelesítő adatait, vagy
* hozzon létre egy egyszerű szolgáltatásnevet, és frissítse a fürtöt az új hitelesítő adatok használatára.

### <a name="update-existing-service-principal-expiration"></a>Meglévő egyszerű szolgáltatásnév lejáratának frissítése

A meglévő szolgáltatásnév hitelesítő adatainak frissítéséhez szerezze be a fürt egyszerű szolgáltatásnév-AZONOSÍTÓját az az az [AK show][az-aks-show] paranccsal. A következő példa lekéri a *myAKSCluster* nevű fürt azonosítóját a *myResourceGroup* erőforráscsoporthoz. Az egyszerű szolgáltatásnév AZONOSÍTÓját *SP_ID* nevű változóként kell beállítani a további parancsokban való használathoz.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Az egyszerű szolgáltatás AZONOSÍTÓját tartalmazó változóval most állítsa alaphelyzetbe a hitelesítő adatokat az [az ad SP hitelesítőadat-visszaállítás][az-ad-sp-credential-reset]használatával. Az alábbi példa lehetővé teszi, hogy az Azure platform új biztonságos titkos kulcsot állítson be az egyszerű szolgáltatás számára. Ez az új biztonsági titok is változóként van tárolva.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Most folytassa az [AK-fürt frissítését az új hitelesítő adatokkal](#update-aks-cluster-with-new-credentials). Ez a lépés szükséges ahhoz, hogy az egyszerű szolgáltatásnév az AK-fürtön tükrözze.

### <a name="create-a-new-service-principal"></a>Új egyszerű szolgáltatás létrehozása

Ha úgy döntött, hogy frissíti a meglévő szolgáltatásnév hitelesítő adatait az előző szakaszban, hagyja ki ezt a lépést. Folytassa az [AK-fürt frissítését az új hitelesítő adatokkal](#update-aks-cluster-with-new-credentials).

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

```azurecli-interactive
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Most folytassa az [AK-fürt frissítését az új hitelesítő adatokkal](#update-aks-cluster-with-new-credentials). Ez a lépés szükséges ahhoz, hogy az egyszerű szolgáltatásnév az AK-fürtön tükrözze.

## <a name="update-aks-cluster-with-new-credentials"></a>AK-fürt frissítése új hitelesítő adatokkal

Függetlenül attól, hogy frissíteni kívánja-e a meglévő szolgáltatásnév hitelesítő adatait, vagy hozzon létre egy egyszerű szolgáltatásnevet, az az [AK Update-hitelesítő adatok][az-aks-update-credentials] paranccsal frissítheti az AK-fürtöt az új hitelesítő adatokkal. A *--Service-Principal* és *--Client-Secret* változók a következők:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Néhány percet vesz igénybe, hogy az egyszerű szolgáltatás hitelesítő adatai frissítve legyenek az AK-ban.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben a saját AK-fürthöz tartozó egyszerű szolgáltatás frissült. A fürtön belüli számítási feladatok identitásának kezelésével kapcsolatos további információkért lásd: [ajánlott eljárások a hitelesítéshez és engedélyezéshez az AK-ban][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
