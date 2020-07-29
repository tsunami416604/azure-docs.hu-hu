---
title: Azure CLI-minták
description: Azure CLI-minták
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: 977d67010f97b2cf29e6949b0e6fde1ac488f046
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87370138"
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Azure CLI-minták Linux rendszerű virtuális gépekhez

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

| Script | Leírás |
|---|---|
|**Virtuális gépek létrehozása**||
| [Virtuális gép létrehozása](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Egy minimális konfigurációval rendelkező linuxos virtuális gépet hoz létre. |
| [Teljes konfigurációjú virtuális gép létrehozása](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy erőforráscsoportot, egy virtuális gépet és az összes kapcsolódó erőforrást.|
| [Magas rendelkezésre állású virtuális gépek létrehozása](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Több virtuális gépet hoz létre egy magasan elérhető és terheléselosztásos konfigurációban. |
| [Virtuális gép létrehozása és konfigurációs szkript futtatása](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy virtuális gépet, és az egyéni Azure script bővítmény használatával telepíti az NGINX-et. |
| [Virtuális gép létrehozása telepített WordPresszel](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy virtuális gépet, és az egyéni Azure script bővítményt használja a WordPress telepítéséhez. |
| [Virtuális gép létrehozása felügyelt operációsrendszer-lemezről](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy virtuális gépet egy meglévő felügyelt lemez operációsrendszer-lemezként való csatolásával. |
| [Virtuális gép létrehozása pillanatképből](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy virtuális gépet egy pillanatképből, ha először létrehoz egy felügyelt lemezt a pillanatképből, majd az új felügyelt lemezt operációsrendszer-lemezként csatolja. |
|**Tárolás kezelése**||
| [Felügyelt lemez létrehozása VHD-ből](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy felügyelt lemezt egy speciális VHD-ből operációsrendszer-lemezként, vagy adatlemezként egy adatvhd-fájlból.  |
| [Felügyelt lemez létrehozása pillanatképből](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy felügyelt lemezt egy pillanatképből. |
| [Felügyelt lemez másolása előfizetésen belül vagy előfizetések között](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Átmásolja a felügyelt lemezt ugyanarra vagy másik előfizetésre, de ugyanabban a régióban, ahol a szülő felügyelt lemez található. 
| [Pillanatkép exportálása VHD-ként egy tárfiókba](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | A felügyelt pillanatképet VHD-ként exportálja egy másik régióban lévő Storage-fiókba. |
| [Felügyelt lemez VHD-jének exportálása egy tárfiókba](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | A felügyelt lemez mögöttes VHD-jét egy másik régióban lévő Storage-fiókba exportálja. |
| [Pillanatkép másolása előfizetésen belül vagy előfizetések között](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Átmásolja a pillanatképet ugyanabba vagy másik előfizetésbe, de ugyanabban a régióban, mint a szülő pillanatkép. |
|**Hálózati virtuális gépek**||
| [A virtuális gépek közötti hálózati adatforgalom védelme](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Két virtuális gépet hoz létre, minden kapcsolódó erőforrást és egy belső és külső hálózati biztonsági csoportot (NSG). |
|**Virtuális gépek védelme**||
| [Virtuális gép és adatlemezeinek titkosítása](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy Azure Key Vault, egy titkosítási kulcsot és egy egyszerű szolgáltatást, majd titkosít egy virtuális gépet. |
|**Virtuális gépek figyelése**||
| [Virtuális gép monitorozása Azure Monitor-naplókkal](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy virtuális gépet, telepíti a Log Analytics ügynököt, és regisztrálja a virtuális gépet egy Log Analytics munkaterületen.  |
|**Virtuális gépek hibáinak megoldása**||
| [Egy virtuális gép operációsrendszer-lemezének hibaelhárítása](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Az operációsrendszer-lemez csatlakoztatása egy virtuális gépről egy második virtuális gépen lévő adatlemezként. |
| | |
