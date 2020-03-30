---
title: Tárolópéldány üzembe helyezése a GitHub-művelettel
description: Konfiguráljon egy GitHub-műveletet, amely automatizálja a tárolórendszerkép létrehozásának, leküldéseésének és üzembe helyezésének lépéseit az Azure Container Instances-ba
ms.topic: article
ms.date: 03/18/2020
ms.custom: ''
ms.openlocfilehash: 13397cee8197afc65b93c587ae1505e59cfdebc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258039"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>GitHub-művelet konfigurálása tárolópéldány létrehozásához

[A GitHub-műveletek](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions) a GitHub egy szolgáltatáscsomagja, amely ekkel automatizálhatja a szoftverfejlesztési munkafolyamatokat ugyanazon a helyen, ahol a kódot tárolja, és együttműködik a lekéréses kérelmeken és problémákon.

Az [Azure Container Instances](https://github.com/azure/aci-deploy) githubra való üzembe helyezés művelet használatával automatizálhatja egy tároló üzembe helyezését az Azure Container Instances számára. A művelet lehetővé teszi, hogy az az [container create][az-container-create] parancshoz hasonló tulajdonságokat állítson be egy tárolópéldányhoz.

Ez a cikk bemutatja, hogyan állíthat be egy munkafolyamatot egy GitHub-tárházban, amely a következő műveleteket hajtja végre:

* Kép létrehozása Docker-fájlból
* A lemezkép leküldése egy Azure-tároló beállításjegyzékébe
* A tárolórendszerkép üzembe helyezése egy Azure-tárolópéldányra

Ez a cikk a munkafolyamat beállításának két módját mutatja be:

* Konfiguráljon egy munkafolyamatot saját maga egy GitHub-tárházban az Azure Container Instances üzembe helyezéséhez művelet és egyéb műveletek használatával.  
* Használja `az container app up` a parancsot az [Azure CLI Üzembe helyezése az Azure-hoz](https://github.com/Azure/deploy-to-azure-cli-extension) bővítményben. Ez a parancs leegyszerűsíti a GitHub-munkafolyamat és a telepítési lépések létrehozását.

> [!IMPORTANT]
> Az Azure Container Instances GitHub-művelet jelenleg előzetes verzióban érhető el. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="prerequisites"></a>Előfeltételek

* **GitHub-fiók** – Hozzon létre egy fiókot, https://github.com ha még nem rendelkezik ilyen.
* **Azure CLI** – Használhatja az Azure Cloud Shell vagy az Azure CLI helyi telepítését az Azure CLI lépések végrehajtásához. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].
* **Azure container registry** – Ha nem rendelkezik ilyen, hozzon létre egy Azure-tároló beállításjegyzékaz alapszintű az [Azure CLI](../container-registry/container-registry-get-started-azure-cli.md), [Azure Portal](../container-registry/container-registry-get-started-portal.md), vagy más módszerek használatával. Vegye figyelembe a központi telepítéshez használt erőforráscsoportot, amely a GitHub-munkafolyamathoz használatos.

## <a name="set-up-repo"></a>Tártár beállítása

* A jelen cikkben szereplő példákért használja a GitHubot a következő tárház elágazásához:https://github.com/Azure-Samples/acr-build-helloworld-node

  Ez a tárfájl egy Docker-fájl- és forrásfájlokat tartalmaz egy kis webalkalmazás tárolórendszerképének létrehozásához.

  ![A GitHub Fork (Leágaztatás) gombjának (kiemelve) képernyőképe](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Győződjön meg arról, hogy a műveletek engedélyezve vannak a tárházhoz. Nyissa meg a villás tárházat, és válassza a **Beállítások** > **műveletek lehetőséget.** A **Műveletek engedélyek ben**győződjön meg arról, hogy a **tárház helyi és harmadik féltől származó műveletek engedélyezése** jelölőnégyzet be van jelölve.

## <a name="configure-github-workflow"></a>A GitHub-munkafolyamat konfigurálása

### <a name="create-service-principal-for-azure-authentication"></a>Egyszerű szolgáltatás létrehozása az Azure-hitelesítéshez

A GitHub-munkafolyamatban meg kell adnia az Azure hitelesítő adatait az Azure CLI hitelesítéséhez. A következő példa létrehoz egy egyszerű szolgáltatás a közreműködői szerepkör hatóköre az erőforráscsoport a tároló beállításjegyzék.

Először az erőforráscsoport erőforrás-azonosítóját. Helyettesítse a csoport nevét a következő [az csoportshow][az-acr-show] parancsban:

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

Az [ad sp create-for-rbac][az-ad-sp-create-for-rbac] használatával hozza létre a szolgáltatásnév:

```azurecli
az ad sp create-for-rbac \
  --scope $groupId \
  --role Contributor \
  --sdk-auth
```

A kimenet a következőhöz hasonló:

```json
{
  "clientId": "xxxx6ddc-xxxx-xxxx-xxx-ef78a99dxxxx",
  "clientSecret": "xxxx79dc-xxxx-xxxx-xxxx-aaaaaec5xxxx",
  "subscriptionId": "xxxx251c-xxxx-xxxx-xxxx-bf99a306xxxx",
  "tenantId": "xxxx88bf-xxxx-xxxx-xxxx-2d7cd011xxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Mentse a JSON kimenetet, mert egy későbbi lépésben használja. Vegye figyelembe `clientId`a , amely a következő szakaszban frissítenie kell a szolgáltatásnév.

### <a name="update-service-principal-for-registry-authentication"></a>Egyszerű szolgáltatásfrissítése a beállításjegyzék-hitelesítéshez

Frissítse az Azure egyszerű szolgáltatás hitelesítő adatait, hogy leküldéses és lekéréses engedélyeket a tároló beállításjegyzékben. Ez a lépés lehetővé teszi, hogy a GitHub-munkafolyamat a szolgáltatásnév használatával [hitelesítse magát a tároló beállításjegyzékével.](../container-registry/container-registry-auth-service-principal.md) 

A tároló beállításjegyzékének erőforrás-azonosítójának beszereznie. Helyettesítse a rendszerleíró adatbázis nevét a következő [az acr show][az-acr-show] parancsban:

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Az [szerepkör-hozzárendelés létrehozása][az-role-assignment-create] az AcrPush szerepkör hozzárendeléséhez használja az AcrPush szerepkört, amely leküldéses és lekéréses hozzáférést biztosít a beállításjegyzékhez. A szolgáltatásnév ügyfélazonosítójának helyettesítése:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Hitelesítő adatok mentése a GitHub-tárfélre

1. A GitHub felhasználói felületén keresse meg a villás tárházat, és válassza a **Beállítások** > **titkos kulcsok**lehetőséget. 

1. Válassza **az Új titok hozzáadása** lehetőséget a következő titkos kulcsok hozzáadásához:

|Titkos  |Érték  |
|---------|---------|
|`AZURE_CREDENTIALS`     | A szolgáltatásegyszerű létrehozása teljes JSON-kimenet |
|`REGISTRY_LOGIN_SERVER`   | A rendszerleíró adatbázis bejelentkezési kiszolgálójának neve (az összes kisbetű). Példa: *myregistry.azure.cr.io*        |
|`REGISTRY_USERNAME`     |  A `clientId` szolgáltatásegyszerű létrehozása JSON-kimenetből származik       |
|`REGISTRY_PASSWORD`     |  A `clientSecret` szolgáltatásegyszerű létrehozása JSON-kimenetből származik |
| `RESOURCE_GROUP` | Az egyszerű szolgáltatás hatóköréhez használt erőforráscsoport neve |

### <a name="create-workflow-file"></a>Munkafolyamat-fájl létrehozása

1. A GitHub felhasználói felületén válassza **a Műveletek** > **új munkafolyamatát.**
1. Válassza **a Munkafolyamat saját beállítása**lehetőséget.
1. Az **Új fájl szerkesztése**mezőben illessze be a következő YAML-tartalmat a mintakód felülírásához. Fogadja el az `main.yml`alapértelmezett fájlnevet , vagy adja meg a választott fájlnevet.
1. Válassza **a Véglegesítés indítása**lehetőséget, és adja meg a véglegesítés rövid és bővített leírását, majd válassza **az Új fájl véglegesítése**lehetőséget.

```yml
on: [push]
name: Linux_Container_Workflow

jobs:
    build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout GitHub Action'
          uses: actions/checkout@master
          
        - name: 'Login via Azure CLI'
          uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
        
        - name: 'Build and push image'
          uses: azure/docker-login@v1
          with:
            login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            username: ${{ secrets.REGISTRY_USERNAME }}
            password: ${{ secrets.REGISTRY_PASSWORD }}
        - run: |
            docker build . -t ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            docker push ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}

        - name: 'Deploy to Azure Container Instances'
          uses: 'azure/aci-deploy@v1'
          with:
            resource-group: ${{ secrets.RESOURCE_GROUP }}
            dns-name-label: ${{ secrets.RESOURCE_GROUP }}${{ github.run_number }}
            image: ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            registry-login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            registry-username: ${{ secrets.REGISTRY_USERNAME }}
            registry-password: ${{ secrets.REGISTRY_PASSWORD }}
            name: aci-sampleapp
            location: 'west us'
```

### <a name="validate-workflow"></a>Munkafolyamat ellenőrzése

A munkafolyamat-fájl véglegesítése után a munkafolyamat aktiválódik. A munkafolyamat előrehaladásának áttekintéséhez keresse meg a **Műveletek** > **munkafolyamatait.** 

![Munkafolyamat-állapot megtekintése](./media/container-instances-github-action/github-action-progress.png)

A munkafolyamat egyes lépéseinek állapotáról és eredményeiről a [Munkafolyamat futtatásának kezelése](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run) című témakörben talál tájékoztatást.

Amikor a munkafolyamat befejeződik, az [az container show][az-container-show] parancs futtatásával információkat kaphat az *aci-sampleapp* nevű tárolópéldányról. Az erőforráscsoport nevének helyettesítése: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name aci-sampleapp \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

A kimenet a következőhöz hasonló:

```console
FQDN                                   ProvisioningState
---------------------------------      -------------------
aci-action01.westus.azurecontainer.io  Succeeded
```

A példány kiépítése után keresse meg a tároló teljes tartománynevét a böngészőben a futó webalkalmazás megtekintéséhez.

![Webalkalmazás futtatása böngészőben](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>A Deploy to Azure bővítmény használata

Másik lehetőségként használja a [Deploy to Azure bővítményt](https://github.com/Azure/deploy-to-azure-cli-extension) az Azure CLI-ben a munkafolyamat konfigurálásához. A `az container app up` bővítményben lévő parancs bemeneti paramétereket vesz igénybe az Azure Container Instances-re való üzembe helyezéshez. 

Az Azure CLI által létrehozott munkafolyamat hasonló ahhoz a munkafolyamathoz, [amelyet manuálisan hozhat létre a GitHub használatával.](#configure-github-workflow)

### <a name="additional-prerequisite"></a>További előfeltétel

A forgatókönyv [előfeltételein](#prerequisites) és [tártárbeállításán](#set-up-repo) kívül telepítenie kell az Azure CLI **Üzembe helyezése az Azure-ba bővítményt.**

Futtassa az [az extension add][az-extension-add] parancsot a bővítmény telepítéséhez:

```azurecli
az extension add \
  --name deploy-to-azure
```

A bővítmények kereséséről, telepítéséről és kezeléséről a [Bővítmények használata az Azure CLI-vel című](/cli/azure/azure-cli-extensions-overview)témakörben talál.

### <a name="run-az-container-app-up"></a>Futtassa a `az container app up` parancsot.

Az [az container app up][az-container-app-up] parancs futtatásához legalább a következőket kell megadnia:

* Az Azure-tároló beállításjegyzékének neve, például *myregistry*
* A GitHub-tár-tárlat URL-címe, például:`https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Minta parancs:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Parancs állapotának

* Amikor a rendszer kéri, adja meg a GitHub-hitelesítő adatait, vagy adjon meg egy [GitHub-személyes hozzáférési jogkivonatot](https://help.github.com/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) (PAT), amely *tárpát* és *felhasználói* hatókört rendelkezik a beállításjegyzékkel való hitelesítéshez. Ha github-hitelesítő adatokat ad meg, a parancs létrehoz egy PAT-t.

* A parancs tárpokkal titkos kulcsokat hoz létre a munkafolyamathoz:

  * Az Azure CLI szolgáltatásnévhitelesítő adatai
  * Hitelesítő adatok az Azure-tároló beállításjegyzékének eléréséhez

* Miután a parancs véglegesíti a munkafolyamat-fájlt a tárműre, a munkafolyamat aktiválódik. 

A kimenet a következőhöz hasonló:

```console
[...]
Checking in file github/workflows/main.yml in the Github repository myid/acr-build-helloworld-node
Creating workflow...
GitHub Action Workflow has been created - https://github.com/myid/acr-build-helloworld-node/runs/515192398
GitHub workflow completed.
Workflow succeeded
Your app is deployed at:  http://acr-build-helloworld-node.eastus.azurecontainer.io:8080/
```

### <a name="validate-workflow"></a>Munkafolyamat ellenőrzése

A munkafolyamat üzembe helyez egy Azure-tárolópéldányt a GitHub-tárház alapnevével, ebben az esetben az acr-build-helloworld-node.The workflow deploys an Azure container instance with the base name of your GitHub repo, in this case, *acr-build-helloworld-node.* A böngészőben a futó webalkalmazás megtekintéséhez keresse meg a megadott hivatkozást. Ha az alkalmazás nem 8080-as porton figyel, adja meg azt az URL-címben.

A GitHub felhasználói felületén az egyes lépések munkafolyamat-állapotának és eredményeinek megtekintéséhez olvassa el a Munkafolyamat futtatásának kezelése című [témakört.](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Állítsa le a tárolópéldányt az [az container delete][az-container-delete] paranccsal:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Az erőforráscsoport és a benne lévő összes erőforrás törléséhez futtassa az [az csoport törlési][az-group-delete] parancsát:

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>További lépések

Böngésszen a [GitHub Piactéren](https://github.com/marketplace?type=actions) további műveletekért a fejlesztési munkafolyamat automatizálásához


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-container-create]: /cli/azure/container#az-container-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-container-show]: /cli/azure/container#az-container-show
[az-container-delete]: /cli/azure/container#az-container-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up
