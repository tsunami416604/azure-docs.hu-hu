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
ms.date: 06/01/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6861399b63b7f06bac7599704a6dd1aa87800ebf
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49403338"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Az Azure CLI minták a Windows virtual machines

A következő táblázat használatával létrehozott bash szkriptekre az Azure parancssori felület, amely a Windows virtuális gépek üzembe helyezése mutató hivatkozásokat tartalmaz.

| | |
|---|---|
|**Virtuális gépek létrehozása**||
| [Virtuális gép létrehozása](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fcli%2fazure%2ftoc.json) | Egy Windows virtuális gépet hoz létre minimális konfigurációval. |
| [Teljes konfigurációjú virtuális gép létrehozása](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy erőforráscsoportot, virtuális gép és minden kapcsolódó erőforrás.|
| [Magas rendelkezésre állású virtuális gépek létrehozása](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fcli%2fazure%2ftoc.json) | Több magas rendelkezésre állású virtuális gépek és az elosztott terhelésű konfigurációs hoz létre. |
| [Hozzon létre egy virtuális Gépet, és konfigurációs parancsfájl futtatása](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy virtuális gépet, és használja az Azure egyéni szkriptek bővítménye az IIS telepítéséhez. |
| [Hozzon létre egy virtuális Gépet, és futtassa a DSC-konfiguráció](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy virtuális gépet és az Azure Desired State Configuration (DSC) bővítmény segítségével telepítse az IIS szolgáltatást. |
|**Hálózati virtuális gépek**||
| [Virtuális gépek közötti hálózati adatforgalom védelme](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fcli%2fazure%2ftoc.json) | Két virtuális gépet, az összes kapcsolódó erőforrást és a egy belső és külső hálózati biztonsági csoportok (NSG) hoz létre. |
|**Virtuális gépek védelme**||
| [A virtuális gép és az adatlemezek titkosítása](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy Azure Key Vault, a titkosítási kulcsot és az egyszerű szolgáltatás, majd a virtuális gép titkosítja. |
|**Virtuális gépek figyelése**||
| [A Log Analytics használatával egy virtuális gép figyelése](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy virtuális gépet, telepíti a Log Analytics-ügynököt, és regisztrálja a virtuális gép a Log Analytics-munkaterületen.  |
| | |
