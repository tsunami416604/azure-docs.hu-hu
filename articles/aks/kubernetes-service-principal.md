---
title: Az Azure Kubernetes-fürthöz tartozó egyszerű szolgáltatás
description: Kubernetes-fürthöz tartozó Azure Active Directory szolgáltatásnév létrehozása és felügyelete az AKS-ben
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: get-started-article
ms.date: 04/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4ad0fc3fdb7d5b7c14f13fd6c279915974558dc9
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578796"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Szolgáltatásnevek és az Azure Kubernetes Service (AKS)

Az AKS-fürtöknek szükségük van egy [Azure Active Directory egyszerű szolgáltatásra][aad-service-principal] az Azure API-kkal való kommunikációhoz. Az egyszerű szolgáltatással dinamikusan hozhat létre és kezelhet olyan erőforrásokat, mint az [Azure Load Balancer][azure-load-balancer-overview].

Ebben a cikkben különböző lehetőségeket talál arra, hogyan állíthat be egy szolgáltatásnevet a Kubernetes-fürtökhöz az AKS-ben.

## <a name="before-you-begin"></a>Előkészületek


Azure AD szolgáltatásnév létrehozásához rendelkeznie kell alkalmazásregisztrációs engedéllyel az Azure AD-bérlőben és alkalmazások szerepkörhöz rendeléséhez az előfizetésben. Ha nem rendelkezik a szükséges engedélyekkel, lehet, hogy meg kell kérnie Azure AD- vagy előfizetés-rendszergazdáját, hogy biztosítsa a szükséges engedélyeket, vagy előzetesen létre kell hoznia egy szolgáltatásnevet a Kubernetes-fürthöz.

Emellett az Azure CLI 2.0.27-es vagy újabb, telepített és konfigurált verziójával is rendelkeznie kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="create-sp-with-aks-cluster"></a>Szolgáltatásnév létrehozása AKS-fürttel

Mikor az `az aks create` paranccsal AKS-fürtöt helyez üzembe, lehetősége van automatikusan létrehozni egy szolgáltatásnevet.

A következő példa egy AKS-fürtöt hoz létre, és mivel nincs meglévő szolgáltatásnév megadva, a rendszer létrehoz egyet a fürt számára. A művelet végrehajtásához a fióknak rendelkeznie kell a szükséges jogosultságokkal a szolgáltatásnév létrehozásához.

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>Meglévő szolgáltatásnév használata

Használhat egy meglévő Azure AD szolgáltatásnevet, vagy előre létrehozhat egyet az AKS-fürttel való használathoz. Ez hasznos, amikor az Azure Portalról telepít fürtöt, ahol meg kell adnia a szolgáltatásnév adatait. Meglévő egyszerű szolgáltatás használata esetén a titkos ügyfélkódot kell beállítani jelszóként.

## <a name="pre-create-a-new-sp"></a>Új szolgáltatásnév előzetes létrehozása

A szolgáltatásnév Azure CLI felületen való létrehozásához használja az [az ad sp create-for-rbac][az-ad-sp-create] parancsot.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A kimenet a következőkhöz hasonló. Jegyezze fel az `appId` és a `password` értékét. A rendszer ezeket az értékeket használja az AKS-fürtök létrehozása során.

```json
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

## <a name="use-an-existing-sp"></a>Meglévő szolgáltatásnév használata

Előre létrehozott szolgáltatásnév használatakor adja be az `appId` és a `password` értékét argumentumértékként az `az aks create` parancsba.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --service-principal <appId> --client-secret <password>
```

Ha az Azure Portal használatával helyez üzembe egy AKS-fürtöt, az **Egyszerű szolgáltatás ügyfél-azonosítója** mezőbe az `appId` értéket, az **egyszerű szolgáltatás titkos ügyfélkódja** mezőbe pedig a `password` értéket írja be az AKS-fürt konfigurációs űrlapján.

![Az Azure Vote keresését ábrázoló kép](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

AKS és Azure AD szolgáltatásnevek használata esetén vegye figyelembe a következőket.

* A Kubernetes egyszerű szolgáltatása része a fürtkonfigurációnak. Azonban nem ajánlott az identitást használni a fürt üzembe helyezésére.
* Minden egyszerű szolgáltatás társítva van egy Azure AD-alkalmazáshoz. A Kubernetes-fürt egyszerű szolgáltatása társítható bármilyen érvényes Azure AD-alkalmazásnévhez (például: `https://www.contoso.org/example`). Az alkalmazás URL-címének nem szükséges valódi végpontnak lennie.
* Amikor megadja a szolgáltatásnév **ügyfél-azonosítóját**, használja az `appId` értékét.
* A Kubernetes-fürt mester és csomópont virtuális gépein az egyszerű szolgáltatás hitelesítő adatai az `/etc/kubernetes/azure.json` fájlban lesznek tárolva.
* Ha az `az aks create` parancsot használja az egyszerű szolgáltatás automatikus létrehozásához, az egyszerű szolgáltatás hitelesítő adatai a `~/.azure/aksServicePrincipal.json` fájlba lesznek írva azon a gépen, amelyen a parancsot futtatta.
* Az `az aks create` használatával létrehozott AKS-fürt törlésekor az automatikusan létrehozott szolgáltatásnév nem törlődik. A szolgáltatásnév törléséhez először szerezze be annak azonosítóját az [az ad app list][az-ad-app-list] paranccsal. Az alábbi példa lekérdezi a *myAKSCluster* nevű fürtöt, majd törli az alkalmazásazonosítót az [az ad app delete][az-ad-app-delete] paranccsal. Helyettesítse ezeket a neveket a saját értékeivel:

    ```azurecli-interactive
    az ad app list --query "[?displayName=='myAKSCluster'].{Name:displayName,Id:appId}" --output table
    az ad app delete --id <appId>
    ```

## <a name="next-steps"></a>További lépések

Az Azure Active Directory szolgáltatásnevekkel kapcsolatos további információért tekintse meg az Azure AD-alkalmazások dokumentációját.

> [!div class="nextstepaction"]
> [Alkalmazás- és egyszerű szolgáltatási objektumok][service-principal]

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
