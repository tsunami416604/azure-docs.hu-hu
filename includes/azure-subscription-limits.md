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
ms.openlocfilehash: d8fa510d8a7eb7d9f5a0e9d3c7475dc76fe8207d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553603"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| / vcpu-k [előfizetés](../articles/billing-buy-sign-up-azure-subscription.md)<sup>1</sup> |20 |10,000 |
| [Társrendszergazdák](../articles/billing-add-change-azure-subscription-administrator.md) előfizetésenként |200 |200 |
| [Storage-fiókok](../articles/storage/common/storage-create-storage-account.md) előfizetésenként<sup>2</sup> |100 |100 |
| [Felhőszolgáltatások](../articles/cloud-services/cloud-services-choose-me.md) előfizetésenként |20 |200 |
| [Helyi hálózatok](https://msdn.microsoft.com/library/jj157100.aspx) előfizetésenként |10 |500 |
| DNS-kiszolgálók száma előfizetésenként |9 |100 |
| Fenntartott IP-címek száma előfizetésenként |20 |100 |
| [Affinitáscsoportok](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) előfizetésenként |256 |256 |


<sup>1</sup>felé a vCPU-korlátot annak ellenére, hogy egy részleges CPU-magot használ egy vCPU nagyon kis méretű példányok számát.

<sup>2</sup>a storage-fiók korlátot tartalmaz a Standard és prémium szintű storage-fiókok. 

