---
title: Azure CLI-minták Windows
description: Azure CLI-minták Windows
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
ms.openlocfilehash: 8d57220f79f1349937a279e57a17d51a76bcccb3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033637"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Azure CLI-minták a Windows rendszerű virtuális gépekhez

Az alábbi táblázat a Windows rendszerű virtuális gépeket telepítő Azure CLI használatával létrehozott bash-parancsfájlok hivatkozásait tartalmazza.

| | |
|---|---|
|**Virtuális gépek létrehozása**||
| [Virtuális gép létrehozása](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Egy minimális konfigurációval rendelkező Windows rendszerű virtuális gépet hoz létre. |
| [Teljes mértékben konfigurált virtuális gép létrehozása](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy erőforráscsoportot, egy virtuális gépet és az összes kapcsolódó erőforrást.|
| [Magasan elérhető virtuális gépek létrehozása](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Több magas rendelkezésre állású virtuális gépek és az elosztott terhelésű konfigurációs hoz létre. |
| [Virtuális gép létrehozása és konfigurációs parancsfájl futtatása](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet, és az egyéni Azure script bővítmény használatával telepíti az IIS-t. |
| [Virtuális gép létrehozása és DSC-konfiguráció futtatása](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet, és az Azure desired State Configuration (DSC) bővítmény használatával telepíti az IIS-t. |
|**Tárhely kezelése**||
| [Felügyelt lemez létrehozása VHD-ből](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy felügyelt lemezt egy speciális VHD-ből operációsrendszer-lemezként, vagy adatlemezként egy adatvhd-fájlból.  |
| [Felügyelt lemez létrehozása pillanatképből](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy felügyelt lemezt egy pillanatképből. |
| [Felügyelt lemez másolása azonos vagy eltérő előfizetésre](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Átmásolja a felügyelt lemezt ugyanarra vagy másik előfizetésre, de ugyanabban a régióban, ahol a szülő felügyelt lemez található. 
| [Pillanatkép exportálása virtuális merevlemezként egy Storage-fiókba](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | A felügyelt pillanatképet VHD-ként exportálja egy másik régióban lévő Storage-fiókba. |
| [Felügyelt lemez virtuális merevlemezének exportálása Storage-fiókba](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | A felügyelt lemez mögöttes VHD-jét egy másik régióban lévő Storage-fiókba exportálja. |
| [Pillanatkép másolása azonos vagy eltérő előfizetésre](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Átmásolja a pillanatképet ugyanabba vagy másik előfizetésbe, de ugyanabban a régióban, mint a szülő pillanatkép. |
|**Hálózati virtuális gépek**||
| [Biztonságos hálózati forgalom a virtuális gépek között](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Két virtuális gépet hoz létre, minden kapcsolódó erőforrást és egy belső és külső hálózati biztonsági csoportot (NSG). |
|**Virtuális gépek biztonságossá tétele**||
| [VIRTUÁLIS gépek és adatlemezek titkosítása](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy Azure Key Vault, egy titkosítási kulcsot és egy egyszerű szolgáltatást, majd titkosít egy virtuális gépet. |
|**Virtuális gépek figyelése**||
| [Virtuális gép figyelése Azure Monitor](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet, telepíti a Log Analytics ügynököt, és regisztrálja a virtuális gépet egy Log Analytics munkaterületen.  |
| | |
