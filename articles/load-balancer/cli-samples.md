---
title: Azure CLI-minták a Load Balancerhoz
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
ms.openlocfilehash: 3198160fe443f4650d8c5a880c3538efeb9346a1
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86205248"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Azure CLI-minták a Load Balancerhoz

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

* [A virtuális gépek forgalmának elosztása magas rendelkezésre állás érdekében](./scripts/load-balancer-linux-cli-sample-nlb.md)

  Több virtuális gépet hoz létre egy magasan elérhető és terheléselosztásos konfigurációban.

* [Virtuális gépek terheléselosztása rendelkezésre állási zónák között](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md)

  Három virtuális gépet hoz létre különböző rendelkezésre állási zónákban a régión belül, és standard Load Balancer egy zóna redundáns előtér-IP-címével. Ez a terheléselosztó-konfiguráció segít az alkalmazások és adatok védelme érdekében egy teljes adatközpont valószínűtlen meghibásodása vagy elvesztése miatt.

* [Virtuális gépek terheléselosztása adott rendelkezésre állási zónán belül](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)

  Három virtuális gépet hoz létre, amely egy olyan standard Load Balancer, amely egy zónabeli előtér-IP-címmel rendelkezik, amely segítségével egyetlen zónában igazíthatja az adatelérési útvonalat és erőforrásokat egy adott régió

* [Terheléselosztás több webhelyről a virtuális gépeken](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md)

  Két, több IP-konfigurációval rendelkező virtuális gépet hoz létre, amelyek egy Azure-beli rendelkezésre állási készlethez csatlakoznak, és egy Azure Load Balanceron keresztül
