---
title: Egy kezdő parancsot használja az Azure Container Instances szolgáltatásban
description: Bírálja felül a belépési pont konfigurálva van egy tárolórendszerképet telepít egy Azure Container Instances szolgáltatáshoz
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: cbe14066cfd7493806176e834373e952daf19339
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610222"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>A parancssor tárolópéldány felülbírálhatja az alapértelmezett parancssor művelet beállítása

Amikor létrehoz egy tárolópéldányt, szükség esetén bírálja felül az alapértelmezett parancssor utasítás a tárolórendszerképbe számlázásnak kívánt parancs megadásához. Ez a viselkedés hasonlít a `--entrypoint` parancssori argumentumot `docker run`.

A beállítás, például [környezeti változók](container-instances-environment-variables.md) container Instances, adjon meg egy kezdő parancssori akkor hasznos, ha a batch-feladatok, elő kell készíteni az egyes tárolók dinamikusan feladatspecifikus konfigurációval.

## <a name="command-line-guidelines"></a>Parancssori útmutató

* Alapértelmezés szerint a parancssorban megadja egy *egyszeri folyamat, amelyet egy felület nélkül indítja el* a tárolóban. Például a parancssor futhat egy Python-szkript vagy végrehajtható fájlt. 

* Hajtsa végre a több parancsokat, megkezdheti a parancssori rendszerhéj-környezetet az operációs rendszer beállításával (példák: `bin/sh`, `/bin/bash`, `cmd`). Az konvencióinak a rendszerhéj úgy, hogy több parancsok sorrendben lefutnak.

* A tároló konfigurációjától függően szükség lehet beállítani egy parancssori végrehajtható fájl teljes elérési útja nebo argumenty obsahují.

* Állítsa be a megfelelő [újraindítási házirend](container-instances-restart-policy.md) a tárolópéldány, attól függően, hogy a parancssori megadja egy hosszan futó feladatot vagy egyszer futó feladat. Ha például az újraindítási házirend `Never` vagy `OnFailure` egyszer futtatott feladat esetében ajánlott. 

* Ha az alapértelmezett belépési pont beállítása egy tárolórendszerképet az információ van szüksége, használja a [vizsgálja meg a docker-rendszerkép](https://docs.docker.com/engine/reference/commandline/image_inspect/) parancsot.

## <a name="command-line-syntax"></a>Parancssori szintaxis

A parancssori szintaxis az Azure API-t vagy a-példányok létrehozásához használt eszköz függően változik. Ha megad egy rendszerhéj-környezetet, is látni fogja a parancs szintaxisát egyezmények a rendszerhéj.

* [az tároló létrehozása] [ az-container-create] parancsot: Egy karakterláncot adja át a `--command-line` paraméter. Példa: `--command-line "python myscript.py arg1 arg2"`).

* [Új-AzureRmContainerGroup] [ new-azurermcontainergroup] Azure PowerShell-parancsmagot: Egy karakterláncot adja át a `-Command` paraméter. Példa: `-Command "echo hello"`.

* Az Azure Portalon: Az a **parancs felülbírálás** a tárolókonfigurációt tulajdonsága adja meg idézőjelek nélküli karakterláncok vesszővel tagolt listája. Példa: `python, myscript.py, arg1, arg2`). 

* Resource Manager-sablon vagy a YAML-fájlt, vagy az Azure SDK-k valamelyikét: Adja meg a parancssori tulajdonság egy karakterláncokból álló tömbre. Példa: a JSON-tömböt `["python", "myscript.py", "arg1", "arg2"]` a Resource Manager-sablonnal. 

  Ha ismeri a [Dockerfile](https://docs.docker.com/engine/reference/builder/) szintaxist, ez a formátum akkor hasonló a *exec* CMD formában.

### <a name="examples"></a>Példák

|    |  Azure CLI   | Portál | Sablon | 
| ---- | ---- | --- | --- |
| Egyetlen paranccsal | `--command-line "python myscript.py arg1 arg2"` | **Felülbírálás parancs**: `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Több parancs | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Felülbírálás parancs**: `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Az Azure CLI-példa

Például működésének módosítása a [microsoft/aci-wordcount] [ aci-wordcount] tárolórendszerképet, amely elemzi a Shakespeare a szöveg *apró település* a leggyakrabban keresése előforduló szavakat. Helyett elemzése *apró település*, beállíthat egy parancssor, amely egy másik szöveges forrás mutat.

Az a kimenet megtekintéséhez a [microsoft/aci-wordcount] [ aci-wordcount] tárolót, ha megvizsgálja az alapértelmezett szöveget, és futtassa az alábbi [az tároló létrehozása] [ az-container-create] parancsot. Nincs kezdő parancssori van megadva, így az alapértelmezett tároló parancsot futtatja. Illusztrációs célok esetében ez a példa beállítja [környezeti változók](container-instances-environment-variables.md) a 3 leggyakoribb szavak, amelyek legalább öt betűből megkereséséhez:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Miután a tároló állapota állapota *kilépett* (használata [az container show] [ az-container-show] állapotának ellenőrzése), a naplót a megjelenítendő [az tárolónaplók] [ az-container-logs] a kimenet megtekintéséhez.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Kimenet:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Most már beállítása egy második példa tároló különböző szöveg elemzése egy másik parancssori megadásával. A tároló által futtatott Python-szkript *wordcount.py*, fogad el argumentumként egy URL-címet, és feldolgozza a lapon alapértelmezett letöltője helyett.

Például megállapításához az első 3 szavak, amelyek legalább öt betűket hosszú a *Romeo és Juliet*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Újra Ha a tároló *kilépett*, a kimenet megtekintéséhez a tároló naplóinak megjelenítése:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Kimenet:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>További lépések

Feladatalapú forgatókönyvek, például a kötegelt feldolgozási egy nagy méretű adathalmazt a különböző tárolók esetében is kihasználhatják egyéni parancssorok futásidőben. Futó tárolók feladataival kapcsolatos további információkért lásd: [tárolóalapú feladatok futtatása az újraindítási házirendek](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
