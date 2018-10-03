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
ms.date: 11/30/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 564e8e2968f31b9e2aef8f7eadbf848885037ccb
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48040762"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure virtuális gépek PowerShell-minták

Az alábbi táblázat, amely a Windows virtuális gépek létrehozása és kezelése PowerShell-mintaszkriptekre mutató hivatkozásokat tartalmaz.

| | |
|---|---|
|**Virtuális gépek létrehozása**||
| [Virtuális gép gyors létrehozása](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy erőforráscsoportot, virtuális gép és minden kapcsolódó erőforrás utasításokat minimális.|
| [Teljes konfigurációjú virtuális gép létrehozása](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy erőforráscsoportot, virtuális gép és minden kapcsolódó erőforrás.|
| [Magas rendelkezésre állású virtuális gépek létrehozása](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Több magas rendelkezésre állású virtuális gépek és az elosztott terhelésű konfigurációs hoz létre.|
| [Hozzon létre egy virtuális Gépet, és konfigurációs parancsfájl futtatása](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy virtuális gépet, és használja az Azure egyéni szkriptek bővítménye az IIS telepítéséhez. |
| [Hozzon létre egy virtuális Gépet, és futtassa a DSC-konfiguráció](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy virtuális gépet és az Azure Desired State Configuration (DSC) bővítmény segítségével telepítse az IIS szolgáltatást. |
| [VHD feltöltése és virtuális gépek létrehozása](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Feltölt egy helyi VHD-fájlt az Azure-ba, hoz létre, és a virtuális Merevlemezt a rendszerkép és majd létrehoz egy virtuális Gépet abból a rendszerképből. |
| [Virtuális gép létrehozása egy felügyelt operációsrendszer-lemez](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy virtuális gép operációsrendszer-lemezként egy meglévő felügyelt lemez csatlakoztatásával. |
| [Virtuális gép létrehozása pillanatképből](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy virtuális gépet egy pillanatképből, először hoz létre egy felügyelt lemezt pillanatképből, és ezután csatolja az új felügyelt lemez operációsrendszer-lemezként. |
|**Tárhely kezelése**||
| [Felügyelt lemez létrehozása azonos vagy eltérő előfizetésben található virtuális merevlemezből](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy felügyelt lemezt egy operációsrendszer-lemezként speciális virtuális merevlemezből vagy adatlemezről adatlemezként azonos vagy eltérő előfizetésben található virtuális Merevlemezt.  |
| [Felügyelt lemez létrehozása pillanatképből](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy felügyelt lemezt egy pillanatképből. |
| [Felügyelt lemez másolása azonos vagy eltérő előfizetéshez](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Másolja a felügyelt lemez azonos vagy eltérő előfizetéshez, de ugyanabban a régióban, mint a szülő felügyelt lemez. 
| [A pillanatkép exportálása merevlemezként egy storage-fiókba](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Egy felügyelt pillanatképet exportál merevlemezként egy másik régióban lévő tárfiókhoz. |
| [A felügyelt lemez VHD exportálás tárfiókba](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Exportálja a mögöttes VHD-t egy felügyelt lemez egy másik régióban lévő tárfiókhoz. |
| [Pillanatkép létrehozása virtuális merevlemezből](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Pillanatkép a virtuális merevlemezről több azonos felügyelt lemez létrehozása pillanatképből rövid időn belül hoz létre.  |
| [Azonos vagy eltérő előfizetéshez pillanatkép másolása](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Pillanatkép példányban, azonos vagy eltérő előfizetéshez, de a szülőpillanatkép ugyanabban a régióban. |
|**Virtuális gépek védelme**||
| [A virtuális gép és az adatlemezek titkosítása](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Létrehoz egy Azure Key Vault, a titkosítási kulcsot és az egyszerű szolgáltatás, majd a virtuális gép titkosítja. |
|**Virtuális gépek figyelése**||
| [Az Operations Management Suite egy virtuális gép figyelése](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy virtuális gépet, telepíti az Operations Management Suite-ügynököt, és regisztrálja a virtuális Gépet egy OMS-munkaterületen.  |
| | |

