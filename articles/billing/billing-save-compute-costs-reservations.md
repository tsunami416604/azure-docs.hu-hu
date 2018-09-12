---
title: Mi az az Azure Reservations? | Microsoft Docs
description: További tudnivalók az Azure-foglalásokat és a díjszabás menteni a virtuális gépeket, SQL Database-adatbázisok és egyéb erőforrás-használati díjak a.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: yashar
ms.openlocfilehash: 9ca31a09cd14a8b05e6f98d05f552e202ca4a9fd
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391861"
---
# <a name="what-are-azure-reservations"></a>Mi az az Azure Reservations?

Azure foglalások révén pénzt takaríthat meg előre egyéves vagy hároméves virtuális gép, SQL Database és a számítási kapacitás, vagy más Azure-erőforrások. Előre kell fizetnie lehetővé teszi a kedvezményt szeretne kapni a használt erőforrások. Foglalások jelentősen csökkentheti a virtuális gép, SQL database számítási vagy más erőforrásköltségek akár 72 % a használatalapú fizetéssel. Foglalások számlázási engedményt adja meg, és az erőforrások futási állapotát nem befolyásolják.

Vásárolhat egy foglalást a [az Azure portal](https://aka.ms/reservations). További információkért tekintse át a következők témaköröket:

- [Fizessen elő az Azure fenntartott VM-példányok a virtuális gépekkel](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Fizessen elő az SQL-adatbázis számítási erőforrásokat, hogy az Azure SQL Database szolgáltatás számára fenntartott kapacitás](../sql-database/sql-database-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>Miért érdemes megvásárolni a foglalást?

Ha virtuális gépeket vagy SQL-adatbázisok, amelyek hosszú ideig futnak, egy foglalás megvásárlása felajánlja a leginkább költséghatékony lehetőséget. Például ha folyamatosan négy szolgáltatáspéldányok egy fenntartása nélkül futtatja, akkor számoljuk el utólagos elszámolású díjszabás szerint. Ha vásárol egy foglalást adott erőforrásokhoz tartozó, azonnal megjelenik a foglalási kedvezményt. Az erőforrások már nem számoljuk el az utólagos elszámolású díjszabás szerint.

## <a name="what-charges-does-a-reservation-cover"></a>Milyen díjak nem egy foglalás cover?

- Fenntartott VM-példány: Egy foglalást csak terjed ki a virtuális gép számítási költségeket. További szoftverek, hálózati vagy tárolási díjakat nem fed le.
- Az SQL Database szolgáltatás számára fenntartott virtuális mag: csak a számítási költségek megtalálhatók a foglalást. A licenc külön kell fizetni.

Windows virtuális gépek és az SQL Database, a licencelési költségek is foglalkozik [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Aki nem jogosult egy foglalás megvásárlása?

Az ilyen előfizetések Azure-ügyfelek is vásárolhatnak foglalást:

- Nagyvállalati szerződés típusú előfizetési ajánlat (MS-AZR-0017P).
- [Használatalapú fizetéses](https://azure.microsoft.com/offers/ms-azr-0003p/) típusú előfizetési ajánlat (MS-AZR-003P). A "Tulajdonos" szerepkörrel kell rendelkeznie az előfizetésben a megvásárolni a foglalást.
- Cloud Solution Provider (CSP) partner használhatja az Azure Portalon vagy [Partnerközpont](https://docs.microsoft.com/partner-center/azure-reservations) megvásárolható Azure-foglalásokat.

A foglalási kedvezményt csak az Enterprise, a használatalapú fizetés vagy a CSP előfizetéstípusok társított erőforrások vonatkozik.

## <a name="how-is-a-reservation-billed"></a>Hogyan történik a Foglalás?

A Foglalás díját a fizetési módot az előfizetéshez kötött. Ha egy vállalati előfizetéssel rendelkezik, a fenntartás költsége a pénzügyi kötelezettségvállalási egyenleg vonni. Ha a pénzügyi kötelezettségvállalási egyenleg nem fedi le a fenntartás költsége, a számlázás a kerettúllépést. Ha egy használatalapú fizetéses előfizetésre, azonnal számlázott a hitelkártyát a fiókjában rendelkezik. Ha számla ellenében a számlázás, láthatja a díjak a következő számla.

## <a name="how-is-the-reservation-discount-applied"></a>Hogyan kell alkalmazni a foglalási kedvezményt?

A foglalási kedvezményt érvényes erőforrás-használat megfelel az attribútumokat, akkor válassza, ha a foglalást vásárolhat. Az attribútumok közé tartozik a hatókör ahol a megfelelő virtuális gépeket, SQL-adatbázisok, vagy más erőforrások futtassa. Például ha azt szeretné, a foglalási kedvezményt négy Standard D2 virtuális gépek az USA nyugati régiójában, válassza ki az előfizetést, ahol a virtuális gépek futnak. A virtuális gépek futnak, a regisztráció /-fiókon belül különböző előfizetésben található, majd válassza ki azt a hatókört, megosztott. Megosztott hatókör lehetővé teszi, hogy a foglalási kedvezményt a alkalmazni lehet előfizetések között. A hatókör, egy foglalás megvásárlása után módosíthatja. További információkért lásd: [kezelése az Azure-foglalások](billing-manage-reserved-vm-instance.md).

A foglalási kedvezményt csak az Enterprise, a használatalapú fizetés vagy a CSP előfizetéstípusok társított erőforrások vonatkozik. Egy előfizetésben a más típusú futtató erőforrások nem kapja meg a foglalási kedvezményt. A vállalati belépéseket nem a Foglalás értékelemekre feljogosító nagyvállalati fejlesztési és tesztelési előfizetések.

Jobban megérteni, milyen hatással van a foglalások a számlázási, a következő témakörökben talál:

-  [Megismerheti az Azure Reserved VM Instances kedvezmény](billing-understand-vm-reservation-charges.md)
- [Az Azure a foglalási kedvezményt ismertetése](billing-understand-vm-reservation-charges.md)
- Az Azure a foglalási kedvezményt- és használati SUSE ismertetése

## <a name="what-happens-when-the-reservation-term-expires"></a>Mi történik, ha a Foglalás időtartama lejár?

A Foglalás az előfizetési időszak végén lejár a számlázási kedvezményeket, és a virtuális gép, SQL database vagy más erőforráshoz elszámolása a használatalapú – mint-akkor lépjen ár. Az Azure-foglalások nem automatikus megújítási. Bevezetés a számlázási kedvezményeket a folytatáshoz meg kell vásárolnia egy új foglalást a jogosult szolgáltatások és szoftverek.

## <a name="discount-applies-to-different-sizes-with-instance-size-flexibility"></a>Különböző méretű példány mérete rugalmasan vonatkozik kedvezmény

Ha vásárol egy foglalást, a kedvezmény alkalmazhat más példányaira, amelyek az ugyanazon méretcsoport attribútumokkal. A kedvezmény lefedettség rugalmasságát foglalás és az attribútumokat, válassza ki, amikor azt megvásárolni a foglalást függ.

- Fenntartott VM-példányok: Ha azt megvásárolni a foglalást, ha **optimalizált**: **példány mérete rugalmasan**, a kedvezmény lefedettség választja virtuális gép méretétől függ. A Foglalás az azonos méretű adatsorozat-csoport a virtuális gépek (VM)-méretek is alkalmazhat. További információkért lásd: [virtuális gép mérete rugalmasan Reserved VM Instances](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- SUSE Linux Enterprise szoftvercsomag: A kedvezményes lefedettség függ a vcpu-k, a virtuális gépek, a SUSE szoftver futtatásához. További információkért lásd: [megismerheti, hogyan kell alkalmazni a SUSE Linux Enterprise szoftverfrissítési csomag kedvezményt](billing-understand-suse-reservation-charges.md).
- Az SQL Database szolgáltatás számára fenntartott kapacitás: A kedvezményes lefedettség attól függ, a teljesítményszint, válasszon ki. További információkért lásd: [megismerheti, hogyan kell alkalmazni az Azure a foglalási kedvezményt](billing-understand-reservation-charges.md).

## <a name="next-steps"></a>További lépések

Kezdő mentése a virtuális gépeken megvásárlásával egy [fenntartott VM-példány](../virtual-machines/windows/prepay-reserved-vm-instances.md) vagy [SQL Database szolgáltatás számára fenntartott kapacitás](../sql-database/sql-database-reserved-capacity.md).

Azure-foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Az Azure Reservations kezelése](billing-manage-reserved-vm-instance.md)
- [A használatalapú fizetéses előfizetést foglalás használati adatai](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalás használati adatai](billing-understand-reserved-instance-usage-ea.md)
- [Windows szoftverek díjait nem tartalmazza a foglalások](billing-reserved-instance-windows-software-costs.md)
- [A Partner Center Felhőszolgáltató (CSP) program Azure foglalások](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdése van, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
