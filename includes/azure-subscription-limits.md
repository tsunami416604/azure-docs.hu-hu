---
title: fájl belefoglalása
description: fájl belefoglalása
services: billing
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: f49d91a2dffbd02067efd82b88963b5cb6eaa8b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76020965"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| vCPU-k [előfizetésenként](../articles/billing-buy-sign-up-azure-subscription.md)<sup>1</sup> |20 |10,000 |
| [Társadminisztrátorok](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) előfizetésenként |200 |200 |
| [Tárfiókok](../articles/storage/common/storage-create-storage-account.md) előfizetésenként<sup>2</sup> |100 |100 |
| [Felhőszolgáltatások](../articles/cloud-services/cloud-services-choose-me.md) előfizetésenként |20 |200 |
| [Helyi hálózatok](/previous-versions/azure/reference/jj157100(v=azure.100)) előfizetésenként |10 |500 |
| DNS-kiszolgálók előfizetésenként |9 |100 |
| Fenntartott IP-k előfizetésenként |20 |100 |
| [Affinitáscsoportok](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) előfizetésenként |256 |256 |
| Az előfizetés nevének hossza (karakterek) | 64 | 64 |

<sup>1 1</sup> Az extra kis példányok egy vCPU-nak számítanak a vCPU-korlát felé, annak ellenére, hogy részleges CPU-magot használnak.

<sup>2.</sup> A tárfiók-korlát standard és prémium szintű tárfiókokat is tartalmaz. 

