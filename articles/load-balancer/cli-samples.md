---
title: Terheléselosztó az Azure CLI-minták
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
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225418"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Terheléselosztó az Azure CLI-minták

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

| | |
|-|-|
| [A virtuális gépek forgalmának elosztása magas rendelkezésre állás érdekében](./scripts/load-balancer-linux-cli-sample-nlb.md) | Több magas rendelkezésre állású virtuális gépek és az elosztott terhelésű konfigurációs hoz létre. |
| [Virtuális gépek terheléselosztása rendelkezésre állási zónák között](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Zónaredundáns előtérbeli IP-címmel egy régióban, valamint a Standard Load Balancer különböző rendelkezésre állási zónák három virtuális gépet hoz létre. A terheléselosztó-konfiguráció segít megvédeni alkalmazásait és adatait nem valószínű, hiba vagy egy teljes adatközpontot elvesztését. |
|[Virtuális gépek terheléselosztása adott rendelkezésre állási zónán belül](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Standard Load Balancer zónaszintű előtérbeli IP-címmel, amely segít az adatok elérési útja és erőforrások adott régió egy zóna igazítása három virtuális gépet hoz létre.|
| [Terheléselosztás több webhelyről a virtuális gépeken](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Két virtuális gépet hoz létre több IP-konfiguráció, csatlakozik az Azure rendelkezésre állási csoport, egy Azure Load Balanceren keresztül érhető el. |
| | |

