---
title: "Az Azure CLI Windows minták |} Microsoft Docs"
description: "Az Azure CLI Windows – minták"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: f4b2e8a5583855df7472af3fbef01ac641caf6bf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Az Azure CLI minták a Windows virtuális gépek

A következő táblázat az Azure parancssori felület használatával készített olyan parancsfájlok, amelyek Windows virtuális gépek telepítése bash mutató hivatkozásokat tartalmaz.

| | |
|---|---|
|**Virtuális gépek létrehozása**||
| [Virtuális gép létrehozása](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fcli%2fazure%2ftoc.json) | A Windows rendszerű virtuális gép minimális konfigurációs hoz létre. |
| [A teljesen konfigurált virtuális gép létrehozása](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások.|
| [Magas rendelkezésre állású virtuális gépek létrehozása](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fcli%2fazure%2ftoc.json) | Több magas rendelkezésre állású virtuális gépek és az elosztott terhelésű konfigurációs hoz létre. |
| [Hozzon létre egy virtuális Gépet, és futtassa a konfigurációs parancsfájl](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy virtuális gépet, és az Azure egyéni parancsprogramok futtatására szolgáló bővítmény segítségével telepítse az IIS szolgáltatást. |
| [Hozzon létre egy virtuális Gépet, és futtassa a DSC-konfiguráció](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy virtuális gépet, és az Azure kívánt állapot konfigurációs szolgáltatása (DSC) bővítmény segítségével telepítse az IIS. |
|**Virtuális gépek hálózati**||
| [Virtuális gépek közötti hálózati forgalmának biztonságossá tétele](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fcli%2fazure%2ftoc.json) | Két virtuális gép minden kapcsolódó erőforrások és egy belső és külső hálózati biztonsági csoportokkal (NSG) hoz létre. |
|**Virtuális gépek védelme**||
| [A Virtuálisgép- és adatlemezek titkosítása](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy Azure Key Vault, a titkosítási kulcs és a szolgáltatás egyszerű, majd a virtuális gépek titkosítja. |
|**Virtuális gépek figyelése**||
| [Virtuális gép és az Operations Management Suite figyelése](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy virtuális gépet, telepíti az Operations Management Suite-ügynököt, és regisztrálja az OMS-munkaterület virtuális gép.  |
| | |
