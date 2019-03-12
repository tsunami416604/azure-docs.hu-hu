---
title: Azure virtuális gépek PowerShell-minták |} A Microsoft Docs
description: Azure virtuális gépek PowerShell-minták
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
ms.openlocfilehash: 576fe268bec12c16c7c2e2076dfa066c908693d5
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539530"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure virtuális gépek PowerShell-minták

A következő táblázat, létrehozása és kezelése Windows virtuális gépek (VM) PowerShell-szkript minták mutató hivatkozásokat tartalmaz.

| | |
|---|---|
|**Virtuális gépek létrehozása**||
| [Virtuális gép gyors létrehozása](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Kérések legalább létrehoz egy erőforráscsoportot, egy virtuális gép és minden kapcsolódó erőforrás.|
| [Teljes konfigurációjú virtuális gép létrehozása](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy erőforráscsoportot, egy virtuális gép és minden kapcsolódó erőforrás.|
| [Magas rendelkezésre állású virtuális gépek létrehozása](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Magas rendelkezésre állású, elosztott terhelésű konfigurációban több virtuális gépeket hoz létre.|
| [Hozzon létre egy virtuális Gépet, és a egy konfigurációs parancsfájl futtatása](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet, és használja az Azure egyéni szkriptek bővítménye az IIS telepítéséhez. |
| [Hozzon létre egy virtuális Gépet, és futtassa a DSC-konfiguráció](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet és az Azure Desired State Configuration (DSC) bővítmény segítségével telepítse az IIS szolgáltatást. |
| [VHD feltöltése és virtuális gépek létrehozása](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Feltölt egy helyi VHD-fájlt az Azure-ba, és létrehoz egy képet a VHD-ből majd létrehoz egy virtuális Gépet abból a rendszerképből. |
| [Virtuális gép létrehozása egy felügyelt operációsrendszer-lemez](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gép operációsrendszer-lemezként egy meglévő felügyelt lemez csatlakoztatásával. |
| [Virtuális gép létrehozása pillanatképből](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet először hoz létre egy felügyelt lemezt a pillanatképből, és majd az operációsrendszer-lemezként az új felügyelt lemez csatolása egy pillanatképből. |
|**Tárhely kezelése**||
| [Felügyelt lemez létrehozása azonos, vagy egy másik előfizetésben található virtuális merevlemezből](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Felügyelt lemez operációsrendszer-lemezként speciális virtuális merevlemezből vagy adatlemezről adatlemezként, ugyanazon a virtuális Merevlemezt vagy egy másik előfizetésben hoz létre.  |
| [Felügyelt lemez létrehozása pillanatképből](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy felügyelt lemezt egy pillanatképből. |
| [Felügyelt lemez másolása, de akár egy másik előfizetésre](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Másolja át egy felügyelt lemezt, de akár egy másik előfizetést, amely a szülő felügyelt lemez ugyanabban a régióban.
| [Pillanatkép exportálni egy VHD-t egy storage-fiókba](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Egy felügyelt pillanatképet exportál egy merevlemezként egy másik régióban lévő tárfiókhoz. |
| [A felügyelt lemez VHD exportálás tárfiókba](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exportálja a mögöttes VHD-iről egy felügyelt lemezt egy másik régióban lévő tárfiókhoz. |
| [Pillanatkép létrehozása virtuális merevlemezből](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy pillanatképet egy VHD-ből, majd azt több azonos felügyelt lemez gyors létrehozásához.  |
| [Másolja a pillanatképet, de akár egy másik előfizetésben](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Másolja a pillanatkép, de akár egy másik előfizetést, amely a szülőpillanatkép ugyanabban a régióban. |
|**Virtuális gépek védelme**||
| [Virtuális gép és az adatlemezek titkosítása](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Létrehoz egy Azure key vault, a titkosítási kulcsot és a egy egyszerű szolgáltatást, és ezután titkosítja a virtuális gép. |
|**Virtuális gépek figyelése**||
| [Virtuális gép monitorozása az Azure monitorral](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet, telepíti az Azure Log Analytics-ügynököt, és regisztrálja a virtuális gép a Log Analytics-munkaterületen.  |
| | |
