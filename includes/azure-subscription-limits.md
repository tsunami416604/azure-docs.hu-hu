---
title: fájlbefoglalás
description: fájlbefoglalás
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: db822722b12921ab98b3e5cae67e28f4ca7ede04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87298877"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| vCPU/ [előfizetés](../articles/billing-buy-sign-up-azure-subscription.md)<sup>1</sup> |20 |10,000 |
| A [rendszergazdák](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) /előfizetés |200 |200 |
| [Storage-fiókok](../articles/storage/common/storage-create-storage-account.md) /előfizetés<sup>2</sup> |100 |100 |
| [Felhőszolgáltatások](../articles/cloud-services/cloud-services-choose-me.md) előfizetésenként |20 |200 |
| [Helyi hálózatok](/previous-versions/azure/reference/jj157100(v=azure.100)) /előfizetés |10 |500 |
| DNS-kiszolgálók/előfizetés |9 |100 |
| Fenntartott IP-címek/előfizetés |20 |100 |
| [Affinitáscsoportok](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) előfizetésenként |256 |256 |
| Előfizetés nevének hossza (karakter) | 64 | 64 |

<sup>1</sup> Az extra kisméretű példányok egy részleges CPU-mag használata ellenére a vCPU-korlát felé vCPU számítanak.

<sup>2</sup> A Storage-fiók korlátja a standard és a Premium Storage-fiókokat is tartalmazza. 

