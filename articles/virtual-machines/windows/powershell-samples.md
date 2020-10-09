---
title: Azure-beli virtuális gépek PowerShell-mintái
description: Azure-beli virtuális gépek PowerShell-mintái
author: cynthn
ms.service: virtual-machines-windows
ms.topic: sample
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e8489ecb9343cf70bd8523aa45f04a6bde171b98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89321752"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure-beli virtuális gépek PowerShell-mintái

Az alábbi táblázat a Windows rendszerű virtuális gépeket létrehozó és kezelő PowerShell-parancsfájlokra mutató hivatkozásokat tartalmaz.

| Script | Leírás |
|---|---|
|**Virtuális gépek létrehozása**||
| [Virtuális gép gyors létrehozása](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy erőforráscsoportot, egy virtuális gépet és az összes kapcsolódó erőforrást, és legalább rákérdez.|
| [Teljes konfigurációjú virtuális gép létrehozása](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy erőforráscsoportot, egy virtuális gépet és az összes kapcsolódó erőforrást.|
| [Magas rendelkezésre állású virtuális gépek létrehozása](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Több virtuális gépet hoz létre egy magasan elérhető és elosztott terhelésű konfigurációban.|
| [Virtuális gép létrehozása és konfigurációs parancsfájl futtatása](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet, és az egyéni Azure script bővítmény használatával telepíti az IIS-t. |
| [Virtuális gép létrehozása és DSC-konfiguráció futtatása](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet, és az Azure desired State Configuration (DSC) bővítmény használatával telepíti az IIS-t. |
| [VHD feltöltése és virtuális gépek létrehozása](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Feltölt egy helyi VHD-fájlt az Azure-ba, létrehoz egy rendszerképet a VHD-ből, majd létrehoz egy virtuális gépet az adott rendszerképből. |
| [Virtuális gép létrehozása felügyelt operációsrendszer-lemezről](./../scripts/virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet egy meglévő felügyelt lemez operációsrendszer-lemezként való csatolásával. |
| [Virtuális gép létrehozása pillanatképből](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet egy pillanatképből, ha először létrehoz egy felügyelt lemezt a pillanatképből, majd az új felügyelt lemezt operációsrendszer-lemezként csatolja. |
|**Tárolás kezelése**||
| [Felügyelt lemez létrehozása virtuális merevlemezről ugyanazon vagy egy másik előfizetésben](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy felügyelt lemezt egy speciális virtuális merevlemezről operációsrendszer-lemezként, vagy egy adatlemezről adatlemezként, ugyanabban vagy egy másik előfizetésben.  |
| [Felügyelt lemez létrehozása pillanatképből](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy felügyelt lemezt egy pillanatképből. |
| [Felügyelt lemez másolása ugyanabba vagy egy másik előfizetésbe](../scripts/virtual-machines-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Átmásol egy felügyelt lemezt ugyanarra a vagy egy másik előfizetésre, amely ugyanabban a régióban található, mint a szülő felügyelt lemez.
| [Pillanatkép exportálása VHD-ként egy tárfiókba](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Felügyelt pillanatképet exportál virtuális merevlemezként egy másik régióban lévő Storage-fiókba. |
| [Felügyelt lemez VHD-jének exportálása egy tárfiókba](../scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Felügyelt lemez mögöttes VHD-fájljának exportálása egy másik régióban lévő Storage-fiókba. |
| [Pillanatkép létrehozása VHD-ből](../scripts/virtual-machines-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy pillanatképet egy virtuális merevlemezről, majd ezt a pillanatképet használja több azonos felügyelt lemez gyors létrehozásához.  |
| [Pillanatkép másolása ugyanabba vagy egy másik előfizetésbe](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Átmásolja a pillanatképet ugyanarra a vagy egy másik előfizetésre, amely ugyanabban a régióban található, mint a szülő pillanatkép. |
|**Virtuális gépek védelme**||
| [Virtuális gép és adatlemezeinek titkosítása](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Létrehoz egy Azure Key vaultot, egy titkosítási kulcsot és egy egyszerű szolgáltatást, majd titkosítja a virtuális gépet. |
|**Virtuális gépek figyelése**||
| [Virtuális gép figyelése Azure Monitor](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy virtuális gépet, telepíti az Azure Log Analytics ügynököt, és regisztrálja a virtuális gépet egy Log Analytics munkaterületen.  |
| [Az előfizetésben található összes virtuális gép adatainak összegyűjtése a PowerShell használatával](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Létrehoz egy CSV-t, amely tartalmazza a virtuális gép nevét, az erőforráscsoport nevét, a régiót, a Virtual Network, az alhálózatot, a magánhálózati IP-címet, az operációs rendszer típusát és a virtuális gépek nyilvános IP-címét a megadott előfizetésben
| | |
