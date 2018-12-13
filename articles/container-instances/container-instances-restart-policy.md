---
title: Használja az Azure Container Instancesben újraindításra vonatkozó házirendek tárolóalapú feladatok
description: Ismerje meg, hogyan használható az Azure Container Instances szolgáltatásban futtatott feladatok befejezését, mint például a buildelési, tesztelési vagy kép renderelési feladatok végrehajtásához.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 12/10/2018
ms.author: danlep
ms.openlocfilehash: b254adb050aa9826170c0849c3811380db6d9b38
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321033"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Tárolóalapú feladatok futtatása az újraindítási házirendek

Az egyszerű és az Azure Container Instances a tárolók üzembe helyezésének sebességét meggyőző platformot kínál egyszeri futtatású feladatok, mint például a buildelési, tesztelési és képrenderelés végrehajtása a tárolópéldány.

A konfigurálható újraindítási házirend megadhatja, hogy a tárolók le vannak állítva a folyamatok befejezését. Container instances szolgáltatás számlázása másodpercalapú, mert csak a használt, amíg a feladat végrehajtása a tároló fut-e számítási erőforrások díjkötelesek.

A példákban Ez a cikk a használati bemutatni az Azure CLI. Rendelkeznie kell Azure CLI 2.0.21-es vagy újabb [helyileg telepített][azure-cli-install], vagy a CLI használata a [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Tároló újraindítási szabályzata

Amikor létrehoz egy [tárolócsoport](container-instances-container-groups.md) az Azure Container Instancesben, kiválaszthat egyet a három újraindítási házirend-beállításokat.

| Újraindítási szabályzat   | Leírás |
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

Az újraindítási házirend működés közben látni, hozzon létre egy tároló-példányát a [microsoft/aci-wordcount] [ aci-wordcount-image] rendszerképet, és adja meg a `OnFailure` újraindítási házirend. Ebben a példában a tárolóban fut egy Python-szkriptet, amely, alapértelmezés szerint elemzi a Shakespeare szövege [apró település](http://shakespeare.mit.edu/hamlet/full.html), a 10 leggyakoribb szavakat ír az STDOUT és majd kilép.

A példa tároló futtassa az alábbi [az tároló létrehozása] [ az-container-create] parancsot:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image microsoft/aci-wordcount:latest \
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

## <a name="manually-stop-and-start-a-container-group"></a>Manuálisan állítsa le és indítsa el a tárolócsoport

Függetlenül az újraindítási házirend konfigurált egy [tárolócsoport](container-instances-container-groups.md), érdemes manuális indítása vagy leállítása egy tárolócsoportot.

* **Állítsa le** - manuálisan állítsa le egy futó tárolócsoportra bármikor – például használatával a [az tároló stop] [ az-container-stop] parancsot. Az egyes tárolókhoz kapcsolódó számítási feladatok, érdemes leállítani egy tárolócsoport menteni a költségeket a meghatározott időszak után. 

  Leállítása folyamatban van egy tárolócsoport leállítja és újraindítja a tárolók a csoport; nem őrzi meg a tároló állapota. 

* **Indítsa el** – Ha egy tárolócsoport le van állítva – vagy mert a tárolók saját leállt, vagy manuálisan leállították a csoport – használhatja a [tároló indítása API](/rest/api/container-instances/containergroups/start) vagy manuálisan elindítani a tárolók Azure Portalon a csoport. Bármely tárolóra a tárolórendszerkép frissül, ha egy új rendszerképet kéri le. 

  Új központi telepítést azonos konfigurációjú tároló indítása egy tárolócsoport kezdődik. Ez a művelet segítségével gyorsan felhasználhatja egy ismert tároló a portcsoportok, amely a várt módon működik. Nem kell ugyanazt a számítási feladatot futtatni egy új tárolócsoport létrehozásához.

* **Indítsa újra a** -újraindíthatja a tárolócsoport működés közben – például használja a [az tároló újraindítási] [ az-container-restart] parancsot. Ez a művelet újraindítja az összes tároló tárolócsoportban. Bármely tárolóra a tárolórendszerkép frissül, ha egy új rendszerképet kéri le. 

  A tárolócsoport újraindítása akkor hasznos, ha a kívánt központi telepítési hiba elhárítása. Például ha egy ideiglenes erőforrás-korlátozás meggátolja, hogy a tárolók sikeresen fut, a csoport újraindítása megoldhatja a problémát.

Manuálisan indítsa el, vagy indítsa újra egy tárolócsoportot, miután a tároló csoport fut a beállított megfelelően újraindítási házirend.

## <a name="configure-containers-at-runtime"></a>Tárolók konfigurálása a futási időben

Amikor létrehoz egy tárolópéldányt, beállíthatja a **környezeti változók**, illetve adja meg az egyéni **parancssori** végrehajtani, amikor a tároló elindult. Ezek a beállítások a kötegelt feladatok használatával készítse elő az egyes tárolók feladatspecifikus konfigurációval.

## <a name="environment-variables"></a>Környezeti változók

Környezeti változók értékét az alkalmazás vagy a tároló által futtatott parancsfájl dinamikus konfiguráció a tárolóban. Ez hasonlít a `--env` parancssori argumentumot `docker run`.

Például a parancsfájl a példa tárolóban működése módosítható a tárolópéldány létrehozásakor az alábbi környezeti változókat megadásával:

*NumWords*: A STDOUT küldött szavak számát.

*A MinLength*: Ahhoz, hogy a megszámlálandó szó karaktereinek minimális száma. Ha nagyobb figyelmen kívül hagyja a gyakori szavakat, például a "," és "a."

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Megadásával `NumWords=5` és `MinLength=8` a tároló naplóit a tároló környezeti változók esetében eltérő kimeneti kell megjelenítenie. Miután a tároló állapota *kilépett* (használata `az container show` ellenőrizheti annak állapotát), a naplók megtekintéséhez az új kimenet megjelenítéséhez:

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



## <a name="command-line-override"></a>Parancssor felülbírálása

Adja meg egy parancssort, amikor létrehoz egy tárolópéldányt, felülbírálhatja a tárolórendszerképbe beépített parancssori. Ez hasonlít a `--entrypoint` parancssori argumentumot `docker run`.

Például rendelkezhet a példa tárolót más, a szöveg elemzése *apró település* egy másik parancssori megadásával. A tároló által futtatott Python-szkript *wordcount.py*, argumentumként egy URL-cím fogadja és dolgozza fel a lapon alapértelmezett letöltője helyett.

Például határozza meg a 3 leggyakoribb öt levelek szavak *Romeo és Juliet*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer3 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Újra Ha a tároló *kilépett*, a kimenet megtekintéséhez a tároló naplóinak megjelenítése:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer3
```

Kimenet:

```bash
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>További lépések

### <a name="persist-task-output"></a>A feladat kimenetének megőrzése

Hogyan kell a tárolókat, amelyek befejezését kimenetének megőrzése a részletekért lásd: [csatlakoztatása egy Azure-fájlmegosztás az Azure Container Instances](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
[azure-cli-install]: /cli/azure/install-azure-cli
