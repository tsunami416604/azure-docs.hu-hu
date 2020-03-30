---
title: Az Azure virtuális gép PowerShell-mintái
description: Az Azure virtuális gép PowerShell-mintái
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f068b79f1b1eaa9a11df70052619c8e3993101cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033001"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Az Azure Virtuális gép PowerShell-mintái

Az alábbi táblázat a Windows virtuális gépeket (VM-eket) létrehozó és kezelő PowerShell-parancsfájlmintákra mutató hivatkozásokat tartalmaz.

| | |
|---|---|
|**Virtuális gépek létrehozása**||
| [Virtuális gép gyors létrehozása](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy erőforráscsoportot, egy virtuális gépet és az összes kapcsolódó erőforrást, minimális kérdésekkel.|
| [Teljes konfigurációjú virtuális gép létrehozása](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy erőforráscsoportot, egy virtuális gépet és az összes kapcsolódó erőforrást.|
| [Magas rendelkezésre állású virtuális gépek létrehozása](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Több virtuális gépet hoz létre egy magas rendelkezésre állású és terheléselosztásos konfigurációban.|
| [Virtuális gép létrehozása és konfigurációs parancsfájl futtatása](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet, és az Azure Custom Script bővítmény t használja az IIS telepítéséhez. |
| [Virtuális gép létrehozása és DSC-konfiguráció futtatása](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet, és az Azure Desired State Configuration (DSC) bővítményt használja az IIS telepítéséhez. |
| [Virtuális merevlemez feltöltése és virtuális gépek létrehozása](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Feltölt egy helyi virtuális merevlemez-fájlt az Azure-ba, létrehoz egy lemezképet a virtuális merevlemezről, majd létrehoz egy virtuális gép az adott lemezképből. |
| [Virtuális gép létrehozása felügyelt operációsrendszer-lemezről](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Virtuális gépet hoz létre egy meglévő felügyelt lemez operációsrendszer-lemezként való csatolásával. |
| [Virtuális gép létrehozása pillanatképből](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Virtuális gépet hoz létre pillanatképből úgy, hogy először létrehoz egy felügyelt lemezt a pillanatképből, majd csatolja az új felügyelt lemezt operációsrendszer-lemezként. |
|**Tárolás kezelése**||
| [Felügyelt lemez létrehozása virtuális merevlemezről ugyanabban vagy egy másik előfizetésben](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Felügyelt lemezt hoz létre egy speciális virtuális merevlemezről operációs rendszerlemezként, vagy adatvirtuális merevlemezből adatlemezként, ugyanabban vagy egy másik előfizetésben.  |
| [Felügyelt lemez létrehozása pillanatképből](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Felügyelt lemezt hoz létre pillanatképből. |
| [Felügyelt lemez másolása ugyanarra vagy egy másik előfizetésre](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | A felügyelt lemezt ugyanabba vagy egy másik előfizetésbe másolja, amely ugyanabban a régióban található, mint a szülő által felügyelt lemez.
| [Pillanatkép exportálása VHD-ként egy tárfiókba](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | A felügyelt pillanatképet virtuális merevlemezként exportálja egy másik régióban lévő tárfiókba. |
| [Felügyelt lemez VHD-jének exportálása egy tárfiókba](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | A felügyelt lemez alapjául szolgáló virtuális merevlemezt egy másik régióban lévő tárfiókba exportálja. |
| [Pillanatkép létrehozása VHD-ből](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Pillanatképet hoz létre egy virtuális merevlemezről, majd ezt a pillanatképet használva gyorsan több azonos felügyelt lemezt hoz létre.  |
| [Pillanatkép másolása ugyanarra vagy egy másik előfizetésre](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | A pillanatképet ugyanabba vagy egy másik előfizetésbe másolja, amely ugyanabban a régióban van, mint a szülő pillanatkép. |
|**Biztonságos virtuális gépek**||
| [Virtuális gép és adatlemezeinek titkosítása](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Létrehoz egy Azure-kulcstartót, egy titkosítási kulcsot és egy egyszerű szolgáltatást, majd titkosítja a virtuális gép. |
|**Virtuális gépek figyelése**||
| [Virtuális gép figyelése az Azure Monitorsegítségével](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet, telepíti az Azure Log Analytics-ügynököt, és beállítja a virtuális gépet egy Log Analytics-munkaterületen.  |
| [Az előfizetésben lévő összes virtuális gép részleteinek összegyűjtése a PowerShell segítségével](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy csv-t, amely tartalmazza a virtuális gép nevét, erőforráscsoport nevét, régióját, virtuális hálózatát, alhálózatát, privát IP-címét, operációs rendszer típusát és nyilvános IP-címét a virtuális gépeknek a megadott előfizetésben.
| | |
