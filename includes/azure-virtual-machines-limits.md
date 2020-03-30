---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 2cfd376f595ae70daf9ab468d464dd9c8ff13d74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335011"
---
| Erőforrás | Korlát |
| --- | --- |
| [Virtuális gépek](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) felhőszolgáltatásonként<sup>1</sup> |50 |
| Bemeneti végpontok felhőalapú szolgáltatásonként<sup>2</sup> |150 |

<sup>1 1</sup> Az Azure Resource Manager helyett a klasszikus üzembe helyezési modell használatával létrehozott virtuális gépek automatikusan egy felhőszolgáltatásban tárolódnak. További virtuális gépeket adhat hozzá a felhőszolgáltatáshoz a terheléselosztás és a rendelkezésre állás érdekében. 

<sup>2.</sup> A bemeneti végpontok lehetővé teszik a virtuális géphez a virtuális gép felhőszolgáltatásán kívülről történő kommunikációt. Az azonos felhőszolgáltatásban vagy virtuális hálózatban lévő virtuális gépek automatikusan kommunikálhatnak egymással. További információ: [Végpontok beállítása virtuális gépre című témakörben.](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) 
