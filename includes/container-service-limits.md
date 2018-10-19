---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 08/31/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 71294824bd3dd5215c388cfcd44382c7eee123ad
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48874062"
---
| Erőforrás | Alapértelmezett korlát |
| --- | :--- |
| Fürtök maximális száma előfizetésenként | 100 |
| Csomópontok maximális száma fürtönként | 100 |
| Podok maximális száma csomópontonként: [Alapszintű hálózatkezelés][basic-networking] a Kubenet használatával | 110 |
| Podok maximális száma csomópontonként: [Speciális hálózatkezelés][advanced-networking] az Azure CNI használatával | Azure CLI üzemelő példány: 30<sup>1</sup><br />Resource Manager-sablon: 30<sup>1</sup><br />Portálon keresztüli üzembe helyezés: 30 |

<sup>1</sup> Ez az érték a fürt üzembe helyezésekor konfigurálható, ha AKS-fürtöt helyez üzembe az Azure CLI-vel vagy egy Resource Manager-sablonnal.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
