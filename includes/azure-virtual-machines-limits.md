---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 7e390e2134df02b0ca9c0d1752c3207aff7b9314
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279676"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| [Virtuális gépek](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) felhőszolgáltatásonként<sup>1</sup> |50 |50 |
| Bemeneti végpontok felhőszolgáltatásonként<sup>2</sup> |150 |150 |

<sup>1</sup>a Service Management (helyett erőforrás-kezelő) létrehozott virtuális gépek automatikusan vannak tárolva egy felhőalapú szolgáltatás. További virtuális gépeket adhat hozzá, amely a felhőszolgáltatás, terheléselosztás és a rendelkezésre állás érdekében. 

<sup>2</sup>bemeneti végpontok engedélyezik a kommunikációt egy virtuális géphez a virtuális gép felhőszolgáltatás kívül. Virtuális gépek ugyanazon felhőalapú szolgáltatás, vagy a virtuális hálózat automatikusan képes kommunikálni egymással. Lásd: [egy virtuális géphez végpontok beállítása](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
