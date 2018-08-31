---
title: Azure virtuális gépekhez csatolt lemezek hibaelhárítása |} A Microsoft Docs
description: Hibaelhárítással kapcsolatos források az Azure virtuális gép virtuális merevlemezek (VHD) mutató hivatkozásokat tartalmaz.
services: storage
author: genlin
ms.service: storage
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.openlocfilehash: 81db3a819c28aab8f4b644a940eeb5e6c5ecf3d6
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307172"
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
