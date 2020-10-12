---
title: fájlbefoglalás
description: fájlbefoglalás
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6b4678b381e769993b01bbedd1cb4c0aeefc0cc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "80335058"
---
| Erőforrás | Korlát |
| --- | :--- |
| Fürtök maximális száma előfizetéskor | 100 |
| Csomópontok maximális száma a virtuális gépek rendelkezésre állási csoportjaival és az alapszintű Load Balancer SKU-val  | 100 |
| Csomópontok maximális száma fürtön Virtual Machine Scale Sets és [standard Load BALANCER SKU][standard-load-balancer] -val | 1000 (100 csomópont/ [csomópont-készlet][node-pool]) |
| Maximális hüvely/csomópont: [alapszintű hálózatkezelés][basic-networking] a Kubenet | 110 |
| A hüvelyek maximális száma/csomópont: [speciális hálózatkezelés][advanced-networking] az Azure Container Network Interface felülettel | Azure CLI üzemelő példány: 30<sup>1</sup><br />Azure Resource Manager sablon: 30<sup>1</sup><br />Portálon keresztüli üzembe helyezés: 30 |

<sup>1</sup> Ha az Azure CLI-vel vagy egy Resource Manager-sablonnal helyez üzembe egy Azure Kubernetes Service (ak) fürtöt, ez az érték legfeljebb 250 hüvelyre konfigurálható. Ha már üzembe helyezett egy AK-fürtöt, a csomópontok maximális száma nem konfigurálható, vagy ha a Azure Portal használatával helyez üzembe egy fürtöt.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
