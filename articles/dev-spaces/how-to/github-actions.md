---
title: GitHub-műveletek & Azure Kubernetes Service-ben
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 10/24/2019
ms.topic: conceptual
description: A GitHub-műveletek és az Azure dev Spaces használatával közvetlenül az Azure Kubernetes szolgáltatásban tekintheti át és tesztelheti a lekéréses kérelmek módosításait.
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, GitHub-műveletek, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 2638fe2cd12407e43d3b3b698cdfca5231362db4
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065937"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub-műveletek & Azure Kubernetes Service (előzetes verzió)

Az Azure dev Spaces egy olyan GitHub-műveletekkel rendelkező munkafolyamatot biztosít, amely lehetővé teszi, hogy a lekéréses kérelmeket közvetlenül az AK-ban tesztelje, mielőtt a pull-kérelem beolvad az adattár fő ágában. A lekéréses kérelmek változásainak áttekintéséhez futó alkalmazásokkal növelheti a fejlesztő és a csapattagok megbízhatóságát is. Ez a futó alkalmazás segítheti a csoporttagokat, például a termék-kezelőket és a tervezőket, a fejlesztés korai szakaszában a felülvizsgálati folyamat részévé válnak.

Ebből az útmutatóból a következőket tudhatja meg:

- Azure fejlesztői tárhelyek beállítása egy felügyelt Kubernetes-fürtön az Azure-ban.
- Helyezzen üzembe egy nagyméretű alkalmazást több szolgáltatással egy fejlesztői tárhelyen.
- A CI/CD beállítása GitHub-műveletekkel.
- Egyetlen, a teljes alkalmazás kontextusában lévő, elkülönített fejlesztési területen tesztelheti a szolgáltatást.

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
- [Telepített Azure CLI][azure-cli-installed].
- A [Helm 2,13 vagy újabb rendszer van telepítve][helm-installed].
- Egy GitHub-fiók, amelyen [engedélyezve van a GitHub-művelet][github-actions-beta-signup].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service-fürt létrehozása

A [támogatott régiókban][supported-regions]létre kell hoznia egy AK-fürtöt. Az alábbi parancsok létrehoznak egy *MyResourceGroup* nevű erőforráscsoportot és egy *MyAKS*nevű AK-fürtöt.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Az Azure dev-helyek engedélyezése az AK-fürtön

A `use-dev-spaces` parancs használatával engedélyezze a fejlesztői szóközöket az AK-fürtön, és kövesse az utasításokat. Az alábbi parancs lehetővé teszi a dev Spaces használatát a *MyAKS* -fürtön a *MyResourceGroup* csoportban, és létrehoz egy *dev*nevű fejlesztői helyet.

> [!NOTE]
> A `use-dev-spaces` parancs az Azure dev Spaces CLI-t is telepíti, ha még nincs telepítve. Az Azure dev Spaces CLI nem telepíthető a Azure Cloud Shell.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása

Azure Container Registry létrehozása (ACR):

```cmd
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> Az ACR nevének egyedinek kell lennie az Azure-on belül, és 5-50 alfanumerikus karaktert kell tartalmaznia. A használt betűknek kisbetűsnek kell lenniük.

Mentse a *lekéréséhez* értéket a kimenetből, mert azt egy későbbi lépésben használják.

## <a name="create-a-service-principal-for-authentication"></a>Egyszerű szolgáltatásnév létrehozása a hitelesítéshez

Egyszerű szolgáltatásnév létrehozásához használja [az az ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] . Példa:

```cmd
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Mentse a JSON-kimenetet, mert egy későbbi lépésben használatos.


Az az [AK show][az-aks-show] paranccsal jelenítheti meg az AK-fürt *azonosítóját* :

```cmd
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Az ACR *azonosítójának* megjelenítéséhez használja az [az ACR show következőt][az-acr-show] :

```cmd
az acr show --name <acrName> --query id
```

Használja az [az role hozzárendelés Create][az-role-assignment-create] lehetőséget, hogy *közreműködői* hozzáférést biztosítson az AK-fürthöz, és *AcrPush* a hozzáférést az ACR-hez.

```cmd
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Ahhoz, hogy a szolgáltatás egyszerű hozzáférést biztosítson az adott erőforrásokhoz, mindkét AK-fürt és az ACR tulajdonosának kell lennie.

## <a name="get-sample-application-code"></a>Minta alkalmazás kódjának beolvasása

Ebben a cikkben az [Azure dev Spaces Bike Sharing Sample Application][bike-sharing-gh] használatával mutatjuk be az Azure dev Spaces használatát a GitHub-műveletekkel.

Az Azure dev Spaces minta adattárának elágazásával keresse meg az elágazó tárházat. Kattintson a *műveletek* lapra, és válassza a műveletek lehetőséget ehhez a tárházhoz.

Klónozott adattárat, és navigáljon a könyvtárába:

```cmd
git clone https://github.com/USERNAME/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>A *fejlesztői* HostSuffix beolvasása

A `azds show-context` parancs használatával jelenítse meg a HostSuffix a *fejlesztői*számára.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>A Helm-diagram frissítése a HostSuffix

Nyissa meg a [diagramok/értékek. YAML][bike-sharing-values-yaml] , és cserélje le a `<REPLACE_ME_WITH_HOST_SUFFIX>` összes példányát a korábban lekért HostSuffix értékre. Mentse a módosításokat, és zárjuk be a fájlt.

## <a name="run-the-sample-application-in-kubernetes"></a>A minta alkalmazás futtatása a Kubernetes-ben

A Kubernetes alkalmazáshoz való futtatására szolgáló parancsok egy meglévő folyamat részét képezik, és nincs függőségük az Azure dev Spaces-eszközökön. Ebben az esetben a Helm a minta alkalmazás futtatásához használt eszköz, de más eszközök is használhatók a teljes alkalmazás futtatására egy fürtön belüli névtérben. A Helm-parancsok célja a korábban létrehozott *dev* -terület, de ez a fejlesztői terület is Kubernetes névtér. Ennek eredményeképpen a dev Spaces más eszközökkel is megcélozható, mint a többi névtér.

A fejlesztéshez az Azure dev Spaces szolgáltatást használhatja, ha az alkalmazás egy fürtön fut, függetlenül az üzembe helyezéséhez használt eszköztől.

A `helm init` és `helm install` paranccsal állíthatja be és telepítheti a minta alkalmazást a fürtön.

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

> [!Note]
> **Ha RBAC-kompatibilis fürtöt használ**, mindenképpen állítson be [egy szolgáltatásfiókot a kormányrúdhoz][tiller-rbac]. Ellenkező esetben `helm` parancsok sikertelenek lesznek.

A `helm install` parancs végrehajtása több percet is igénybe vehet. A parancs kimenete a fürtön üzembe helyezett összes szolgáltatás állapotát jeleníti meg, ha befejeződött:

```cmd
$ cd charts/
$ helm init --wait
...
Happy Helming!

$ helm install -n bikesharing . --dep-up --namespace dev --atomic

Hang tight while we grab the latest from your chart repositories...
...
NAME               READY  UP-TO-DATE  AVAILABLE  AGE
bikes              1/1    1           1          4m32s
bikesharingweb     1/1    1           1          4m32s
billing            1/1    1           1          4m32s
gateway            1/1    1           1          4m32s
reservation        1/1    1           1          4m32s
reservationengine  1/1    1           1          4m32s
users              1/1    1           1          4m32s
```

Miután a minta alkalmazást telepítette a fürtön, és mivel a fürtön engedélyezve vannak a dev Spaces, a `azds list-uris` parancs használatával jelenítse meg a jelenleg kiválasztott *fejlesztői* alkalmazás URL-címeit.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Nyissa meg a *bikesharingweb* szolgáltatást a `azds list-uris` parancs nyilvános URL-címének megnyitásával. A fenti példában a *bikesharingweb* szolgáltatás nyilvános URL-címe `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Válassza az *Aurelia Briggs (ügyfél)* lehetőséget felhasználóként, majd válassza ki a bérelni kívánt kerékpárt. Ellenőrizze, hogy megjelenik-e a bike helyőrző képe.

## <a name="configure-your-github-action"></a>A GitHub-művelet konfigurálása

Navigáljon az elágazó tárházhoz, és kattintson a *Beállítások*elemre. Kattintson a *Secrets* elemre a bal oldali oldalsávon. Kattintson *az új titkos kulcs hozzáadása* lehetőségre az alábbi új titkos kódok hozzáadásához:

1. *AZURE_CREDENTIALS*: az egyszerű szolgáltatás létrehozásának teljes kimenete.
1. *RESOURCE_GROUP*: az AK-fürthöz tartozó erőforráscsoport, amely ebben a példában a *MyResourceGroup*.
1. *CLUSTER_NAME*: az AK-fürt neve, amely ebben a példában a *MyAKS*.
1. *CONTAINER_REGISTRY*: az ACR *lekéréséhez* .
1. *Gazdagép*: a fejlesztői terület gazdagépe, amely az űrlapot *< MASTER_SPACE >. < APP_NAME >. < HOST_SUFFIX >* , amely ebben a példában a *dev.bikesharingweb.fedcab0987.EUs.azds.IO*.
1. *IMAGE_PULL_SECRET*: a használni kívánt titok neve, például *demo-Secret*.
1. *MASTER_SPACE*: a szülő fejlesztői terület neve, amely ebben a példában a *dev*.
1. *REGISTRY_USERNAME*: az egyszerű szolgáltatás létrehozásakor a JSON-kimenetből származó *clientId* .
1. *REGISTRY_PASSWORD*: az egyszerű szolgáltatás létrehozásakor a JSON-kimenetből származó *clientSecret* .

> [!NOTE]
> Az összes ilyen titkot a GitHub-művelet használja, és a [. GitHub/munkafolyamatok/Bikes. YML][github-action-yaml]-ben vannak konfigurálva.

## <a name="create-a-new-branch-for-code-changes"></a>Új ág létrehozása a kód módosításaihoz

Váltson vissza `BikeSharingApp/`re, és hozzon létre egy *Bike-images*nevű új ágat.

```cmd
cd ..
git checkout -b bike-images
```

A [Bikes/Server. js][bikes-server-js] szerkesztésével távolítsa el a 232-es és a 233-es sorokat:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

A szakasznak így kell kinéznie:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Mentse a fájlt, majd `git add` és `git commit` használatával hajtsa végre a módosításokat.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>A módosítások leküldése

A `git push` segítségével küldje le az új ágat az elágazó adattárba:

```cmd
git push origin bike-images
```

A leküldéses folyamat befejezése után navigáljon a GitHubon található elágazó adattárhoz, és hozzon létre egy lekéréses kérelmet a *fő* ágként a *Bike-images* ág összevetésével.

A lekéréses kérelem megnyitása után navigáljon a *műveletek* lapra. Ellenőrizze, hogy az új művelet elindult-e, és hogy a *Bikes* szolgáltatást épít-e ki.

## <a name="view-the-child-space-with-your-changes"></a>Az alárendelt terület megtekintése a módosításaival

A művelet befejezését követően egy megjegyzés jelenik meg az új gyermekobjektum URL-címével, a lekéréses kérelem módosításai alapján.

> [!div class="mx-imgBorder"]
> ![GitHub-művelet URL-címe](../media/github-actions/github-action-url.png)

Nyissa meg a *bikesharingweb* szolgáltatást, és nyissa meg az URL-címet a megjegyzésből. Válassza az *Aurelia Briggs (ügyfél)* lehetőséget felhasználóként, majd válassza ki a bérelni kívánt kerékpárt. Győződjön meg arról, hogy már nem jelenik meg a bike helyőrző képe.

## <a name="clean-up-your-azure-resources"></a>Azure-erőforrások karbantartása

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogy az Azure dev Spaces hogyan segíti az összetettebb alkalmazások fejlesztését több tárolóban, és hogyan egyszerűsítheti az együttműködésen alapuló fejlesztést, ha a kód különböző verzióival vagy ágaival dolgozik a különböző helyeken.

> [!div class="nextstepaction"]
> [Csoportmunka az Azure fejlesztői Spaces szolgáltatásban][team-quickstart]

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
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[tiller-rbac]: https://helm.sh/docs/using_helm/#role-based-access-control
[supported-regions]: ../about.md#supported-regions-and-configurations
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
[team-quickstart]: ../quickstart-team-development.md