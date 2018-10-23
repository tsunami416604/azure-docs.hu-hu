---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 4251f379c517d5ccfd0430987e3d5280208590ff
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49400453"
---
| Erőforrás | Alapértelmezett korlát |
| --- | :--- |
| Fürtök maximális száma előfizetésenként | 100 |
| Csomópontok maximális száma fürtönként | 100 |
| Podok maximális száma csomópontonként: [Alapszintű hálózatkezelés][basic-networking] a Kubenet használatával | 110 |
| Podok maximális száma csomópontonként: [Speciális hálózatkezelés][advanced-networking] az Azure CNI használatával | Azure CLI üzemelő példány: 30<sup>1</sup><br />Resource Manager-sablon: 30<sup>1</sup><br />Portálon keresztüli üzembe helyezés: 30 |

<sup>1</sup> Amikor az Azure CLI-vel vagy a Resource Manager-sablonnal helyez üzembe egy AKS-fürtöt, ez az érték akár **csomópontonként 110 pod** is lehet. Ha már üzembe helyezett egy AKS-fürtöt, vagy ha egy fürtöt az Azure Portalon helyez üzembe, nem konfigurálhatja a podok csomópontonkénti maximális számát.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
