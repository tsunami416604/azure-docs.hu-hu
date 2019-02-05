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
ms.openlocfilehash: 23c25953d2f493d2dd799bfd11dbbb69db002d1b
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55736238"
---
| Erőforrás | Alapértelmezett korlát |
| --- | :--- |
| Fürtök maximális száma előfizetésenként | 100 |
| Csomópontok maximális száma fürtönként | 100 |
| Csomópontonkénti maximális podok: [Alapszintű hálózatkezelési] [ basic-networking] Kubenet az | 110 |
| Csomópontonkénti maximális podok: [Speciális hálózatkezelés] [ advanced-networking] az Azure CNI | Az Azure CLI-telepítés: 30<sup>1</sup><br />Resource Manager-sablon: 30<sup>1</sup><br />Portál központi telepítése: 30 |

<sup>1</sup> Amikor az Azure CLI-vel vagy a Resource Manager-sablonnal helyez üzembe egy AKS-fürtöt, ez az érték akár **csomópontonként 110 pod** is lehet. Ha már üzembe helyezett egy AKS-fürtöt, vagy ha egy fürtöt az Azure Portalon helyez üzembe, nem konfigurálhatja a podok csomópontonkénti maximális számát.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
