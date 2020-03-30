---
title: Belépési pont felülbírálása a tárolópéldányban
description: Állítson be egy parancssort, amely felülbírálja a belépési pontot egy tárolólemezképben, amikor egy Azure-tárolópéldányt telepít
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: d9554603f78a07fa44af51d8f39a91e1b3c39f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247123"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>A tárolópéldány parancssorának beállítása az alapértelmezett parancssori művelet felülbírálására

Amikor tárolópéldányt hoz létre, adja meg azt a parancsot, amely felülbírálja a tárolólemezképbe sütött alapértelmezett parancssori utasítást. Ez a viselkedés `--entrypoint` hasonló a parancssori argumentumhoz. `docker run`

A [tárolópéldányok környezeti változóinak](container-instances-environment-variables.md) beállításához hasonlóan a kezdő parancssor megadása is hasznos a kötegelt feladatokhoz, ahol minden tárolót dinamikusan kell előkészíteni feladatspecifikus konfigurációval.

## <a name="command-line-guidelines"></a>Parancssori irányelvek

* Alapértelmezés szerint a parancssor *egyetlen folyamatot* ad meg, amely a tárolóban lévő rendszerhéj nélkül indul el. Előfordulhat például, hogy a parancssor python-parancsfájlt vagy végrehajtható fájlt futtat. A folyamat további paramétereket vagy argumentumokat adhat meg.

* Több parancs végrehajtásához kezdje el a parancssort a tárolóoperációs rendszerben támogatott rendszerhéj-környezet beállításával. Példák:

  |Operációs rendszer  |Alapértelmezett rendszerhéj  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpesi     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Kövesse a rendszerhéj konvencióit, és több parancsot egyesítsen egymás után.

* A tároló konfigurációjától függően előfordulhat, hogy be kell állítania egy teljes elérési utat a parancssori végrehajtható fájlhoz vagy argumentumhoz.

* Állítsa be a megfelelő [újraindítási házirendet](container-instances-restart-policy.md) a tárolópéldányhoz, attól függően, hogy a parancssor hosszú ideig futó vagy egy egyszeri feladatot ad-e meg. Például egy egyszeri `Never` feladat `OnFailure` újraindítási házirendje, vagy ajánlott. 

* Ha információra van szüksége a tárolórendszerkép alapértelmezett belépési pontjáról, használja a [docker-rendszerkép-vizsgálat](https://docs.docker.com/engine/reference/commandline/image_inspect/) parancsot.

## <a name="command-line-syntax"></a>Parancssori szintaxis

A parancssori szintaxis a példányok létrehozásához használt Azure API-tól vagy eszköztől függ. Ha parancshéj-környezetet ad meg, tartsa be a parancs szintaxisának konvencióit is.

* [az container create][az-container-create] parancs: Adjon `--command-line` át egy karakterláncot a paraméterrel. Példa: `--command-line "python myscript.py arg1 arg2"`).

* [Új-AzureRmContainerGroup][new-azurermcontainergroup] Azure PowerShell-parancsmag: Adja át `-Command` a paramétert. Példa: `-Command "echo hello"`.

* Azure Portal: A **parancs felülbírálása** tulajdonsága a tároló konfiguráció, adjon meg egy vesszővel tagolt karakterláncok listáját, idézőjelek nélkül. Példa: `python, myscript.py, arg1, arg2`). 

* Resource Manager-sablon vagy YAML-fájl, vagy az Azure SDK-k egyike: Adja meg a parancssori tulajdonságot karakterláncok tömbjeként. Példa: a JSON-tömb `["python", "myscript.py", "arg1", "arg2"]` egy Erőforrás-kezelő sablonban. 

  Ha ismeri a [Dockerfile](https://docs.docker.com/engine/reference/builder/) szintaxisát, ez a formátum hasonló a CMD utasítás *exec* formájához.

### <a name="examples"></a>Példák

|    |  Azure CLI   | Portál | Sablon | 
| ---- | ---- | --- | --- |
| Egyetlen parancs | `--command-line "python myscript.py arg1 arg2"` | **Parancs felülbírálása:**`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Több parancs | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Parancs felülbírálása:**`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Példa az Azure CLI-re

Például módosítsa a [microsoft/aci-wordcount][aci-wordcount] tárolókép viselkedését, amely shakespeare-i *hamletszöveget* elemez, hogy megtalálja a leggyakrabban előforduló szavakat. A *Hamlet*elemzése helyett beállíthat egy parancssort, amely egy másik szövegforrásra mutat.

Ha meg szeretné tekinteni a [microsoft/aci-wordcount][aci-wordcount] tároló kimenetét, amikor az az alapértelmezett szöveget elemzi, futtassa azt a következő [az container create][az-container-create] paranccsal. Nincs megadva indítási parancssor, így az alapértelmezett tárolóparancs fut. Szemléltetésképpen ez a példa [beállítja a környezeti változókat,](container-instances-environment-variables.md) hogy megtalálják a legalább öt betűhosszú első 3 szót:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Miután a tároló állapota *megállítva* jelenik meg (használja [az container show][az-container-show] állapot ellenőrzéséhez), [jelenítse][az-container-logs] meg a naplót az az tároló naplók a kimenet megtekintéséhez.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Kimenet:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Most állítson be egy második példatárolót a különböző szövegek elemzéséhez egy másik parancssor megadásával. A tároló által végrehajtott Python-parancsfájl, *wordcount.py*, egy URL-címet fogad el argumentumként, és az alapértelmezett helyett feldolgozza az oldal tartalmát.

Például, hogy meghatározza a top 3 szó, amely legalább öt betű hosszú *Rómeó és Júlia:*

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Ismét, ha a tároló *leállítva,* tekintse meg a kimenetet a tároló naplóinak megjelenítésével:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Kimenet:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>További lépések

A feladatalapú forgatókönyvek, például a több tárolóval rendelkező nagy adatkészlet kötegelt feldolgozása, futásidőben egyéni parancssorok előnyeit élvezhetik. A feladatalapú tárolók futtatásáról további információt a [Tárolóalapú feladatok futtatása újraindítási házirendekkel című témakörben talál.](container-instances-restart-policy.md)

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
