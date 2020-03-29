---
title: Azure CLI-minták
description: Azure CLI-minták
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: 76803f8c3a703071eb733c5cfde65482ffd07f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970080"
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Azure CLI-minták Linux rendszerű virtuális gépekhez

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

| | |
|---|---|
|**Virtuális gépek létrehozása**||
| [Virtuális gép létrehozása](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Linuxos virtuális gépet hoz létre minimális konfigurációval. |
| [Teljes konfigurációjú virtuális gép létrehozása](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy erőforráscsoportot, virtuális gépet és az összes kapcsolódó erőforrást.|
| [Magas rendelkezésre állású virtuális gépek létrehozása](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Több virtuális gépet hoz létre magas rendelkezésre állású és terheléselosztásos konfigurációban. |
| [Virtuális gép létrehozása és konfigurációs parancsfájl futtatása](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Virtuális gépet hoz létre, és az Azure Custom Script bővítmény t használja az NGINX telepítéséhez. |
| [Virtuális gép létrehozása telepítve a WordPress programmal](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy virtuális gépet, és az Azure Custom Script bővítmény használatával telepíti a WordPress. |
| [Virtuális gép létrehozása felügyelt operációsrendszer-lemezről](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Virtuális gépet hoz létre egy meglévő felügyelt lemez operációsrendszer-lemezként való csatolásával. |
| [Virtuális gép létrehozása pillanatképből](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Virtuális gépet hoz létre pillanatképből úgy, hogy először létrehoz egy felügyelt lemezt a pillanatképből, majd csatolja az új felügyelt lemezt operációsrendszer-lemezként. |
|**Tárolás kezelése**||
| [Felügyelt lemez létrehozása VHD-ből](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Felügyelt lemezt hoz létre egy speciális virtuális merevlemezből operációs rendszerlemezként vagy adatvirtuális merevlemezből adatlemezként.  |
| [Felügyelt lemez létrehozása pillanatképből](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Felügyelt lemezt hoz létre pillanatképből. |
| [Felügyelt lemez másolása ugyanarra vagy másik előfizetésre](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | A felügyelt lemezt ugyanabba vagy különböző előfizetésbe másolja, de ugyanabban a régióban, mint a szülő által kezelt lemez. 
| [Pillanatkép exportálása VHD-ként egy tárfiókba](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | A felügyelt pillanatképet virtuális merevlemezként exportálja egy különböző régióbeli tárfiókba. |
| [Felügyelt lemez VHD-jének exportálása egy tárfiókba](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | A felügyelt lemez alapjául szolgáló virtuális merevlemezt egy különböző régióban lévő tárfiókba exportálja. |
| [Pillanatkép másolása ugyanarra vagy másik előfizetésbe](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | A pillanatképet ugyanabba vagy egy másik előfizetésbe másolja, de ugyanabban a régióban, mint a szülő pillanatkép. |
|**Hálózati virtuális gépek**||
| [A virtuális gépek közötti hálózati adatforgalom védelme](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Két virtuális gépet hoz létre, az összes kapcsolódó erőforrást, valamint egy belső és külső hálózati biztonsági csoportot (NSG). |
|**Biztonságos virtuális gépek**||
| [Virtuális gép és adatlemezeinek titkosítása](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy Azure Key Vault, titkosítási kulcs és egyszerű szolgáltatás, majd titkosítja a virtuális gép. |
|**Virtuális gépek figyelése**||
| [Virtuális gép figyelése az Azure Monitor-naplókkal](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy virtuális gépet, telepíti a Log Analytics-ügynököt, és beállítja a virtuális gépet egy Log Analytics-munkaterületen.  |
|**Virtuális gépek – problémamegoldás**||
| [Egy virtuális gép operációsrendszer-lemezének hibaelhárítása](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Az operációs rendszer lemezét egy virtuális gépről egy második virtuális gép adatlemezeként csatlakoztatja. |
| | |
