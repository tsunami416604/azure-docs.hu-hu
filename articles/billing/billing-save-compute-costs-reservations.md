---
title: Mi az az Azure Reservations? | Microsoft Docs
description: További tudnivalók az Azure-foglalásokat és a díjszabás menteni a virtuális gépeken, az SQL-adatbázisok, Azure Cosmos DB és más erőforrás-használati díjak.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: banders
ms.openlocfilehash: 3081ab352152d6d736f20da94ed0b513121e231b
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401799"
---
# <a name="what-are-azure-reservations"></a>Mi az az Azure Reservations?

Az Azure-foglalások segítséget pénzt takaríthat meg, egy éves előre és, vagy virtuális gépeket, SQL Database három éves számítási kapacitását, az Azure Cosmos DB átviteli sebesség vagy más Azure-erőforrások. Előre kell fizetnie lehetővé teszi a kedvezményt szeretne kapni a használt erőforrások. Foglalások jelentősen csökkentheti a virtuális gép, SQL-adatbázis számítási erőforrásokat, az Azure Cosmos dB-ben, vagy más erőforrást költségek akár 72 % a használatalapú fizetéssel. Foglalások számlázási engedményt adja meg, és az erőforrások futási állapotát nem befolyásolják.

Vásárolhat egy foglalást a [az Azure portal](https://aka.ms/reservations). További információkért tekintse át a következők témaköröket:

- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Előre fizetés fenntartott Azure SQL Database-kapacitással rendelkező SQL Database számítási erőforrásokért](../sql-database/sql-database-reserved-capacity.md)
- [Fizessen elő az Azure Cosmos DB-erőforrásokat, hogy az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás](../cosmos-db/cosmos-db-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>Miért érdemes megvásárolni a foglalást?

Ha virtuális gépek, az Azure Cosmos DB vagy az SQL-adatbázisok, amelyek hosszú ideig futnak, egy foglalás megvásárlása felajánlja a leginkább költséghatékony lehetőséget. Például ha folyamatosan négy szolgáltatáspéldányok egy fenntartása nélkül futtatja, akkor számoljuk el utólagos elszámolású díjszabás szerint. Ha vásárol egy foglalást adott erőforrásokhoz tartozó, azonnal megjelenik a foglalási kedvezményt. Az erőforrások már nem számoljuk el az utólagos elszámolású díjszabás szerint.

## <a name="what-charges-does-a-reservation-cover"></a>Milyen díjak nem egy foglalás cover?

- Fenntartott virtuálisgép-példány: Egy foglalást csak a virtuális gép számítási költségeit tartalmazza. További szoftverek, hálózati vagy tárolási díjakat nem fed le.
- Az SQL Database szolgáltatás számára fenntartott virtuális mag: Csak a számítási költségek is tartalmaz egy foglalást. A licenc külön kell fizetni.
- Az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás: Egy foglalás terjed ki az átviteli sebesség kiosztott részéért, az erőforrások, a tárolási és hálózatkezelési díjakat nem fed le.

Windows virtuális gépek és az SQL Database, a licencelési költségek is foglalkozik [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Aki nem jogosult egy foglalás megvásárlása?

Csomag vásárlása egy előfizetés-tulajdonosi szerepkör (MS-AZR - 0017P vagy MS-AZR - 0148P) vállalati vagy használatalapú fizetéses előfizetésre (MS-AZR - 003P vagy MS-AZR - 0023P) kell rendelkeznie. Felhőszolgáltatók használhatja az Azure Portalon vagy [Partnerközpont](/partner-center/azure-reservations) megvásárolható Azure-foglalásokat.

Nagyvállalati szerződéssel rendelkező ügyfeleink a nagyvállalati szerződéssel rendelkező rendszergazdák vásárlások korlátozhatja tiltsa le a **fenntartott példányok hozzáadása** lehetőség az EA-portálon. Nagyvállalati szerződéssel rendelkező rendszergazdák tulajdonosának kell lennie egy előfizetés számára legalább egy nagyvállalati szerződéses előfizetésében vásárolhat egy foglalást. A beállítás akkor hasznos, amelyek egy másik költséghelyek lefoglalását beszerzési központi csapat nagyvállalatok számára. A vásárlás után központi csapatok költség center tulajdonosok adhat hozzá a foglalást. Tulajdonosok majd gazdagépcsoportjaira a foglalást saját előfizetésekhez. A központi csapat az előfizetés-tulajdonosi hozzáféréssel rendelkezik, ahol a Foglalás megvásárlása esetén nem szükséges.

A foglalási kedvezményt csak az Enterprise, a használatalapú fizetés vagy a CSP előfizetéstípusok társított erőforrások vonatkozik.

## <a name="how-is-a-reservation-billed"></a>Hogyan történik a Foglalás?

A Foglalás díját a fizetési módot az előfizetéshez kötött. Ha egy vállalati előfizetéssel rendelkezik, a fenntartás költsége a pénzügyi kötelezettségvállalási egyenleg vonni. Ha a pénzügyi kötelezettségvállalási egyenleg nem fedi le a fenntartás költsége, a számlázás a kerettúllépést. Ha egy használatalapú fizetéses előfizetésre, azonnal számlázott a hitelkártyát a fiókjában rendelkezik. Ha számla ellenében a számlázás, láthatja a díjak a következő számla.

## <a name="how-is-the-reservation-discount-applied"></a>Hogyan kell alkalmazni a foglalási kedvezményt?

A foglalási kedvezményt érvényes erőforrás-használat megfelel az attribútumokat, akkor válassza, ha a foglalást vásárolhat. Az attribútumok közé tartozik a hatókör, ahol a megfelelő virtuális gépek, az SQL adatbázisok, Azure Cosmos DB vagy egyéb erőforrások futtat. Például ha azt szeretné, a foglalási kedvezményt négy Standard D2 virtuális gépek az USA nyugati régiójában, válassza ki az előfizetést, ahol a virtuális gépek futnak. A virtuális gépek futnak, a regisztráció /-fiókon belül különböző előfizetésben található, majd válassza ki azt a hatókört, megosztott. Megosztott hatókör lehetővé teszi, hogy a foglalási kedvezményt a alkalmazni lehet előfizetések között. A hatókör, egy foglalás megvásárlása után módosíthatja. További információkért lásd: [kezelése az Azure-foglalások](billing-manage-reserved-vm-instance.md).

A foglalási kedvezményt csak az Enterprise, a használatalapú fizetés vagy a CSP előfizetéstípusok társított erőforrások vonatkozik. Egy előfizetésben a más típusú futtató erőforrások nem kapja meg a foglalási kedvezményt.

Jobban megérteni, milyen hatással van a foglalások a számlázási, a következő témakörökben talál:

- [Megismerheti az Azure Reserved VM Instances kedvezmény](billing-understand-vm-reservation-charges.md)
- [Az Azure a foglalási kedvezményt ismertetése](billing-understand-vm-reservation-charges.md)
- [Azure Cosmos DB-foglalási kedvezményt ismertetése](billing-understand-cosmosdb-reservation-charges.md)
- [Az Azure a foglalási kedvezményt- és használati SUSE ismertetése](billing-understand-suse-reservation-charges.md)

## <a name="what-happens-when-the-reservation-term-expires"></a>Mi történik, ha a Foglalás időtartama lejár?

A Foglalás az előfizetési időszak végén lejár a számlázási kedvezményeket, és a virtuális gép, SQL database, Azure Cosmos DB vagy egyéb erőforrás elszámolása a használatalapú – mint-akkor lépjen ár. Az Azure-foglalások nem automatikus megújítási. Bevezetés a számlázási kedvezményeket a folytatáshoz meg kell vásárolnia egy új foglalást a jogosult szolgáltatások és szoftverek.

## <a name="discount-applies-to-different-sizes-with-instance-size-flexibility"></a>Különböző méretű példány mérete rugalmasan vonatkozik kedvezmény

Ha vásárol egy foglalást, a kedvezmény alkalmazhat más példányaira, amelyek az ugyanazon méretcsoport attribútumokkal. A kedvezmény lefedettség rugalmasságát foglalás és az attribútumokat, válassza ki, amikor azt megvásárolni a foglalást függ.

- Fenntartott VM-példányok: Ha amikor megvásárolni a foglalást **optimalizált**: **példány mérete rugalmasan**, kedvezmény lefedettséget választja virtuális gép méretétől függ. A Foglalás az azonos méretű adatsorozat-csoport a virtuális gépek (VM)-méretek is alkalmazhat. További információkért lásd: [virtuális gép mérete rugalmasan Reserved VM Instances](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- SUSE Linux Enterprise szoftvercsomag: A kedvezmény lefedettség a vcpu-k, a virtuális gépek futtatásához a SUSE szoftver függ. További információkért lásd: [megismerheti, hogyan kell alkalmazni a SUSE Linux Enterprise szoftverfrissítési csomag kedvezményt](billing-understand-suse-reservation-charges.md).
- Az SQL Database szolgáltatás számára fenntartott kapacitás: A kedvezmény lefedettség attól függ, hogy a kiválasztott teljesítményszint. További információkért lásd: [megismerheti, hogyan kell alkalmazni az Azure a foglalási kedvezményt](billing-understand-reservation-charges.md).
- Az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás: A kedvezmény lefedettség attól függ, hogy a kiosztott átviteli sebesség. További információkért lásd: [megismerheti, hogyan kell alkalmazni az Azure Cosmos DB-foglalási kedvezményt](billing-understand-cosmosdb-reservation-charges.md).

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- Kezdő mentése a virtuális gépeken megvásárlásával egy [fenntartott VM-példány](../virtual-machines/windows/prepay-reserved-vm-instances.md), [SQL Database szolgáltatás számára fenntartott kapacitás](../sql-database/sql-database-reserved-capacity.md), vagy [Azure Cosmos DB szolgáltatás számára fenntartott kapacitás](../cosmos-db/cosmos-db-reserved-capacity.md).
- További információ az Azure-fenntartások a következő cikkekben:
    - [Az Azure Reservations kezelése](billing-manage-reserved-vm-instance.md)
    - [A használatalapú fizetéses előfizetést foglalás használati adatai](billing-understand-reserved-instance-usage.md)
    - [A nagyvállalati beléptetés foglalás használati adatai](billing-understand-reserved-instance-usage-ea.md)
    - [Windows szoftverek díjait nem tartalmazza a foglalások](billing-reserved-instance-windows-software-costs.md)
    - [A Partner Center Felhőszolgáltató (CSP) program Azure foglalások](https://docs.microsoft.com/partner-center/azure-reservations)
