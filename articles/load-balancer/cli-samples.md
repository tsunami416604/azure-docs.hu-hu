---
title: Terheléselosztó az Azure CLI-minták |} A Microsoft Docs
description: Azure CLI-minták
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: kumud
ms.openlocfilehash: 41e80e04051a24fd32086c61c65bc3eec1564c57
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35923447"
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

