---
title: GitHub-műveletek & Az Azure Kubernetes szolgáltatás (előzetes verzió)
services: azure-dev-spaces
ms.date: 02/04/2020
ms.topic: conceptual
description: A lekéréses kérelmek változásainak áttekintése és tesztelése közvetlenül az Azure Kubernetes-szolgáltatásban a GitHub-műveletek és az Azure Dev Spaces használatával
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók, GitHub-műveletek, Helm, szolgáltatásháló, szolgáltatásháló-útválasztás, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 49715e38f36d4421b7327640ec8392a83b3c2996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252374"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub-műveletek & Az Azure Kubernetes szolgáltatás (előzetes verzió)

Az Azure Dev Spaces egy munkafolyamatot biztosít a GitHub-műveletek használatával, amely lehetővé teszi, hogy közvetlenül az AKS-ben tesztelje a lekéréses kérelem módosításait, mielőtt a lekéréses kérelem beolvadna a tárház fő ágába. Miután egy futó alkalmazás, hogy vizsgálja felül a változásokat a lekéréses kérelem növelheti a megbízhatóságot mind a fejlesztő, valamint a csapat tagjai. Ez a futó alkalmazás is segíthet a csapat tagjai, mint például a termék menedzserek és a tervezők, részévé válnak a felülvizsgálati folyamat korai szakaszában a fejlesztés.

Ebből az útmutatóból a következőket tudhatja meg:

* Állítsa be az Azure Dev Spaces egy felügyelt Kubernetes-fürtön az Azure-ban.
* Üzembe helyezhet egy több mikroszolgáltatásokat tartalmazó nagy alkalmazást egy fejlesztői térbe.
* Ci/CD beállítása GitHub-műveletekkel.
* Tesztelje egyetlen mikroszolgáltatás egy elszigetelt fejlesztési térben a teljes alkalmazás környezetében.

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
* [Az Azure CLI telepítve van.][azure-cli-installed]
* [Helm 3 telepítve][helm-installed].
* Egy GitHub-fiók, amelyen [engedélyezve van a GitHub-műveletek.][github-actions-beta-signup]
* Az [Azure Dev Spaces bike sharing mintaalkalmazás](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) fut egy AKS-fürtön.

## <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása

Hozzon létre egy Azure Container Registry (ACR):

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> Az ACR névnek egyedinek kell lennie az Azure-ban, és 5–50 alfanumerikus karaktert kell tartalmaznia. A használt betűknek kisbetűseknek kell lenniük.

Mentse a *loginServer* értéket a kimenetből, mert egy későbbi lépésben használatos.

## <a name="create-a-service-principal-for-authentication"></a>Egyszerű szolgáltatás létrehozása a hitelesítéshez

Az [ad sp create-for-rbac][az-ad-sp-create-for-rbac] használatával hozzon létre egy egyszerű szolgáltatás. Példa:

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Mentse a JSON kimenetet, mert egy későbbi lépésben használja.

Az [AKS-fürt][az-aks-show] *azonosítójának* megjelenítéséhez használja az aks show-t:

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Az ACR *azonosítójának* megjelenítéséhez használja az [acr show-t:][az-acr-show]

```azurecli
az acr show --name <acrName> --query id
```

Az [szerepkör-hozzárendelés létrehozása][az-role-assignment-create] használatával *közreműködői* hozzáférést biztosít az AKS-fürthöz és *az AcrPush-hoz* az ACR-hez.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Az AKS-fürt és az ACR tulajdonosának kell lennie ahhoz, hogy a szolgáltatásegyszerű hozzáférést biztosítson ezekhez az erőforrásokhoz.

## <a name="configure-your-github-action"></a>A GitHub-művelet konfigurálása

> [!IMPORTANT]
> A tárházhoz engedélyeznie kell a GitHub-műveleteket. A GitHub-műveletek tárházhoz való engedélyezéséhez keresse meg a githubon lévő tárházat, kattintson a Műveletek fülre, és válassza ki a műveletek engedélyezését ehhez a tárházhoz.

Nyissa meg a villás tárházat, és kattintson a *Beállítások gombra.* Kattintson a *titkok* a bal oldalsávon. Kattintson *az Új titok hozzáadása* gombra az alábbi új titkos titok hozzáadásához:

1. *AZURE_CREDENTIALS*: a szolgáltatásnév létrehozásának teljes kimenete.
1. *RESOURCE_GROUP*: az AKS-fürt erőforráscsoportja, amely ebben a példában a *MyResourceGroup*.
1. *CLUSTER_NAME:* az AKS-fürt neve, amely ebben a példában *a MyAKS*.
1. *CONTAINER_REGISTRY:* az ACR *loginServer kiszolgálója.*
1. *HOST*: a fejlesztői tér állomása, amely *>,<MASTER_SPACE>.APP_NAME <>.<HOST_SUFFIX>*<formában jelenik *meg,* amely ebben a példában dev.bikesharingweb.fedcab0987.eus.azds.io .
1. *IMAGE_PULL_SECRET*: a használni kívánt titok neve, például *a demó-titok*.
1. *MASTER_SPACE:* a szülő fejlesztői tárhelyének neve, amely ebben a példában *dev*.
1. *REGISTRY_USERNAME*: a szolgáltatásnév létrehozása json kimenetének *ügyfélazonosítója.*
1. *REGISTRY_PASSWORD*: az *ügyféltitkos kulcsot* a JSON kimenettől a szolgáltatásnév létrehozása.

> [!NOTE]
> Ezeket a titkos kulcsokat a GitHub-művelet használja, és [.github/workflows/bikes.yml][github-action-yaml]nyelven konfigurálva van.

Ha a PR egyesítése után frissíteni szeretné a főterületet, adja hozzá a *GATEWAY_HOST* titkos kulcsot, amely *<MASTER_SPACE>.<HOST_SUFFIX>*<formában jelenik *meg,* amely ebben a példában dev.gateway.fedcab0987.eus.azds.io . Miután egyesíti a módosításokat a főág a villa, egy másik művelet fog futni, hogy újraépítse és futtassa a teljes alkalmazást a fő fejlesztői térben. Ebben a példában a főtér *dev*. Ez a művelet [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml]nyelven van konfigurálva.

## <a name="create-a-new-branch-for-code-changes"></a>Új fiók létrehozása kódmódosításokhoz

Keresse `BikeSharingApp/` meg, és hozzon létre egy új ág úgynevezett *bike-képek*.

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

[Kerékpár/server.js szerkesztése][bikes-server-js] a 232-es és 233-as sorok eltávolításához:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

A szakasznak most így kell kinéznie:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Mentse a fájlt, majd használja, `git add` és `git commit` a fázisa a módosításokat.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>A módosítások leküldése

Segítségével `git push` az új ágat a villás tárházba tolhatja:

```cmd
git push origin bike-images
```

Miután a leküldéses befejeződött, keresse meg a forked tárház a GitHubon, hogy hozzon létre egy lekéréses kérelmet a *fő* ág a villás tárház, mint az alapág, mint a *kerékpár-images* ág.

A lekéréses kérelem megnyitása után keresse meg a *Műveletek* lapot. *Bikes*

## <a name="view-the-child-space-with-your-changes"></a>A gyermektér megtekintése a módosításokkal

Miután a művelet befejeződött, megjelenik egy megjegyzés egy URL-t az új gyermek tér alapján a változások a lekéréses kérelemben.

> [!div class="mx-imgBorder"]
> ![GitHub művelet url-címe](../media/github-actions/github-action-url.png)

Keresse meg a *bikesharingweb* szolgáltatást a megjegyzés URL-címének megnyitásával. Válassza az *Aurelia Briggs (ügyfél) lehetőséget* felhasználóként, majd válassza ki a bérelhető kerékpárt. Ellenőrizze, hogy már nem látja-e a kerékpár helyőrző képét.

Ha a módosításokat az elágazás *főágába* egyesíti, egy másik művelet fog futni a teljes alkalmazás újraépítéséhez és futtatásához a szülő fejlesztési területen. Ebben a példában a szülőterület *dev*. Ez a művelet [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml]nyelven van konfigurálva.

## <a name="clean-up-your-azure-resources"></a>Az Azure-erőforrások karbantartása

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogy az Azure Dev Spaces hogyan segít összetettebb alkalmazások fejlesztésében több tárolóközött, és hogyan egyszerűsítheti az együttműködésen alapuló fejlesztést a kód különböző verzióival vagy ágaival való együttműködéssel különböző helyeken.

> [!div class="nextstepaction"]
> [Csapatfejlesztés az Azure Dev Spaces-ben][team-quickstart]

[azure-cli-installed]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
[team-quickstart]: ../quickstart-team-development.md
