---
title: A Virtual Machines Azure dedikált gazdagépek áttekintése
description: További információ arról, hogyan használhatók az Azure dedikált gazdagépek a virtuális gépek telepítéséhez.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: 39d1c0ddb4961800e889346ec110ca629ae73546
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251555"
---
# <a name="azure-dedicated-hosts"></a>Dedikált Azure-gazdagépek

Az Azure dedikált gazdagép olyan szolgáltatás, amely fizikai kiszolgálókat biztosít, amelyek egy vagy több virtuális gép üzemeltetésére alkalmasak egyetlen Azure-előfizetéshez. A dedikált gazdagépek ugyanazok a fizikai kiszolgálók, amelyek az adatközpontokban, erőforrásként vannak megadva. Dedikált gazdagépeket a régión, a rendelkezésre állási zónán és a tartalék tartományon belül is kiépítheti. Ezután elhelyezheti a virtuális gépeket közvetlenül a kiépített gazdagépeken, bármilyen konfigurációban, amely a legjobban megfelel az igényeinek.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Következő lépések

- A dedikált gazdagépeket [Azure PowerShell](dedicated-hosts-powershell.md), a [portál](dedicated-hosts-portal.md)és az [Azure CLI](../linux/dedicated-hosts-cli.md)használatával helyezheti üzembe.

- [Itt](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)található egy minta sablon, amely mindkét zónát és tartalék tartományt használja a maximális rugalmasság érdekében egy régióban.

- A költségeket az [Azure dedikált gazdagépek fenntartott példányával](../prepay-dedicated-hosts-reserved-instances.md)is elvégezheti.
