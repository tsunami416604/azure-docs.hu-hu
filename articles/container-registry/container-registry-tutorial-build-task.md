---
title: Oktatóanyag – rendszerkép létrehozása kód véglegesítve
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat egy Azure Container Registry feladatot úgy, hogy automatikusan aktiválja a tároló rendszerképét a felhőben, amikor a forráskódot egy git-tárházba véglegesíti.
ms.topic: tutorial
ms.date: 05/04/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: 2f70b829e2202c3d28adcfbbb07338923c43e8a8
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402847"
---
# <a name="tutorial-automate-container-image-builds-in-the-cloud-when-you-commit-source-code"></a>Oktatóanyag: tároló-rendszerkép automatizálása a felhőben a forráskód elküldésekor

Egy [gyors feladaton](container-registry-tutorial-quick-task.md)kívül az ACR-feladatok támogatják az automatizált Docker-tárolók rendszerképét a felhőben, amikor a forráskódot egy git-tárházba véglegesíti. Az ACR-feladatokhoz támogatott git-környezetek a következők lehetnek: nyilvános vagy privát GitHub vagy Azure Repos.

> [!NOTE]
> Az ACR-feladatok jelenleg nem támogatják a commit vagy a pull kérelem eseményindítóit a GitHub Enterprise reposban.

Ebben az oktatóanyagban az ACR-feladat létrehozza és leküldi a Docker megadott egyetlen tároló-rendszerképet, amikor egy git-tárházba véglegesíti a forráskódot. Ha olyan [többlépéses feladatot](container-registry-tasks-multi-step.md) szeretne létrehozni, amely egy YAML-fájlt használ a kód végrehajtásához szükséges több tároló létrehozásához, leküldéséhez és opcionális teszteléséhez, tekintse meg [az oktatóanyag: többlépéses tároló-munkafolyamat futtatása a felhőben a forráskód](container-registry-tutorial-multistep-task.md)beléptetése során című témakört. Az ACR-feladatok áttekintését lásd: az [operációs rendszer és a keretrendszer javításának automatizálása az ACR-feladatokkal](container-registry-tasks-overview.md)

Ebben az oktatóanyagban:

> [!div class="checklist"]
> * Tevékenység létrehozása
> * A feladat tesztelése
> * Tevékenységek állapotának megtekintése
> * A feladat aktiválása kódvéglegesítéssel

Ez az oktatóanyag feltételezi, hogy elvégezte az [előző oktatóanyag](container-registry-tutorial-quick-task.md) lépéseit. Ha még nem tette meg, hajtsa végre az előző oktatóanyag [Előfeltételek](container-registry-tutorial-quick-task.md#prerequisites) szakaszának lépéseit, mielőtt továbblépne.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha helyileg szeretné használni az Azure CLI-t, az Azure CLI **2.0.46** vagy újabb verzióját kell telepítenie, és be kell jelentkeznie az [az login][az-login]paranccsal. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepítenie vagy frissítenie kell a CLI-t, tekintse meg az [Azure CLI telepítését][azure-cli]ismertető témakört.

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-the-build-task"></a>Az összeállítási feladat létrehozása

Most, miután végrehajtotta az ahhoz szükséges lépéseket, hogy az ACR Tasks olvashassa a véglegesítési állapotokat, és webhookokat hozhasson létre egy adattárban, létrehozhat egy feladatot, amely egy tárolórendszerkép összeállítását váltja ki az adattárban való véglegesítés esetén.

Először lássa el ezeket a rendszerhéj-környezeti változókat a környezetnek megfelelő értékekkel. Ez a lépés nem feltétlenül szükséges, de némileg könnyebbé teszi az oktatóanyagban lévő többsoros Azure CLI-parancsok végrehajtását. Ha nem tölti fel ezeket a környezeti változókat, manuálisan kell lecserélnie az egyes értékeket, bárhol is megjelenjenek a példában szereplő parancsokban.

[![Beágyazás elindítása](https://shell.azure.com/images/launchcloudshell.png "Az Azure Cloud Shell indítása")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Most hozza létre a feladatot a következő az [ACR Task Create][az-acr-task-create] parancs végrehajtásával:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> Ha az előzetes verzióban korábban létrehozott feladatokat az `az acr build-task` paranccsal, ezeket a feladatokat az az [ACR Task][az-acr-task] paranccsal újra létre kell hozni.

A feladat megadja, hogy minden alkalommal, amikor kódot véglegesítenek a *argumentumban megadott adattár*fő`--context` elágazásába, az ACR Tasks összeállítja a tárolórendszerképet az elágazásban lévő kódból. A rendszer a tárház gyökerében `--file` által megadott Docker használja a rendszerkép létrehozásához. Az `--image` argumentum a `{{.Run.ID}}` egy parametrikus értékét adja meg a rendszerkép címkéjének a verzióra vonatkozó részéhez, ezzel biztosítva, hogy az összeállított rendszerkép egy adott összeállításhoz tartozzon és egyedi címkével legyen jelölve.

A sikeres az [ACR Task Create][az-acr-task-create] parancs kimenete az alábbihoz hasonló:

```output
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2018-09-14T22:42:32.972298+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskhelloworld",
  "location": "westcentralus",
  "name": "taskhelloworld",
  "platform": {
    "architecture": "amd64",
    "os": "Linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myregistry",
  "status": "Enabled",
  "step": {
    "arguments": [],
    "baseImageDependencies": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node",
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Run.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "type": "Docker"
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "master",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
          "sourceControlAuthProperties": null,
          "sourceControlType": "GitHub"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-build-task"></a>Az összeállítási feladat tesztelése

Most már rendelkezik az összeállítást definiáló feladattal. A létrehozási folyamat teszteléséhez indítson el egy összeállítást manuálisan az az [ACR Task Run][az-acr-task-run] parancs végrehajtásával:

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Alapértelmezés szerint az `az acr task run` a naplókimenetet a konzolra streameli a parancs végrehajtásakor.

```output
2018/09/17 22:51:00 Using acb_vol_9ee1f28c-4fd4-43c8-a651-f0ed027bbf0e as the home volume
2018/09/17 22:51:00 Setting up Docker configuration...
2018/09/17 22:51:02 Successfully set up Docker configuration
2018/09/17 22:51:02 Logging in to registry: myregistry.azurecr.io
2018/09/17 22:51:03 Successfully logged in
2018/09/17 22:51:03 Executing step: build
2018/09/17 22:51:03 Obtaining source code and scanning for dependencies...
2018/09/17 22:51:05 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  23.04kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
Status: Image is up to date for node:9-alpine
 ---> a56170f59699
Step 2/5 : COPY . /src
 ---> 5f574fcf5816
Step 3/5 : RUN cd /src && npm install
 ---> Running in b1bca3b5f4fc
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.078s
Removing intermediate container b1bca3b5f4fc
 ---> 44457db20dac
Step 4/5 : EXPOSE 80
 ---> Running in 9e6f63ec612f
Removing intermediate container 9e6f63ec612f
 ---> 74c3e8ea0d98
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 7382eea2a56a
Removing intermediate container 7382eea2a56a
 ---> e33cd684027b
Successfully built e33cd684027b
Successfully tagged myregistry.azurecr.io/helloworld:da2
2018/09/17 22:51:11 Executing step: push
2018/09/17 22:51:11 Pushing image: myregistry.azurecr.io/helloworld:da2, attempt 1
The push refers to repository [myregistry.azurecr.io/helloworld]
4a853682c993: Preparing
[...]
4a853682c993: Pushed
[...]
da2: digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419 size: 1366
2018/09/17 22:51:21 Successfully pushed image: myregistry.azurecr.io/helloworld:da2
2018/09/17 22:51:21 Step id: build marked as successful (elapsed time in seconds: 7.198937)
2018/09/17 22:51:21 Populating digests for step id: build...
2018/09/17 22:51:22 Successfully populated digests for step id: build
2018/09/17 22:51:22 Step id: push marked as successful (elapsed time in seconds: 10.180456)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloworld
    tag: da2
    digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 68cdf2a37cdae0873b8e2f1c4d80ca60541029bf


Run ID: da2 was successful after 27s
```

## <a name="trigger-a-build-with-a-commit"></a>Összeállítás kiváltása véglegesítéssel

A feladat manuális futtatással való tesztelését követően aktiválja automatikusan a feladatot a forráskód módosításával.

Először is győződjön meg arról, hogy az [adattár][sample-repo]helyi klónját tartalmazó könyvtárban van:

```console
cd acr-build-helloworld-node
```

Ezután az alábbi parancsok futtatásával hozzon létre, véglegesítsen és küldjön le egy új fájlt a GitHub-adattár adott elágazásába:

```console
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

Előfordulhat, hogy a rendszer a GitHub hitelesítő adatok megadását kéri a `git push` parancs végrehajtásakor. Adja meg GitHub-felhasználónevét, valamint a jelszóként korábban létrehozott személyes hozzáférési jogkivonatot (PAT).

```azurecli-interactive
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Miután leküldött egy véglegesítést az adattárba, az ACR Tasks által létrehozott webhook aktiválódik, és elindít egy összeállítást az Azure Container Registryben. Megjeleníti az aktuálisan futó feladat naplóit az összeállítási folyamat ellenőrzéséhez és monitorozásához:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

A kimenet az alábbihoz hasonló, és az aktuálisan (vagy legutóbb) futtatott feladatot mutatja:

```output
Showing logs of the last created run.
Run ID: da4

[...]

Run ID: da4 was successful after 38s
```

## <a name="list-builds"></a>Összeállítások listázása

Ha szeretné megtekinteni a feladat által futtatott ACR-feladatokat a beállításjegyzékben, futtassa az az [ACR Task List-Run][az-acr-task-list-runs] parancsot:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

A parancs kimenete az alábbihoz hasonlóan néz ki. Megjelennek az ACR Tasks által végrehajtott futtatások, és a „Git Commit” (Gitbeli véglegesítés) jelölés jelenik meg a legutóbbi feladat TRIGGER oszlopában:

```output
RUN ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag azt mutatta be, hogyan használhatók a feladatok a tárolórendszerképek összeállításának automatikus aktiválására az Azure-ban, amikor forráskódot véglegesít egy Git-adattárban. Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre olyan feladatokat, amelyek a tárolórendszerképek alapként szolgáló rendszerképeinek frissítésekor aktiválnak összeállításokat.

> [!div class="nextstepaction"]
> [Összeállítások automatizálása alapként szolgáló rendszerképek frissítésekor](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-login]: /cli/azure/reference-index#az-login



