---
title: Az Azure virtuális gép PowerShell-mintái
description: Az Azure virtuális gép PowerShell-mintái
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035251"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Az Azure Virtuális gép PowerShell-mintái

Az alábbi táblázat a PowerShell-parancsfájlok mintáira mutató hivatkozásokat tartalmaz, amelyek Linux-virtuális gépeket hoznak létre és kezelnek.

| | |
|---|---|
|**Virtuális gépek létrehozása**||
| [Teljes konfigurációjú virtuális gép létrehozása](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy erőforráscsoportot, virtuális gépet és az összes kapcsolódó erőforrást.|
| [Virtuális gép létrehozása a Docker engedélyezve](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Virtuális gépet hoz létre, konfigurálja ezt a virtuális gépet Docker-gazdagépként, és egy NGINX-tárolót futtat. |
| [Virtuális gép létrehozása és konfigurációs parancsfájl futtatása](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Virtuális gépet hoz létre, és az Azure Custom Script bővítmény t használja az NGINX telepítéséhez. |
| [Virtuális gép létrehozása telepítve a WordPress programmal](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy virtuális gépet, és az Azure Custom Script bővítmény használatával telepíti a WordPress. |
| [Virtuális gép létrehozása felügyelt operációsrendszer-lemezről](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Virtuális gépet hoz létre egy meglévő felügyelt lemez operációsrendszer-lemezként való csatolásával. |
| [Virtuális gép létrehozása pillanatképből](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Virtuális gépet hoz létre pillanatképből úgy, hogy először létrehoz egy felügyelt lemezt a pillanatképből, majd csatolja az új felügyelt lemezt operációsrendszer-lemezként. |
|**Tárolás kezelése**||
| [Felügyelt lemez létrehozása virtuális merevlemezről ugyanabban vagy egy másik előfizetésben](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Felügyelt lemezt hoz létre egy speciális virtuális merevlemezről operációs rendszerlemezként, vagy adatvirtuális merevlemezből adatlemezként, ugyanabban vagy egy másik előfizetésben.  |
| [Felügyelt lemez létrehozása pillanatképből](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Felügyelt lemezt hoz létre pillanatképből. |
| [Pillanatkép exportálása VHD-ként egy tárfiókba](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | A felügyelt pillanatképet virtuális merevlemezként exportálja egy másik régióban lévő tárfiókba. |
| [Felügyelt lemez VHD-jének exportálása egy tárfiókba](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | A felügyelt lemez alapjául szolgáló virtuális merevlemezt egy másik régióban lévő tárfiókba exportálja. |
| [Pillanatkép létrehozása VHD-ből](../scripts/virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Pillanatképet hoz létre egy virtuális merevlemezről, majd ezt a pillanatképet használva gyorsan több azonos felügyelt lemezt hoz létre.  |
| [Pillanatkép másolása ugyanarra vagy egy másik előfizetésre](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | A pillanatképet ugyanabba vagy egy másik előfizetésbe másolja, amely ugyanabban a régióban van, mint a szülő pillanatkép. |
|**Virtuális gépek figyelése**||
| [Virtuális gép figyelése az Azure Monitor-naplókkal](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy virtuális gépet, telepíti a Log Analytics-ügynököt, és beállítja a virtuális gépet egy Log Analytics-munkaterületen.  |
| [Felügyelt lemez másolása ugyanarra vagy egy másik előfizetésre](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | A felügyelt lemezt ugyanabba vagy egy másik előfizetésbe másolja, amely ugyanabban a régióban található, mint a szülő által felügyelt lemez.
| [Az előfizetésben lévő összes virtuális gép részleteinek összegyűjtése a PowerShell segítségével](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy csv-t, amely tartalmazza a virtuális gép nevét, erőforráscsoport nevét, régióját, virtuális hálózatát, alhálózatát, privát IP-címét, operációs rendszer típusát és nyilvános IP-címét a virtuális gépeknek a megadott előfizetésben.
| | |
