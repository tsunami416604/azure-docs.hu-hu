---
title: Oktatóanyag – Tárolórendszerképek összeállítása a felhőben az Azure Container Registry Build használatával
description: Ebben az oktatóanyagban megtudhatja, hogy hogyan állíthat össze Docker-tárolórendszerképet az Azure-ban az Azure Container Registry Build (ACR Build) használatával, majd hogyan helyezheti azokat üzembe az Azure Container Instances szolgáltatásban.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: d86c47c890fd15515c590e06b395ca82f9747ffe
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38461469"
---
# <a name="tutorial-build-container-images-in-the-cloud-with-azure-container-registry-build"></a>Oktatóanyag: Tárolórendszerképek összeállítása a felhőben az Azure Container Registry Build használatával

Az **ACR Build** az Azure Container Registry egy szolgáltatáscsomagja, amely lehetővé teszi a Docker-tárolórendszerképek zökkenőmentes és hatékony összeállítását az Azure-ban. Ebben a cikkben az ACR Build *gyors összeállítási* szolgáltatásának használatával ismerkedhet meg. A gyors összeállítás kiterjeszti a „belső fejlesztési ciklust” a felhőbe, és lehetővé teszi az összeállítások sikerességének ellenőrzését, valamint a sikeresen összeállított rendszerképek automatikus leküldését a tárolóregisztrációs adatbázisba. A rendszerképek összeállítása natív módon a felhőben történik, a regisztrációs adatbázis közelében, ami meggyorsítja az üzembe helyezést.

A Docker-fájlokkal kapcsolatos ismeretek közvetlenül kamatoztathatók az ACR Buildben. A Docker-fájlokat nem, csupán a futtatott parancsot kell módosítani, ha az ACR Builddel szeretne a felhőben összeállítást végrehajtani.

Ez az oktatóanyag egy sorozat első része, és az alábbiakat ismerteti:

> [!div class="checklist"]
> * Mintaalkalmazás forráskódjának lekérése
> * Tárolórendszerkép összeállítása az Azure-ban
> * Tároló üzembe helyezése az Azure Container Instances szolgáltatásban

A további oktatóanyagokban elsajátítja majd, hogy hogyan lehet az ACR Build összeállítási feladataival automatizálni a tárolórendszerképek összeállítását kódvéglegesítés, illetve az alapként szolgáló rendszerképek frissítése alkalmával.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha az Azure CLI-t helyben szeretné használni, az Azure CLI **2.0.32-es** vagy újabb verzióját kell telepítenie. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretné a parancssori felületet, olvassa el [az Azure CLI telepítését][azure-cli] ismertető témakört.

## <a name="prerequisites"></a>Előfeltételek

### <a name="github-account"></a>GitHub-fiók

Hozzon létre egy fiókot a https://github.com webhelyen, ha még nem rendelkezik fiókkal. Ebben az oktatóanyag-sorozatban egy GitHub-adattárat használunk az ACR Build automatizált rendszerkép-összeállítási képességeinek bemutatásához.

### <a name="fork-sample-repository"></a>Mintaadattár leágaztatása

Ezután a GitHub felhasználói felületén ágaztassa le a mintaadattárat a GitHub-fiókba. Ebben az oktatóanyagban egy tárolórendszerképet állít össze az adattárban lévő forrásból, a következő oktatóanyagban pedig egy véglegesítést küld le a leágaztatott adattárba egy automatizált összeállítás elindítása céljából.

Ágaztassa le ezt az adattárat: https://github.com/Azure-Samples/acr-build-helloworld-node.

![A GitHub Fork (Leágaztatás) gombjának (kiemelve) képernyőképe][quick-build-01-fork]

### <a name="clone-your-fork"></a>A leágaztatás klónozása

Miután leágaztatta az adattárat, klónozza a leágaztatást, és lépjen a helyi klónt tartalmazó könyvtárba.

Klónozza az adattárat a `git` paranccsal, helyettesítse be a **\<your-github-username\>** helyőrzőt a GitHub-felhasználónévvel:

```azurecli-interactive
git clone https://github.com/<your-github-username>/acr-build-helloworld-node
```

Lépjen a forráskódot tartalmazó könyvtárba:

```azurecli-interactive
cd acr-build-helloworld-node
```

### <a name="bash-shell"></a>Bash-felület

Az oktatóanyag-sorozatban használt parancsok a Bash-felületnek megfelelően vannak formázva. Ha inkább a PowerShellt, a parancssort vagy egy másik rendszerhéjat szeretne használni, ennek megfelelően esetleg módosítania kell a sorok tartalmát és a környezeti változók formátumát.

## <a name="build-in-azure-with-acr-build"></a>Összeállítás az Azure-ban az ACR Build használatával

Miután lekérte a forráskódot a gépre, az alábbi lépéseket követve hozzon lére egy tárolóregisztrációs adatbázist, és állítsa össze a tárolórendszerképet az ACR Build használatával.

A mintaparancsok könnyebb végrehajtása érdekében a sorozat oktatóanyagai rendszerhéj-környezeti változókat használnak. Futtassa a következő parancsot az `ACR_NAME` változó beállításához. A **\<registry-name\>** helyőrzőt cserélje az új tárolóregisztrációs adatbázis egyedi nevével. A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Az oktatóanyagban létrehozott egyéb erőforrások is ezen néven alapulnak, így csak ezt az első változót kell módosítania.

```azurecli-interactive
ACR_NAME=<registry-name>
```

Miután a tárolóregisztrációs adatbázis környezeti változóját ellátta értékkel, az oktatóanyagban szereplő többi parancsot az értékek szerkesztése nélkül másolhatja és beillesztheti. Az alábbi parancsok végrehajtásával hozzon létre egy erőforráscsoportot és egy tárolóregisztrációs adatbázist:

```azurecli-interactive
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

Miután a regisztrációs adatbázis létrejött, az ACR Build használatával állítson össze egy tárolórendszerképet a mintakódból. Az [az acr build][az-acr-build] parancs futtatásával hajtson végre egy *gyors összeállítást*:

```azurecli-interactive
az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
```

Az [az acr build][az-acr-build] parancs kimenete az alábbihoz hasonló. Láthatja a forráskód (a „környezet”) feltöltését az Azure-ba, valamint az ACR Build által a felhőben futtatott `docker build` művelet részleteit. Mivel az ACR Build a `docker build` használatával állítja össze a rendszerképeket, a Docker-fájlokat nem kell módosítani az ACR Build használatának azonnali megkezdéséhez.

```console
$ az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
Sending build context (4.909 KiB) to ACR.
Queued a build with build ID: aa1
Waiting for a build agent...
Sending build context to Docker daemon  22.53kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
Status: Image is up to date for node:9-alpine
 ---> 7af437a39ec2
Step 2/5 : COPY . /src
 ---> 0c4814714938
Step 3/5 : RUN cd /src && npm install
 ---> Running in 4f77ce7b330d
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.096s
Removing intermediate container 4f77ce7b330d
 ---> e0eb24339e07
Step 4/5 : EXPOSE 80
 ---> Running in 872bd29edbc7
Removing intermediate container 872bd29edbc7
 ---> 22ba8d8ffb4e
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 1a40c05c4122
Removing intermediate container 1a40c05c4122
 ---> 0a9a4b74fb53
Successfully built 0a9a4b74fb53
Successfully tagged mycontainerregistry.azurecr.io/helloacrbuild:v1
time="2018-05-10T19:10:20Z" level=info msg="Running command docker push mycontainerregistry.azurecr.io/helloacrbuild:v1"
The push refers to repository [mycontainerregistry.azurecr.io/helloacrbuild]
d2b301f7ef94: Preparing
d0e0f2bb8747: Preparing
26b0c207c4a9: Preparing
917e7cdebc8b: Preparing
9dfa40a0da3b: Preparing
26b0c207c4a9: Pushed
d2b301f7ef94: Pushed
d0e0f2bb8747: Pushed
9dfa40a0da3b: Pushed
917e7cdebc8b: Pushed
v1: digest: sha256:78d7980b4c80a078192bd4749c27eeae56421079606ed7b7d8ae84dbb04193fd size: 1366
time="2018-05-10T19:11:07Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistry.azurecr.io/helloacrbuild:v1"
"["mycontainerregistry.azurecr.io/helloacrbuild@sha256:78d7980b4c80a078192bd4749c27eeae56421079606ed7b7d8ae84dbb04193fd"]"
time="2018-05-10T19:11:07Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistry.azurecr.io
    repository: helloacrbuild
    tag: v1
    digest: sha256:78d7980b4c80a078192bd4749c27eeae56421079606ed7b7d8ae84dbb04193fd
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3

Build complete
Build ID: aa1 was successful after 52.522222729s
```

A kimenet vége felé az ACR Build a rendszerkép észlelt függőségeit jeleníti meg. Ez lehetővé teszi az ACR Build számára a rendszerképek összeállításának automatikus elindítását az alapként szolgáló rendszerképek frissítésekor, például amikor az alapként szolgáló rendszerképet az operációs rendszer vagy a keretrendszer javításával frissítették. Az oktatóanyag-sorozat egy későbbi része ismerteti, hogy az ACR Build hogyan támogatja az alapként szolgáló rendszerképek frissítését.

## <a name="deploy-to-azure-container-instances"></a>Üzembe helyezés az Azure Container Instances szolgáltatásban

Az ACR Build alapértelmezés szerint automatikusan leküldi a sikeresen összeállított rendszerképeket a regisztrációs adatbázisba, így Ön onnan azonnal üzembe helyezheti azokat.

Ebben a szakaszban létrehoz egy Azure Key Vault kulcstárolót és egy szolgáltatásnevet, majd üzembe helyezi a tárolót az Azure Container Instances (ACI) szolgáltatásban a szolgáltatásnév hitelesítő adataival.

### <a name="configure-registry-authentication"></a>Regisztrációs adatbázis hitelesítésének konfigurálása

Az Azure-beli tárolóregisztrációs adatbázisok eléréséhez minden éles forgatókönyvben [szolgáltatásneveket][service-principal-auth] ajánlott használni. A szolgáltatásnevek lehetővé teszik a szerepköralapú hozzáférés-vezérlés biztosítását a tárolórendszerképek számára. Konfigurálhat például egy olyan szolgáltatásnevet, amely csak lekérés céljából férhet hozzá a regisztrációs adatbázishoz.

#### <a name="create-key-vault"></a>Kulcstároló létrehozása

Ha még nem rendelkezik tárolóval az [Azure Key Vaultban](/azure/key-vault/), hozzon létre egyet az Azure CLI alábbi parancsaival.

```azurecli-interactive
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-service-principal-and-store-credentials"></a>Szolgáltatásnév létrehozása és a hitelesítő adatok tárolása

Létre kell hoznia egy szolgáltatásnevet, és el kell tárolnia annak hitelesítő adatait a kulcstárolóban.

Az [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] paranccsal hozhatja létre a szolgáltatásnevet, és a **jelszavát** az [az keyvault secret set][az-keyvault-secret-set] paranccsal tárolhatja el a tárolóban:

```azurecli-interactive
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role reader \
                --query password \
                --output tsv)
```

Az előző parancs `--role` argumentuma a *reader* (olvasó) szerepkörrel konfigurálja a szolgáltatásnevet, amely csak lekérési jogosultságot biztosít a regisztrációs adatbázishoz. Ha lekérési és leküldési jogosultságot egyaránt biztosítani kíván, módosítsa a `--role` argumentum értékét a *contributor* (közreműködő) értékre.

Ezután tárolja el a szolgáltatásnév *appId* azonosítóját a tárolóban, amely az Azure Container Registry szolgáltatásban a hitelesítéskor megadandó **felhasználónév** lesz:

```azurecli-interactive
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Létrehozott egy Azure Key Vault-tárolót, és eltárolt benne két titkos kulcsot:

* `$ACR_NAME-pull-usr`: A szolgáltatásnév azonosítója, amely a tárolóregisztrációs adatbázis **felhasználóneveként** szolgál.
* `$ACR_NAME-pull-pwd`: A szolgáltatásnév jelszava, amely a tárolóregisztrációs adatbázis **jelszavaként** szolgál.

Innentől ezekre a titkos kulcsokra név alapján hivatkozhat, amikor Ön vagy az alkalmazások és szolgáltatások rendszerképeket kérnek le a regisztrációs adatbázisból.

### <a name="deploy-container-with-azure-cli"></a>Tároló üzembe helyezése az Azure CLI használatával

Miután a szolgáltatásnév hitelesítő adatait titkos kulcsként mentette az Azure Key Vaultban, az alkalmazások és a szolgáltatások a kulcsok használatával hozzáférhetnek a privát regisztrációs adatbázishoz.

A következő [az container create][az-container-create] parancs végrehajtásával helyezzen üzembe egy tárolópéldányt. A parancs a szolgáltatásnévnek az Azure Key Vault-tárolóban tárolt hitelesítő adatait használja a tárolóregisztrációs adatbázisban való hitelesítéshez.

```azurecli-interactive
az container create \
    --resource-group $RES_GROUP \
    --name acr-build \
    --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-build-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

A `--dns-name-label` értékének egyedinek kell lennie az Azure-ban, így az előző parancs hozzáfűzi a tárolóregisztrációs adatbázis nevét a tároló DNS-nevének címkéjéhez. A parancs kimenete a tároló teljes tartománynevét jeleníti meg, például:

```console
$ az container create \
>     --resource-group $RES_GROUP \
>     --name acr-build \
>     --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
>     --registry-login-server $ACR_NAME.azurecr.io \
>     --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
>     --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
>     --dns-name-label acr-build-$ACR_NAME \
>     --query "{FQDN:ipAddress.fqdn}" \
>     --output table
FQDN
-------------------------------------------
acr-build-1175.eastus.azurecontainer.io
```

Jegyezze fel a tároló teljes tartománynevét, amelyet a következő szakaszban fog használni.

### <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

A tároló indítási folyamatának megtekintéséhez használja az [az container attach][az-container-attach] parancsot:

```azurecli-interactive
az container attach --resource-group $RES_GROUP --name acr-build
```

Az `az container attach` kimenete először a tároló állapotát jeleníti meg, miközben a tároló lekéri a rendszerképet, és elindul, majd a helyi konzol STDOUT és STDERR értékeit a tároló ugyanezen értékeihez köti.

```console
$ az container attach --resource-group $RES_GROUP --name acr-build
Container 'acr-build' is in state 'Waiting'...
Container 'acr-build' is in state 'Running'...
(count: 1) (last timestamp: 2018-04-03 19:45:37+00:00) pulling image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Successfully pulled image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Created container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Started container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413

Start streaming logs:
Server running at http://localhost:80
```

Amikor megjelenik a `Server running at http://localhost:80` üzenet, adja meg a tároló teljes tartománynevét a böngészőben a futó alkalmazás megtekintéséhez:

![A böngészőben megjelenített mintaalkalmazás képernyőképe][quick-build-02-browser]

A konzolt a `Control+C` billentyűkombinációval választhatja le a tárolóról.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Állítsa le a tárolópéldányt az [az container delete][az-container-delete] paranccsal:

```azurecli-interactive
az container delete --resource-group $RES_GROUP --name acr-build
```

Az oktatóanyagban létrehozott *összes* erőforrás, így a tárolóregisztrációs adatbázis, a kulcstároló és a szolgáltatásnév eltávolításához hajtsa végre az alábbi parancsokat. Ezeket az erőforrásokat azonban a sorozat [következő oktatóanyagában](container-registry-tutorial-build-task.md) is használjuk majd, ezért érdemes megtartani azokat, ha egyből továbblép a következő oktatóanyagra.

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>További lépések

Miután egy gyors összeállítással tesztelte a belső ciklust, konfiguráljon egy **összeállítási feladatot** a tárolórendszerképek összeállításának automatikus aktiválására, amikor forráskódot véglegesít egy Git-adattárban:

> [!div class="nextstepaction"]
> [Automatikus összeállítás aktiválása összeállítási feladatokkal](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
