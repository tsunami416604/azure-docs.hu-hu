---
title: Azure-beli virtuális gépek PowerShell-mintái
description: Azure-beli virtuális gépek PowerShell-mintái
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: 903860dfd1e30f7941770efd759227a1349d41ad
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035251"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure-beli virtuális gépek PowerShell-mintái

Az alábbi táblázat a Linux rendszerű virtuális gépeket létrehozó és kezelő PowerShell-parancsfájlokra mutató hivatkozásokat tartalmaz.

| | |
|---|---|
|**Virtuális gépek létrehozása**||
| [Teljes mértékben konfigurált virtuális gép létrehozása](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy erőforráscsoportot, egy virtuális gépet és az összes kapcsolódó erőforrást.|
| [Virtuális gép létrehozása a Docker engedélyezésével](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy virtuális gépet, a virtuális gépet Docker-gazdagépként konfigurálja, és egy NGINX-tárolót futtat. |
| [Virtuális gép létrehozása és konfigurációs parancsfájl futtatása](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy virtuális gépet, és az egyéni Azure script bővítmény használatával telepíti az NGINX-et. |
| [Telepített WordPress-alapú virtuális gép létrehozása](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy virtuális gépet, és az egyéni Azure script bővítményt használja a WordPress telepítéséhez. |
| [Virtuális gép létrehozása felügyelt operációsrendszer-lemezről](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy virtuális gépet egy meglévő felügyelt lemez operációsrendszer-lemezként való csatolásával. |
| [Virtuális gép létrehozása pillanatképből](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy virtuális gépet egy pillanatképből, ha először létrehoz egy felügyelt lemezt a pillanatképből, majd az új felügyelt lemezt operációsrendszer-lemezként csatolja. |
|**Tárhely kezelése**||
| [Felügyelt lemez létrehozása virtuális merevlemezről ugyanazon vagy egy másik előfizetésben](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy felügyelt lemezt egy speciális virtuális merevlemezről operációsrendszer-lemezként, vagy egy adatlemezről adatlemezként, ugyanabban vagy egy másik előfizetésben.  |
| [Felügyelt lemez létrehozása pillanatképből](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy felügyelt lemezt egy pillanatképből. |
| [Pillanatkép exportálása virtuális merevlemezként egy Storage-fiókba](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Felügyelt pillanatképet exportál virtuális merevlemezként egy másik régióban lévő Storage-fiókba. |
| [Felügyelt lemez virtuális merevlemezének exportálása Storage-fiókba](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Felügyelt lemez mögöttes VHD-fájljának exportálása egy másik régióban lévő Storage-fiókba. |
| [Pillanatkép létrehozása virtuális merevlemezről](../scripts/virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy pillanatképet egy virtuális merevlemezről, majd ezt a pillanatképet használja több azonos felügyelt lemez gyors létrehozásához.  |
| [Pillanatkép másolása ugyanabba vagy egy másik előfizetésbe](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Átmásolja a pillanatképet ugyanarra a vagy egy másik előfizetésre, amely ugyanabban a régióban található, mint a szülő pillanatkép. |
|**Virtuális gépek figyelése**||
| [Virtuális gép figyelése Azure Monitor naplókkal](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy virtuális gépet, telepíti a Log Analytics ügynököt, és regisztrálja a virtuális gépet egy Log Analytics munkaterületen.  |
| [Felügyelt lemez másolása ugyanabba vagy egy másik előfizetésbe](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Átmásol egy felügyelt lemezt ugyanarra a vagy egy másik előfizetésre, amely ugyanabban a régióban található, mint a szülő felügyelt lemez.
| [Az előfizetésben lévő összes virtuális gép adatainak összegyűjtése a PowerShell-lel](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy CSV-t, amely tartalmazza a virtuális gép nevét, az erőforráscsoport nevét, a régiót, a Virtual Network, az alhálózatot, a magánhálózati IP-címet, az operációs rendszer típusát és a virtuális gépek nyilvános IP-címét a megadott előfizetésben
| | |
