---
title: Azure CLI-minták Windows
description: Azure CLI-minták Windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 6c4b707a3cebc1bcdad7c9e14a96d82a8dda2371
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89318794"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Azure CLI-minták Windows rendszerű virtuális gépekhez

Az alábbi táblázat a Windows rendszerű virtuális gépeket telepítő Azure CLI használatával létrehozott bash-parancsfájlok hivatkozásait tartalmazza.

| Script | Description |
|---|---|
|**Virtuális gépek létrehozása**||
| [Virtuális gép létrehozása](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Egy minimális konfigurációval rendelkező Windows rendszerű virtuális gépet hoz létre. |
| [Teljes konfigurációjú virtuális gép létrehozása](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy erőforráscsoportot, egy virtuális gépet és az összes kapcsolódó erőforrást.|
| [Magas rendelkezésre állású virtuális gépek létrehozása](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Több virtuális gépet hoz létre egy magasan elérhető és terheléselosztásos konfigurációban. |
| [Virtuális gép létrehozása és konfigurációs szkript futtatása](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet, és az egyéni Azure script bővítmény használatával telepíti az IIS-t. |
| [Virtuális gép létrehozása és DSC-konfiguráció futtatása](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet, és az Azure desired State Configuration (DSC) bővítmény használatával telepíti az IIS-t. |
|**Tárolás kezelése**||
| [Felügyelt lemez létrehozása VHD-ből](../scripts/virtual-machines-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy felügyelt lemezt egy speciális VHD-ből operációsrendszer-lemezként, vagy adatlemezként egy adatvhd-fájlból.  |
| [Felügyelt lemez létrehozása pillanatképből](../scripts/virtual-machines-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy felügyelt lemezt egy pillanatképből. |
| [Felügyelt lemez másolása előfizetésen belül vagy előfizetések között](../scripts/virtual-machines-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Átmásolja a felügyelt lemezt ugyanarra vagy másik előfizetésre, de ugyanabban a régióban, ahol a szülő felügyelt lemez található. 
| [Pillanatkép exportálása VHD-ként egy tárfiókba](../scripts/virtual-machines-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | A felügyelt pillanatképet VHD-ként exportálja egy másik régióban lévő Storage-fiókba. |
| [Felügyelt lemez VHD-jének exportálása egy tárfiókba](../scripts/virtual-machines-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | A felügyelt lemez mögöttes VHD-jét egy másik régióban lévő Storage-fiókba exportálja. |
| [Pillanatkép másolása előfizetésen belül vagy előfizetések között](../scripts/virtual-machines-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Átmásolja a pillanatképet ugyanabba vagy másik előfizetésbe, de ugyanabban a régióban, mint a szülő pillanatkép. |
|**Hálózati virtuális gépek**||
| [A virtuális gépek közötti hálózati adatforgalom védelme](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Két virtuális gépet hoz létre, minden kapcsolódó erőforrást és egy belső és külső hálózati biztonsági csoportot (NSG). |
|**Virtuális gépek védelme**||
| [Virtuális gép és adatlemezeinek titkosítása](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy Azure Key Vault, egy titkosítási kulcsot és egy egyszerű szolgáltatást, majd titkosít egy virtuális gépet. |
|**Virtuális gépek figyelése**||
| [Virtuális gép figyelése Azure Monitor](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet, telepíti a Log Analytics ügynököt, és regisztrálja a virtuális gépet egy Log Analytics munkaterületen.  |
| | |
