---
title: "Az Azure CLI parancsfájl minta - újraindítás virtuális gépek |} Microsoft Docs"
description: "Az Azure CLI parancsfájl minta - újraindítás virtuális gépek kódcímke és -azonosító szerint"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: ea114f484c774573b7d219cff9102a7308af356e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="restart-vms"></a>Indítsa újra a virtuális gépek

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Ez a példa bemutatja a több módon néhány virtuális gépeket, és újra kell indítania őket.

Az első újraindítja a virtuális gépek erőforráscsoportban.

```bash
az vm restart --ids $(az vm list --resource-group myResourceGroup --query "[].id" -o tsv)
```

A második lekérdezi a címkézett virtuális gépek `az resouce list` és -szűrők erőforrást, amely virtuális gépeket, és újraindítja a virtuális gépek.

```bash
az vm restart --ids $(az resource list --tag "restart-tag" --query "[?type=='Microsoft.Compute/virtualMachines'].id" -o tsv)
```

Ez a minta a rendszerhéjakba működik. Az Azure parancssori felület parancsfájlok futtatásához a Windows-ügyfelén beállítások, lásd: [futtatása az Azure parancssori felület a Windows](../windows/cli-options.md).


## <a name="sample-script"></a>Mintaparancsfájl

A minta három parancsfájlok rendelkezik.
Az első címtárra látja el a virtuális gépek.
Használ a no-wait, a parancsot úgy kell létrehozni minden virtuális gép várakozás nélkül adja vissza.
A második a virtuális gép teljesen kiépített vár.
A harmadik parancsfájl újraindul összes virtuális gépet, melyeket a kiosztott, és végül csak a címkézett virtuális gépeket.

### <a name="provision-the-vms"></a>A virtuális gépek kiépítése

Ezt a parancsfájlt hoz létre egy erőforráscsoportot, és ezután hoz létre három virtuális gépek újraindítására.
Kettő címkével rendelkeznek.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/provision.sh "Provision the VMs")]

### <a name="wait"></a>Várakozás

. A szkript ellenőrzi az üzembe helyezési állapotát minden 20 másodperc kiépített összes három virtuális gépet, vagy egyiket kiépítése sikertelen.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/wait.sh "Wait for the VMs to be provisioned")]

### <a name="restart-the-vms"></a>Indítsa újra a virtuális gépek

Ezt a parancsfájlt a virtuális gépek újraindítja az erőforráscsoporthoz tartozik, és azt csak a címkézett virtuális gépek újraindítja.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/restart.sh "Restart VMs by tag")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A parancsfájl-minta futtatása után a következő parancs segítségével távolítsa el az erőforráscsoportot, a virtuális gépek és az összes kapcsolódó erőforrások.

```azurecli-interactive 
az group delete -n myResourceGroup --no-wait --yes
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, virtuális gép, a rendelkezésre állási csoporthoz, terheléselosztó és minden kapcsolódó erőforrások létrehozásához. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az virtuális gép létrehozása](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | A virtuális gépeket hoz létre.  |
| [az vm listája](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | A használt `--query` annak érdekében, hogy a virtuális gépek törlődnek, az újraindítás előtt, majd a virtuális gépek későbbi újraindítása azonosítóinak beszerzését. |
| [az erőforrások listája](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | A használt `--query` a címke használata virtuális gépek azonosítóinak beszerzését. |
| [az a virtuális gép újraindítása](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Újraindítja a virtuális gépeket. |
| [az csoport törlése](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További virtuális gép CLI parancsfájl minták megtalálhatók a [Azure Linux virtuális dokumentációját](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
