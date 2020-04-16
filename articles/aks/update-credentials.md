---
title: Fürt hitelesítő adatainak alaphelyzetbe állítása
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogy miként frissíti vagy alaphelyzetbe állítja az Egyszerű szolgáltatás vagy az AAD-alkalmazás hitelesítő adatait egy Azure Kubernetes-fürt (AKS) fürthöz
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 8420771e32aa792aa79a07fdf4362ad0d9b45d48
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392634"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Az Azure Kubernetes-szolgáltatás (AKS) hitelesítő adatainak frissítése vagy elforgatása

Alapértelmezés szerint az AKS-fürtök egy egyéves lejárati idővel rendelkező egyszerű szolgáltatással jönnek létre. A lejárati dátum közeledtével alaphelyzetbe állíthatja a hitelesítő adatokat, hogy a szolgáltatásnév további időtartamra meghosszabbítsa. Előfordulhat, hogy egy meghatározott biztonsági házirend részeként szeretné frissíteni vagy elforgatni a hitelesítő adatokat. Ez a cikk ismerteti, hogyan frissítheti ezeket a hitelesítő adatokat egy AKS-fürthöz.

Előfordulhat, hogy [az AKS-fürtöt is integrálta az Azure Active Directoryval,][aad-integration]és a fürt hitelesítési szolgáltatójaként használhatja. Ebben az esetben további 2 identitást hoz létre a fürthöz, az AAD-kiszolgáló alkalmazáshoz és az AAD-ügyfélalkalmazáshoz, és alaphelyzetbe állíthatja ezeket a hitelesítő adatokat is.

Azt is megteheti, hogy egy felügyelt identitás engedélyek helyett egy egyszerű szolgáltatás. A felügyelt identitások kezelése könnyebb, mint a szolgáltatástagok, és nem igényelnek frissítéseket vagy rotációkat. További információ: [Felügyelt identitások használata.](use-managed-identity.md)

## <a name="before-you-begin"></a>Előkészületek

Az Azure CLI 2.0.65-ös vagy újabb verziójára van szükség telepítve és konfigurálva. Futtassa `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése][install-azure-cli]című témakört.

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Új egyszerű szolgáltatás frissítése vagy létrehozása az AKS-fürthöz

Ha frissíteni szeretné egy AKS-fürt hitelesítő adatait, a következőkközül választhat:

* a fürt által használt meglévő egyszerű szolgáltatás hitelesítő adatainak frissítése, vagy
* hozzon létre egy egyszerű szolgáltatás, és frissítse a fürtet, hogy használja ezeket az új hitelesítő adatokat.

### <a name="reset-existing-service-principal-credential"></a>Meglévő egyszerű szolgáltatáshitelesítő adatok alaphelyzetbe állítása

A meglévő egyszerű szolgáltatás hitelesítő adatainak frissítéséhez az [az aks show][az-aks-show] parancs használatával lekell kérnie a fürt egyszerű szolgáltatásazonosítóját. A következő példa lekéri a *myResourceGroup* erőforráscsoport ban a *myResourceGroup* nevű fürt azonosítóját. Az egyszerű szolgáltatásazonosító *SP_ID* nevű változóként van beállítva a további parancsokban való használatra.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Az egyszerű szolgáltatásazonosítót tartalmazó változókészlet teljében most állítsa alaphelyzetbe a hitelesítő adatokat az [ad sp hitelesítő adatok alaphelyzetbe állításával.][az-ad-sp-credential-reset] A következő példa lehetővé teszi, hogy az Azure platform hozzon létre egy új biztonságos titkos kulcsot a szolgáltatásnév. Ez az új biztonságos titkos kulcsot is változóként tárolják.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Most folytassa az [AKS-fürt frissítését az új egyszerű szolgáltatás hitelesítő adatokkal.](#update-aks-cluster-with-new-service-principal-credentials) Ez a lépés szükséges ahhoz, hogy a rendszernévi műveletek tükrözzék az AKS-fürtöt.

### <a name="create-a-new-service-principal"></a>Új egyszerű szolgáltatás létrehozása

Ha úgy döntött, hogy frissíti a meglévő egyszerű szolgáltatás hitelesítő adatait az előző szakaszban, hagyja ki ezt a lépést. Folytassa az [AKS-fürt frissítését az új egyszerű szolgáltatáshitelesítő adatokkal.](#update-aks-cluster-with-new-service-principal-credentials)

Egyszerű szolgáltatás létrehozásához, majd frissítse az AKS-fürtet az új hitelesítő adatok használatához, használja az [az ad sp create-for-rbac][az-ad-sp-create] parancsot. A következő példában a `--skip-assignment` paraméter megakadályozza bármilyen további alapértelmezett hozzárendelés használatát:

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

Most definiálja a változókat a szolgáltatás egyszerű azonosító és az ügyfél titkos kimenethasználatával a saját [az ad sp create-for-rbac][az-ad-sp-create] parancs, ahogy az alábbi példában látható. A *SP_ID* az ön *appId*, és a *SP_SECRET* a *jelszó:*

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Most folytassa az [AKS-fürt frissítését az új egyszerű szolgáltatás hitelesítő adatokkal.](#update-aks-cluster-with-new-service-principal-credentials) Ez a lépés szükséges ahhoz, hogy a rendszernévi műveletek tükrözzék az AKS-fürtöt.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>Az AKS-fürt frissítése új egyszerű szolgáltatáshitelesítő adatokkal

Függetlenül attól, hogy úgy döntött, hogy frissíti a hitelesítő adatokat a meglévő egyszerű szolgáltatás, vagy hozzon létre egy egyszerű szolgáltatás, most frissíti az AKS-fürt az új hitelesítő adatok az [az aks update-credentials][az-aks-update-credentials] paranccsal. A változók a *--service-principal* és *--client-secret* a következők:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Néhány percet vesz igénybe, amíg a szolgáltatásnév hitelesítő adatait frissíteni kell az AKS-ben.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>Az AKS-fürt frissítése új AAD-alkalmazáshitelesítő adatokkal

Új AAD-kiszolgáló- és ügyfélalkalmazásokat hozhat létre az [AAD-integrációs lépések][create-aad-app]végrehajtásával. Vagy állítsa alaphelyzetbe a meglévő AAD-alkalmazásokat [a szolgáltatásegyszerű alaphelyzetbe állításával megegyező módszerrel.](#reset-existing-service-principal-credential) Ezt követően csak frissítenie kell a fürt AAD-alkalmazás hitelesítő adatait ugyanazzal az [aks update-credentials][az-aks-update-credentials] paranccsal, de a *--reset-aad* változók használatával.

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

Ebben a cikkben az AKS-fürt és az AAD-integrációs alkalmazások szolgáltatásnév frissítése megtörtént. A fürtön belüli számítási feladatok identitásának kezeléséről az [AKS-ben a hitelesítéssel és engedélyezéssel kapcsolatos gyakorlati tanácsok][best-practices-identity]című témakörben talál további információt.

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: azure-ad-integration.md
[create-aad-app]: azure-ad-integration.md#create-the-server-application
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
