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
ms.openlocfilehash: a2729af6a689daa551fc01f585324d53a8770a9b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66143001"
---
| Resource | Alapértelmezett korlát |
| --- | :--- |
| Előfizetésenként legfeljebb fürtök | 100 |
| Csomópontok maximális száma fürtönként | 100 |
| Csomópontonkénti maximális podok: [Alapszintű hálózatkezelési] [ basic-networking] Kubenet az | 110 |
| Csomópontonkénti maximális podok: [Speciális hálózatkezelés] [ advanced-networking] az Azure Container hálózati adapter | Az Azure CLI-telepítés: 30<sup>1</sup><br />Az Azure Resource Manager-sablon: 30<sup>1</sup><br />Portál központi telepítése: 30 |

<sup>1</sup>központi telepítésekor egy Azure Kubernetes Service (AKS)-fürtöt az Azure CLI vagy a Resource Manager-sablonnal, értéke legfeljebb 250 podok száma csomópontonként konfigurálható. Csomópontonkénti maximális podok után már telepített egy AKS-fürtöt, vagy ha a fürt üzembe helyezése az Azure portal használatával nem konfigurálható.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
