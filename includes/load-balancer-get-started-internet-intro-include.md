---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: 459c99d1b45af9c98cc1a6d0d7dd7a9a04c824ec
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179541"
---
Az Azure Load Balancer 4. szintű (TCP, UDP) terheléselosztónak minősül. A terheléselosztó a felhőszolgáltatások vagy virtuális gépek kifogástalan állapotú szolgáltatási példányai között osztja meg a bejövő forgalmat egy terheléselosztói készletben, és ezáltal biztosítja a magas rendelkezésre állást. Az Azure Load Balancer a szolgáltatásokat több portra vagy több IP-címre, illetve portokra és IP-címekre egyaránt továbbíthatja.

A terheléselosztót az alábbi feladatokra lehet konfigurálni:

* Bejövő internetes forgalom terheléselosztása virtuális gépek (VM-ek) között. Ebben a forgatókönyvben a terheléselosztó [internetre kapcsolódó terheléselosztót](../articles/load-balancer/load-balancer-internet-overview.md) jelent.
* Forgalom terheléselosztása virtuális hálózat (VNet) virtuális gépei között, felhőszolgáltatások virtuális gépei között, vagy helyszíni számítógépek és létesítmények közötti virtuális hálózat virtuális gépei között. Ebben a forgatókönyvben a terheléselosztó [belső terheléselosztót (ILB)](../articles/load-balancer/load-balancer-internal-overview.md) jelent.
* Külső forgalom továbbítása egy adott VM-példányhoz.
