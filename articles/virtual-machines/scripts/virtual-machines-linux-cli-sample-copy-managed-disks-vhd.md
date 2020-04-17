---
title: Felügyelt lemezek másolása tárfiókba – CLI-minta
description: Azure CLI-minta – Felügyelt lemezek exportálása vagy másolása tárfiókba.
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/09/2019
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: d841519aeedb4b85fb897b45fa09be6c62b1fa2a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459984"
---
# <a name="exportcopy-a-managed-disk-to-a-storage-account-using-the-azure-cli"></a>Felügyelt lemez exportálása/másolása tárfiókba az Azure CLI használatával

Ez a szkript egy felügyelt lemez mögöttes VHD-fájlját exportálja egy tárfiókba, amely ugyanabban a régióban vagy egy másikban található. Először létrehozza a felügyelt lemez SAS URI-azonosítóját, majd annak használatával átmásolja a VHD-fájlt egy tárfiókba. A szkript használatával másolatot készíthet felügyelt lemezeiről egy másik régióba a regionális terjeszkedéshez. Ha közzé szeretné tenni egy felügyelt lemez virtuális merevlemez-fájlját az Azure Marketplace-en, ezzel a parancsfájllal másolhatja a virtuális merevlemez-fájlt egy tárfiókba, majd létrehozhat egy SAS URI-t a másolt virtuális merevlemezből, hogy közzétegye azt a Marketplace-en.   


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.sh "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy felügyelt lemez SAS URI-azonosítójának létrehozásához, majd a mögöttes VHD-fájl SAS URI használatával való tárfiókba másolásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az disk grant-access](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az-disk-grant-access) | Létrehoz egy írásvédett SAS-t, amelynek használatával a mögöttes VHD-fájl átmásolható egy tárfiókba, vagy letölthető a helyszíni rendszerre  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy) | Aszinkron módon átmásol egy blobot egy tárfiókból egy másikba. |

## <a name="next-steps"></a>További lépések

[Felügyelt lemez létrehozása VHD-ből](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[Virtuális gép létrehozása felügyelt lemezből](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

A virtuális gépekhez és felügyelt lemezekhez kapcsolódó további CLI-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
