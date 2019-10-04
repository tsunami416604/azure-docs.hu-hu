---
title: Azure virtuális gépekhez csatolt lemezek hibaelhárítása |} A Microsoft Docs
description: Hibaelhárítással kapcsolatos források az Azure virtuális gép virtuális merevlemezek (VHD) mutató hivatkozásokat tartalmaz.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: rogarana
ms.reviewer: wmgries
ms.openlocfilehash: c00fdf3ad02edc1faf0d3257d3836a1c2f44d682
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65150773"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Azure virtuális gépekhez csatolt lemezek hibaelhárítása 

Azure-beli virtuális gépek (VM) virtuális merevlemezek (VHD-k) az operációsrendszer-lemez és bármely csatlakoztatott adatlemezek támaszkodnak. Virtuális merevlemezek és a egy vagy több Azure Storage-fiókokban tárolt. Ez a cikk a hibaelhárító virtuális merevlemezzel rendelkező esetleg felmerülő gyakori problémák a tartalom mutat. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Virtuálisgép-tároló törlésével kapcsolatos hibák elhárítása

Bizonyos esetekben amíg egy tárolási erőforrás törlése, ha egy Resource Manager-alapú virtuális gép tartalmaz csatolt virtuális merevlemezek felmerülhet hiba. Segítségre van szüksége a probléma elhárításához a következő cikkekben talál: 

  * Linuxos virtuális gépeken: [A Resource Manager-alapú tárolási törlésével kapcsolatos hibák](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Windows virtuális gépeken: [A Resource Manager-alapú tárolási törlésével kapcsolatos hibák](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Virtuális merevlemezekkel rendelkező virtuális gépek váratlan újraindulása hibaelhárítása

Ha nagy számú virtuális merevlemezekkel rendelkező virtuális gépek váratlan újraindulása tapasztal, tekintse meg az alábbi cikkekben:

  * Linuxos virtuális gépeken: [Virtuális merevlemezekkel rendelkező virtuális gépek váratlan újraindulása](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Windows virtuális gépeken: [Virtuális merevlemezekkel rendelkező virtuális gépek váratlan újraindulása](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
