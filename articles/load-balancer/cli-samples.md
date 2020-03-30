---
title: Azure CLI-minták a terheléselosztóhoz
titleSuffix: Azure Load Balancer
description: Azure CLI-minták
services: load-balancer
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: allensu
ms.openlocfilehash: f35007bc3c47aeec9bcdd8a418983b95f6f20ad6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225418"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Azure CLI-minták a terheléselosztóhoz

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

| | |
|-|-|
| [Terheléselosztási forgalom a virtuális gépekre a magas rendelkezésre állás érdekében](./scripts/load-balancer-linux-cli-sample-nlb.md) | Több virtuális gépet hoz létre magas rendelkezésre állású és terheléselosztásos konfigurációban. |
| [Virtuális gépek terheléselosztása rendelkezésre állási zónák között](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Három virtuális gépet hoz létre egy régión belül különböző rendelkezésre állási zónákban, és a normál terheléselosztót zónaredundáns előtér IP-címmel. Ez a terheléselosztó-konfiguráció segít megvédeni az alkalmazásokat és az adatokat egy nem valószínű hiba vagy veszteség egy teljes adatközpont. |
|[Virtuális gépek terheléselosztása adott rendelkezésre állási zónán belül](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Három virtuális gépet hoz létre, egy szabványos terheléselosztót zónaszintű előtér-IP-címvel, amely segít az adatelérési út és az erőforrások egyetlen zónában való igazításában egy adott régióban.|
| [Több webhely terheléselosztása a virtuális gépeken](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Két virtuális gépet hoz létre több IP-konfigurációval, amelyek egy Azure-os terheléselosztón keresztül elérhető Azure rendelkezésre állási csoporthoz csatlakoznak. |
| | |

