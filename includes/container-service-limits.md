---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-service
author: mmacy
ms.service: container-service
ms.topic: include
ms.date: 08/31/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 1e8913d31677f3da9b6eb9d2216d8d9ec8b186b4
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666909"
---
| Erőforrás | Alapértelmezett korlát |
| --- | :--- |
| Fürtök maximális száma előfizetésenként | 100 |
| Csomópontok maximális száma fürtönként | 100 |
| Podok maximális száma csomópontonként: [Alapszintű hálózatkezelés][basic-networking] a Kubenet használatával | 110 |
| Podok maximális száma csomópontonként: [Speciális hálózatkezelés][advanced-networking] az Azure CNI használatával | Azure CLI üzemelő példány: 110<sup>1</sup><br />Resource Manager-sablon: 110<sup>1</sup><br />Portálon keresztüli üzembe helyezés: 30 |

<sup>1</sup> Ez az érték a fürt üzembe helyezésekor konfigurálható, ha AKS-fürtöt helyez üzembe az Azure CLI-vel vagy egy Resource Manager-sablonnal.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
