---
title: "Azure-tároló példányát indexelése feladatok futtatása"
description: "Ismerje meg, hogyan használható az Azure tároló példányok futtatott feladatok befejezését, többek között a build, a vizsgálat vagy a kép megjelenítési feladatok végrehajtásához."
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 11/16/2017
ms.author: marsma
ms.openlocfilehash: a922525970eac9af6657e58daae971912183b369
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="run-a-containerized-task-in-azure-container-instances"></a>Azure-tároló példányok indexelése feladat futtatása

A könnyű és sebességét, a tárolókat az Azure-tároló példányok telepítése kényszerítő platformot kínál a végrehajtás alatt egyszer futó feladatokat, mint a build, a vizsgálati és a kép megjelenítési egy tároló-példányt.

Konfigurálható újraindítási házirend megadhatja, hogy ha a folyamat befejeződését, a tárolók vannak-e állítva. Tároló példányok számlázása a második, mert meg van szó, csak a a számítási erőforrásokat, akkor használják, amikor a feladat végrehajtása a tárolóban fut-e.

A be ez a cikk használja az Azure parancssori felület. Rendelkeznie kell Azure CLI 2.0.21 verzió vagy újabb [helyileg telepített][azure-cli-install], vagy a CLI használata a [Azure Cloud rendszerhéj](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Tároló újraindítási házirend

Amikor Azure-tároló példányát létrehozni egy tárolót, megadhatja, három újraindítás házirend-beállítások egyikét.

| Indítsa újra a házirendet   | Leírás |
| ---------------- | :---------- |
| `Always` | A tárolók a tárolócsoport mindig újraindul. Ez a **alapértelmezett** beállítást alkalmazza, nincs újraindítási házirend megadása a tároló létrehozásakor. |
| `Never` | A tárolók a tárolócsoport soha nem újraindul. A tárolók legfeljebb egyszer futnak le. |
| `OnFailure` | A tárolók a tároló csoport csak akkor, ha a tárolóban végrehajtása sikertelen (Ha a rendszer megszakítja, egy nem nulla kilépési kód) újraindul. A tárolók legalább egyszer futnak le. |

## <a name="specify-a-restart-policy"></a>Adja meg az újraindítási házirend

Hogyan határozza meg az újraindítási házirend attól függ, hogyan hoz létre a tároló példányok, például az Azure CLI Azure PowerShell-parancsmagjaival, vagy az Azure portálon. Az Azure parancssori felület, adja meg a `--restart-policy` paraméter hívásakor [az tároló létrehozása][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Futtassa a befejezési – példa

A művelet az újraindítási házirend megtekintéséhez a tároló példány létrehozása a [microsoft/aci-wordcount] [ aci-wordcount-image] lemezképet, és adja meg a `OnFailure` indítsa újra a házirendet. Ez a példa tároló fut egy Python-parancsfájl kiszolgálószoftverek, alapértelmezés szerint Shakespeare tartozó szövegét [apró település](http://shakespeare.mit.edu/hamlet/full.html), a 10 leggyakoribb szavak írja az STDOUT és majd kilép.

A példa tároló futtassa a következő [az tároló létrehozása] [ az-container-create] parancs:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure tároló példányok a tároló kezdődik, és azután leáll, amikor az alkalmazás (vagy parancsfájl, ebben az esetben) kilép. Ha Azure-tároló példányok leállítja egy tároló, amelynek újraindítás házirend `Never` vagy `OnFailure`, a tároló állapota **kilépett**. Egy tároló állapotát a ellenőrizheti a [az tároló megjelenítése] [ az-container-show] parancs:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Példa a kimenetre:

```bash
"Terminated"
```

Miután a példa tároló állapota: *kilépett*, láthatja a feladat kimenete a tároló naplók megtekintésével. Futtassa a [az tároló naplók] [ az-container-logs] megjelenítése a parancs kimenetének:

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

Ez a példa bemutatja a STDOUT küldött a parancsfájl kimenete. A tárolóalapú feladatokat, azonban előfordulhat, hogy inkább írni a kimeneti állandó tároló a későbbi beolvasásához. Például, hogy egy [Azure fájlmegosztás](container-instances-mounting-azure-files-volume.md).

## <a name="configure-containers-at-runtime"></a>Futásidőben tárolók konfigurálása

Amikor létrehoz egy tároló-példányt, beállíthatja a **környezeti változók**, illetve adjon meg egy egyéni **parancssori** a tároló indításakor végrehajtandó. Ezek a beállítások a kötegelt feladatok segítségével készítse elő a konfigurációs feladat minden egyes tárolóban.

## <a name="environment-variables"></a>Környezeti változók

Környezeti változók értékét a tárolóban, az alkalmazás vagy a tároló által futtatott parancsfájl dinamikus beállításainak megadásához. Ez hasonlít a `--env` parancssori argumentumának `docker run`.

A parancsfájl például tárolóban működése módosítható például a tároló példány létrehozásakor a következő környezeti változók megadásával:

*NumWords*: STDOUT küldött szót.

*A MinLength*: ahhoz, hogy a megszámlálandó szót karakterek minimális száma. Minél nagyobb karakterkészletszámot figyelmen kívül hagyja a gyakori szavakat, például "a" és "a."

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Megadásával `NumWords=5` és `MinLength=8` a tároló környezeti változók, a tároló naplók megjelenjen-e eltérő kimenetet. A tároló állapota Amennyiben *kilépett* (használja `az container show` állapotának ellenőrzéséhez), az új kimeneti megjelenítéséhez naplók megjelenítése:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Kimenet:

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="command-line-override"></a>Parancssori felülbírálása

Adjon meg egy parancssort, amikor a parancssorból, a tároló lemezképpel bővíthetőség felülbírálására tároló példány létrehozása. Ez hasonlít a `--entrypoint` parancssori argumentumának `docker run`.

Például rendelkezhet az a szöveg eltérő elemezheti például tároló *apró település* különböző parancssori megadásával. A Python-parancsfájl végrehajtása a tároló *wordcount.py*, fogad el argumentumként egy URL-címet, és feldolgozza a lap tartalma helyett az alapértelmezett.

Ahhoz például, hogy határozza meg az első 3 öt levelek szavak *Romeo és Juliet*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer3 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Újra miután a tároló *kilépett*, jelenít meg a tároló naplók eredményének megtekintéséhez:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer3
```

Kimenet:

```bash
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>További lépések

### <a name="persist-task-output"></a>A feladat kimenetének megőrzése

Megtudhatja, hogyan megőrizni a kimenetét a tárolókban, hogy lefusson, lásd: [csatlakoztatása az Azure fájlmegosztások Azure tároló osztályt](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli
