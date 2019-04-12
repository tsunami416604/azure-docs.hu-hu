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
ms.openlocfilehash: e8fe694cc757f5bcb9cf470f17306e8aa0028744
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502854"
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
| Előfizetés nevének hossza (karakter) | 64 | 64 |

<sup>1</sup>felé a vCPU-korlátot annak ellenére, hogy egy részleges CPU-magot használ egy vCPU nagyon kis méretű példányok számát.

<sup>2</sup>a storage-fiók korlátot tartalmaz a Standard és prémium szintű storage-fiókok. 

