---
title: Azure virtuális gépekhez csatlakoztatott lemezek – problémamegoldás | Microsoft dokumentumok
description: Az Azure virtuálisgépek virtuális merevlemezeihez (VHD) kapcsolatos hibaelhárítási erőforrásokra mutató hivatkozásokat tartalmaz.
services: storage
author: roygara
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rogarana
ms.reviewer: wmgries
ms.openlocfilehash: b81c0947327dc8e84c5d3fbbecde3aa31313cc70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061186"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Azure virtuális gépekhez csatlakoztatott lemezek – problémamegoldás 

Az Azure virtuális gépek (VM-ek) virtuális merevlemezekre (VHD-k) támaszkodnak az operációs rendszer lemezei és a csatlakoztatott adatlemezek. A Virtuális- és virtuális gépek egy vagy több Azure Storage-fiókban lapblobként tárolódnak. Ez a cikk a virtuális szolgáltatásokkal kapcsolatos gyakori problémák kalkulálása érdekében a tartalom hibaelhárítására mutat. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Virtuális gép tárolási törlési hibáinak elhárítása

Bizonyos esetekben hibát tapasztalhat egy tárolási erőforrás törlése közben, amikor egy Erőforrás-kezelő központi telepítésében lévő virtuális gép csatolt virtuális gépeket tartalmaz. A probléma megoldásához az alábbi cikkekben talál segítséget: 

  * Linuxos virtuális gépeken: [Tárolási törlési hibák az Erőforrás-kezelő központi telepítésében](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Windows virtuális gépeken: [Tárolási törlési hibák az Erőforrás-kezelő központi telepítésében](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>A csatlakoztatott virtuális gépek váratlan újraindításának elhárítása csatlakoztatott virtuális számítógépekkel

Ha nagy számú csatlakoztatott virtuális gép váratlan újraindítását tapasztalja, tekintse meg az alábbi cikkek egyikét:

  * Linuxos virtuális gépeken: [A csatlakoztatott virtuális gépek váratlan újraindítása csatlakoztatott virtuális számítógépekkel](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Windows virtuális gépeken: [A csatlakoztatott virtuális gépek váratlan újraindítása csatlakoztatott virtuális számítógépekkel](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
