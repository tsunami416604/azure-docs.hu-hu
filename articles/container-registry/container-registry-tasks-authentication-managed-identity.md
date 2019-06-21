---
title: Az Azure Container Registry feladatok egy felügyelt identitás használata
description: Egyéb privát tárolójegyzékek például úgy, hogy egy felügyelt identitás hozzárendelése az Azure-erőforrások Azure-erőforrások egy Azure Container Registry tevékenységhez hozzáférést biztosítanak.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 5b60727472a06aaac8ccd3dce8609461e8972311
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148031"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Használja az Azure-identitás az ACR-feladatokban 

Használja a [-identitás az Azure-erőforrások](../active-directory/managed-identities-azure-resources/overview.md) hitelesítéséhez az ACR-feladatok egy Azure container Registry tárolóregisztrációs adatbázist, vagy más Azure-erőforrások nélkül kellene vagy kódot a hitelesítő adatok kezelése. Például használja egy felügyelt identitás lekéréses vagy tárolórendszerképek leküldése egy másik beállításjegyzék, hogy egy feladatban lépést.

Ebben a cikkben, tudjon meg többet a felügyelt identitásokból és:

> [!div class="checklist"]
> * Egy rendszer által hozzárendelt identitással és a egy ACR-tevékenység egy felhasználó által hozzárendelt identitás engedélyezése
> * Az identitás hozzáférést biztosít az Azure-erőforrások, például más Azure tárolóregisztrációs adatbázis
> * Az erőforrások eléréséhez a feladat a felügyelt identitás használata 

Az Azure-erőforrások létrehozását, ehhez a cikkhez futtatása az Azure CLI 2.0.66 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

## <a name="why-use-a-managed-identity"></a>Miért érdemes használni egy felügyelt identitás?

Egy felügyelt identitás, az Azure-erőforrások Azure-szolgáltatásokat az Azure Active Directoryban (Azure AD) automatikusan felügyelt identitást biztosít. Konfigurálható [bizonyos Azure-erőforrások](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), beleértve az ACR-feladatokat, egy felügyelt identitás. Ezután használja az identitás programkódban vagy parancsprogramban hitelesítő adatok nélkül férhetnek hozzá a többi Azure-erőforrások.

Felügyelt identitások két típusa van:

* *Felhasználó által hozzárendelt identitások*, amely hozzárendelése több erőforrást, és addig, amíg a kívánt fenntartását. Felhasználó által hozzárendelt identitások jelenleg előzetes verzióban érhető el.

* A *rendszer által felügyelt identitás*, amely egy adott erőforrással, például egy ACR-tevékenység egyedi, és az erőforrás teljes tart.

Egy Azure-erőforrás egy felügyelt identitás beállítása után hozzáférést az azonosító egy másik erőforrás, mint egy rendszerbiztonsági tagot. Például egy felügyelt identitás szerepkör hozzárendelése lekéréses, leküldéses és lekéréses vagy egyéb engedélyek az Azure-ban egy privát tárolójegyzékben. (Beállításjegyzék szerepkörök teljes listáját lásd: [Azure Container Registry-szerepkörökről és engedélyekről](container-registry-roles.md).) Egy vagy több erőforrást akkor is hozzáférést egy identitás.

## <a name="create-container-registries"></a>Tárolóregisztrációs adatbázis létrehozása

Ebben az oktatóanyagban három tároló-beállításjegyzékek szüksége lesz:

* Az első beállításjegyzék használatával hozzon létre, és hajtsa végre az ACR-feladatokat. Ez a cikk a forrás tárolójegyzék neve *myregistry*. 
* A második és harmadik beállításjegyzékek target beállításjegyzékekhez épít, rendszerkép leküldése az első példa tevékenységet is. Ez a cikk a target beállításjegyzékekhez nevesített *customregistry1* és *customregistry2*.

Cserélje le a saját beállításjegyzék nevével, a későbbi lépésekben.

Ha még nem rendelkezik a szükséges Azure tároló-beállításjegyzékek, [a rövid útmutató: Az Azure CLI használatával privát tárolóregisztrációs adatbázis létrehozása](container-registry-get-started-azure-cli.md). Nem kell még rendszerképek leküldése a beállításjegyzékbe.

## <a name="example-task-with-a-system-assigned-identity"></a>Példa: A feladat egy rendszer által hozzárendelt identitással

Ez a példa bemutatja, hogyan hozhat létre egy [többlépéses feladat](container-registry-tasks-multi-step.md) egy rendszer által hozzárendelt identitással. A feladat összeállít egy rendszerképet, és ezután a két target beállításjegyzékekhez le a rendszerképet a hitelesítéshez használja az identitást.

Ez a példa a feladat lépéseinek vannak meghatározva egy [YAML-fájl](container-registry-tasks-reference-yaml.md) nevű `testtask.yaml`. A fájl multipleRegistries könyvtárában található a [acr-feladatok](https://github.com/Azure-Samples/acr-tasks) minták adattárat. Itt jelenik meg a fájlt:

```yml
version: v1.0.0
steps:
  - build: -t {{.Values.REGISTRY1}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY1}}/hello-world:{{.Run.ID}}"]
  - build: -t {{.Values.REGISTRY2}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY2}}/hello-world:{{.Run.ID}}"]
```

### <a name="create-task-with-system-assigned-identity"></a>Feladat létrehozása a rendszer által hozzárendelt identitással

A feladat létrehozása *több – reg* az alábbiak végrehajtásával [az acr-feladat létrehozása] [ az-acr-task-create] parancsot. A feladat környezete a minta tárház a multipleRegistries mappa, és a parancsot a fájl hivatkozik `testtask.yaml` az adattárban. A `--assign-identity` további értéket a paraméter a tevékenység alapértelmezett azonosítót hoz létre. Ez a feladat be van állítva, így manuális aktiválása kell, de beállíthat, futtatását, amikor a véglegesítés kerüljenek a tárház vagy egy lekéréses kérelmet. 

```azurecli
az acr task create \
  --registry myregistry \
  --name multiple-reg \
  --context https://github.com/Azure-Samples/acr-tasks.git#:multipleRegistries \
  --file testtask.yaml \
  --commit-trigger-enabled false \
  --pull-request-trigger-enabled false \
  --assign-identity
```

A parancs kimenetében a `identity` szakasz bemutatja az identitás típusú `SystemAssigned` a feladat be van állítva. A `principalId` a szolgáltatásnév-Azonosítót az identitás van:

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 

Használja a [az acr feladat megjelenítése] [ az-acr-task-show] parancs tárolására a `principalId` egy változóban, az újabb parancsok használatához:

```azurecli
principalID=$(az acr task show --name multiple-reg --registry myregistry --query identity.principalId --output tsv)
```

### <a name="give-identity-push-permissions-to-two-target-container-registries"></a>Két cél tárolójegyzékek identitás leküldéses engedélyt

Ebben a szakaszban a két target beállításjegyzékekhez, nevű küldje le a rendszer által hozzárendelt identitás engedélyeket biztosíthat a *customregistry1* és *customregistry2*.

Első alkalommal használják a [az acr show] [ az-acr-show] parancsot minden egyes beállításjegyzék erőforrás-Azonosítóját, és tárolja az azonosítók változókat:

```azurecli
reg1_id=$(az acr show --name customregistry1 --query id --output tsv)
reg2_id=$(az acr show --name customregistry2 --query id --output tsv)
```

Használja a [az szerepkör-hozzárendelés létrehozása] [ az-role-assignment-create] parancsot az identitás hozzárendelése a `acrpush` szerepkör minden egyes regisztrációs adatbázisba. Ez a szerepkör jogosult a lehívásos és a leküldéses lemezképek továbbíthat egy tárolóregisztrációs adatbázisba.

```azurecli
az role assignment create --assignee $principalID --scope $reg1_id --role acrpush
az role assignment create --assignee $principalID --scope $reg2_id --role acrpush
```

### <a name="add-target-registry-credentials-to-task"></a>A feladat célja a tárolójegyzék hitelesítő adatainak hozzáadása

Mostantól használhatja a [az acr feladat hitelesítő adatok hozzáadása] [ az-acr-task-credential-add] parancs használatával adja hozzá az identitás hitelesítő adatait a feladat, hogy mindkét target beállításjegyzékekhez a hitelesítést.

```azurecli
az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry1.azurecr.io \
  --use-identity [system]

az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry2.azurecr.io \
  --use-identity [system]
```

### <a name="manually-run-the-task"></a>A feladat manuális futtatása

Használja a [az acr-feladat futtatása] [ az-acr-task-run] paranccsal manuálisan aktiválhatja a feladatot. A `--set` paraméterrel adja át a változók értékeit, a bejelentkezési kiszolgáló nevét a két target beállításjegyzékekhez `REGISTRY1` és `REGISTRY2`.

```azurecli
az acr task run \
  --name multiple-reg \
  --registry myregistry \
  --set REGISTRY1=customregistry1.azurecr.io \
  --set REGISTRY2=customregistry2.azurecr.io
```

A kimenet a következőhöz hasonló:

```console
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:02 Successfully executed container: acb_step_0
2019/05/31 22:16:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:02 Pushing image: customregistry2.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry2.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:08 Successfully pushed image: customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:08 Executing step ID: acb_step_2. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:08 Scanning for dependencies...
2019/05/31 22:16:08 Successfully scanned dependencies
2019/05/31 22:16:08 Launching container with name: acb_step_2
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:09 Successfully executed container: acb_step_2
2019/05/31 22:16:09 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:09 Pushing image: customregistry1.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry1.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:21 Successfully pushed image: customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:21 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.985291)
2019/05/31 22:16:21 Populating digests for step ID: acb_step_0...
2019/05/31 22:16:22 Successfully populated digests for step ID: acb_step_0
2019/05/31 22:16:22 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 5.743225)
2019/05/31 22:16:22 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 1.925959)
2019/05/31 22:16:22 Populating digests for step ID: acb_step_2...
2019/05/31 22:16:24 Successfully populated digests for step ID: acb_step_2
2019/05/31 22:16:24 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.061057)
2019/05/31 22:16:24 The following dependencies were found:
2019/05/31 22:16:24
- image:
    registry: customregistry2.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b
- image:
    registry: customregistry1.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b

Run ID: cf31 was successful after 35s
```

## <a name="example-task-with-a-user-assigned-identity"></a>Példa: A feladat egy felhasználó által hozzárendelt identitással

Ebben a példában egy felhasználó által hozzárendelt identitással létrehozhat egy Azure key vault titkos kódok olvasni engedélyekkel. Ezt az identitást, amely beolvassa a titkos kulcsot egy képet, létrehozásával és jelentkezik be az Azure CLI, olvassa el a képcímke többlépéses feladat rendelheti hozzá.

### <a name="create-a-key-vault-and-store-a-secret"></a>Hozzon létre egy kulcstartót, és a titkos kulcs tárolása

Először Ha kell, hozzon létre egy erőforráscsoportot *myResourceGroup* a a *eastus* helyet az alábbi [az csoport létrehozása] [ az-group-create]parancsot:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Használja a [az keyvault létrehozása] [ az-keyvault-create] parancs használatával hozzon létre egy kulcstartót. Mindenképpen adjon meg egy egyedi kulcstároló nevét. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

A kulcstartóban található titkos kulcs minta Store a [az keyvault secret set] [ az-keyvault-secret-set] parancsot:

```azurecli
az keyvault secret set \
  --name SampleSecret \
  --value "Hello ACR Tasks!" \
  --description ACRTasksecret  \
  --vault-name mykeyvault
```

Például érdemes tárolhatjuk a hitelesítő adatokat egy privát Docker regisztrációs adatbázist a hitelesítéshez, privát rendszerkép kérheti le.

### <a name="create-an-identity"></a>Hozzon létre egy azonosítót

Hozzon létre egy azonosítót nevű *myACRTasksId* be az előfizetés a [az identitás létrehozása] [ az-identity-create] parancsot. Hozzon létre egy tároló-beállításjegyzéket, vagy a key vault, illetve egy másikat a korábban használt ugyanazt az erőforráscsoportot is használhatja.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Az alábbi lépéseket az identitás beállításához használja a [az identitás show] [ az-identity-show] parancsot az identitáshoz tartozó erőforrás-azonosító és a résztvevő-azonosító tárolására változókba.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)
```

### <a name="grant-identity-access-to-keyvault-to-read-secret"></a>A keyvault titkos elolvasható identitás hozzáférés engedélyezése

Futtassa a következő [az keyvault set-policy] [ az-keyvault-set-policy] parancsot a kulcstartóra vonatkozó hozzáférési szabályzat beállításához. Az alábbi példa lehetővé teszi, hogy a felhasználó által hozzárendelt identitás a kulcstartóban található titkos kódok lekéréséhez. Ez a hozzáférés később szükség van a többlépéses feladat sikeresen lefutott.

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $principalID --secret-permissions get
```

### <a name="grant-identity-reader-access-to-the-resource-group-for-registry"></a>Az erőforráscsoport, a beállításjegyzék identitás olvasó hozzáférést biztosítani

Futtassa a következő [az szerepkör-hozzárendelés létrehozása] [ az-role-assignment-create] parancs hozzárendelése az identitás-olvasó szerepkör ebben az esetben a forrás beállításjegyzék tartalmazó erőforráscsoportot. Ez a szerepkör szükséges később a többlépéses feladat sikeresen lefutott.

```azurecli
az role assignment create --role reader --resource-group myResourceGroup --assignee $principalID
```

### <a name="create-task-with-user-assigned-identity"></a>Feladat létrehozása a felhasználó által hozzárendelt identitással

Most hozzon létre egy [többlépéses feladat](container-registry-tasks-multi-step.md) , és rendelje hozzá a felhasználó által hozzárendelt identitás. Ez a példa a feladat, hozzon létre egy [YAML-fájl](container-registry-tasks-reference-yaml.md) nevű `managed-identities.yaml` egy helyi munkakönyvtárba, és illessze be a következő tartalmakat. Ügyeljen arra, hogy a kulcstartó nevét, a fájlban cserélje le a kulcstartó neve

```yml
version: v1.0.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: name
    keyvault: https://mykeyvault.vault.azure.net/secrets/SampleSecret
steps:
  # Verify that the task can access the secret in the key vault
  - cmd: bash -c 'if [ -z "$MY_SECRET" ]; then echo "Secret not resolved"; else echo "Secret resolved"; fi'
    env: 
      - MY_SECRET='{{.Secrets.name}}' 

  # Build/push the website image to source registry, using dockerfile in the Azure-Samples repo
  - cmd: docker build -t {{.Run.Registry}}/my-website:{{.Run.ID}} https://github.com/Azure-Samples/aci-helloworld.git
  - push: 
    - "{{.Run.Registry}}/my-website:{{.Run.ID}}"
  
  # Login to Azure CLI with identity and list the tags to verify that the image is in the registry
  - cmd: microsoft/azure-cli az login --identity
  - cmd: microsoft/azure-cli az acr repository show-tags -n {{.Values.registryName}} --repository my-website
```

Ez a feladat a következőket teszi:

* Ellenőrzi, hogy hozzá tudjon férni a titkos kulcsot tárol a kulcstárolóban. Ebben a lépésben bemutatásra szolgál. A való életből vett helyzet szüksége lehet egy lépés egy privát Docker Hub-adattár eléréséhez szükséges hitelesítő adatokat beolvasni.
* Hozza létre és leküldi a `mywebsite` lemezkép a forrás beállításjegyzékhez.
* Bejelentkezik az Azure CLI-listát a `my-website` kép címkék a forrás-beállításjegyzékben.

Hozzon létre egy feladatot nevű *msitask* és adja át azt a felhasználó által hozzárendelt identitás korábban létrehozott erőforrás-Azonosítóját. Ez a példa a feladat jön létre a `managed-identities.yaml` a helyi munkakönyvtárból, így manuális aktiválása kell mentett fájlt.

```azurecli
az acr task create \
  --name msitask \
  --registry myregistry \
  --context /dev/null \
  --file managed-identities.yaml \
  --pull-request-trigger-enabled false \
  --commit-trigger-enabled false \
  --assign-identity $resourceID
```

A parancs kimenetében a `identity` szakasz bemutatja az identitás típusú `UserAssigned` a feladat be van állítva. A `principalId` a szolgáltatásnév-Azonosítót az identitás van:

```console
[...]
"identity": {
    "principalId": null,
    "tenantId": null,
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRTasksId": {
        "clientId": "xxxxxxxx-f17e-4768-bb4e-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-1335-433d-bb6c-xxxxxxxxxxxx"
      }
[...]
```

### <a name="manually-run-the-task"></a>A feladat manuális futtatása

Használja a [az acr-feladat futtatása] [ az-acr-task-run] paranccsal manuálisan aktiválhatja a feladatot. A `--set` paraméter adja meg a forrás beállításjegyzék-nevet a feladat használható:

```azurecli
az acr task run --name msitask --registry myregistry --set registryName=myregistry  
```

Kimenet mutatja, hogy a titkos kulcsnak feloldva, a rendszerkép sikeresen kiépítette és leküldeni, és a feladat bejelentkezik az Azure CLI-t az identitás a képcímke kiolvasása a forrás beállításjegyzék:

```console
Queued a run with ID: cf32
Waiting for an agent...
2019/06/10 23:25:37 Downloading source code...
2019/06/10 23:25:38 Finished downloading source code
2019/06/10 23:25:39 Using acb_vol_4e4a0a7c-b6ef-4ec5-b40f-3436fc5eb0f5 as the home volume
2019/06/10 23:25:41 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/10 23:25:41 Successfully set up Docker network: acb_default_network
2019/06/10 23:25:41 Setting up Docker configuration...
2019/06/10 23:25:42 Successfully set up Docker configuration
2019/06/10 23:25:42 Logging in to registry: myregistry.azurecr.io
2019/06/10 23:25:43 Successfully logged into myregistry.azurecr.io
2019/06/10 23:25:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:43 Launching container with name: acb_step_0
Secret resolved
2019/06/10 23:25:44 Successfully executed container: acb_step_0
2019/06/10 23:25:44 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:44 Launching container with name: acb_step_1
Sending build context to Docker daemon  74.75kB

[...]

cf32: digest: sha256:cbb4aa83b33f6959d83e84bfd43ca901084966a9f91c42f111766473dc977f36 size: 1577
2019/06/10 23:26:05 Successfully pushed image: myregistry.azurecr.io/my-website:cf32
2019/06/10 23:26:05 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:05 Launching container with name: acb_step_3
[
  {
    "environmentName": "AzureCloud",
    "id": "xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx",
    "isDefault": true,
    "name": "DanLep Internal Consumption",
    "state": "Enabled",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
      "user": {
      "assignedIdentityInfo": "MSI",
      "name": "systemAssignedIdentity",
      "type": "servicePrincipal"
    }
  }
]
2019/06/10 23:26:09 Successfully executed container: acb_step_3
2019/06/10 23:26:09 Executing step ID: acb_step_4. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:09 Launching container with name: acb_step_4
[
  "cf32"
]
2019/06/10 23:26:14 Successfully executed container: acb_step_4
2019/06/10 23:26:14 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.025312)
2019/06/10 23:26:14 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 13.703823)
2019/06/10 23:26:14 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 6.791506)
2019/06/10 23:26:14 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 3.852972)
2019/06/10 23:26:14 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 5.079079)
```


## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett az Azure Container Registry feladatok felügyelt identitások használatával, és hogyan:

> [!div class="checklist"]
> * A rendszer által hozzárendelt identitás engedélyezése vagy egy ACR-feladatot a felhasználó által hozzárendelt
> * Az identitás hozzáférést biztosít az Azure-erőforrások, például más Azure tárolóregisztrációs adatbázis
> * Az erőforrások eléréséhez a feladat a felügyelt identitás használata  

* Tudjon meg többet [felügyelt identitások az Azure-erőforrások](/azure/active-directory/managed-identities-azure-resources/).

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create
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
