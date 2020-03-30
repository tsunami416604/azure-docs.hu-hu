---
title: Azure CLI minták Windows
description: Azure CLI minták Windows
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033637"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Azure CLI-minták Windows rendszerű virtuális gépekhez

Az alábbi táblázat a Windows virtuális gépeket üzembe helyező Azure CLI használatával készített bash-parancsfájlokra mutató hivatkozásokat tartalmaz.

| | |
|---|---|
|**Virtuális gépek létrehozása**||
| [Virtuális gép létrehozása](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Minimális konfigurációval létrehozott windowsos virtuális gépet. |
| [Teljes konfigurációjú virtuális gép létrehozása](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy erőforráscsoportot, virtuális gépet és az összes kapcsolódó erőforrást.|
| [Magas rendelkezésre állású virtuális gépek létrehozása](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Több virtuális gépet hoz létre magas rendelkezésre állású és terheléselosztásos konfigurációban. |
| [Virtuális gép létrehozása és konfigurációs parancsfájl futtatása](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet, és az Azure Custom Script bővítmény t használja az IIS telepítéséhez. |
| [Virtuális gép létrehozása és DSC-konfiguráció futtatása](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet, és az Azure Desired State Configuration (DSC) bővítményt használja az IIS telepítéséhez. |
|**Tárolás kezelése**||
| [Felügyelt lemez létrehozása VHD-ből](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Felügyelt lemezt hoz létre egy speciális virtuális merevlemezből operációs rendszerlemezként vagy adatvirtuális merevlemezből adatlemezként.  |
| [Felügyelt lemez létrehozása pillanatképből](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Felügyelt lemezt hoz létre pillanatképből. |
| [Felügyelt lemez másolása ugyanarra vagy másik előfizetésre](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | A felügyelt lemezt ugyanabba vagy különböző előfizetésbe másolja, de ugyanabban a régióban, mint a szülő által kezelt lemez. 
| [Pillanatkép exportálása VHD-ként egy tárfiókba](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | A felügyelt pillanatképet virtuális merevlemezként exportálja egy különböző régióbeli tárfiókba. |
| [Felügyelt lemez VHD-jének exportálása egy tárfiókba](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | A felügyelt lemez alapjául szolgáló virtuális merevlemezt egy különböző régióban lévő tárfiókba exportálja. |
| [Pillanatkép másolása ugyanarra vagy másik előfizetésbe](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | A pillanatképet ugyanabba vagy egy másik előfizetésbe másolja, de ugyanabban a régióban, mint a szülő pillanatkép. |
|**Hálózati virtuális gépek**||
| [A virtuális gépek közötti hálózati adatforgalom védelme](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Két virtuális gépet hoz létre, az összes kapcsolódó erőforrást, valamint egy belső és külső hálózati biztonsági csoportot (NSG). |
|**Biztonságos virtuális gépek**||
| [Virtuális gép és adatlemezeinek titkosítása](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy Azure Key Vault, titkosítási kulcs és egyszerű szolgáltatás, majd titkosítja a virtuális gép. |
|**Virtuális gépek figyelése**||
| [Virtuális gép figyelése az Azure Monitorsegítségével](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet, telepíti a Log Analytics-ügynököt, és beállítja a virtuális gépet egy Log Analytics-munkaterületen.  |
| | |
