---
title: A Virtual Machines Azure dedikált gazdagépek áttekintése | Microsoft Docs
description: További információ arról, hogyan használhatók az Azure dedikált gazdagépek a virtuális gépek telepítéséhez.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/25/2019
ms.author: cynthn
ms.openlocfilehash: 011bfeb337f3c04b2d9041abedac50affe1f86b0
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977261"
---
# <a name="preview-azure-dedicated-hosts"></a>Előzetes verzió: Dedikált Azure-gazdagépek

Az Azure dedikált gazdagép olyan szolgáltatás, amely fizikai kiszolgálókat biztosít, amelyek egy vagy több virtuális gép üzemeltetésére alkalmasak egyetlen Azure-előfizetéshez. A dedikált gazdagépek ugyanazok a fizikai kiszolgálók, amelyek az adatközpontokban, erőforrásként vannak megadva. Dedikált gazdagépeket a régión, a rendelkezésre állási zónán és a tartalék tartományon belül is kiépítheti. Ezután elhelyezheti a virtuális gépeket közvetlenül a kiépített gazdagépeken, bármilyen konfigurációban, amely a legjobban megfelel az igényeinek.

[!INCLUDE [virtual-machines-common-dedicated-hosts-preview](../../../includes/virtual-machines-common-dedicated-hosts-preview.md)]

[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>További lépések

- A dedikált gazdagépeket [Azure PowerShell](dedicated-hosts-powershell.md), a [portál](dedicated-hosts-portal.md)és az [Azure CLI](../linux/dedicated-hosts-cli.md)használatával helyezheti üzembe.

- [Itt](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)található egy minta sablon, amely mindkét zónát és tartalék tartományt használja a maximális rugalmasság érdekében egy régióban.
