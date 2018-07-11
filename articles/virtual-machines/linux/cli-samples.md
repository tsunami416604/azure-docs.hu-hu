---
title: Az Azure CLI-minták |} A Microsoft Docs
description: Azure CLI-minták
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
ms.date: 03/08/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 1e4b7027ecbc6e70fadf8c540fa92903d276956b
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931711"
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Linux rendszerű virtuális gépek Azure CLI-minták

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

| | |
|---|---|
|**Virtuális gépek létrehozása**||
| [Virtuális gép létrehozása](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy Linux rendszerű virtuális gép minimális konfigurációval. |
| [Teljes konfigurációjú virtuális gép létrehozása](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy erőforráscsoportot, virtuális gép és minden kapcsolódó erőforrás.|
| [Magas rendelkezésre állású virtuális gépek létrehozása](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fcli%2fazure%2ftoc.json) | Több magas rendelkezésre állású virtuális gépek és az elosztott terhelésű konfigurációs hoz létre. |
| [Hozzon létre egy virtuális Gépet, és konfigurációs parancsfájl futtatása](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy virtuális gépet, és használja az Azure egyéni szkriptek bővítménye az NGINX telepítése céljából. |
| [Virtuális gép létrehozása WordPress telepítése](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy virtuális gépet, és a WordPress telepítése az Azure egyéni szkriptek futtatására szolgáló bővítmény használatával. |
| [Virtuális gép létrehozása egy felügyelt operációsrendszer-lemez](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json) | Létrehoz egy virtuális gép operációsrendszer-lemezként egy meglévő felügyelt lemez csatlakoztatásával. |
| [Virtuális gép létrehozása pillanatképből](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) | Létrehoz egy virtuális gépet egy pillanatképből, először hoz létre egy felügyelt lemezt pillanatképből, és ezután csatolja az új felügyelt lemez operációsrendszer-lemezként. |
|**Tárhely kezelése**||
| [Felügyelt lemez létrehozása VHD-fájlból](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json) | Felügyelt lemez létrehozása speciális virtuális merevlemezből operációsrendszer-lemezként, vagy virtuális adatlemezről adatlemezként.  |
| [Felügyelt lemez létrehozása pillanatképből](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) | Létrehoz egy felügyelt lemezt egy pillanatképből. |
| [Felügyelt lemez másolása azonos vagy eltérő előfizetéshez](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Másolja a felügyelt lemez azonos vagy eltérő előfizetéshez, de ugyanabban a régióban, mint a szülő felügyelt lemez. 
| [A pillanatkép exportálása merevlemezként egy storage-fiókba](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fcli%2fmodule%2ftoc.json) | Egy felügyelt pillanatképet exportál merevlemezként egy másik régióban lévő tárfiókhoz. |
| [Azonos vagy eltérő előfizetéshez pillanatkép másolása](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Pillanatkép példányban, azonos vagy eltérő előfizetéshez, de a szülőpillanatkép ugyanabban a régióban. |
|**Hálózati virtuális gépek**||
| [Virtuális gépek közötti hálózati adatforgalom védelme](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fcli%2fazure%2ftoc.json) | Két virtuális gépet, az összes kapcsolódó erőforrást és a egy belső és külső hálózati biztonsági csoportok (NSG) hoz létre. |
|**Virtuális gépek védelme**||
| [A virtuális gép és az adatlemezek titkosítása](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy Azure Key Vault, a titkosítási kulcsot és az egyszerű szolgáltatás, majd a virtuális gép titkosítja. |
|**Virtuális gépek figyelése**||
| [Az Operations Management Suite egy virtuális gép figyelése](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy virtuális gépet, telepíti az Operations Management Suite-ügynököt, és regisztrálja a virtuális Gépet egy OMS-munkaterületen.  |
|**Virtuális gépek hibaelhárítása**||
| [A virtuális gépek operációsrendszer-lemez hibaelhárítása](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fcli%2fazure%2ftoc.json) | Csatlakoztatja az operációsrendszer-lemezt egy virtuális gépről adatlemezként egy második virtuális gépen. |
| | |
