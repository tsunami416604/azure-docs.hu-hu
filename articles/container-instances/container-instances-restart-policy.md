---
title: Újraindítási szabályzatok használata a Azure Container Instances tárolóban lévő feladatokkal
description: Megtudhatja, hogyan használhatja a Azure Container Instances a befejezésre futó feladatok végrehajtásához, például a létrehozási, tesztelési vagy képrenderelési feladatokban.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 4fe5d9a20249a17030e0ccfa34f6a4f183be0d82
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325681"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Tárolózott feladatok futtatása újraindítási szabályzatokkal

A tárolók üzembe helyezésének egyszerűsége és gyorsasága a Azure Container Instances vonzó platformot biztosít a futtatott egyszeri feladatok végrehajtásához, például a Build, a test és a kép megjelenítéséhez egy tároló-példányban.

Konfigurálható újraindítási szabályzattal megadhatja, hogy a tárolók le legyenek állítva a folyamatok befejezésekor. Mivel a tároló-példányok számlázása a második, csak a felszámított számítási erőforrásokért kell fizetnie, miközben a feladatot végrehajtó tároló fut.

A cikkben bemutatott példák az Azure CLI-t használják. Az Azure CLI verziójának 2.0.21 [vagy újabb][azure-cli-install]verziójára van szükség, vagy a CLI-t kell használnia a [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Tároló újraindítási szabályzata

Amikor Azure Container Instancesban hoz létre egy [tároló csoportot](container-instances-container-groups.md) , megadhatja a három újraindítási házirend-beállítás egyikét.

| Újraindítási szabályzat   | Leírás |
| ---------------- | :---------- |
| `Always` | A tároló csoportban lévő tárolók mindig újraindulnak. Ez az **alapértelmezett** beállítás, ha nem ad meg újraindítási szabályzatot a tároló létrehozásakor. |
| `Never` | A tároló csoportban lévő tárolók soha nem indulnak újra. A tárolók legfeljebb egyszer futnak. |
| `OnFailure` | A tároló csoportban lévő tárolók csak akkor lesznek újraindulva, ha a tárolóban végrehajtott folyamat meghiúsul (ha nem nulla kilépési kóddal végződik). A tárolók legalább egyszer futnak. |

## <a name="specify-a-restart-policy"></a>Újraindítási szabályzat meghatározása

Az újraindítási szabályzatok megadásának módja attól függ, hogyan hozza létre a tároló példányait, például az Azure CLI-vel, Azure PowerShell-parancsmagokkal vagy a Azure Portal. Az Azure CLI-ben határozza meg `--restart-policy` a paramétert az [az Container Create][az-container-create]parancs meghívásakor.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Futtatás a befejezésig példa

Ha szeretné megtekinteni az újraindítási szabályzatot, hozzon létre egy Container-példányt a Microsoft [ACI-WordCount][aci-wordcount-image] rendszerképből, és határozza meg az `OnFailure` újraindítási szabályzatot. Ez a példában szereplő tároló egy Python-szkriptet futtat, amely alapértelmezés szerint a Shakespeare [Hamlet](http://shakespeare.mit.edu/hamlet/full.html)szövegét elemzi, a 10 leggyakoribb szót írja a stdout értékre, majd kilép.

Futtassa a példában szereplő tárolót a következő az [Container Create][az-container-create] paranccsal:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Container Instances elindítja a tárolót, majd leállítja, amikor az alkalmazás (vagy parancsfájl, ebben az esetben) kilép. Amikor Azure Container instances leállít egy olyan tárolót, `Never` amelynek `OnFailure`újraindítási szabályzata vagy, a tároló állapota leállítva értékre van **állítva.** A tároló állapotát az [az Container show][az-container-show] paranccsal tekintheti meg:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Példa a kimenetre:

```bash
"Terminated"
```

Miután a példa tároló állapota megszakadt, megtekintheti a feladat kimenetét a tároló naplófájljainak megtekintésével. Futtassa az az [Container logs][az-container-logs] parancsot a szkript kimenetének megtekintéséhez:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Kimenet:

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

Ez a példa azt a kimenetet mutatja, amelyet a parancsfájl az STDOUT-nak küld. A tárolóban lévő feladatok azonban Ehelyett a kimenetet állandó tárterületre írhatja a későbbi lekéréshez. Egy [Azure-fájlmegosztás](container-instances-mounting-azure-files-volume.md)esetében például.

## <a name="next-steps"></a>További lépések

A feladat-alapú forgatókönyvek, például a Batch több tárolóval rendelkező nagyméretű adathalmazok feldolgozásával kihasználhatják az egyéni [környezeti változókat](container-instances-environment-variables.md) vagy a [parancssori vonalakat](container-instances-start-command.md) futásidőben.

Az [Azure-fájlmegosztás Azure Container Instancessal](container-instances-mounting-azure-files-volume.md)való csatlakoztatásával kapcsolatos részletekért tekintse meg a (z) című témakört.

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
