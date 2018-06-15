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
ms.openlocfilehash: 3daf0e214c2c10d682e908ca430f4852df105926
ms.sourcegitcommit: caebf2bb2fc6574aeee1b46d694a61f8b9243198
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2018
ms.locfileid: "35414654"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| / Vcpu [előfizetés](../articles/billing-buy-sign-up-azure-subscription.md) <sup>1</sup> |20 |10,000 |
| [Társadminisztrátorok](../articles/billing-add-change-azure-subscription-administrator.md) előfizetésenként |200 |200 |
| [Storage-fiókok](../articles/storage/common/storage-create-storage-account.md) régiónként<sup>2</sup> |200 |250 |
| [Felhőszolgáltatások](../articles/cloud-services/cloud-services-choose-me.md) előfizetésenként |20 |200 |
| [Helyi hálózatok](http://msdn.microsoft.com/library/jj157100.aspx) előfizetésenként |10 |500 |
| SQL Database-kiszolgálók előfizetésenként |6 |200 |
| DNS-kiszolgálók előfizetésenként |9 |100 |
| Fenntartott IP-címek előfizetésenként |20 |100 |
| Üzemeltetett szolgáltatás tanúsítványok előfizetésenként |199 |199 |
| [Affinitáscsoportok](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) előfizetésenként |256 |256 |


<sup>1</sup>extra Small-példányok száma szerint egy vCPU, annak ellenére, hogy egy részleges Processzormagok használatával az vCPU határérték felé számolnak.

<sup>2</sup>fiók méretkorlátot magában foglalja a Standard és prémium szintű storage-fiókok. Ha több mint 200 storage-fiókok egy adott régióban van szüksége, ellenőrizze a kérelmet [Azure támogatási](https://azure.microsoft.com/support/faq/). Az Azure Storage csapat rendszer tekintse át az Ön üzleti esetében, és előfordulhat, hogy jóváhagyásához legfeljebb 250 tárfiókok az adott régió. 

