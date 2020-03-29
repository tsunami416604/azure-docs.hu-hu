---
title: Adatbázisközi hitelesítés az ACR-feladatból
description: Azure Container Registry Task (ACR-feladat) konfigurálása egy másik azure-tároló-beállításjegyzék eléréséhez felügyelt identitás használatával az Azure-erőforrásokhoz
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 47b2a50784cf56b089fea0981e5a06d581b8ba3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842493"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Adatbázisközi hitelesítés Egy ACR-feladatban Azure által felügyelt identitás használatával 

Egy [ACR-feladatban](container-registry-tasks-overview.md) [engedélyezheti az Azure-erőforrások felügyelt identitását.](container-registry-tasks-authentication-managed-identity.md) A feladat az identitás használatával más Azure-erőforrások eléréséhez, anélkül, hogy a hitelesítő adatok megadása vagy kezelése. 

Ebből a cikkből megtudhatja, hogyan engedélyezheti, hogy egy feladat felügyelt identitása lekéri a rendszerképet a feladat futtatásához használtól eltérő beállításjegyzékből.

Az Azure-erőforrások létrehozásához ez a cikk megköveteli, hogy futtassa az Azure CLI 2.0.68-as vagy újabb verzióját. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

A példafeladat lekéri egy alaplemezképet egy másik Azure-tároló beállításjegyzékből egy alkalmazáslemezkép létrehozásához és leküldéses. Az alaplemezkép lekérése felügyelt identitással konfigurálja a feladatot, és rendelje hozzá a megfelelő engedélyeket. 

Ez a példa a felhasználó által hozzárendelt vagy a rendszer által hozzárendelt felügyelt identitást használó lépéseket mutatja be. Az identitás kiválasztása a szervezet igényeitől függ.

Egy valós forgatókönyv esetén előfordulhat, hogy a szervezet az összes fejlesztőcsapat által az alkalmazások létrehozásához használt alaplemezképek készletét karbantarthatja. Ezek az alaplemezképek egy vállalati nyilvántartásban tárolódnak, és minden fejlesztőcsapat csak lekéréses jogokkal rendelkezik. 

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben két Azure-tároló-beállításjegyzékre van szükség:

* Az első beállításjegyzéksegítségével hoz létre és hajt végre ACR-feladatokat. Ebben a cikkben ez a rendszerleíró adatbázis neve *myregistry*. 
* A második beállításjegyzék egy alaplemezképet ad a feladathoz egy lemezkép létrehozásához. Ebben a cikkben a második rendszerleíró adatbázis neve *mybaseregistry*. 

Cserélje le a saját rendszerleíró adatbázis nevét a későbbi lépésekben.

Ha még nem rendelkezik a szükséges Azure-tároló-beállításjegyzékekkel, olvassa el [a rövid útmutató: Hozzon létre egy privát tároló beállításjegyzéket az Azure CLI használatával.](container-registry-get-started-azure-cli.md) Még nem kell a rendszerképeket a rendszerleíró adatbázisba átvinnie.

## <a name="prepare-base-registry"></a>Alap rendszerleíró adatbázis előkészítése

Először hozzon létre egy működő könyvtárat, majd hozzon létre egy Dockerfile nevű fájlt a következő tartalommal. Ez az egyszerű példa egy Node.js alaprendszerképet hoz létre egy nyilvános rendszerképből a Docker Hubban.
    
```bash
echo FROM node:9-alpine > Dockerfile
```
Az aktuális könyvtárban futtassa az [az acr build][az-acr-build] parancsot az alaplemezkép létrehozásához és leküldéseshez az alap beállításjegyzékbe. A gyakorlatban a szervezet egy másik csoportja vagy folyamata karbantarthatja az alaprendszerleíró adatbázist.
    
```azurecli
az acr build --image baseimages/node:9-alpine --registry mybaseregistry --file Dockerfile .
```

## <a name="define-task-steps-in-yaml-file"></a>Feladatlépések definiálása a YAML-fájlban

A példa [többlépéses feladatlépéseit](container-registry-tasks-multi-step.md) egy [YAML-fájl](container-registry-tasks-reference-yaml.md)határozza meg. Hozzon létre `helloworldtask.yaml` egy nevet a helyi munkakönyvtárban megnevezett fájlt, és illessze be a következő tartalmat. Frissítse a `REGISTRY_NAME` buildlépés értékét az alapbeállítási adatbázis kiszolgálónevével.

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

A build lépés `Dockerfile-app` az [Azure-Samples/acr-build-helloworld-node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) repo fájl használatával hoz létre egy lemezképet. Az `--build-arg` alapbeállítási beállításjegyzékre hivatkozik az alaplemezkép lekérése érdekében. Ha sikeresen létrejött, a rendszerkép a feladat futtatásához használt rendszerleíró adatbázisba kerül.

## <a name="option-1-create-task-with-user-assigned-identity"></a>1. lehetőség: Feladat létrehozása a felhasználó által hozzárendelt identitással

Ebben a szakaszban található lépések létrehoznak egy feladatot, és engedélyezik a felhasználó által hozzárendelt identitást. Ha ehelyett a rendszer hez rendelt identitást szeretné engedélyezni, olvassa el [a 2.](#option-2-create-task-with-system-assigned-identity) 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Feladat létrehozása

Hozza létre a feladatot *helloworldtask* végrehajtásával a következő [az acr feladat létrehozása][az-acr-task-create] parancsot. A feladat forráskódkörnyezet nélkül fut, és a `helloworldtask.yaml` parancs a munkakönyvtárban lévő fájlra hivatkozik. A `--assign-identity` paraméter átadja a felhasználó által hozzárendelt identitás erőforrásazonosítóját. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>2. lehetőség: Feladat létrehozása rendszeráltal hozzárendelt identitással

Ebben a szakaszban található lépések létrehoznak egy feladatot, és engedélyezik a rendszer által hozzárendelt identitást. Ha ehelyett felhasználó által hozzárendelt identitást szeretne engedélyezni, olvassa el [az 1.](#option-1-create-task-with-user-assigned-identity) 

### <a name="create-task"></a>Feladat létrehozása

Hozza létre a feladatot *helloworldtask* végrehajtásával a következő [az acr feladat létrehozása][az-acr-task-create] parancsot. A feladat forráskódkörnyezet nélkül fut, és a `helloworldtask.yaml` parancs a munkakönyvtárban lévő fájlra hivatkozik. Az `--assign-identity` érték nélküli paraméter engedélyezi a rendszer által hozzárendelt identitást a feladaton. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="give-identity-pull-permissions-to-the-base-registry"></a>Identitáslekéréses engedélyek megadása az alapbeállításjegyzékhez

Ebben a szakaszban adja meg a felügyelt identitás engedélyeket, hogy lekérése az alap rendszerleíró adatbázis, *mybaseregistry*.

Az [az acr show][az-acr-show] parancs segítségével leszeretné késelni az alaprendszerleíró adatbázis erőforrás-azonosítóját, és egy változóban tárolni:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Az [az szerepkör-hozzárendelés létrehozása][az-role-assignment-create] parancs segítségével `acrpull` rendelje hozzá a szerepkört az alap beállításjegyzékhez. Ez a szerepkör csak a lemezképek rendszerleíró adatbázisból történő lekéréseihez rendelkezik.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>Célrendszerleíró adatbázis hitelesítő adatainak hozzáadása a feladathoz

Most használja az [az acr feladat hitelesítő adatok hozzáadása][az-acr-task-credential-add] parancsot, hogy a feladat hitelesítse magát az alap rendszerleíró adatbázis az identitás hitelesítő adatait. Futtassa a feladatban engedélyezett felügyelt identitás típusának megfelelő parancsot. Ha engedélyezte a felhasználó által hozzárendelt `--use-identity` identitást, adja át az identitás ügyfélazonosítóját. Ha engedélyezte a rendszer által hozzárendelt `--use-identity [system]`identitást, adja át a .

```azurecli
# Add credentials for user-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity $clientID

# Add credentials for system-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity [system]
```

## <a name="manually-run-the-task"></a>A feladat manuális futtatása

Annak ellenőrzéséhez, hogy a feladat, amelyben engedélyezte a felügyelt identitás sikeresen fut, manuálisan indítsa el a feladatot az [az acr feladat futtatása][az-acr-task-run] paranccsal. 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

Ha a feladat sikeresen fut, a kimenet a következőhöz hasonló:

```
Queued a run with ID: cf10
Waiting for an agent...
2019/06/14 22:47:32 Using acb_vol_dbfbe232-fd76-4ca3-bd4a-687e84cb4ce2 as the home volume
2019/06/14 22:47:39 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/14 22:47:40 Successfully set up Docker network: acb_default_network
2019/06/14 22:47:40 Setting up Docker configuration...
2019/06/14 22:47:41 Successfully set up Docker configuration
2019/06/14 22:47:41 Logging in to registry: myregistry.azurecr.io
2019/06/14 22:47:42 Successfully logged into myregistry.azurecr.io
2019/06/14 22:47:42 Logging in to registry: mybaseregistry.azurecr.io
2019/06/14 22:47:43 Successfully logged into mybaseregistry.azurecr.io
2019/06/14 22:47:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:43 Scanning for dependencies...
2019/06/14 22:47:45 Successfully scanned dependencies
2019/06/14 22:47:45 Launching container with name: acb_step_0
Sending build context to Docker daemon   25.6kB
Step 1/6 : ARG REGISTRY_NAME
Step 2/6 : FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
9-alpine: Pulling from baseimages/node
[...]
Successfully built 41b49a112663
Successfully tagged myregistry.azurecr.io/hello-world:cf10
2019/06/14 22:47:56 Successfully executed container: acb_step_0
2019/06/14 22:47:56 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:56 Pushing image: myregistry.azurecr.io/hello-world:cf10, attempt 1
The push refers to repository [myregistry.azurecr.io/hello-world]
[...]
2019/06/14 22:48:00 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.517011)
2019/06/14 22:48:00 The following dependencies were found:
2019/06/14 22:48:00
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf10
    digest: sha256:611cf6e3ae3cb99b23fadcd89fa144e18aa1b1c9171ad4a0da4b62b31b4e38d1
  runtime-dependency:
    registry: mybaseregistry.azurecr.io
    repository: baseimages/node
    tag: 9-alpine
    digest: sha256:e8e92cffd464fce3be9a3eefd1b65dc9cbe2484da31c11e813a4effc6105c00f
  git:
    git-head-revision: 0f988779c97fe0bfc7f2f74b88531617f4421643

Run ID: cf10 was successful after 32s
```

Futtassa az [az acr repository show-tags][az-acr-repository-show-tags] parancsot annak ellenőrzéséhez, hogy a lemezkép létrejött-e, és sikeresen átkerült-e a *myregistry-ba:*

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

Példa a kimenetre:

```console
cf10
```

## <a name="next-steps"></a>További lépések

* További információ a felügyelt identitás okának engedélyezéséről [az ACR-feladatban.](container-registry-tasks-authentication-managed-identity.md)
* Az [ACR-feladatok YAML-hivatkozásának megtekintése](container-registry-tasks-reference-yaml.md)

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
