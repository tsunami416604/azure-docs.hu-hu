---
title: Az egyszer futó feladatok házirendjének újraindítása
description: Megtudhatja, hogyan használhatja az Azure Container Instances feladatok végrehajtása, amelyek a befejezésig futnak, például a build, teszt, vagy képrenderelési feladatok.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 8ef4ef228038242f53abc8041470f7f596ab1157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131495"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Tárolóba rendelt feladatok futtatása újraindítási házirendekkel

A tárolók Azure Container Instances szolgáltatásban való üzembe helyezésének egyszerűsége és gyorsasága kényelmes platformot biztosít, amellyel egy tárolópéldányban hajthat végre olyan egyszer futó feladatokat, mint a létrehozás, a tesztelés és a képrenderelés.

A konfigurálható újraindítási szabályzat segítségével megadhatja, hogy a tárolók leálljanak, amikor a folyamataik befejeződtek. Mivel a Container Instances számlázása másodpercalapú, csak azokért a számítási erőforrásokért kell díjat fizetnie, amelyek használatban voltak, amikor a feladatot végrehajtó tároló futott.

Az ebben a cikkben bemutatott példák az Azure CLI használatával. Az Azure CLI 2.0.21-es vagy újabb [verziójával helyileg][azure-cli-install]kell telepítve lennie, vagy az Azure Cloud Shellben kell használnia a CLI-t. [Azure Cloud Shell](../cloud-shell/overview.md)

## <a name="container-restart-policy"></a>Tároló újraindítási házirendje

Amikor létrehoz egy [tárolócsoportot](container-instances-container-groups.md) az Azure Container Instances, megadhatja a három újraindítási szabályzat beállításait.

| Újraindítási szabályzat   | Leírás |
| ---------------- | :---------- |
| `Always` | A tárolócsoportban lévő tárolókat a rendszer mindig újraindítja. Ez az **alapértelmezés szerint** érvényben lévő beállítás, ha nem ad meg újraindítási szabályzatot a tároló létrehozásakor. |
| `Never` | A tárolócsoportban lévő tárolókat a rendszer sosem indítja újra. A tárolók legfeljebb egyszer futnak. |
| `OnFailure` | A tárolócsoportban lévő tárolókat a rendszer csak akkor indítja újra, ha a tárolóban végrehajtott folyamat meghiúsult (azaz nullától eltérő kilépési kóddal zárul). A tárolók legalább egyszer futnak. |

## <a name="specify-a-restart-policy"></a>Újraindítási házirend megadása

Az újraindítási szabályzat megadása attól függ, hogyan hozza létre a tárolópéldányokat, például az Azure CLI, az Azure PowerShell-parancsmagokkal vagy az Azure Portalon. Az Azure CLI-ben `--restart-policy` adja meg a paramétert az [az container create hívásakor.][az-container-create]

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Futtatás a befejezésig példa

Az újraindítási szabályzat működés közbeni megtekintéséhez hozzon létre egy tárolópéldányt `OnFailure` a Microsoft [aci-wordcount][aci-wordcount-image] lemezképből, és adja meg az újraindítási házirendet. Ebben a példában a tároló egy Python-parancsfájlt futtat, amely alapértelmezés szerint shakespeare hamletjének szövegét [elemzi,](http://shakespeare.mit.edu/hamlet/full.html)a 10 leggyakoribb szót írja az STDOUT-ba, majd kilép.

Futtassa a példatárolót a következő [az container create][az-container-create] paranccsal:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Az Azure Container Instances elindítja a tárolót, majd leállítja, amikor az alkalmazás (vagy ebben az esetben a szkript) kilép. Amikor az Azure Container Instances leállít `OnFailure`egy olyan tárolót, amelynek újraindítási szabályzata vagy `Never` a tároló állapota **Leállítva.** A tároló állapotát az az [container show][az-container-show] paranccsal ellenőrizheti:

```azurecli-interactive
az container show \
    --resource-group myResourceGroup \
    --name mycontainer \
    --query containers[0].instanceView.currentState.state
```

Példa a kimenetre:

```bash
"Terminated"
```

Amint a példatároló *Terminated* (Leállított) állapotra vált, a feladat kimenetét a tárolónaplókban ellenőrizheti. Futtassa az [az container logs parancsot][az-container-logs] a parancsfájl kimenetének megtekintéséhez:

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

Ez a példa az STDOUT-nak küldött parancsfájl kimenetét mutatja be. A tárolóba ütemezett feladatok azonban előfordulhat, hogy ehelyett a kimenetet az állandó tárolóba írják későbbi lekérésre. Például egy [Azure-fájlmegosztáshoz.](container-instances-mounting-azure-files-volume.md)

## <a name="next-steps"></a>További lépések

A feladatalapú forgatókönyvek, például a több tárolóval rendelkező nagy adatkészlet kötegelt feldolgozása, kihasználhatják az egyéni [környezeti változók](container-instances-environment-variables.md) vagy [parancssorok](container-instances-start-command.md) előnyeit futásidőben.

A befejezésig futó tárolók kimenetének megőrzéséről az [Azure Container Instances használatával című témakörben](container-instances-mounting-azure-files-volume.md)talál további részleteket.

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
