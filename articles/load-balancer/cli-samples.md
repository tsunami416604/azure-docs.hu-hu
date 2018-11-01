---
title: Terheléselosztó az Azure CLI-minták |} A Microsoft Docs
description: Azure CLI-minták
services: load-balancer
documentationcenter: load-balancer
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: kumud
ms.openlocfilehash: 3973b541a0816473641f11391ddb59ca14618e30
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50415287"
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

