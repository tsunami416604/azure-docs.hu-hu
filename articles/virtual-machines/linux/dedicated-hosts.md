---
title: A Virtual Machines Azure dedikált gazdagépek áttekintése
description: További információ arról, hogyan használhatók az Azure dedikált gazdagépek a virtuális gépek telepítéséhez.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: ddd08f87fb7847a81b28b18c1dedc1c4f0a7a169
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78246216"
---
# <a name="azure-dedicated-hosts"></a>Dedikált Azure-gazdagépek

Az Azure dedikált gazdagép olyan szolgáltatás, amely fizikai kiszolgálókat biztosít, amelyek egy vagy több virtuális gép üzemeltetésére alkalmasak egyetlen Azure-előfizetéshez. A dedikált gazdagépek ugyanazok a fizikai kiszolgálók, amelyek az adatközpontokban, erőforrásként vannak megadva. Dedikált gazdagépeket a régión, a rendelkezésre állási zónán és a tartalék tartományon belül is kiépítheti. Ezután elhelyezheti a virtuális gépeket közvetlenül a kiépített gazdagépeken, bármilyen konfigurációban, amely a legjobban megfelel az igényeinek.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>Következő lépések

- A dedikált gazdagépeket az [Azure CLI](dedicated-hosts-cli.md), a [portál](dedicated-hosts-portal.md)és a [PowerShell](../windows/dedicated-hosts-powershell.md)használatával helyezheti üzembe.

- További információt a [dedikált gazdagépek](dedicated-hosts.md) áttekintése című témakörben talál.

- [Itt](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)található egy minta sablon, amely mindkét zónát és tartalék tartományt használja a maximális rugalmasság érdekében egy régióban.

- A költségeket az [Azure dedikált gazdagépek fenntartott példányával](../prepay-dedicated-hosts-reserved-instances.md)is elvégezheti.
