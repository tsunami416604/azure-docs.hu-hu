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
ms.openlocfilehash: c6ef868d80e628f9120acc0775179e89ccd03674
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45979050"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| / vcpu-k [előfizetés](../articles/billing-buy-sign-up-azure-subscription.md) <sup>1</sup> |20 |10,000 |
| [Társadminisztrátorok](../articles/billing-add-change-azure-subscription-administrator.md) előfizetésenként |200 |200 |
| [Storage-fiókok](../articles/storage/common/storage-quickstart-create-account.md) régiónként és előfizetésenként<sup>2</sup> |200 |250 |
| [Felhőszolgáltatások](../articles/cloud-services/cloud-services-choose-me.md) előfizetésenként |20 |200 |
| [Helyi hálózatok](http://msdn.microsoft.com/library/jj157100.aspx) előfizetésenként |10 |500 |
| SQL Database-kiszolgálók száma előfizetésenként |6 |200 |
| DNS-kiszolgálók száma előfizetésenként |9 |100 |
| Fenntartott IP-címek száma előfizetésenként |20 |100 |
| Üzemeltetett szolgáltatási tanúsítványok száma előfizetésenként |199 |199 |
| [Affinitáscsoportok](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) előfizetésenként |256 |256 |


<sup>1</sup>extra Small-példányok száma felé a vCPU-korlátot annak ellenére, hogy egy részleges CPU-magot használ egy vCPU-t.

<sup>2</sup>a storage-fiók korlátot tartalmaz a Standard és prémium szintű storage-fiókok. Ha egy adott régióban több mint 200 tárfiókra van szüksége, győződjön meg a kérést [Azure-támogatási](https://azure.microsoft.com/support/faq/). Az Azure Storage csapata áttekinti az üzleti esetekhez, és jóváhagyhat legfeljebb 250 tárfiókot az adott régió. 

