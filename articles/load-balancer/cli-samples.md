---
title: Terheléselosztó az Azure CLI-minták
titlesuffix: Azure Load Balancer
description: Azure CLI-minták
services: load-balancer
documentationcenter: load-balancer
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: kumud
ms.openlocfilehash: b66a61be8995806db60effcaf6c1c92ce6359164
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084736"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Terheléselosztó az Azure CLI-minták

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

| | |
|-|-|
| [Bejövő forgalmának terheléselosztása virtuális gépek magas rendelkezésre állás érdekében](./scripts/load-balancer-linux-cli-sample-nlb.md) | Több magas rendelkezésre állású virtuális gépek és az elosztott terhelésű konfigurációs hoz létre. |
| [Virtuális gépek terheléselosztása rendelkezésre állási zónák között](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Zónaredundáns előtérbeli IP-címmel egy régióban, valamint a Standard Load Balancer különböző rendelkezésre állási zónák három virtuális gépet hoz létre. A terheléselosztó-konfiguráció segít megvédeni alkalmazásait és adatait nem valószínű, hiba vagy egy teljes adatközpontot elvesztését. |
|[Virtuális gépek terheléselosztása adott rendelkezésre állási zónán belül](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Standard Load Balancer zónaszintű előtérbeli IP-címmel, amely segít az adatok elérési útja és erőforrások adott régió egy zóna igazítása három virtuális gépet hoz létre.|
| [A virtuális gépek több webhely terheléselosztását kezelheti](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Két virtuális gépet hoz létre több IP-konfiguráció, csatlakozik az Azure rendelkezésre állási csoport, egy Azure Load Balanceren keresztül érhető el. |
| | |

