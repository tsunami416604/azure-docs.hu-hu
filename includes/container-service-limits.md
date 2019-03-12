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
ms.openlocfilehash: 62eb75ef18d3ac81be65783e57c21c0aefd7a429
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554659"
---
| Erőforrás | Alapértelmezett korlát |
| --- | :--- |
| Előfizetésenként legfeljebb fürtök | 100 |
| Csomópontok maximális száma fürtönként | 100 |
| Csomópontonkénti maximális podok: [Alapszintű hálózatkezelési] [ basic-networking] Kubenet az | 110 |
| Csomópontonkénti maximális podok: [Speciális hálózatkezelés] [ advanced-networking] az Azure Container hálózati adapter | Az Azure CLI-telepítés: 30<sup>1</sup><br />Az Azure Resource Manager-sablon: 30<sup>1</sup><br />Portál központi telepítése: 30 |

<sup>1</sup>központi telepítésekor egy Azure Kubernetes Service (AKS)-fürtöt az Azure CLI vagy a Resource Manager-sablonnal, értéke legfeljebb 110 podok száma csomópontonként konfigurálható. Csomópontonkénti maximális podok után már telepített egy AKS-fürtöt, vagy ha a fürt üzembe helyezése az Azure portal használatával nem konfigurálható.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
