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
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "67179482"
---
| Resource | Alapértelmezett korlát |
| --- | :--- |
| Fürtök maximális száma előfizetéskor | 100 |
| Csomópontok maximális száma fürt szerint | 100 |
| Hüvelyek maximális száma (csomópont): [Alapszintű hálózatkezelés][basic-networking] a Kubenet | 110 |
| Hüvelyek maximális száma (csomópont): [Speciális hálózatkezelés][advanced-networking] az Azure Container Network Interface felülettel | Azure CLI üzembe helyezése: 30<sup>1</sup><br />Azure Resource Manager sablon: 30<sup>1</sup><br />Portál üzembe helyezése: 30 |

<sup>1</sup> Ha az Azure CLI-vel vagy egy Resource Manager-sablonnal helyez üzembe egy Azure Kubernetes Service (ak) fürtöt, ez az érték legfeljebb 250 hüvelyre konfigurálható. Ha már üzembe helyezett egy AK-fürtöt, a csomópontok maximális száma nem konfigurálható, vagy ha a Azure Portal használatával helyez üzembe egy fürtöt.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
