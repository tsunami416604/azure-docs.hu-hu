---
title: Azure virtuális gépekhez csatlakoztatott lemezek hibaelhárítása |} Microsoft Docs
description: Az Azure Blob Storage nagy mennyiségű strukturálatlan objektumadat, például szöveg vagy bináris adatok tárolására szolgál. Az alkalmazások a PowerShellből vagy az Azure CLI-ből érhetik el a Blob Storage tárolóban lévő objektumokat, vagy egy kódból az Azure Storage ügyfélkódtárakon vagy REST-kódon keresztül.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/01/2018
ms.author: tamram
ms.openlocfilehash: ae309efb95e2f633effcfb5723d8377f5e94d406
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Azure virtuális gépekhez csatlakoztatott lemezek hibaelhárítása 

Az Azure virtuális gépek (VM) virtuális merevlemezeket (VHD) az operációsrendszer-lemezképet, és minden kapcsolódó adatlemezek támaszkodnak. Virtuális merevlemezek, egy vagy több Azure Storage-fiókok a lapblobokat tárolódnak. Ez a cikk mutat, a virtuális merevlemezek esetleg felmerülő gyakori problémákat a hibaelhárítási információkat. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>A virtuális gépek tárolási törlési hibák elhárítása

Bizonyos esetekben amíg a tároló egyik erőforrásához törlése, ha a virtuális gépek erőforrás-kezelő telepítések tartalmaz csatlakoztatva a virtuális merevlemezek felmerülhet hiba. Segítséget nyújthat a probléma elhárításához tekintse meg a következő cikkeket: 

  * A Linux virtuális gépek: [tárolási tárfióktörlési hibák az erőforrás-kezelő telepítése](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * A Windows virtuális gépek: [tárolási tárfióktörlési hibák az erőforrás-kezelő telepítése](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Váratlan újraindítások csatlakoztatott virtuális merevlemezek a virtuális gépek hibaelhárítása

Ha váratlan vesznek el egy csatlakoztatott virtuális merevlemezek nagy számú virtuális Gépet, a következő cikkekben talál:

  * A Linux virtuális gépek: [nem várt újraindul a csatlakoztatott virtuális merevlemezek a virtuális gépek](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * A Windows virtuális gépek: [nem várt újraindul a csatlakoztatott virtuális merevlemezek a virtuális gépek](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
