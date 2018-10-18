---
title: Az Azure Kubernetes Services (AKS) szolgáltatásnevei
description: Az Azure Kubernetes Service-ben található fürthöz tartozó Azure Active Directory szolgáltatásnév létrehozása és felügyelete
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: get-started-article
ms.date: 09/26/2018
ms.author: iainfou
ms.openlocfilehash: ef3139c4b3f06644b219e177fad0c094ed600fb6
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394590"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Szolgáltatásnevek és az Azure Kubernetes Service (AKS)

Az AKS-fürtöknek szükségük van egy [Azure Active Directory (AS) szolgáltatásnévre][aad-service-principal] az Azure API-kkal való kommunikációhoz. A szolgáltatásnévvel dinamikusan hozhat létre és kezelhet olyan egyéb Azure-erőforrásokat, mint az Azure Load Balancer vagy a tárolóregisztrációs adatbázis (ACR).

Ez a cikk azt mutatja be, hogyan lehet létrehozni és használni egy szolgáltatásnevet az AKS-fürtökhöz.

## <a name="before-you-begin"></a>Előkészületek

Azure AD szolgáltatásnév létrehozásához rendelkeznie kell alkalmazásregisztrációs engedéllyel az Azure AD-bérlőben és alkalmazások szerepkörhöz rendeléséhez az előfizetésben. Ha nem rendelkezik a szükséges engedélyekkel, lehet, hogy meg kell kérnie az Azure AD vagy az előfizetés rendszergazdáját, hogy biztosítsa a szükséges engedélyeket, vagy hogy hozzon létre előzetesen egy szolgáltatásnevet, hogy használhassa az AKS-fürthöz.

Emellett az Azure CLI 2.0.46-os vagy újabb, telepített és konfigurált verziójával is rendelkeznie kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Szolgáltatásnév automatikus létrehozása és használata

Amikor az Azure Portalon vagy az [az aks create][az-aks-create] paranccsal létrehoz egy AKS-fürtöt, az Azure automatikusan létre tud hozni egy szolgáltatásnevet.

Az alábbi Azure CLI-példaparancsban nincs megadva szolgáltatásnév. Ebben a forgatókönyvben az Azure CLI létrehoz egy szolgáltatásnevet az AKS-fürthöz. A művelet sikeres végrehajtásához az Azure-fiókjának rendelkeznie kell a szükséges jogosultságokkal a szolgáltatásnév létrehozásához.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="manually-create-a-service-principal"></a>Szolgáltatásnév manuális létrehozása

Egy szolgáltatásnév Azure CLI felületen való manuális létrehozásához használja az [az ad sp create-for-rbac][az-ad-sp-create] parancsot. A következő példában a `--skip-assignment` paraméter megakadályozza bármilyen további alapértelmezett hozzárendelés használatát:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A kimenet a következő példához hasonló. Jegyezze fel a saját `appId` és `password` adatait. A rendszer a következő szakaszban ezeket az értékeket fogja használni az AKS-fürtök létrehozása során.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "azure-cli-2018-09-25-21-10-19",
  "name": "http://azure-cli-2018-09-25-21-10-19",
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

Ha az Azure Portal használatával helyez üzembe egy AKS-fürtöt, akkor a **Kubernetes-fürt létrehozása** párbeszédablak *Hitelesítés* oldalán válassza a **Szolgáltatásnév konfigurálása** lehetőséget. Válassza a **Meglévő használata** lehetőséget, majd adja meg a következő értékeket:

- A **szolgáltatásnév ügyfél-azonosítója** az *appId*
- A **szolgáltatásnév titkos ügyfélkulcsa** pedig *jelszó* értéke

![Az Azure Vote keresését ábrázoló kép](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

AKS és Azure AD szolgáltatásnevek használata esetén vegye figyelembe a következőket.

- A Kubernetes egyszerű szolgáltatása része a fürtkonfigurációnak. Azonban nem ajánlott az identitást használni a fürt üzembe helyezésére.
- Minden egyszerű szolgáltatás társítva van egy Azure AD-alkalmazáshoz. A Kubernetes-fürt szolgáltatásneve társítható bármilyen érvényes Azure AD-alkalmazásnévhez (például: *https://www.contoso.org/example*). Az alkalmazás URL-címének nem szükséges valódi végpontnak lennie.
- Amikor megadja a szolgáltatásnév **Client ID-ját** (Ügyfél-azonosítóját), használja az `appId` értékét.
- A Kubernetes-fürt mester és csomópont virtuális gépein a szolgáltatásnév hitelesítő adatai az `/etc/kubernetes/azure.json` fájlban lesznek tárolva.
- Ha az [az aks create][az-aks-create] parancsot használja a szolgáltatásnév automatikus létrehozásához, a szolgáltatásnév hitelesítő adatai a `~/.azure/aksServicePrincipal.json` fájlba lesznek írva azon a gépen, amelyen a parancsot futtatta.
- Az [az aks create][az-aks-create] használatával létrehozott AKS-fürt törlésekor az automatikusan létrehozott szolgáltatásnév nem törlődik.
    - A szolgáltatásnév törléséhez először szerezze be annak azonosítóját az [az ad app list][az-ad-app-list] paranccsal. Az alábbi példa lekérdezi a *myAKSCluster* nevű fürtöt, majd törli az alkalmazásazonosítót az [az ad app delete][az-ad-app-delete] paranccsal. Helyettesítse ezeket a neveket a saját értékeivel:

        ```azurecli
        az ad app list --query "[?displayName=='myAKSCluster'].{Name:displayName,Id:appId}" --output table
        az ad app delete --id <appId>
        ```

## <a name="next-steps"></a>További lépések

Az Azure Active Directory szolgáltatásnevekkel kapcsolatos további információért tekintse meg az [Alkalmazás- és szolgáltatásnév-objektumok][service-principal] című cikket.

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
