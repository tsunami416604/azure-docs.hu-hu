---
title: Azure virtuális gépekhez csatolt lemezek hibaelhárítása |} A Microsoft Docs
description: Az Azure Blob Storage nagy mennyiségű strukturálatlan objektumadat, például szöveg vagy bináris adatok tárolására szolgál. Az alkalmazások a PowerShellből vagy az Azure CLI-ből érhetik el a Blob Storage tárolóban lévő objektumokat, vagy egy kódból az Azure Storage ügyfélkódtárakon vagy REST-kódon keresztül.
services: storage
author: genlin
ms.service: storage
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.component: disks
ms.openlocfilehash: 0dbd89c28d18d64908d92cd38d8bd1cf3138fd5c
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397967"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Azure virtuális gépekhez csatolt lemezek hibaelhárítása 

Azure-beli virtuális gépek (VM) virtuális merevlemezek (VHD-k) az operációsrendszer-lemez és bármely csatlakoztatott adatlemezek támaszkodnak. Virtuális merevlemezek és a egy vagy több Azure Storage-fiókokban tárolt. Ez a cikk a hibaelhárító virtuális merevlemezzel rendelkező esetleg felmerülő gyakori problémák a tartalom mutat. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Virtuálisgép-tároló törlésével kapcsolatos hibák elhárítása

Bizonyos esetekben amíg egy tárolási erőforrás törlése, ha egy Resource Manager-alapú virtuális gép tartalmaz csatolt virtuális merevlemezek felmerülhet hiba. Segítségre van szüksége a probléma elhárításához a következő cikkekben talál: 

  * A Linux rendszerű virtuális gépek: [a Resource Manager-alapú tárolási törlésével kapcsolatos hibák](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * A Windows virtuális gépek: [a Resource Manager-alapú tárolási törlésével kapcsolatos hibák](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Virtuális merevlemezekkel rendelkező virtuális gépek váratlan újraindulása hibaelhárítása

Ha nagy számú virtuális merevlemezekkel rendelkező virtuális gépek váratlan újraindulása tapasztal, tekintse meg az alábbi cikkekben:

  * A Linux rendszerű virtuális gépek: [váratlan újraindítja a virtuális merevlemezekkel rendelkező virtuális gépek](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * A Windows virtuális gépek: [váratlan újraindítja a virtuális merevlemezekkel rendelkező virtuális gépek](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
