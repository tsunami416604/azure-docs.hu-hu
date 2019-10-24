---
title: Kiinduló parancssor használata Azure Container Instances
description: Egy tároló lemezképében konfigurált BelépésiPont felülbírálása Azure Container-példány telepítésekor
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 40d946db48a65452d2da529098c07d0d0c60d472
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619660"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>A parancssor beállítása tároló-példányban az alapértelmezett parancssori művelet felülbírálásához

Tároló-példány létrehozásakor opcionálisan megadhat egy parancsot, amely felülbírálja az alapértelmezett parancssori utasításokat a tároló képére. Ez a viselkedés hasonló a `--entrypoint` parancssori `docker run`argumentumhoz.

Például a [környezeti változók](container-instances-environment-variables.md) beállítása a Container instances esetében, a kezdő parancssor megadása olyan batch-feladatok esetében hasznos, ahol az egyes tárolókat a feladat-specifikus konfigurációval dinamikusan kell előkészíteni.

## <a name="command-line-guidelines"></a>Parancssori irányelvek

* Alapértelmezés szerint a parancssor egyetlen folyamatot határoz meg, amely a tárolóban lévő *rendszerhéj nélkül indul* el. Előfordulhat például, hogy a parancssor egy Python-parancsfájlt vagy egy végrehajtható fájlt futtat. A folyamat további paramétereket vagy argumentumokat adhat meg.

* Több parancs végrehajtásához indítsa el a parancssort a tároló operációs rendszer által támogatott rendszerhéj-környezet beállításával. Példák:

  |Operációs rendszer  |Alapértelmezett rendszerhéj  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  A rendszerhéj konvencióinak követésével több parancsot is egyesíthet a sorozatokban való futtatáshoz.

* Előfordulhat, hogy a tároló konfigurációjától függően meg kell adnia a parancssori végrehajtható fájl vagy argumentumok teljes elérési útját.

* Állítson be [](container-instances-restart-policy.md) egy megfelelő újraindítási szabályzatot a tároló-példányhoz attól függően, hogy a parancssor egy hosszan futó feladatot vagy egyszeri futtatású feladatot határoz meg. Például a `Never` vagy `OnFailure` a rendszer újraindítási szabályzata javasolt egy egyszeri futtatású feladathoz. 

* Ha információra van szüksége a tároló-rendszerképben beállított alapértelmezett BelépésiPont, használja a [Docker-rendszerkép vizsgálata](https://docs.docker.com/engine/reference/commandline/image_inspect/) parancsot.

## <a name="command-line-syntax"></a>Parancssori szintaxis

A parancssori szintaxis a példányok létrehozásához használt Azure API vagy eszköztől függően változhat. Ha a rendszerhéj-környezetet is megadja, figyelje meg a rendszerhéj parancs szintaxisát is.

* [az Container Create][az-container-create] parancs: Adjon át egy karakterláncot `--command-line` a paraméterrel. Példa: `--command-line "python myscript.py arg1 arg2"`).

* [Új – AzureRmContainerGroup][new-azurermcontainergroup] Azure PowerShell parancsmag: Adjon át egy karakterláncot `-Command` a paraméterrel. Példa: `-Command "echo hello"`.

* Azure Portal: A tároló konfigurációjának **parancs felülbírálása** tulajdonságában adja meg a karakterláncok vesszővel tagolt listáját idézőjelek nélkül. Példa: `python, myscript.py, arg1, arg2`). 

* Resource Manager-sablon vagy YAML-fájl, vagy egy Azure SDK-k egyike: Karakterláncok tömbje a parancssori tulajdonságot határozza meg. Példa: a JSON- `["python", "myscript.py", "arg1", "arg2"]` tömb egy Resource Manager-sablonban. 

  Ha már ismeri a [Docker](https://docs.docker.com/engine/reference/builder/) szintaxist, ez a formátum a cmd utasítás *exec* formájához hasonlóan fog megjelenni.

### <a name="examples"></a>Példák

|    |  Azure CLI   | Portál | Sablon | 
| ---- | ---- | --- | --- |
| Egyetlen parancs | `--command-line "python myscript.py arg1 arg2"` | **Parancs felülbírálása**:`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Több parancs | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Parancs felülbírálása**:`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Azure CLI-példa

Tegyük fel például, hogy módosítja a [Microsoft/ACI-WordCount][aci-wordcount] tároló rendszerképének viselkedését, amely elemzi a szöveget a Shakespeare Hamletben, hogy megtalálja a leggyakrabban előforduló szavakat. A *Hamlet*elemzése helyett olyan parancssort állíthat be, amely egy másik szöveges forrásra mutat.

Ha meg szeretné tekinteni a [Microsoft/ACI-WordCount][aci-wordcount] tároló kimenetét, amikor elemzi az alapértelmezett szöveget, futtassa azt a következő az [Container Create][az-container-create] paranccsal. Nincs megadva indítási parancssor, ezért az alapértelmezett tároló parancs fut. Illusztrációs célokra ez a példa [környezeti változókat](container-instances-environment-variables.md) állít be a legalább öt betűből álló első három szó megkereséséhez:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Miután a tároló állapota leálltként jelenik meg (az az [Container show][az-container-show] paranccsal ellenőrizze az állapotot), a kimenet megtekintéséhez jelenítse meg a naplót az [az Container logs][az-container-logs] használatával.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Kimenet:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Most állítson be egy második példát tárolót egy másik parancssor megadásával a különböző szövegek elemzéséhez. A tároló által végrehajtott Python-szkript, a *WordCount.py*, elfogad egy URL-címet argumentumként, és a lap tartalmát dolgozza fel az alapértelmezett érték helyett.

Például a *Rómeó és Júlia*közül legalább öt betűből álló első három szó meghatározásához:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Ha a tárolót leállították, akkor a tároló naplóinak megjelenítésével megtekintheti a kimenetet:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Kimenet:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>További lépések

A feladat-alapú forgatókönyvek, például a Batch több tárolóval rendelkező nagyméretű adathalmazok feldolgozására alkalmasak, a futtatáskor az egyéni parancssorok is hasznosak lehetnek. A Task-alapú tárolók futtatásával kapcsolatos további információkért lásd: [a tárolózott feladatok futtatása](container-instances-restart-policy.md)újraindítási szabályzatokkal.

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
