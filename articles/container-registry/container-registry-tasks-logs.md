---
title: Feladatfuttatási naplók megtekintése – Feladatok
description: Az ACR-feladatok által létrehozott futtatási naplók megtekintése és kezelése.
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: f7098f470a3f8a0cdac019f4bf8eb8fe14330337
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246967"
---
# <a name="view-and-manage-task-run-logs"></a>Feladatfuttatási naplók megtekintése és kezelése

Az Azure [Container Registry feladatokban](container-registry-tasks-overview.md) futtatott minden egyes feladat naplókimenetet hoz létre, amely et megvizsgálhatja annak megállapításához, hogy a feladatlépések sikeresen futottak-e. 

Ez a cikk a feladatfuttatási naplók megtekintését és kezelését ismerteti.

## <a name="view-streamed-logs"></a>Streamelt naplók megtekintése

Ha manuálisan indít el egy feladatot, a naplókimenet közvetlenül a konzolra kerül. Ha például manuálisan indít el egy feladatot az [az acr build](/cli/azure/acr#az-acr-build), az [acr run](/cli/azure/acr#az-acr-run)vagy az [acr task run](/cli/azure/acr/task#az-acr-task-run) paranccsal, megjelenik a konzolra streamelt naplókimenet. 

A következő minta [az acr run](/cli/azure/acr#az-acr-run) parancs manuálisan indít ja el egy feladatot, amely ugyanazon rendszerleíró adatbázisból lehúzott tárolót futtat:

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

Streamelt napló:

```console
Queued a run with ID: cf4
Waiting for an agent...
2020/03/09 20:30:10 Alias support enabled for version >= 1.1.0, please see https://aka.ms/acr/tasks/task-aliases for more information.
2020/03/09 20:30:10 Creating Docker network: acb_default_network, driver: 'bridge'
2020/03/09 20:30:10 Successfully set up Docker network: acb_default_network
2020/03/09 20:30:10 Setting up Docker configuration...
2020/03/09 20:30:11 Successfully set up Docker configuration
2020/03/09 20:30:11 Logging in to registry: mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Successfully logged into mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2020/03/09 20:30:12 Launching container with name: acb_step_0
Unable to find image 'mycontainerregistry1220azurecr.io/samples/hello-world:v1' locally
v1: Pulling from samples/hello-world
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e888a
Status: Downloaded newer image for mycontainerregistry1220azurecr.io/samples/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]

2020/03/09 20:30:13 Successfully executed container: acb_step_0
2020/03/09 20:30:13 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.180081)

Run ID: cf4 was successful after 5s
```

## <a name="view-stored-logs"></a>Tárolt naplók megtekintése 

Az Azure Container Registry tárolja az összes feladat naplóinak futtatását. A tárolt futtatási naplók az Azure Portalon tekintheti meg. Vagy használja az [az acr feladatnaplók parancsot](/cli/azure/acr/task#az-acr-task-logs) a kijelölt napló megtekintéséhez. Alapértelmezés szerint a naplók 30 napig megmaradnak.

Ha egy feladat automatikusan aktiválódik, például egy forráskód frissítése, a tárolt naplók elérése az *egyetlen* módja a futtatási naplók megtekintésének. Az automatikus feladateseményindítók közé tartoznak a forráskód véglegesítése vagy lekéréses kérések, az alaplemezképek frissítései és az időzítőeseményindítók.

A portálon lévő futtatási naplók megtekintése:

1. Nyissa meg a tároló beállításjegyzékét.
1. A **Szolgáltatások területen**válassza a **Feladatok** > **futtatása**lehetőséget.
1. Válassza ki a **Futtatás idazonosítót** a futtatási állapot megtekintéséhez és a naplók futtatásához. A napló ugyanazokat az információkat tartalmazza, mint az adatfolyam-napló, ha jön létre.

![Feladatfuttatási bejelentkezési portál megtekintése](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Az Azure CLI használatával napló megtekintéséhez futtassa [az acr feladatnaplókat,](/cli/azure/acr/task#az-acr-task-logs) és adja meg a futtatási azonosítót, a feladat nevét vagy egy adott lemezképet, amelyet egy buildfeladat hoz létre. Ha meg van adva egy feladatnév, a parancs az utoljára létrehozott futtatás naplóját jeleníti meg.

A következő példa az ID cf4 azonosítóval adja ki a futtatás *naplóját:*

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>Alternatív naplótárolás

Előfordulhat, hogy a feladat futtatása naplókat egy helyi fájlrendszeren szeretné tárolni, vagy egy alternatív archiválási megoldást, például az Azure Storage-t.

Hozzon létre például egy helyi *tasklogs könyvtárat,* és irányítsa át az [az acr feladatnaplók](/cli/azure/acr/task#az-acr-task-logs) kimenetét egy helyi fájlba:

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

Helyi naplófájlokat is menthet az Azure Storage-ba. Például használja az [Azure CLI,](../storage/blobs/storage-quickstart-blobs-cli.md)az [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md), vagy más módszerek fájlok feltöltése egy tárfiókba.


## <a name="next-steps"></a>További lépések

* További információ az [Azure Container beállításjegyzék-hibáiról](container-registry-tasks-overview.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
