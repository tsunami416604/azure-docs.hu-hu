---
title: Használja az Azure Container Instancesben újraindításra vonatkozó házirendek tárolóalapú feladatok
description: Ismerje meg, hogyan használható az Azure Container Instances szolgáltatásban futtatott feladatok befejezését, mint például a buildelési, tesztelési vagy kép renderelési feladatok végrehajtásához.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 06872eefd0d500a22214109ad5055dd236b5a6ac
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59606837"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Tárolóalapú feladatok futtatása az újraindítási házirendek

Az egyszerű és az Azure Container Instances a tárolók üzembe helyezésének sebességét meggyőző platformot kínál egyszeri futtatású feladatok, mint például a buildelési, tesztelési és képrenderelés végrehajtása a tárolópéldány.

A konfigurálható újraindítási házirend megadhatja, hogy a tárolók le vannak állítva a folyamatok befejezését. Container instances szolgáltatás számlázása másodpercalapú, mert csak a használt, amíg a feladat végrehajtása a tároló fut-e számítási erőforrások díjkötelesek.

A példákban Ez a cikk a használati bemutatni az Azure CLI. Rendelkeznie kell Azure CLI 2.0.21-es vagy újabb [helyileg telepített][azure-cli-install], vagy a CLI használata a [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Tároló újraindítási szabályzata

Amikor létrehoz egy [tárolócsoport](container-instances-container-groups.md) az Azure Container Instancesben, kiválaszthat egyet a három újraindítási házirend-beállításokat.

| Újraindítási házirend   | Leírás |
| ---------------- | :---------- |
| `Always` | A tárolócsoport tárolók mindig újra lesz indítva. Ez a **alapértelmezett** beállítást alkalmazza, amikor nincs újraindítási szabályzata van megadva a tároló létrehozásakor. |
| `Never` | A tárolócsoport tárolók soha nem újra lesz indítva. A tárolók legfeljebb egyszer futtatni. |
| `OnFailure` | A tárolócsoport tárolók újra lesz indítva, csak akkor, ha a tárolóban futtatott folyamat meghiúsul (amikor nullától eltérő kilépési kódot végződik). A tárolók legalább egyszer futnak le. |

## <a name="specify-a-restart-policy"></a>Adjon meg egy újraindítási szabályzata

Újraindítási házirend adhatja meg attól függ, hogyan hoz létre a container Instances szolgáltatásban, mint például az Azure CLI, Azure PowerShell-parancsmagok használatával vagy az Azure Portalon. Az Azure CLI-n, adja meg a `--restart-policy` paraméter hívja [az tároló létrehozása][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Futtatás befejezési példához

Az újraindítási házirend működés közben látni, hozzon létre egy tárolópéldányt a Microsofttól [aci-wordcount] [ aci-wordcount-image] rendszerképet, és adja meg a `OnFailure` újraindítási házirend. Ebben a példában a tárolóban fut egy Python-szkriptet, amely, alapértelmezés szerint elemzi a Shakespeare szövege [apró település](http://shakespeare.mit.edu/hamlet/full.html), a 10 leggyakoribb szavakat ír az STDOUT és majd kilép.

A példa tároló futtassa az alábbi [az tároló létrehozása] [ az-container-create] parancsot:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Az Azure Container Instances elindítja a tárolót, és azután leáll, amikor az alkalmazás (vagy szkript, ebben az esetben) kilép. Amikor az Azure Container Instances leáll egy tároló, amelynek újraindítás házirend `Never` vagy `OnFailure`, a tároló állapota **kilépett**. Ellenőrizheti a tárolót a a [az container show] [ az-container-show] parancsot:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Példa a kimenetre:

```bash
"Terminated"
```

Miután a példa tároló állapota: *kilépett*, láthatja a feladat kimenetét a tároló naplóinak megtekintésével. Futtassa a [az tárolónaplók] [ az-container-logs] paranccsal tekintheti meg a szkript kimenetének:

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

Ez a példa bemutatja a kimenete a STDOUT küldött a parancsfájlt. A tárolóalapú feladatok, azonban előfordulhat, hogy inkább írni a kimeneti újabb lekérés-tároláshoz. Például, hogy egy [Azure-fájlmegosztás](container-instances-mounting-azure-files-volume.md).

## <a name="next-steps"></a>További lépések

A feladat-alapú forgatókönyvek, például a kötegelt feldolgozási egy nagy méretű adathalmazt a több tárolókkal, igénybe veheti az egyéni [környezeti változók](container-instances-environment-variables.md) vagy [parancssorok](container-instances-start-command.md) futásidőben.

Hogyan kell a tárolókat, amelyek befejezését kimenetének megőrzése a részletekért lásd: [csatlakoztatása egy Azure-fájlmegosztás az Azure Container Instances](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
