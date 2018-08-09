---
title: Mik az Azure-foglalásokat? | Microsoft Docs
description: Ismerje meg az Azure-fenntartásokat és a díjszabás a virtuális gépeket, SQL Database-adatbázisok és egyéb erőforrások költségeit.
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
ms.openlocfilehash: 93c11852a11e0bb57a0b92090368298fc14b8c2a
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626304"
---
# <a name="what-are-azure-reservations"></a>Mik az Azure-foglalásokat?

Ön pénzt takaríthat meg, és előre egyéves vagy hároméves virtuális gép vagy SQL-adatbázis Azure foglalások segít a számítási kapacitást. Előre kell fizetnie lehetővé teszi a kedvezményt szeretne kapni a használt erőforrások. Az Azure-foglalások jelentősen csökkentheti a virtuális gép vagy SQL-adatbázis számítási költségeket – akár 72 % a használatalapú fizetéssel – egyéves vagy hároméves előzetes kötelezettségvállalás. foglalások számlázási engedményt adja meg, és a virtuális gépek vagy az SQL Database-adatbázisok futási állapotát nem befolyásolják.

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

Az ilyen előfizetések Azure-ügyfelek is vásárolhat egy foglalást:

- Nagyvállalati szerződés típusú előfizetési ajánlat (MS-AZR-0017P).
- [Használatalapú fizetéses](https://azure.microsoft.com/offers/ms-azr-0003p/) típusú előfizetési ajánlat (MS-AZR-003P). A "Tulajdonos" szerepkörrel kell rendelkeznie az előfizetésben a megvásárolni a foglalást. Vállalati regisztrációs foglalásokat vásárol, a vállalati rendszergazda engedélyezni kell a nagyvállalati szerződések portáljának foglalás vásárlások. Alapértelmezés szerint ez a beállítás engedélyezett.
- Cloud Solution Provider (CSP) partner használhatja az Azure Portalon vagy [Partnerközpont](https://docs.microsoft.com/partner-center/azure-reservations) megvásárolni a foglalást az Azure. 

Az Azure a foglalási kedvezményt csak virtuális gépekre vonatkozik, vagy Enterprise, a használatalapú fizetés vagy a CSP előfizetéstípusok társított SQL-adatbázisok.

## <a name="how-is-a-reservation-billed"></a>Hogyan történik a Foglalás?

A Foglalás díját a fizetési módot az előfizetéshez kötött. Ha egy vállalati előfizetéssel rendelkezik, a fenntartás költsége a pénzügyi kötelezettségvállalási egyenleg vonni. Ha a pénzügyi kötelezettségvállalási egyenleg nem fedi le a fenntartás költsége, a számlázás a kerettúllépést. Ha egy használatalapú fizetéses előfizetésre, azonnal számlázott a hitelkártyát a fiókjában rendelkezik. Ha számla ellenében a számlázás, láthatja a díjak a következő számla.

## <a name="how-is-the-reservation-discount-applied"></a>Hogyan kell alkalmazni a foglalási kedvezményt?

A foglalási kedvezményt érvényes erőforrás-használat megfelel az attribútumokat, akkor válassza, ha a foglalást vásárolhat. Az attribútumok közé tartozik a hatókör ahol a megfelelő virtuális gépeket, SQL-adatbázisok, vagy más erőforrások futtassa. Például ha azt szeretné, a foglalási kedvezményt négy Standard D2 virtuális gépek az USA nyugati régiójában, válassza ki az előfizetést, ahol a virtuális gépek futnak. A virtuális gépek futnak, a regisztráció /-fiókon belül különböző előfizetésben található, majd válassza ki azt a hatókört, megosztott. Megosztott hatókör lehetővé teszi, hogy a foglalási kedvezményt a alkalmazni lehet előfizetések között. A hatókör, egy foglalás megvásárlása után módosíthatja. További információkért lásd: [kezelése az Azure-ban foglalások](billing-manage-reserved-vm-instance.md).

A foglalási kedvezményt csak virtuális gépekre vonatkozik, vagy a vállalati vagy használatalapú fizetéses előfizetésre típusok társított SQL-adatbázisok. Virtuális gépek vagy más típusú rendelkező előfizetés futtató SQL-adatbázisok nem kapja meg a foglalási kedvezményt. A vállalati belépéseket nem a Foglalás értékelemekre feljogosító nagyvállalati fejlesztési és tesztelési előfizetések.

Hogyan érinti a foglalásokat, a virtuális gép vagy az SQL database számlázási jobb megismeréséhez tekintse meg [megismerheti, hogyan kell alkalmazni a foglalási kedvezményt](billing-understand-vm-reservation-charges.md).

## <a name="what-happens-when-the-reservation-term-expires"></a>Mi történik, ha a Foglalás időtartama lejár?

A Foglalás időtartamára végén a számlázási kedvezmény jár le, és a virtuális gép, SQL database vagy más erőforrások, a használatalapú – mint-akkor lépjen ár kiszámlázzuk. Az Azure-foglalások nem automatikus megújítási. Bevezetés a számlázási kedvezményeket a folytatáshoz meg kell vásárolnia egy új foglalás foglalás jogosult szolgáltatások.

## <a name="next-steps"></a>További lépések

Kezdő mentése a virtuális gépeken megvásárlásával egy [fenntartott VM-példány](../virtual-machines/windows/prepay-reserved-vm-instances.md) vagy [SQL Database szolgáltatás számára fenntartott kapacitás](../sql-database/sql-database-reserved-capacity.md).

Foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Az Azure-fenntartások kezelése](billing-manage-reserved-vm-instance.md)
- [Megismerheti, hogyan kell alkalmazni a foglalási kedvezményt](billing-understand-vm-reservation-charges.md)
- [A használatalapú fizetéses előfizetést foglalás használati adatai](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalás használati adatai](billing-understand-reserved-instance-usage-ea.md)
- [Windows szoftverek díjait nem tartalmazza a foglalások](billing-reserved-instance-windows-software-costs.md)
- [A Partner Center Felhőszolgáltató (CSP) program Azure foglalások](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdése van, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
