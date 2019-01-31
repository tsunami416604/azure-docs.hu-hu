---
title: Az Azure Kubernetes Service (AKS)-fürt hitelesítő adatainak alaphelyzetbe állítása
description: Ismerje meg, hogyan frissítés vagy visszaállítása az egyszerű szolgáltatás hitelesítő adatait egy fürtöt az Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/30/2019
ms.author: iainfou
ms.openlocfilehash: 8552a22db1518fe41be78d67f91d74f0370abd93
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55479305"
---
# <a name="update-or-rotate-the-credentials-for-a-service-principal-in-azure-kubernetes-service-aks"></a>Frissítés vagy a hitelesítő adatok forgatása egyszerű szolgáltatások Azure Kubernetes Service (AKS)

Alapértelmezés szerint az AKS-fürtök jönnek létre, amely egy éves lejárati ideje szolgáltatásnévvel. Az Ön közelében a lejárati dátum alaphelyzetbe állíthatja a bővítése az egyszerű szolgáltatás egy további időszakban a hitelesítő adatokat. Érdemes azt is, frissítéséhez vagy, egy meghatározott biztonsági szabályzat részeként a hitelesítő adatok forgatása. Ez a cikk részletesen bemutatja ezeket a hitelesítő adatokat az AKS-fürt frissítéséhez.

## <a name="before-you-begin"></a>Előkészületek

Az Azure CLI 2.0.56 verziójára van szükség, vagy később telepített és konfigurált. Futtatás `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni, tekintse meg kell [Azure CLI telepítése][install-azure-cli].

## <a name="choose-to-update-or-create-a-service-principal"></a>Válassza ki a frissíteni vagy egyszerű szolgáltatás létrehozása

Ha meg szeretné frissíteni az AKS-fürt hitelesítő adatait, a választhat:

* Frissítse a hitelesítő adatokat a meglévő szolgáltatásnévhez a fürt által használt vagy
* Hozzon létre egy egyszerű szolgáltatást, és frissítse a fürt ezen új hitelesítő adatokkal.

Ha azt szeretné, hozzon létre egy egyszerű szolgáltatást az AKS-fürt frissítése, hagyja ki az a jelen szakaszban ismertetett lépések és továbbra is [egyszerű szolgáltatás létrehozása](#create-a-service-principal). Ha szeretné frissíteni a hitelesítő adatait a meglévő egyszerű szolgáltatást az AKS-fürt által használt, folytassa a jelen szakaszban ismertetett lépéseket.

### <a name="get-the-service-principal-id"></a>A résztvevő-azonosító beszerzése

A létező egyszerű szolgáltatás hitelesítő adatainak frissítéséhez, kérje le a szolgáltatásnév-Azonosítót, a fürt használata a [az aks show] [ az-aks-show] parancsot. Az alábbi példa lekéri a fürt nevű azonosítója *myAKSCluster* a a *myResourceGroup* erőforráscsoportot. A szolgáltatásnév-Azonosítót további parancs használható változóként van megadva.

```azurecli-interactive
SP_ID=$(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
```

### <a name="update-the-service-principal-credentials"></a>A szolgáltatásnév hitelesítő adatainak frissítése

A változó beállítása, amely tartalmazza a szolgáltatásnév-Azonosítót, állítsa alaphelyzetbe a hitelesítő adatok használatával [az ad sp hitelesítő adatok alaphelyzetbe állítása][az-ad-sp-credential-reset]. Az alábbi példa az Azure-biztonságos új titkos kód létrehozása a szolgáltatásnévhez tartozó platform lehetővé teszi. Az új biztonságos titkos kulcsot is egy változóban van tárolva.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $AKS_SP --query password -o tsv)
```

Most már továbbléphet a következőre [AKS-fürt frissítése új hitelesítő adatokkal](#update-aks-cluster-with-new-credentials).

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Ha úgy döntött, hogy frissítse a meglévő egyszerű szolgáltatás hitelesítő adatai az előző szakaszban, kihagyhatja ezt a lépést. Továbbra is [AKS-fürt frissítése új hitelesítő adatokkal](#update-aks-cluster-with-new-credentials).

Hozzon létre egy egyszerű szolgáltatást, és frissítse az AKS-fürt új ezekkel a hitelesítő adatokkal, használja a [az ad sp create-for-rbac] [ az-ad-sp-create] parancsot. A következő példában a `--skip-assignment` paraméter megakadályozza bármilyen további alapértelmezett hozzárendelés használatát:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A kimenet a következő példához hasonló. Jegyezze fel a saját `appId` és `password` adatait. Ezekkel az értékekkel a következő lépésben.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Ezután határozza meg a szolgáltatás egyszerű azonosító és a titkos ügyfélkódra kimenetét a saját használatával változóival [az ad sp create-for-rbac] [ az-ad-sp-create] parancsot, az alábbi példában látható módon. A *SP_ID* van a *appId*, és a *SP_SECRET* van a *jelszó*:

```azurecli-interactive
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

## <a name="update-aks-cluster-with-new-credentials"></a>AKS-fürt frissítése új hitelesítő adatokkal

Függetlenül attól, hogy úgy döntött, hogy a létező egyszerű szolgáltatás hitelesítő adatainak frissítéséhez, vagy hozzon létre egy egyszerű szolgáltatást, most frissítenie az AKS-fürtöt az új hitelesítő adatok használatával a [az aks-hitelesítő adatok frissítése] [ az-aks-update-credentials] parancsot. Változói a *– egyszerű szolgáltatás* és *--client-secret* használhatók:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Az egyszerű szolgáltatás hitelesítő adatai az AKS frissíteni kell néhány másodpercig tart.

## <a name="next-steps"></a>További lépések

Ez a cikk az AKS-fürthöz tartozó egyszerű szolgáltatás frissítve lett. Identitáskezelés a fürtön belüli számítási feladatokhoz való további információkért lásd: [gyakorlati tanácsok a hitelesítés és engedélyezés az aks-ben][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
