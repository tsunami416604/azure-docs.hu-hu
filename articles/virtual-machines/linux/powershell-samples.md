---
title: Azure virtuális gépek PowerShell-minták |} A Microsoft Docs
description: Azure virtuális gépek PowerShell-minták
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: 53784c3d74f9e6af5f1e84cc098194113e81333b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533357"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure virtuális gépek PowerShell-minták

Az alábbi táblázat, amely létrehozása és kezelése a Linux rendszerű virtuális gépek PowerShell-mintaszkriptekre mutató hivatkozásokat tartalmaz.

| | |
|---|---|
|**Virtuális gépek létrehozása**||
| [Teljes konfigurációjú virtuális gép létrehozása](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy erőforráscsoportot, virtuális gép és minden kapcsolódó erőforrás.|
| [A docker használatával engedélyezve van a virtuális gép létrehozása](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy virtuális gépet, konfigurálja a virtuális gép Docker-gazdagépként és futtat egy NGINX-tároló. |
| [Hozzon létre egy virtuális Gépet, és konfigurációs parancsfájl futtatása](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy virtuális gépet, és használja az Azure egyéni szkriptek bővítménye az NGINX telepítése céljából. |
| [Virtuális gép létrehozása WordPress telepítése](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy virtuális gépet, és a WordPress telepítése az Azure egyéni szkriptek futtatására szolgáló bővítmény használatával. |
| [Virtuális gép létrehozása egy felügyelt operációsrendszer-lemez](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy virtuális gép operációsrendszer-lemezként egy meglévő felügyelt lemez csatlakoztatásával. |
| [Virtuális gép létrehozása pillanatképből](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy virtuális gépet először hoz létre egy felügyelt lemezt a pillanatképből, és majd az operációsrendszer-lemezként az új felügyelt lemez csatolása egy pillanatképből. |
|**Tárhely kezelése**||
| [Felügyelt lemez létrehozása azonos, vagy egy másik előfizetésben található virtuális merevlemezből](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Felügyelt lemez operációsrendszer-lemezként speciális virtuális merevlemezből vagy adatlemezről adatlemezként, ugyanazon a virtuális Merevlemezt vagy egy másik előfizetésben hoz létre.  |
| [Felügyelt lemez létrehozása pillanatképből](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy felügyelt lemezt egy pillanatképből. |
| [Pillanatkép exportálni egy VHD-t egy storage-fiókba](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Egy felügyelt pillanatképet exportál egy merevlemezként egy másik régióban lévő tárfiókhoz. |
| [A felügyelt lemez VHD exportálás tárfiókba](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exportálja a mögöttes VHD-iről egy felügyelt lemezt egy másik régióban lévő tárfiókhoz. |
| [Pillanatkép létrehozása virtuális merevlemezből](../scripts/virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy pillanatképet egy VHD-ből, majd azt több azonos felügyelt lemez gyors létrehozásához.  |
| [Másolja a pillanatképet, de akár egy másik előfizetésben](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Másolja a pillanatkép, de akár egy másik előfizetést, amely a szülőpillanatkép ugyanabban a régióban. |
|**Virtuális gépek figyelése**||
| [A figyelő egy virtuális Gépet az Azure Monitor naplóira](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Létrehoz egy virtuális gépet, telepíti a Log Analytics-ügynököt, és regisztrálja a virtuális gép a Log Analytics-munkaterületen.  |
| [Felügyelt lemez másolása, de akár egy másik előfizetésre](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Másolja át egy felügyelt lemezt, de akár egy másik előfizetést, amely a szülő felügyelt lemez ugyanabban a régióban.
| | |
