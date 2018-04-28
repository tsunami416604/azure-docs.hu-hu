---
title: Környezeti változók beállítása az Azure-tároló példányok
description: 'Útmutató: Azure tároló példányát környezeti változók beállítása'
services: container-instances
author: david-stanford
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/13/2018
ms.author: dastanfo
ms.openlocfilehash: 37fde41b6dc2ea0a4d3b4b38a0e3df81a297c125
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="set-environment-variables"></a>Környezeti változók beállítása

A tároló példányát környezeti változók beállítása lehetővé teszi az alkalmazás vagy a tároló által futtatott parancsfájl dinamikus konfigurálása.

Ön jelenleg állíthatja be a környezeti változók a parancssori felület és a PowerShell. Mindkét esetben szeretné használni a jelzőt a parancsok a környezeti változók értékét. Környezeti változók beállítása a ACI hasonlít a `--env` parancssori argumentumának `docker run`. Például a microsoft/aci-wordcount tároló lemezkép használatakor módosíthatja a viselkedés a következő környezeti változók megadásával:

*NumWords*: STDOUT küldött szót.

*A MinLength*: ahhoz, hogy a megszámlálandó szót karakterek minimális száma. Minél nagyobb karakterkészletszámot figyelmen kívül hagyja a gyakori szavakat, például "a" és "a."

## <a name="azure-cli-example"></a>Az Azure parancssori felület – példa

A tároló kimeneti az alapértelmezett megjelenítéséhez futtassa a következő parancsot:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Megadásával `NumWords=5` és `MinLength=8` a tároló környezeti változók, a tároló naplók megjelenjen-e eltérő kimenetet.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

A tároló állapota Amennyiben *kilépett* (használata [az tároló megjelenítése] [ az-container-show] állapotának ellenőrzése), a kimenet megtekintéséhez naplók megjelenítése.  A tároló nem környezeti változók beállítva – név lesz mycontainer1 helyett mycontainer2 eredményének megtekintéséhez.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

## <a name="azure-powershell-example"></a>Az Azure PowerShell-példa

A tároló kimeneti az alapértelmezett megjelenítéséhez futtassa a következő parancsot:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Megadásával `NumWords=5` és `MinLength=8` a tároló környezeti változók, a tároló naplók megjelenjen-e eltérő kimenetet.

```azurepowershell-interactive
$envVars = @{NumWord=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Miután a tároló állapota *kilépett* (használja [Get-AzureRmContainerInstanceLog] [ azure-instance-log] állapotának ellenőrzése), a kimenet megtekintéséhez naplók megjelenítése. A tároló megtekintéséhez naplók és a nem környezeti változók beállítása ContainerGroupName helyett mycontainer2 mycontainer1 kell.

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog `
    -ResourceGroupName myResourceGroup `
    -ContainerGroupName mycontainer2
```

## <a name="example-output-without-environment-variables"></a>Példa a kimenetre nélkül környezeti változók

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

## <a name="example-output-with-environment-variables"></a>A környezeti változók egy példa a kimenetre

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="next-steps"></a>További lépések

Most, hogy ismeri a tároló bemeneti testreszabása, megtudhatja, hogyan megőrizni a lefusson tárolókat kimenetét.
> [!div class="nextstepaction"]
> [Azure-tároló osztályt az Azure fájlmegosztások csatlakoztatása](container-instances-mounting-azure-files-volume.md)

<!-- LINKS Internal -->
[azure-cloud-shell]: ../cloud-shell/overview.md
[azure-cli-install]: /cli/azure/
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show