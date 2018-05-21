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
ms.openlocfilehash: 8f104ab046fad8dfd9086525b8f942f3d2941821
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Processzormag [előfizetés](../articles/billing-buy-sign-up-azure-subscription.md) <sup>1</sup> |20 |10,000 |
| [Társadminisztrátorok](../articles/billing-add-change-azure-subscription-administrator.md) előfizetésenként |200 |200 |
| [Storage-fiókok](../articles/storage/common/storage-create-storage-account.md) régiónként<sup>2</sup> |200 |250 |
| [Felhőszolgáltatások](../articles/cloud-services/cloud-services-choose-me.md) előfizetésenként |20 |200 |
| [Helyi hálózatok](http://msdn.microsoft.com/library/jj157100.aspx) előfizetésenként |10 |500 |
| SQL Database-kiszolgálók előfizetésenként |6 |200 |
| DNS-kiszolgálók előfizetésenként |9 |100 |
| Fenntartott IP-címek előfizetésenként |20 |100 |
| Üzemeltetett szolgáltatás tanúsítványok előfizetésenként |199 |199 |
| [Affinitáscsoportok](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) előfizetésenként |256 |256 |


<sup>1</sup>extra Small-példányok száma szerint több processzormag, annak ellenére, hogy egy részleges core segítségével az alapvető határérték felé számolnak.

<sup>2</sup>fiók méretkorlátot magában foglalja a Standard és prémium szintű storage-fiókok. Ha több mint 200 storage-fiókok egy régió van szüksége, ellenőrizze a kérelmet [Azure támogatási](https://azure.microsoft.com/support/faq/). Az Azure Storage csapata kiértékeli az Ön vállalkozását, és jóváhagyhat legfeljebb 250 tárfiókot. 

