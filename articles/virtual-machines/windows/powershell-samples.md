---
title: Virtuális gép az Azure PowerShell-példák |} Microsoft Docs
description: Virtuális gép az Azure PowerShell-példák
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/30/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 31dbb8485232d1b446469135b5bda0c8c7884190
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure virtuális gép PowerShell-minták

A következő táblázat a PowerShell-parancsfájlok példák, amelyek Windows virtuális gépek létrehozására és kezelésére mutató hivatkozásokat tartalmaz.

| | |
|---|---|
|**Virtuális gépek létrehozása**||
| [Virtuális gép gyors létrehozása](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hoz létre egy erőforráscsoportot, virtuális gép és az összes kapcsolódó erőforrások kér minimális.|
| [A teljesen konfigurált virtuális gép létrehozása](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások.|
| [Magas rendelkezésre állású virtuális gépek létrehozása](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Több magas rendelkezésre állású virtuális gépek és az elosztott terhelésű konfigurációs hoz létre.|
| [Hozzon létre egy virtuális Gépet, és futtassa a konfigurációs parancsfájl](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy virtuális gépet, és az Azure egyéni parancsprogramok futtatására szolgáló bővítmény segítségével telepítse az IIS szolgáltatást. |
| [Hozzon létre egy virtuális Gépet, és futtassa a DSC-konfiguráció](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy virtuális gépet, és az Azure kívánt állapot konfigurációs szolgáltatása (DSC) bővítmény segítségével telepítse az IIS. |
| [A virtuális merevlemez feltöltéséhez és a virtuális gépek létrehozása](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Egy helyi VHD-fájlt feltölti az Azure-ba, hoz létre és a virtuális merevlemez kép és erről a képről létrehoz egy virtuális Gépet. |
| [A felügyelt operációsrendszer-lemez a virtuális gép létrehozása](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy virtuális gépet egy meglévő kezelt lemez az operációs rendszer lemezeként csatolásával. |
| [Hozzon létre egy virtuális Gépet egy pillanatképből](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy virtuális gépet egy pillanatképből, először hoz létre egy felügyelt lemezes pillanatképből, és majd az operációs rendszer lemezeként az új kezelt lemez csatolása. |
|**Tárhely kezelése**||
| [Felügyelt lemezes ugyanazon vagy másik előfizetésben található virtuális merevlemez létrehozása](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Egy felügyelt lemezt hoz létre, egy speciális olyan operációs rendszert tartalmazó virtuális Merevlemezt vagy egy adatlemez ugyanazon vagy másik előfizetésben található virtuális Merevlemezt a.  |
| [Hozzon létre egy felügyelt lemezes egy pillanatképből](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Felügyelt lemezes pillanatképet hoz létre. |
| [Felügyelt lemezmásolás ugyanazon vagy másik előfizetésbe](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Másolatot ugyanazon vagy másik előfizetés lemez felügyelt, de ugyanabban a régióban, mint a szülő kezelt lemezre. 
| [Pillanatkép virtuális merevlemez tárfiókba exportálása](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Felügyelt pillanatkép exportálja, virtuális Merevlemezt egy másik régióban található tárfiókot. |
| [Pillanatkép virtuális merevlemez létrehozása](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy virtuális merevlemez létrehozásához több azonos felügyelt lemezek pillanatképből rövid időn belül a pillanatkép.  |
| [Azonos vagy azoktól eltérő előfizetés pillanatkép másolása](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Pillanatkép másolatokat ugyanazon vagy másik előfizetésbe, de a szülő pillanatkép ugyanabban a régióban. |
|**Virtuális gépek védelme**||
| [A Virtuálisgép- és adatlemezek titkosítása](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Létrehoz egy Azure Key Vault, a titkosítási kulcs és a szolgáltatás egyszerű, majd a virtuális gépek titkosítja. |
|**Virtuális gépek figyelése**||
| [Virtuális gép és az Operations Management Suite figyelése](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy virtuális gépet, telepíti az Operations Management Suite-ügynököt, és regisztrálja az OMS-munkaterület virtuális gép.  |
| | |

