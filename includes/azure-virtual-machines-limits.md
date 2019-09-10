---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 083d59a2d8720a08abc7ea8998c1fbe048db3f28
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "67179477"
---
| Resource | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| [Virtual Machines](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) /Cloud Service<sup>1</sup> |50 |50 |
| Bemeneti végpontok/Cloud Service<sup>2</sup> |150 |150 |

<sup>1</sup> A Azure Resource Manager helyett a klasszikus üzemi modellel létrehozott virtuális gépeket a rendszer automatikusan egy felhőalapú szolgáltatásban tárolja. További virtuális gépeket adhat hozzá a felhőalapú szolgáltatáshoz a terheléselosztás és a rendelkezésre állás érdekében. 

<sup>2</sup> A bemeneti végpontok lehetővé teszik a virtuális gépeknek a virtuális gép felhőalapú szolgáltatásán kívülről történő kommunikációt. Az azonos felhőalapú szolgáltatásban vagy virtuális hálózaton lévő virtuális gépek automatikusan kommunikálhatnak egymással. További információ: [végpontok beállítása virtuális géphez](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
