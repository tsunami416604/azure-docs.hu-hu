---
title: Pénzt takaríthat meg előre fizető az Azure virtuális gépek - Azure |} Microsoft Docs
description: Tudnivalók Azure fenntartott virtuálisgép-példányt kívánja, mentheti a virtuális gépek költségeit.
services: billing
documentationcenter: ''
author: vikramdesai01
manager: vikramdesai01
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: vikdesai
ms.openlocfilehash: 7d85d3500014962a90ae596598332a95f4aa557e
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="save-money-on-virtual-machines-with-reserved-virtual-machine-instances"></a>Kevesebbet költeni a virtuális gépek a fenntartott virtuálisgép-példányok 
Fenntartott virtuálisgép-példányok engedélyezi, hogy egy- vagy kedvezményes hozzáférhet a virtuális gépek számítási kapacitás három évnyi előre fizet. Ez jelentősen csökkenti a virtuális gép költségek – 72 %-át használatalapú árak – az egy- vagy három év társaságuk előre. Fenntartott virtuálisgép-példányok számlázási kedvezménnyel érhető el, és nem befolyásolja a virtuális gépek futásidejű állapotát.

Megvásárolhatja fenntartott virtuálisgép-példányt a [Azure-portálon](https://aka.ms/reservations). További információkért lásd: [fenntartott virtuális gép osztályt előre virtuális gépekhez, és mentse el pénz fizetés](https://go.microsoft.com/fwlink/?linkid=861721).

## <a name="why-should-i-buy-a-reserved-virtual-machine-instance"></a>Miért kell vásárolni egy fenntartott virtuálisgép-példányt?
Ha hosszú ideig futó virtuális gépek, a fenntartott virtuális gép példánya megvásárlásáról lehetővé teszi a legjobb hatékony ár. Például ha folyamatosan futtatja Standard D2 négy példányai USA nyugati régiója régióban, Foglalás nélkül van szó, ütemben fizetési-,-Ugrás-meg. Ha megvásárolja a fenntartott virtuális gép példánya azok négy virtuális gépeket, a virtuális gépek azonnal élvezheti számlázási. Már nem esetének ütemben a fizetési-,-akkor nyissa meg. 

## <a name="what-charges-does-a-reserved-virtual-machine-instance-cover"></a>Milyen költségekkel megírásának fenntartott virtuálisgép-példányt?
A foglalási vonatkozik a Windows vagy Linux rendszerű virtuális gépek számára a virtuális gép infrastruktúra díjakat. A foglalási nem fedi le további szoftvereket, hálózati vagy tárolási költségek. A Windows virtuális gépek fedik le a licencelési költségek Windows [Azure hibrid juttatás](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reserved-virtual-machine-instance"></a>Aki nem jogosult beszerzési fenntartott virtuálisgép-példányt?
Az ilyen előfizetések Azure-ügyfél vásárolhat egy fenntartott virtuális gép példány:
-   Nagyvállalati Szerződés előfizetés ajánlattípus (MS-AZR - 0017P).
-   [Használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/) előfizetés ajánlattípus (MS-AZR - 003 P).
A "Tulajdonos" szerepkört az előfizetés vásárolható meg a fenntartott példánya a kell lennie. Az beszerzése a vállalati beléptetési-foglalásokat, a vállalati rendszergazda a beállítás engedélyezve van alapértelmezés szerint engedélyezi a Foglalás vásárlás EA portálon kell.

## <a name="how-is-a-reserved-virtual-machine-instances-purchase-billed"></a>Hogyan van egy fenntartott virtuálisgép-példányok beszerzési számlázása?
A foglalási beszerzési a fizetési módot, az előfizetés kötve van rendelve. Ha egy vállalati előfizetéssel rendelkezik, a fenntartás költsége vonni a pénzügyi kötelezettségvállalást a egyenleg. Ha a pénzügyi kötelezettségvállalást a egyenleg nem fedi le a fenntartás költsége, díjon számlázzuk a túlhasználati.
Használatalapú fizetés előfizetéssel rendelkezik, ha a fiók rendelkezik hitelkártya azonnal terhelve. Ha díjon számlázzuk a számlán, a következő számlán látható a költségek.

## <a name="how-is-the-purchased-reserved-virtual-machine-instance-discount-applied"></a>A megvásárolt fenntartott virtuális gép példány kedvezményeket alkalmazásának a módját?
A fenntartott virtuális gép példány kedvezményeket a virtuális gépek, amelyek megfelelnek az attribútumok választja a Foglalás megvásárolt vonatkozik. Az attribútumok közé tartoznak a hatókör, ahol a megfelelő virtuális gépek futtatásához. Például ha egy fenntartott Virtuálisgép-példány kedvezményes négy Standard D2 virtuális gépek az USA nyugati régiója régióban, válassza ki az előfizetést, ahol a virtuális gépek futnak. Ha a virtuális gépek futnak, a beléptetési/fiókon belül különböző előfizetésekhez, válassza a hatókör megosztottként. Megosztott hatókör lehetővé teszi, hogy a Foglalás kedvezményeket különböző előfizetéseknél kell alkalmazni.
A hatókör módosítható, miután a vásárol egy fenntartott Virtuálisgép-példány. A hatókör módosítása, a dokumentációban a foglalások kezelése.

A foglalási kedvezményeket csak a virtuális gépek enterprise vagy a használatalapú ajánlatok típusú előfizetésekhez vonatkozik. Más típusú rendelkező előfizetés működő virtuális gépek nem jelenik meg a Foglalás kedvezményeket. A vállalati regisztráció vállalati fejlesztési és tesztelési célú előfizetések nem abban az esetben jogosult a fenntartott példány előnyeit.

Hogyan foglalás a virtuális gép nemcsak a számlázást érinti az ismertetése [ismertetése az alkalmazás-lefoglalás juttatás számlázási](https://go.microsoft.com/fwlink/?linkid=863405).

## <a name="what-happens-when-the-reservation-term-expires"></a>Mi történik, ha a lejár a Foglalás kifejezés?
A foglalási kifejezés végén a számlázási kedvezményeket lejár, és a virtuális gép infrastruktúra lesz számlázva áron a fizetési-,-akkor nyissa meg. Foglalások nem automatikus megújítás. A számlázási kedvezményeket első a folytatáshoz meg kell vásárolnia egy új fenntartott virtuálisgép-példányt. 

## <a name="sizes-and-regional-availability"></a>Méretek és a regionális rendelkezésre állása
A legtöbb Virtuálisgép-méretek néhány kivétellel foglalások érhetők el:
- Előzetes Virtuálisgép-méretek – bármilyen méretű, amely jelenleg előzetes verzióban érhető fenntartott virtuális gép példány vásárolni nem érhetők el.
- Felhők – fenntartott virtuálisgép-példányok nem érhetők el Azure Amerikai Egyesült államokbeli kormányzati, Németország vagy Kína régiókban vásárolni. 
- Nincs elegendő kvótája – A fenntartott virtuális gép példány egyetlen előfizetéssel hatókörű vCPU kvóta az új RI az előfizetésben elérhető kell rendelkeznie. Például ha a célként megadott előfizetés kvótája D sorozatú termékcsalád 10 Vcpu, majd nem vásárol egy fenntartott Virtuálisgép-példány 11 standard szintű, D1-példányok. A kvóta ellenőrzése, zárolások magában foglalja az előfizetésben már telepített virtuális gépek. Például, ha az előfizetésnek 10 Vcpu tartozó kvóta D sorozatú termékcsalád. Ha ez az előfizetés két standard szintű, D1-példányt, majd vásárolhatja meg a fenntartott virtuális gép példánya 10 standard szintű, D1-példányok ebben az előfizetésben. 
- Kapacitás korlátozások – a ritka esetekben előfordulhat, hogy a VM-méretek régióban alacsony kapacitás miatt részéhez új foglalások Azure korlátozását beszerzési.

## <a name="next-steps"></a>További lépések
Menti a virtuális gépek által biztosított Start egy [fenntartott virtuális gép példány](https://go.microsoft.com/fwlink/?linkid=861721). 

Tudhat meg többet a fenntartott virtuálisgép-példányok, tekintse meg a következő cikkekben talál.

- [A virtuális gépek fenntartott virtuális gép osztályt előre fizetés](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Fenntartott virtuálisgép-példányok kezelése](billing-manage-reserved-vm-instance.md)
- [A fenntartott virtuális gép példány kedvezményeket alkalmazásának a módját ismertetése](billing-understand-vm-reservation-charges.md)
- [A használatalapú fizetéses előfizetésre fenntartott példány használatának megértéséhez](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés használata fenntartott példány ismertetése](billing-understand-reserved-instance-usage-ea.md)
- [A Windows szoftverek költségeit fenntartott példányok nem találhatók](billing-reserved-instance-windows-software-costs.md)

Ha segítségre van szüksége, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.
