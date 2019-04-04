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
ms.openlocfilehash: 188cb21688ee092db426e51c6ae327def3ff02d0
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58919247"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| / vcpu-k [előfizetés](../articles/billing-buy-sign-up-azure-subscription.md)<sup>1</sup> |20 |10,000 |
| [Társrendszergazdák](../articles/billing-add-change-azure-subscription-administrator.md) előfizetésenként |200 |200 |
| [Storage-fiókok](../articles/storage/common/storage-create-storage-account.md) előfizetésenként<sup>2</sup> |100 |100 |
| [Felhőszolgáltatások](../articles/cloud-services/cloud-services-choose-me.md) előfizetésenként |20 |200 |
| [Helyi hálózatok](/previous-versions/azure/reference/jj157100(v=azure.100)) előfizetésenként |10 |500 |
| DNS-kiszolgálók száma előfizetésenként |9 |100 |
| Fenntartott IP-címek száma előfizetésenként |20 |100 |
| [Affinitáscsoportok](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) előfizetésenként |256 |256 |


<sup>1</sup>felé a vCPU-korlátot annak ellenére, hogy egy részleges CPU-magot használ egy vCPU nagyon kis méretű példányok számát.

<sup>2</sup>a storage-fiók korlátot tartalmaz a Standard és prémium szintű storage-fiókok. 

