---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 083d59a2d8720a08abc7ea8998c1fbe048db3f28
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553636"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| [Virtuális gépek](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) felhőszolgáltatásonként<sup>1</sup> |50 |50 |
| Bemeneti végpontok felhőszolgáltatásonként<sup>2</sup> |150 |150 |

<sup>1</sup>helyett az Azure Resource Manager használatával a klasszikus üzemi modellel létrehozott virtuális gépek automatikusan vannak tárolva egy felhőalapú szolgáltatás. További virtuális gépeket adhat hozzá, amely a felhőszolgáltatás, terheléselosztás és a rendelkezésre állás érdekében. 

<sup>2</sup>bemeneti végpontok engedélyezik a kommunikációt egy virtuális géphez a virtuális gép felhőszolgáltatás kívül. Virtuális gépek ugyanazon felhőalapú szolgáltatás, vagy a virtuális hálózat automatikusan képes kommunikálni egymással. További információkért lásd: [beállítása a virtuális gép végpontjainak](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
