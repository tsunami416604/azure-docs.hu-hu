---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6b4678b381e769993b01bbedd1cb4c0aeefc0cc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335058"
---
| Erőforrás | Korlát |
| --- | :--- |
| Fürtök maximális időtartama előfizetésenként | 100 |
| Fürtenként i maximális csomópontok a virtuális gép rendelkezésre állási készleteivel és az alapszintű terheléselosztó termékváltozattal  | 100 |
| Fürtenként i maximális csomópontok virtuálisgép-méretezési készletekkel és [standard terheléselosztó termékváltozattal][standard-load-balancer] | 1000 [(csomópontonként][node-pool]100 csomópont) |
| Maximális podok csomópontonként: [Alapszintű hálózatkezelés a][basic-networking] Kubenet-tel | 110 |
| Podok maximális száma csomópontonként: [Speciális hálózatkezelés az][advanced-networking] Azure Container Networking Interface-el | Azure CLI üzemelő példány: 30<sup>1</sup><br />Azure Resource Manager sablon: 30<sup>1</sup><br />Portálon keresztüli üzembe helyezés: 30 |

<sup>1 1</sup> Ha egy Azure Kubernetes-szolgáltatás (AKS) fürtaz Azure CLI vagy egy Resource Manager-sablon, ez az érték konfigurálható akár 250 podok csomópontonként. Nem konfigurálhatja a maximális podok csomópontonként, miután már üzembe helyezett egy AKS-fürt, vagy ha egy fürt üzembe helyezése az Azure Portalon keresztül.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
