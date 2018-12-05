---
title: fájl belefoglalása
description: fájl belefoglalása
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: be75b982d3e63ecb1edd7398e32150156a06995d
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52886401"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| / vcpu-k [előfizetés](../articles/billing-buy-sign-up-azure-subscription.md) <sup>1</sup> |20 |10,000 |
| [Társadminisztrátorok](../articles/billing-add-change-azure-subscription-administrator.md) előfizetésenként |200 |200 |
| [Storage-fiókok](../articles/storage/common/storage-create-storage-account.md) előfizetésenként <sup>2</sup> |100 |100 |
| [Felhőszolgáltatások](../articles/cloud-services/cloud-services-choose-me.md) előfizetésenként |20 |200 |
| [Helyi hálózatok](https://msdn.microsoft.com/library/jj157100.aspx) előfizetésenként |10 |500 |
| DNS-kiszolgálók száma előfizetésenként |9 |100 |
| Fenntartott IP-címek száma előfizetésenként |20 |100 |
| [Affinitáscsoportok](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) előfizetésenként |256 |256 |


<sup>1</sup>extra Small-példányok száma felé a vCPU-korlátot annak ellenére, hogy egy részleges CPU-magot használ egy vCPU-t.

<sup>2</sup>a storage-fiók korlátot tartalmaz a Standard és prémium szintű storage-fiókok. 

