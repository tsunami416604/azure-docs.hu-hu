---
title: Oktatóanyag – Tárolórendszerképek összeállításának automatizálása az Azure Container Registry Build használatával
description: Ez az oktatóanyag azt mutatja be, hogy hogyan konfigurálhatók az összeállítási feladatok úgy, hogy automatikusan aktiválják a tárolórendszerképek összeállítását a felhőben, amikor forráskódot véglegesít egy Git-adattárban.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 4accbcb61e57d58100b6a4c06142dd3dc633f7f4
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="tutorial-automate-container-image-builds-with-azure-container-registry-build"></a>Oktatóanyag: Tárolórendszerképek összeállításának automatizálása az Azure Container Registry Build használatával

A [gyors összeállítás](container-registry-tutorial-quick-build.md) mellett az ACR Build a Docker tárolórendszerképek automatikus összeállítását is támogatja az *összeállítási feladattal*. Ez az oktatóanyag azt mutatja be, hogyan lehet az Azure CLI használatával olyan összeállítási feladatot létrehozni, amely automatikusan aktiválja a rendszerképek összeállítását a felhőben, amikor forráskódot véglegesít egy Git-adattárban.

Az oktatóanyag-sorozat második része a következő lépésekből áll:

> [!div class="checklist"]
> * Összeállítási feladat létrehozása
> * Az összeállítási feladat tesztelése
> * Az összeállítási állapot megtekintése
> * Az összeállítási feladat aktiválása kódvéglegesítéssel

Ez az oktatóanyag feltételezi, hogy elvégezte az [előző oktatóanyag](container-registry-tutorial-quick-build.md) lépéseit. Ha még nem tette meg, hajtsa végre az előző oktatóanyag [Előfeltételek](container-registry-tutorial-quick-build.md#prerequisites) szakaszának lépéseit, mielőtt továbblépne.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha az Azure CLI-t helyben szeretné használni, az Azure CLI **2.0.32-es** vagy újabb verzióját kell telepítenie. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretné a parancssori felületet: [Az Azure CLI 2.0 telepítése][azure-cli].

## <a name="prerequisites"></a>Előfeltételek

### <a name="get-sample-code"></a>Mintakód letöltése

Ez az oktatóanyag feltételezi, hogy elvégezte az [előző oktatóanyag](container-registry-tutorial-quick-build.md) lépéseit, valamint elágaztatta és klónozta a mintaadattárat. Ha még nem tette meg, hajtsa végre az előző oktatóanyag [Előfeltételek](container-registry-tutorial-quick-build.md#prerequisites) szakaszának lépéseit, mielőtt továbblépne.

### <a name="container-registry"></a>Tárolóregisztrációs adatbázis

Az oktatóanyag elvégzéséhez rendelkeznie kell egy Azure-beli tárolóregisztrációs adatbázissal az Azure-előfizetésében. Amennyiben létre kell hoznia a regisztrációs adatbázist, tekintse meg az [előző oktatóanyagot](container-registry-tutorial-quick-build.md) vagy a [Rövid útmutató: Tárolóregisztrációs adatbázis létrehozása az Azure CLI-vel](container-registry-get-started-azure-cli.md) című cikket.

## <a name="build-task"></a>Összeállítási feladat

Az összeállítási feladat egy automatizált összeállítás tulajdonságait határozza meg, beleértve a tárolórendszerkép forráskódjának helyét és az összeállítást kiváltó eseményt. Amikor az összeállítási feladatban definiált események egyike (például egy véglegesítés egy Git-adattárban) bekövetkezik, az ACR Build elindít egy tárolórendszerkép-összeállítást a felhőben. Alapértelmezés szerint a sikeresen összeállított rendszerképet ezután leküldi a feladatban meghatározott Azure-beli tárolóregisztrációs adatbázisba.

Az ACR Build jelenleg az alábbi eseményindítókat támogatja az összeállítási feladatok esetén:

* Véglegesítés egy Git-adattárba
* Az alapként szolgáló rendszerkép frissítése

## <a name="create-a-build-task"></a>Összeállítási feladat létrehozása

Ebben a szakaszban először egy személyes hozzáférési jogkivonatot (PAT) hoz létre a GitHubban az ACR Builddel való használathoz. Ezután létrehoz egy összeállítási feladatot, amely aktivál egy összeállítást, ha valaki kódot véglegesít az adattár adott elágazásában.

### <a name="create-a-github-personal-access-token"></a>Személyes hozzáférési jogkivonat létrehozása a GitHubban

Ahhoz, hogy a Git-adattárban való véglegesítéskor kiválthasson egy összeállítást, az ACR Buildnek egy személyes hozzáférési jogkivonatra (PAT) van szüksége az adattár eléréséhez. Az alábbi lépéseket követve hozhat végre személyes hozzáférési jogkivonatot a GitHubban:

1. Lépjen a GitHub PAT-létrehozási oldalára a https://github.com/settings/tokens/new címen.
1. Adja meg a jogkivonat rövid **leírását**, például „ACR összeállítási feladat bemutatója”.
1. A **repo** (adattár) területen jelölje be a **repo:status** és a **public_repo** lehetőséget.

   ![A GitHub személyes hozzáférési jogkivonatok létrehozására szolgáló oldalának képernyőképe][build-task-01-new-token]

1. Kattintson a **Generate token** (Jogkivonat létrehozása) gombra (a rendszer kérheti a jelszó megadását).
1. Másolja és mentse a létrehozott jogkivonatot egy **biztonságos helyre** (ezt a jogkivonatot egy összeállítási feladat definiálására fogja használni a következő szakaszban).

   ![A létrehozott személyes hozzáférési jogkivonat képernyőképe a GitHubban][build-task-02-generated-token]

### <a name="create-the-build-task"></a>Az összeállítási feladat létrehozása

Most, miután végrehajtotta az ahhoz szükséges lépéseket, hogy az ACR Build olvashassa a véglegesítési állapotokat, és webhookokat hozhasson létre egy adattárban, létrehozhat egy összeállítási feladatot, amely egy tárolórendszerkép összeállítását váltja ki az adattárban való véglegesítés esetén.

Először lássa el ezeket a rendszerhéj-környezeti változókat a környezetnek megfelelő értékekkel. Ez nem feltétlenül szükséges, de némileg könnyebbé teszi az oktatóanyagban lévő többsoros Azure CLI-parancsok végrehajtását. Ha nem látja el értékkel a változókat, manuálisan kell majd behelyettesítenie az egyes értékeket, amikor megjelennek a példaparancsokban.

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the previous section
```

Most hozza létre az összeállítási feladatot az [az acr build-task create][az-acr-build-task-create] parancs végrehajtásával:

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --branch master \
    --git-access-token $GIT_PAT
```

Az összeállítási feladat megadja, hogy minden alkalommal, amikor kódot véglegesítenek a `--context` argumentumban megadott adattár *fő* elágazásába, az ACR Build összeállítja a tárolórendszerképet az elágazásban lévő kódból. Az `--image` argumentum a `{{.Build.ID}}` egy parametrikus értékét adja meg a rendszerkép címkéjének a verzióra vonatkozó részéhez, ezzel biztosítva, hogy az összeállított rendszerkép egy adott összeállításhoz tartozzon és egyedi címkével legyen jelölve.

A sikeres [az acr build-task create][az-acr-build-task-create] parancs kimenete az alábbihoz hasonló:

```console
$ az acr build-task create \
>     --registry $ACR_NAME \
>     --name buildhelloworld \
>     --image helloworld:{{.Build.ID}} \
>     --context https://github.com/$GIT_USER/acr-build-helloworld-node \
>     --branch master \
>     --git-access-token $GIT_PAT
{
  "additionalProperties": {},
  "alias": "buildhelloworld",
  "creationDate": "2018-05-10T19:34:48.086776+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/mycontainerregistry/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry/buildTasks/buildhelloworld",
  "location": "eastus",
  "name": "buildhelloworld",
  "platform": {
    "additionalProperties": {},
    "cpu": 1,
    "osType": "Linux"
  },
  "properties": {
    "additionalProperties": {
      "imageName": null
    },
    "baseImageDependencies": null,
    "baseImageTrigger": "Runtime",
    "branch": "master",
    "buildArguments": [],
    "contextPath": null,
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Build.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "provisioningState": "Succeeded",
    "type": "Docker"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "mycontainerregistry",
  "sourceRepository": {
    "additionalProperties": {},
    "isCommitTriggerEnabled": true,
    "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
    "sourceControlAuthProperties": null,
    "sourceControlType": "GitHub"
  },
  "status": "Enabled",
  "tags": null,
  "timeout": 3600,
  "type": "Microsoft.ContainerRegistry/registries/buildTasks"
}
```

## <a name="test-the-build-task"></a>Az összeállítási feladat tesztelése

Most már rendelkezik az összeállítást definiáló összeállítási feladattal. Az összeállítás-definíció teszteléséhez aktiváljon manuálisan egy összeállítást az [az acr build-task run][az-acr-build-task-run] parancs végrehajtásával:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

Alapértelmezés szerint az `az acr build-task run` a naplókimenetet a konzolra streameli a parancs végrehajtásakor. Itt a kimenet azt mutatja, hogy az **aa2** összeállítás üzenetsorba került és elkészült.

```console
$ az acr build-task run --registry $ACR_NAME --name buildhelloworld
Queued a build with build ID: aa2
Waiting for a build agent...
time="2018-05-10T19:37:17Z" level=info msg="Running command git clone https://x-access-token:*************@github.com/gituser/acr-build-helloworld-node /root/acr-builder/src"
Cloning into '/root/acr-builder/src'...
time="2018-05-10T19:37:17Z" level=info msg="Running command git checkout master"
Already on 'master'
Your branch is up to date with 'origin/master'.
920f16cfafa36d0bc3f397c3dd48185a03499404
time="2018-05-10T19:37:17Z" level=info msg="Running command git rev-parse --verify HEAD"
time="2018-05-10T19:37:17Z" level=info msg="Running command docker build --pull -f Dockerfile -t mycontainerregistry.azurecr.io/helloworld:aa2 ."
Sending build context to Docker daemon  209.9kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
Status: Image is up to date for node:9-alpine
 ---> 7af437a39ec2
Step 2/5 : COPY . /src
 ---> 48a7735fa94e

[...]

26b0c207c4a9: Pushed
917e7cdebc8b: Pushed
aa2: digest: sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89 size: 1367
time="2018-05-10T19:37:57Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistrtyy.azurecr.io/helloworld:aa2"
"["mycontainerregistrtyy.azurecr.io/helloworld@sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89"]"
time="2018-05-10T19:37:57Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistrtyy.azurecr.io
    repository: helloworld
    tag: aa2
    digest: sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
  git:
    git-head-revision: 920f16cfafa36d0bc3f397c3dd48185a03499404

Build complete
Build ID: aa2 was successful after 46.491407373s
```

## <a name="view-build-status"></a>Az összeállítási állapot megtekintése

Alkalmanként hasznos lehet megtekinteni a nem manuálisan aktivált, folyamatban lévő összeállítások állapotát, például a forráskód-véglegesítések által indított összeállítások hibaelhárításakor. Ebben a szakaszban egy manuális összeállítást aktivál, de letiltja az alapértelmezett működést, ami az összeállítási naplót a konzolra streameli. Ezután az `az acr build-task logs` parancs használatával monitorozhatja a folyamatban lévő összeállítást.

Először manuálisan aktivál egy összeállítást, ahogy korábban is tette, de a `--no-logs` argumentum megadásával letiltja a konzolra való naplózást:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld --no-logs
```

Ezután az `az build-task logs` paranccsal megtekintheti a folyamatban lévő összeállítás naplóját:

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

A rendszer az aktuálisan folyamatban lévő összeállítás naplóját a konzolra streameli, és az alábbi kimenethez hasonlónak kell lennie (itt csonkolva látható):

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build
Build ID: aa3

[...]

Build complete
Build ID: aa3 was successful after 1m14.26397548s
```

## <a name="trigger-a-build-with-a-commit"></a>Összeállítás kiváltása véglegesítéssel

Az összeállítási feladat manuális futtatással való tesztelését követően, aktiválja automatikusan a feladatot a forráskód módosításával.

Először ellenőrizze, hogy az [adattár][sample-repo] helyi klónját tartalmazó könyvtárban van:

```azurecli-interactive
cd acr-build-helloworld-node
```

Ezután az alábbi parancsok futtatásával hozzon létre, véglegesítsen és küldjön le egy új fájlt a GitHub-adattár adott elágazásába:

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Build"
git push origin master
```

Előfordulhat, hogy a rendszer a GitHub hitelesítő adatok megadását kéri a `git push` parancs végrehajtásakor. Adja meg GitHub-felhasználónevét, valamint a jelszóként korábban létrehozott személyes hozzáférési jogkivonatot (PAT).

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Miután leküldött egy véglegesítést az adattárba, az ACR Build által létrehozott webhook aktiválódik, és elindít egy összeállítást az Azure Container Registryben. Megjeleníti az aktuálisan futó összeállítás összeállítási naplóit az összeállítási folyamat ellenőrzéséhez és monitorozásához:

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

A kimenet az alábbihoz hasonló, és az aktuálisan (vagy legutóbb) futtatott összeállítást mutatja:

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build
Build ID: aa4

[...]

Build complete
Build ID: aa4 was successful after 39.164385024s
```

## <a name="list-builds"></a>Összeállítások listázása

Az ACR Build által a regisztrációs adatbázis számára végrehajtott összeállítások listájának megtekintéséhez futtassa az [az acr build-task list-builds][az-acr-build-task-list-builds] parancsot:

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

A parancs kimenete az alábbihoz hasonlóan néz ki. Megjelennek az ACR Build által végrehajtott összeállítások, és a „Git Commit” (Gitbeli véglegesítés) jelölés jelenik meg a legutóbbi összeállítás TRIGGER (Eseményindító) oszlopában:

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
----------  ---------------  ----------  ---------  ----------  --------------------  ----------
aa4         buildhelloworld  Linux       Succeeded  Git Commit  2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual      2018-05-10T19:10:14Z  00:00:55
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag azt mutatta be, hogy hogyan használhatók az összeállítási feladatok a tárolórendszerképek összeállításának automatikus aktiválására az Azure-ban, amikor forráskódot véglegesít egy Git-adattárban. Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre olyan összeállítási feladatokat, amelyek a tárolórendszerképek alapként szolgáló rendszerképeinek frissítésekor aktiválnak összeállításokat.

> [!div class="nextstepaction"]
> [Összeállítások automatizálása alapként szolgáló rendszerképek frissítésekor](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-build-task-list-builds]: /cli/azure/acr#az-acr-build-task-list-build

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
