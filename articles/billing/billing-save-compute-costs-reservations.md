---
title: Mik az Azure fenntartott példányok? | Microsoft Docs
description: Tudnivalók Azure fenntartott Virtuálisgép-példányok és a virtuális gép mentése a virtuális gépek költségeit, és a legjobb hatékony ár árképzési.
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
ms.date: 05/09/2018
ms.author: yashar
ms.openlocfilehash: 12fc88596b4283b6f809575328ab801704cc1c8d
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063940"
---
# <a name="what-are-azure-reserved-vm-instances"></a>Mik azok a Azure fenntartott Virtuálisgép-példányok?
[Az Azure fenntartott Virtuálisgép-példányok](https://azure.microsoft.com/pricing/reserved-vm-instances) pénzt takaríthat az egy- vagy számítási kapacitás így hozzáférhet a virtuális gépek esetén kedvezményes díjat három évnyi előre fizető segítségével használhatja. Az Azure fenntartott példányok jelentősen csökkenti a virtuális gép költségek – 72 %-át használatalapú árak – az egy- vagy három év társaságuk előre. Fenntartott példányok adja meg a számlázási kedvezményeket, és nincsenek hatással a futásidejű állapot, a virtuális gépek.

Megvásárolhatja egy fenntartott példány (RI) a [Azure-portálon](https://aka.ms/reservations). További információkért lásd: [előre fizetés, a virtuális gépekhez, és mentse el fenntartott osztályt pénz](https://go.microsoft.com/fwlink/?linkid=861721).

## <a name="why-should-i-buy-a-reserved-instance"></a>Miért kell vásárolni egy fenntartott példányt?
Ha hosszú ideig futó virtuális gépek, egy fenntartott példány megvásárlásáról biztosítja a leginkább költséghatékony lehetőségét. Például ha folyamatosan négy példányát egy Standard D2 VM USA nyugati régiója régióban, egy fenntartott példány nélkül van szó, használatalapú fizetési ütemben. Ha megvásárolja a fenntartott példánya azok négy virtuális gépeket, a virtuális gépek azonnal élvezheti számlázási. Azok már nem a használatalapú ütemben van szó. 

## <a name="what-charges-does-a-reserved-instance-cover"></a>Mi a díjak nem egy fenntartott példány borítóján?
A fenntartott példánya csak a Windows vagy Linux rendszerű virtuális gépek számára a virtuális gép infrastruktúra díjak ismerteti. A fenntartott példánya nem fedi le további szoftvereket, hálózati vagy tárolási költségek. A Windows virtuális gépek fedik le a licencelési költségek Windows [Azure hibrid juttatás](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reserved-instance"></a>Aki nem jogosult egy fenntartott példány beszerzési?
Az ilyen előfizetések Azure-ügyfél vásárolhat egy fenntartott példány:
-   Nagyvállalati Szerződés előfizetés ajánlattípus (MS-AZR - 0017P).
-   [Használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/) előfizetés ajánlattípus (MS-AZR - 003 P). A "Tulajdonos" szerepkört az előfizetés vásárolható meg a fenntartott példánya a kell lennie. Az beszerzése a vállalati beléptetési fenntartott példánya, a vállalati rendszergazdának engedélyeznie kell a EA portálon fenntartott példány vásárlások. Ez a beállítás alapértelmezés szerint engedélyezve van.
-   Cloud Solution Provider (CSP) partnerek használhatja az Azure portál vagy [Partnerközpontjában](https://docs.microsoft.com/partner-center/azure-reservations) fenntartott példányok megvásárlásához.

## <a name="how-is-a-reserved-instance-purchase-billed"></a>Hogyan van egy fenntartott példány beszerzési számlázása?
A fenntartott példány beszerzési a fizetési módot, az előfizetés kötve van rendelve. Ha nagyvállalati előfizetéssel rendelkezik, a fenntartott példány költség vonni a pénzügyi kötelezettségvállalást a egyenleg. Ha a pénzügyi kötelezettségvállalást a egyenleg nem fedi le a fenntartott példány költségét, díjon számlázzuk a túlhasználati.
Használatalapú fizetés előfizetéssel rendelkezik, ha a fiók rendelkezik hitelkártya azonnal terhelve. Ha díjon számlázzuk a számlán, a következő számlán látható a költségek.

## <a name="how-is-the-purchased-reserved-instance-discount-applied"></a>A megvásárolt fenntartott példány kedvezményeket alkalmazásának a módját?
A fenntartott példány kedvezményeket a virtuális gépek, amelyek megfelelnek az attribútumok választja a fenntartott példány megvásárolt vonatkozik. Az attribútumok közé tartoznak a hatókör, ahol a megfelelő virtuális gépek futtatásához. Például ha egy fenntartott példány kedvezményes négy Standard D2 virtuális gépek az USA nyugati régiója régióban, válassza ki az előfizetést, ahol a virtuális gépek futnak. Ha a virtuális gépek futnak, a beléptetési/fiókon belül különböző előfizetésekhez, válassza a hatókör megosztottként. Megosztott hatókör lehetővé teszi, hogy a különböző előfizetéseknél alkalmazandó fenntartott példány kedvezményeket. A hatókör módosítható, miután a vásárol egy fenntartott példányt. A hatókör módosításához lásd [kezelése az Azure-ban fenntartott példányok](billing-manage-reserved-vm-instance.md).

A fenntartott példány kedvezményeket csak vállalati vagy használatalapú fizetés előfizetéstípusok társított virtuális gépére érvényes lesz. Más típusú rendelkező előfizetés működő virtuális gépek nem jelenik meg a fenntartott példány kedvezményeket. A vállalati regisztráció vállalati fejlesztési és tesztelési célú előfizetések nem abban az esetben jogosult a fenntartott példány előnyeit.

Jobb megértése érdekében hogyan fenntartott példány hatással van a virtuális gép számlázási, lásd: [megérteni, hogyan kell alkalmazni a fenntartott példány kedvezményeket](billing-understand-vm-reservation-charges.md).

## <a name="what-happens-when-the-reserved-instance-term-expires"></a>Mi történik, ha a lejár a fenntartott példány kifejezés?
A fenntartott példány kifejezés végén a számlázási kedvezményeket lejár, és a virtuális gép infrastruktúra lesz számlázva áron a fizetési-,-akkor nyissa meg. Az Azure fenntartott példányok nem automatikus megújítás. A számlázási kedvezményeket első a folytatáshoz meg kell vásárolnia egy új fenntartott példányt. 

## <a name="sizes-and-regional-availability"></a>Méretek és a regionális rendelkezésre állása
A legtöbb Virtuálisgép-méretek néhány kivétellel fenntartott példányok érhetők el:
- Virtuális gépek Preview – bármely Virtuálisgép-sorozat vagy a kép méretének fenntartott példány vásárolni nem érhetők el.
- Felhők – fenntartott példányok nem érhetők el Azure Amerikai Egyesült államokbeli kormányzati, Németország vagy Kína régiókban vásárolni. 
- Nincs elegendő kvótája – egy fenntartott példányt, ami egy-egy előfizetéshez rendelkeznie kell az új RI az előfizetésben elérhető vCPU kvóta. Például ha a célként megadott előfizetés 10 Vcpu kvótát a D sorozat, majd nem vásárol egy fenntartott példány 11 standard szintű, D1-példányok. A kvóta ellenőrzése, fenntartott példányok magában foglalja az előfizetésben már telepített virtuális gépek. Például ha az előfizetéshez tartozó kvóta 10 Vcpu D sorozatú rendelkezik, és két standard szintű, D1 példánya telepítve van, majd vásárolhat egy fenntartott példány 10 standard szintű, D1-példányok ebben az előfizetésben. 
- A kapacitás korlátozások – a ritka esetekben előfordulhat, hogy a virtuális gép részéhez új fenntartott adatpéldányok beszerzési méretének régióban alacsony kapacitás miatt Azure korlátozását.

## <a name="next-steps"></a>További lépések
Menti a virtuális gépek által biztosított Start egy [Azure fenntartott példány](https://go.microsoft.com/fwlink/?linkid=861721). 

Fenntartott példányok kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- [Mik azok a Azure fenntartott Virtuálisgép-példányok?](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Az Azure-ban fenntartott példányok kezelése](billing-manage-reserved-vm-instance.md)
- [A fenntartott példány kedvezményeket alkalmazásának a módját ismertetése](billing-understand-vm-reservation-charges.md)
- [A használatalapú fizetéses előfizetésre fenntartott példány használatának megértéséhez](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés használata fenntartott példány ismertetése](billing-understand-reserved-instance-usage-ea.md)
- [A Windows szoftverek költségeit fenntartott példányok nem találhatók](billing-reserved-instance-windows-software-costs.md)
- [Fenntartott Partner Center Cloud Solution Provider (CSP) program-példány](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdései további, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.
