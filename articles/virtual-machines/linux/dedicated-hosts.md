---
title: A Virtual Machines Azure dedikált gazdagépek áttekintése
description: További információ arról, hogyan használhatók az Azure dedikált gazdagépek Linux rendszerű virtuális gépek üzembe helyezéséhez.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 07/28/2020
ms.author: cynthn
ms.openlocfilehash: acf79448c0dcb81bbe644be822414a7e51b0ee36
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328156"
---
# <a name="azure-dedicated-hosts-for-virtual-machines"></a>Azure dedikált gazdagépek virtuális gépekhez

Az Azure dedikált gazdagép olyan szolgáltatás, amely fizikai kiszolgálókat biztosít, amelyek egy vagy több virtuális gép üzemeltetésére alkalmasak egyetlen Azure-előfizetéshez. A dedikált gazdagépek ugyanazok a fizikai kiszolgálók, amelyek az adatközpontokban, erőforrásként vannak megadva. Dedikált gazdagépeket a régión, a rendelkezésre állási zónán és a tartalék tartományon belül is kiépítheti. Ezután elhelyezheti a virtuális gépeket közvetlenül a kiépített gazdagépeken, bármilyen konfigurációban, amely a legjobban megfelel az igényeinek.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>Következő lépések

- A dedikált gazdagépeket az [Azure CLI](dedicated-hosts-cli.md), a [portál](dedicated-hosts-portal.md)és a [PowerShell](../windows/dedicated-hosts-powershell.md)használatával helyezheti üzembe.

- További információt a [dedikált gazdagépek](dedicated-hosts.md) áttekintése című témakörben talál.

- [Itt](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)található egy minta sablon, amely mindkét zónát és tartalék tartományt használja a maximális rugalmasság érdekében egy régióban.

- A költségeket az [Azure dedikált gazdagépek fenntartott példányával](../prepay-dedicated-hosts-reserved-instances.md)is elvégezheti.
