---
title: fájl belefoglalása
description: fájl belefoglalása
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 07/02/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: f47654d056a26f5a58b1ccc2c200f774bee1f19f
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37348201"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Virtuális gépek [előfizetésenként](../articles/billing-buy-sign-up-azure-subscription.md) |10 000 <sup>1</sup> régiónként |10 000 régiónként |
| Virtuálisgép-magok összesen, [előfizetésenként](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> régiónként | Kapcsolatfelvétel a támogatási szolgáltatással |
| Sorozatonkénti (Dv2, F stb.) virtuálisgép-magok [előfizetésenként](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> régiónként | Kapcsolatfelvétel a támogatási szolgáltatással |
| [Társadminisztrátorok](../articles/billing-add-change-azure-subscription-administrator.md) előfizetésenként |Korlátlan |Korlátlan |
| [Storage-fiókok](../articles/storage/common/storage-create-storage-account.md) régiónként és előfizetésenként |200 |200<sup>2</sup> |
| [Erőforráscsoportok](../articles/azure-resource-manager/resource-group-overview.md) előfizetésenként |980 |980 |
| [Rendelkezésre állási csoportok](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) előfizetésenként |2000 régiónként |2000 régiónként |
| Resource Manager API-olvasási műveletek |15 000 óránként |15 000 óránként |
| Resource Manager API-írási műveletek |1200 óránként |1200 óránként |
| Resource Manager API-kérések mérete |4 194 304 bájt |4 194 304 bájt |
| Címkék előfizetésenként<sup>3</sup> |korlátlan |korlátlan |
| Egyedi címkeszámítások előfizetésenként<sup>3</sup> | 10,000 | 10,000 |
| [Felhőszolgáltatások](../articles/cloud-services/cloud-services-choose-me.md) előfizetésenként |Nem alkalmazható<sup>4</sup> |Nem alkalmazható<sup>4</sup> |
| [Affinitáscsoportok](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) előfizetésenként |Nem alkalmazható<sup>4</sup> |Nem alkalmazható<sup>4</sup> |

<sup>1</sup>Az alapértelmezett korlátok az ajánlat kategóriája (pl. ingyenes próba, használatalapú fizetés) és a sorozat (Dv2, F, G stb.) szerint változnak.

<sup>2</sup>Ebbe a standard és a prémium szintű tárfiókok is beletartoznak. Ha több mint 200 tárfiókra van szüksége, nyújtson be egy kérést az [Azure ügyfélszolgálatán](https://azure.microsoft.com/support/faq/) keresztül. Az Azure Storage csapata kiértékeli az Ön vállalkozását, és jóváhagyhat legfeljebb 250 tárfiókot.

<sup>3</sup>Az előfizetésenként alkalmazott címkék számának nincs korlátja. Az erőforrásonként vagy erőforráscsoportonként alkalmazható címkék száma legfeljebb 15 lehet. A Resource Manager csak akkor adja meg az előfizetésben használt [egyedi címkenevek és értékek listáját](/rest/api/resources/tags#Tags_List), ha a címkék száma nem több 10 000-nél. Ugyanakkor az erőforrásokat címke alapján akkor is meg lehet keresni, ha a címkék száma meghaladja a 10 000-et.  

<sup>4</sup>Ezek a funkciók már nem szükségesek az Azure-erőforráscsoportok és az Azure Resource Manager használatához.

> [!NOTE]
> Fontos kiemelni, hogy a virtuálisgép-magokra egy régió szerinti teljes korlát és egy régió szerinti sorozatonkénti (Dv2, F stb.) korlát is vonatkozik, amelyek egymástól függetlenek.  Például tegyük fel, hogy egy előfizetés az USA keleti régiójára vonatkozó teljes magkorlátja 30, az A sorozatú magkorlátja 30, és a D sorozatú magkorlátja is 30.  Ez az előfizetés üzembe helyezhet 30 A1 virtuális gépet, 30 D1 virtuális gépet, vagy a kettő valamilyen kombinációját, amely nem haladja meg az összesen 30 magot (pl. 10 A1 virtuális gép és 20 D1 virtuális gép).  
> <!-- -->
> 
> 

