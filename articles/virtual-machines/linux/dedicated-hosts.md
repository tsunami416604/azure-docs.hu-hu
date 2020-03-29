---
title: Az Azure dedikált gazdagépeinek áttekintése virtuális gépekhez
description: További információ arról, hogyan használhatók az Azure dedikált állomásai a virtuális gépek üzembe helyezéséhez.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: c19b3410e106aaf5fed53aba45d06eac6dd9d601
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970731"
---
# <a name="azure-dedicated-hosts"></a>Dedikált Azure-állomások

Az Azure Dedicated Host egy olyan szolgáltatás, amely egy Azure-előfizetésnek szentelt fizikai kiszolgálókat biztosít – amely képes egy vagy több virtuális gép üzemeltetésére. A dedikált gazdagépek ugyanazok a fizikai kiszolgálók, amelyeket adatközpontjainkban használnak, és erőforrásként szolgálnak. Egy régión, rendelkezésre állási zónán és tartalék tartományon belül dedikált állomásokat hozhat létre. Ezután a virtuális gépeket közvetlenül a kiépített gazdagépekbe helyezheti, bármilyen konfigurációban, amely a legjobban megfelel az igényeinek.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>További lépések

- Az [Azure CLI](dedicated-hosts-cli.md), a [portal](dedicated-hosts-portal.md)és a [PowerShell](../windows/dedicated-hosts-powershell.md)használatával dedikált gazdatként helyezhet üzembe.

- További információt a [Dedikált állomások](dedicated-hosts.md) áttekintése című témakörben talál.

- Van egy mintasablon, amely zónákat és tartalék tartományokat is használ a maximális rugalmasság érdekében egy régióban. [here](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)

- Az [Azure-ra fenntartott állomások fenntartott példányával](../prepay-dedicated-hosts-reserved-instances.md)is megtakaríthatja a költségeket.
