---
title: Külső hitelesítés az ACR-feladatból
description: Konfiguráljon egy Azure Container Registry Task (ACR-feladat) az Azure-kulcstárolóban tárolt Docker Hub-hitelesítő adatok olvasásához az Azure-erőforrások felügyelt identitásának használatával.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 47d3d643ee1287ef4f444095a2c6cfe6dcab294b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842520"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Külső hitelesítés Egy ACR-feladatban Azure által felügyelt identitás használatával 

Egy [ACR-feladatban](container-registry-tasks-overview.md) [engedélyezheti az Azure-erőforrások felügyelt identitását.](container-registry-tasks-authentication-managed-identity.md) A feladat az identitás használatával más Azure-erőforrások eléréséhez, anélkül, hogy a hitelesítő adatok megadása vagy kezelése. 

Ebben a cikkben megtudhatja, hogyan engedélyezheti a felügyelt identitások egy feladat, amely hozzáfér az Azure key vaultban tárolt titkos kulcsok. 

Az Azure-erőforrások létrehozásához ez a cikk megköveteli, hogy futtassa az Azure CLI 2.0.68-as vagy újabb verzióját. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

A példafeladat az Azure key vaultban tárolt Docker Hub-hitelesítő adatokat olvassa be. A hitelesítő adatok egy Docker Hub-fiók írási (leküldéses) engedélyekkel egy privát Docker Hub-tárház. A hitelesítő adatok olvasásához konfigurálja a feladatot felügyelt identitással, és rendelje hozzá a megfelelő engedélyeket. Az identitáshoz társított feladat létrehoz egy lemezképet, és bejelentkezik a Docker Hub-ba, hogy leküldéses a rendszerképet a privát tárműret. 

Ez a példa a felhasználó által hozzárendelt vagy a rendszer által hozzárendelt felügyelt identitást használó lépéseket mutatja be. Az identitás kiválasztása a szervezet igényeitől függ.

Egy valós forgatókönyv esetén a vállalat egy buildfolyamat részeként közzétehet i. 

## <a name="prerequisites"></a>Előfeltételek

Szüksége van egy Azure-tároló beállításjegyzék, amelyben a feladat futtatása. Ebben a cikkben ez a rendszerleíró adatbázis neve *myregistry*. Későbbi lépésekben cserélje le saját rendszerleíró nevét.

Ha még nem rendelkezik Azure-tároló beállításjegyzékkel, olvassa [el a rövid útmutató: Hozzon létre egy privát tároló beállításjegyzéket az Azure CLI használatával.](container-registry-get-started-azure-cli.md) Még nem kell a rendszerképeket a rendszerleíró adatbázisba átvinnie.

Emellett szüksége van egy privát tárház a Docker Hub, és egy Docker Hub-fiók engedéllyel írni a tárházba. Ebben a példában ez a tártár neve *hubuser/hubrepo.* 

## <a name="create-a-key-vault-and-store-secrets"></a>Kulcstartó létrehozása és titkos kulcsok tárolása

Először is, ha szükséges, hozzon létre egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen a következő [az csoport létrehozási][az-group-create] paranccsal:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Használja az [az keyvault create][az-keyvault-create] parancsot egy key vault létrehozásához. Ügyeljen arra, hogy adjon meg egy egyedi kulcstartó nevét. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Tárolja a szükséges Docker Hub hitelesítő adatokat a key vault [titkos készlet][az-keyvault-secret-set] parancs használatával. Ezekben a parancsokban az értékek et környezeti változók fogadják el:

```azurecli
# Store Docker Hub user name
az keyvault secret set \
  --name UserName \
  --value $USERNAME \
  --vault-name mykeyvault

# Store Docker Hub password
az keyvault secret set \
  --name Password \
  --value $PASSWORD \
  --vault-name mykeyvault
```

Egy valós forgatókönyv, titkok valószínűleg kell beállítani, és egy külön folyamat.

## <a name="define-task-steps-in-yaml-file"></a>Feladatlépések definiálása a YAML-fájlban

A példafeladat lépései [YAML-fájlban](container-registry-tasks-reference-yaml.md)vannak definiálva. Hozzon létre `dockerhubtask.yaml` egy helyi munkakönyvtárban megnevezett fájlt, és illessze be a következő tartalmat. Ügyeljen arra, hogy cserélje le a fájlban lévő kulcstartó nevét a kulcstartó nevére.

```yml
version: v1.1.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: username
    keyvault: https://mykeyvault.vault.azure.net/secrets/UserName
  - id: password
    keyvault: https://mykeyvault.vault.azure.net/secrets/Password
steps:
# Log in to Docker Hub
  - cmd: bash echo '{{.Secrets.password}}' | docker login --username '{{.Secrets.username}}' --password-stdin 
# Build image
  - build: -t {{.Values.PrivateRepo}}:$ID https://github.com/Azure-Samples/acr-tasks.git -f hello-world.dockerfile
# Push image to private repo in Docker Hub
  - push:
    - {{.Values.PrivateRepo}}:$ID
```

A feladat lépései a következőket teszik:

* Titkos hitelesítő adatok kezelése a Docker Hubhasználatával való hitelesítéshez.
* Hitelesítse magát a Docker `docker login` Hub segítségével a titkos kulcsok átadásával a parancs.
* Hozzon létre egy lemezképet egy minta Dockerfile az [Azure-minták/acr-feladatok](https://github.com/Azure-Samples/acr-tasks.git) tárházban.
* A rendszerkép leküldése a privát Docker Hub-tárházba.


## <a name="option-1-create-task-with-user-assigned-identity"></a>1. lehetőség: Feladat létrehozása a felhasználó által hozzárendelt identitással

Ebben a szakaszban található lépések létrehoznak egy feladatot, és engedélyezik a felhasználó által hozzárendelt identitást. Ha ehelyett a rendszer hez rendelt identitást szeretné engedélyezni, olvassa el [a 2.](#option-2-create-task-with-system-assigned-identity) 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Feladat létrehozása

Hozza létre a feladat *dockerhubtask* végrehajtásával a következő [az acr feladat create][az-acr-task-create] parancsot. A feladat forráskódkörnyezet nélkül fut, és a `dockerhubtask.yaml` parancs a munkakönyvtárban lévő fájlra hivatkozik. A `--assign-identity` paraméter átadja a felhasználó által hozzárendelt identitás erőforrásazonosítóját. 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>2. lehetőség: Feladat létrehozása rendszeráltal hozzárendelt identitással

Ebben a szakaszban található lépések létrehoznak egy feladatot, és engedélyezik a rendszer által hozzárendelt identitást. Ha ehelyett felhasználó által hozzárendelt identitást szeretne engedélyezni, olvassa el [az 1.](#option-1-create-task-with-user-assigned-identity) 

### <a name="create-task"></a>Feladat létrehozása

Hozza létre a feladat *dockerhubtask* végrehajtásával a következő [az acr feladat create][az-acr-task-create] parancsot. A feladat forráskódkörnyezet nélkül fut, és a `dockerhubtask.yaml` parancs a munkakönyvtárban lévő fájlra hivatkozik. Az `--assign-identity` érték nélküli paraméter engedélyezi a rendszer által hozzárendelt identitást a feladaton.  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="grant-identity-access-to-key-vault"></a>Identitás-hozzáférés megadása a kulcstartóhoz

Futtassa a következő [az keyvault set-policy][az-keyvault-set-policy] parancsot egy hozzáférési szabályzat beállításához a key vaulton. A következő példa lehetővé teszi, hogy az identitás titkos kulcsokat olvasson be a key vaultból. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>A feladat manuális futtatása

Annak ellenőrzéséhez, hogy a feladat, amelyben engedélyezte a felügyelt identitás sikeresen fut, manuálisan indítsa el a feladatot az [az acr feladat futtatása][az-acr-task-run] paranccsal. A `--set` paraméter a privát tárterület nevének a feladatnak való átadására szolgál. Ebben a példában a helyőrző-tárterület neve *hubuser/hubrepo.*

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

Ha a feladat sikeresen fut, a kimenet sikeres hitelesítést jelenít meg a Docker Hub számára, és a rendszerkép sikeresen létrejön, és a privát tárműtárgépre kerül:

```console
Queued a run with ID: cf24
Waiting for an agent...
2019/06/20 18:05:55 Using acb_vol_b1edae11-30de-4f2b-a9c7-7d743e811101 as the home volume
2019/06/20 18:05:58 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/20 18:05:58 Successfully set up Docker network: acb_default_network
2019/06/20 18:05:58 Setting up Docker configuration...
2019/06/20 18:05:59 Successfully set up Docker configuration
2019/06/20 18:05:59 Logging in to registry: myregistry.azurecr.io
2019/06/20 18:06:00 Successfully logged into myregistry.azurecr.io
2019/06/20 18:06:00 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:00 Launching container with name: acb_step_0
[...]
Login Succeeded
2019/06/20 18:06:02 Successfully executed container: acb_step_0
2019/06/20 18:06:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:02 Scanning for dependencies...
2019/06/20 18:06:04 Successfully scanned dependencies
2019/06/20 18:06:04 Launching container with name: acb_step_1
Sending build context to Docker daemon    129kB
[...]
2019/06/20 18:06:07 Successfully pushed image: hubuser/hubrepo:cf24
2019/06/20 18:06:07 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 2.064353)
2019/06/20 18:06:07 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.594061)
2019/06/20 18:06:07 Populating digests for step ID: acb_step_1...
2019/06/20 18:06:09 Successfully populated digests for step ID: acb_step_1
2019/06/20 18:06:09 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.743923)
2019/06/20 18:06:09 The following dependencies were found:
2019/06/20 18:06:09
- image:
    registry: registry.hub.docker.com
    repository: hubuser/hubrepo
    tag: cf24
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0e11c388b664df8a27a901dce21eb89f11d8292f7fca1b3e3c4321bf7897bffe
  git:
    git-head-revision: b0ffa6043dd893a4c75644c5fed384c82ebb5f9e

Run ID: cf24 was successful after 15s
```

Annak ellenőrzéséhez, hogy a rendszerkép`cf24` leküldéses, ellenőrizze a címkét (ebben a példában) a privát Docker Hub tárházban.

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
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
