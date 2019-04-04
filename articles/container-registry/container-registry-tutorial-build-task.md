---
title: Oktatóanyag – tároló rendszerképek létrehozásának – az Azure Container Registry feladatok automatizálása
description: Ebben az oktatóanyagban elsajátíthatja, hogyan konfigurálhatja egy Azure Container beállításjegyzék feladattal automatikusan indítható a felhőalapú tároló-rendszerképek létrehozásának, ha véglegesítése a forráskód Git-tárházba.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 5aa637938433eb1f906f0a4d81038cec0d6c6dcc
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893010"
---
# <a name="tutorial-automate-container-image-builds-in-the-cloud-when-you-commit-source-code"></a>Oktatóanyag: Felhőalapú tároló-rendszerképek létrehozásának automatizálása, ha forráskódot véglegesítése

A [gyors feladat](container-registry-tutorial-quick-task.md) mellett az ACR Tasks a Docker tárolórendszerképek automatikus összeállítását is támogatja az *összeállítási feladattal*. Ez az oktatóanyag azt mutatja be, hogyan lehet az Azure CLI használatával olyan feladatot létrehozni, amely automatikusan aktiválja a rendszerképek összeállítását a felhőben, amikor forráskódot véglegesít egy Git-adattárban.

Az oktatóanyag-sorozat második része a következő lépésekből áll:

> [!div class="checklist"]
> * Tevékenység létrehozása
> * A feladat tesztelése
> * Tevékenységek állapotának megtekintése
> * A feladat aktiválása kódvéglegesítéssel

Ez az oktatóanyag feltételezi, hogy elvégezte az [előző oktatóanyag](container-registry-tutorial-quick-task.md) lépéseit. Ha még nem tette meg, hajtsa végre az előző oktatóanyag [Előfeltételek](container-registry-tutorial-quick-task.md#prerequisites) szakaszának lépéseit, mielőtt továbblépne.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha az Azure CLI-t helyben szeretné használni, az Azure CLI **2.0.46-os** vagy újabb verzióját kell telepítenie, és be kell jelentkeznie az [az login][az-login] paranccsal. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretné a parancssori felületet, olvassa el [az Azure CLI telepítését][azure-cli] ismertető témakört.

## <a name="prerequisites"></a>Előfeltételek

### <a name="get-sample-code"></a>Mintakód letöltése

Ez az oktatóanyag feltételezi, hogy elvégezte az [előző oktatóanyag](container-registry-tutorial-quick-task.md) lépéseit, valamint elágaztatta és klónozta a mintaadattárat. Ha még nem tette meg, hajtsa végre az előző oktatóanyag [Előfeltételek](container-registry-tutorial-quick-task.md#prerequisites) szakaszának lépéseit, mielőtt továbblépne.

### <a name="container-registry"></a>Tárolóregisztrációs adatbázis

Az oktatóanyag elvégzéséhez rendelkeznie kell egy Azure-beli tárolóregisztrációs adatbázissal az Azure-előfizetésében. Ha a beállításjegyzék van szüksége, tekintse meg a [előző oktatóanyagban](container-registry-tutorial-quick-task.md), vagy [a rövid útmutató: Hozzon létre egy tároló-beállításjegyzéket az Azure CLI-vel](container-registry-get-started-azure-cli.md).

## <a name="overview-of-acr-tasks"></a>Az ACR Tasks áttekintése

A feladat egy automatizált összeállítás tulajdonságait határozza meg, beleértve a tárolórendszerkép forráskódjának helyét és az összeállítást kiváltó eseményt. Amikor a feladatban definiált események egyike (például egy véglegesítés egy Git-adattárban) bekövetkezik, az ACR Tasks elindít egy tárolórendszerkép-összeállítást a felhőben. Alapértelmezés szerint a sikeresen összeállított rendszerképet ezután leküldi a feladatban meghatározott Azure-beli tárolóregisztrációs adatbázisba.

Az ACR Tasks jelenleg a következő triggerek használatát támogatja:

* Véglegesítés egy Git-adattárba
* Az alapként szolgáló rendszerkép frissítése

Ebben az oktatóanyagban az ACR-feladat állít össze, és leküldéses értesítések egy-egy docker-fájlban megadott egyetlen tároló rendszerképét. ACR-feladatokat is futtathat [több lépésből álló feladatokat](container-registry-tasks-multi-step.md), YAML-fájl használatával adja meg a lépés, leküldéses, és választhatóan tesztelheti a több tároló.

## <a name="create-a-build-task"></a>Összeállítási feladat létrehozása

Ebben a szakaszban először egy személyes hozzáférési jogkivonatot (PAT) hoz létre a GitHubban az ACR Tasks szolgáltatással való használathoz. Ezután létrehoz egy feladatot, amely aktivál egy összeállítást, ha valaki kódot véglegesít az adattár adott elágazásában.

### <a name="create-a-github-personal-access-token"></a>Személyes hozzáférési jogkivonat létrehozása a GitHubban

Ahhoz, hogy a Git-adattárban való véglegesítéskor kiválthasson egy összeállítást, az ACR Tasksnak egy személyes hozzáférési jogkivonatra (PAT) van szüksége az adattár eléréséhez. Az alábbi lépéseket követve hozhat végre személyes hozzáférési jogkivonatot a GitHubban:

1. Lépjen a GitHub PAT-létrehozási oldalára a https://github.com/settings/tokens/new címen.
1. Adja meg a jogkivonat rövid **leírását**, például „ACR Tasks bemutatója”.
1. A **repo** (adattár) területen jelölje be a **repo:status** és a **public_repo** lehetőséget.

   ![A GitHub személyes hozzáférési jogkivonatok létrehozására szolgáló oldalának képernyőképe][build-task-01-new-token]

1. Kattintson a **Generate token** (Jogkivonat létrehozása) gombra (a rendszer kérheti a jelszó megadását).
1. Másolja és mentse a létrehozott jogkivonatot egy **biztonságos helyre** (ezt a jogkivonatot egy feladat definiálására fogja használni a következő szakaszban).

   ![A létrehozott személyes hozzáférési jogkivonat képernyőképe a GitHubban][build-task-02-generated-token]

### <a name="create-the-build-task"></a>Az összeállítási feladat létrehozása

Most, miután végrehajtotta az ahhoz szükséges lépéseket, hogy az ACR Tasks olvashassa a véglegesítési állapotokat, és webhookokat hozhasson létre egy adattárban, létrehozhat egy feladatot, amely egy tárolórendszerkép összeállítását váltja ki az adattárban való véglegesítés esetén.

Először lássa el ezeket a rendszerhéj-környezeti változókat a környezetnek megfelelő értékekkel. Ez a lépés nem feltétlenül szükséges, de némileg könnyebbé teszi az oktatóanyagban lévő többsoros Azure CLI-parancsok végrehajtását. Ha nem látja el értékkel ezeket a környezeti változókat, manuálisan kell majd behelyettesítenie az egyes értékeket, amikor megjelennek a példaparancsokban.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Most hozza létre a feladatot az [az acr task create][az-acr-task-create] parancs végrehajtásával:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --branch master \
    --file Dockerfile \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> Ha korábban már létrehozott feladatokat az előzetes verzióban az `az acr build-task` paranccsal, azokat a feladatokat újra létre kell hoznia az [az acr task][az-acr-task] paranccsal.

A feladat megadja, hogy minden alkalommal, amikor kódot véglegesítenek a `--context` argumentumban megadott adattár *fő* elágazásába, az ACR Tasks összeállítja a tárolórendszerképet az elágazásban lévő kódból. A docker-fájlban megadott `--file` a tárházból legfelső szintű a rendszerkép létrehozásához használt. Az `--image` argumentum a `{{.Run.ID}}` egy parametrikus értékét adja meg a rendszerkép címkéjének a verzióra vonatkozó részéhez, ezzel biztosítva, hogy az összeállított rendszerkép egy adott összeállításhoz tartozzon és egyedi címkével legyen jelölve.

A sikeres [az acr task create][az-acr-task-create] parancs kimenete az alábbihoz hasonló:

```console
$ az acr task create \
>     --registry $ACR_NAME \
>     --name taskhelloworld \
>     --image helloworld:{{.Run.ID}} \
>     --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
>     --branch master \
>     --file Dockerfile \
>     --git-access-token $GIT_PAT
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

Most már rendelkezik az összeállítást definiáló feladattal. Az összeállítási folyamat teszteléséhez aktiváljon manuálisan egy összeállítást az [az acr task run][az-acr-task-run] parancs végrehajtásával:

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Alapértelmezés szerint az `az acr task run` a naplókimenetet a konzolra streameli a parancs végrehajtásakor.

```console
$ az acr task run --registry $ACR_NAME --name taskhelloworld

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

Először ellenőrizze, hogy az [adattár][sample-repo] helyi klónját tartalmazó könyvtárban van:

```azurecli-interactive
cd acr-build-helloworld-node
```

Ezután az alábbi parancsok futtatásával hozzon létre, véglegesítsen és küldjön le egy új fájlt a GitHub-adattár adott elágazásába:

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

Előfordulhat, hogy a rendszer a GitHub hitelesítő adatok megadását kéri a `git push` parancs végrehajtásakor. Adja meg GitHub-felhasználónevét, valamint a jelszóként korábban létrehozott személyes hozzáférési jogkivonatot (PAT).

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Miután leküldött egy véglegesítést az adattárba, az ACR Tasks által létrehozott webhook aktiválódik, és elindít egy összeállítást az Azure Container Registryben. Megjeleníti az aktuálisan futó feladat naplóit az összeállítási folyamat ellenőrzéséhez és monitorozásához:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

A kimenet az alábbihoz hasonló, és az aktuálisan (vagy legutóbb) futtatott feladatot mutatja:

```console
$ az acr task logs --registry $ACR_NAME
Showing logs of the last created run.
Run ID: da4

[...]

Run ID: da4 was successful after 38s
```

## <a name="list-builds"></a>Összeállítások listázása

Az ACR Tasks által a regisztrációs adatbázis számára elkészített feladatfuttatások listájának megtekintéséhez futtassa az [az acr task list-runs][az-acr-task-list-runs] parancsot:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

A parancs kimenete az alábbihoz hasonlóan néz ki. Megjelennek az ACR Tasks által végrehajtott futtatások, és a „Git Commit” (Gitbeli véglegesítés) jelölés jelenik meg a legutóbbi feladat TRIGGER oszlopában:

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag azt mutatta be, hogyan használhatók a feladatok a tárolórendszerképek összeállításának automatikus aktiválására az Azure-ban, amikor forráskódot véglegesít egy Git-adattárban. Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre olyan feladatokat, amelyek a tárolórendszerképek alapként szolgáló rendszerképeinek frissítésekor aktiválnak összeállításokat.

> [!div class="nextstepaction"]
> [A rendszerkép alapszintű frissítésének buildek automatizálása](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr
[az-acr-task-create]: /cli/azure/acr
[az-acr-task-run]: /cli/azure/acr
[az-acr-task-list-runs]: /cli/azure/acr
[az-login]: /cli/azure/reference-index#az-login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
