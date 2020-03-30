---
title: Az Azure dedikált gazdagépeinek áttekintése virtuális gépekhez
description: További információ arról, hogyan használhatók az Azure dedikált állomásai a virtuális gépek üzembe helyezéséhez.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251555"
---
# <a name="azure-dedicated-hosts"></a>Dedikált Azure-állomások

Az Azure Dedicated Host egy olyan szolgáltatás, amely egy Azure-előfizetésnek szentelt fizikai kiszolgálókat biztosít – amely képes egy vagy több virtuális gép üzemeltetésére. A dedikált gazdagépek ugyanazok a fizikai kiszolgálók, amelyeket adatközpontjainkban használnak, és erőforrásként szolgálnak. Egy régión, rendelkezésre állási zónán és tartalék tartományon belül dedikált állomásokat hozhat létre. Ezután a virtuális gépeket közvetlenül a kiépített gazdagépekbe helyezheti, bármilyen konfigurációban, amely a legjobban megfelel az igényeinek.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>További lépések

- Az [Azure PowerShell](dedicated-hosts-powershell.md), a [portál](dedicated-hosts-portal.md)és az [Azure CLI](../linux/dedicated-hosts-cli.md)használatával dedikált gazdatként helyezhet üzembe.

- Van egy mintasablon, amely zónákat és tartalék tartományokat is használ a maximális rugalmasság érdekében egy régióban. [here](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)

- Az [Azure-ra fenntartott állomások fenntartott példányával](../prepay-dedicated-hosts-reserved-instances.md)is megtakaríthatja a költségeket.
