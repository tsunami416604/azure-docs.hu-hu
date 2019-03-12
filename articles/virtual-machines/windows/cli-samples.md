---
title: Windows Azure CLI-minták |} A Microsoft Docs
description: Windows Azure CLI-minták
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: abc38d315bc879a06ecd9a9bf7188c15533c018a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57535008"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Az Azure CLI minták a Windows virtual machines

A következő táblázat használatával létrehozott bash szkriptekre az Azure parancssori felület, amely a Windows virtuális gépek üzembe helyezése mutató hivatkozásokat tartalmaz.

| | |
|---|---|
|**Virtuális gépek létrehozása**||
| [Virtuális gép létrehozása](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Egy Windows virtuális gépet hoz létre minimális konfigurációval. |
| [Teljes konfigurációjú virtuális gép létrehozása](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy erőforráscsoportot, virtuális gép és minden kapcsolódó erőforrás.|
| [Magas rendelkezésre állású virtuális gépek létrehozása](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Több magas rendelkezésre állású virtuális gépek és az elosztott terhelésű konfigurációs hoz létre. |
| [Hozzon létre egy virtuális Gépet, és konfigurációs parancsfájl futtatása](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet, és használja az Azure egyéni szkriptek bővítménye az IIS telepítéséhez. |
| [Hozzon létre egy virtuális Gépet, és futtassa a DSC-konfiguráció](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet és az Azure Desired State Configuration (DSC) bővítmény segítségével telepítse az IIS szolgáltatást. |
|**Tárhely kezelése**||
| [Felügyelt lemez létrehozása VHD-fájlból](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Felügyelt lemez létrehozása speciális virtuális merevlemezből operációsrendszer-lemezként, vagy virtuális adatlemezről adatlemezként.  |
| [Felügyelt lemez létrehozása pillanatképből](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy felügyelt lemezt egy pillanatképből. |
| [Felügyelt lemez másolása azonos vagy eltérő előfizetéshez](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Másolja a felügyelt lemez azonos vagy eltérő előfizetéshez, de ugyanabban a régióban, mint a szülő felügyelt lemez. 
| [A pillanatkép exportálása merevlemezként egy storage-fiókba](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Egy felügyelt pillanatképet exportál merevlemezként egy másik régióban lévő tárfiókhoz. |
| [A felügyelt lemez VHD exportálás tárfiókba](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exportálja a mögöttes VHD-t egy felügyelt lemez egy másik régióban lévő tárfiókhoz. |
| [Azonos vagy eltérő előfizetéshez pillanatkép másolása](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Pillanatkép példányban, azonos vagy eltérő előfizetéshez, de a szülőpillanatkép ugyanabban a régióban. |
|**Hálózati virtuális gépek**||
| [Virtuális gépek közötti hálózati adatforgalom védelme](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Két virtuális gépet, az összes kapcsolódó erőforrást és a egy belső és külső hálózati biztonsági csoportok (NSG) hoz létre. |
|**Virtuális gépek védelme**||
| [A virtuális gép és az adatlemezek titkosítása](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy Azure Key Vault, a titkosítási kulcsot és az egyszerű szolgáltatás, majd a virtuális gép titkosítja. |
|**Virtuális gépek figyelése**||
| [Virtuális gép monitorozása az Azure monitorral](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet, telepíti a Log Analytics-ügynököt, és regisztrálja a virtuális gép a Log Analytics-munkaterületen.  |
| | |
