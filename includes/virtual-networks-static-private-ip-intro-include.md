---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 5c1caf87f32ddd827b85263ee634d3e15d821124
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52270375"
---
Az iaas-beli virtuális gépeken (VM) és PaaS szerepkörpéldányok egy virtuális hálózaton automatikusan egy magánhálózati IP-címet a tartományból kapnak egy Ön által megadott, az alhálózathoz csatlakoznak alapján. A cím megőrzi a virtuális gépek és szerepkörpéldányok, mindaddig, amíg azok leselejtezésekor. Egy virtuális gép vagy szerepkörpéldány példány szerelje le a PowerShell, az Azure CLI vagy az Azure Portalon leállításával. Ezekben az esetekben a virtuális gép vagy szerepkörpéldány példány indul újra, miután azt érkezik elérhető IP-cím az Azure-infrastruktúra, amely nem lehet azonos a korábban. Ha leállítja a virtuális gép vagy szerepkörpéldány példány a vendég operációs rendszerből, megtartja a kellett az IP-címet.  

Bizonyos esetekben érdemes egy virtuális gép vagy szerepkörpéldány példány van statikus IP-címet, például, ha a virtuális gép DNS futtatni, vagy egy tartományvezérlő lesz. Ehhez egy statikus magánhálózati IP-cím beállítása.

