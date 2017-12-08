---
title: "Azure fenntartott virtuálisgép-példányok kedvezményes alkalmazás megértése |} Microsoft Docs"
description: "Ismerje meg, hogyan Azure fenntartott Virtuálisgép-példány kedvezményes alkalmazott futó virtuális gépek."
services: billing
documentationcenter: 
author: vikramdesai01
manager: vikdesai
editor: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: vikdesai
ms.openlocfilehash: 3fd12bd3c51eeef57c896da030a83e447dc3e8ff
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="understand-how-the-reserved-virtual-machine-instance-discount-is-applied"></a>A fenntartott virtuális gép példány kedvezményeket alkalmazásának a módját ismertetése
Után vásárol egy fenntartott Virtuálisgép-példány, a rendszer automatikusan alkalmaz a Foglalás kedvezményeket az attribútumokat és a foglalási mennyiség virtuális gépek. A foglalási hozzá van rendelve az infrastrukturális költségek a virtuális gépek. A következő táblázat bemutatja a virtuális gép költségek foglalás megvásárlása után. Minden esetben van szó, a tárolás és a hálózatkezelés, a szokásosnál.

| Virtuális gép típusa  | A foglalási díjak |    
|-----------------------|--------------------------------------------| 
|Linux virtuális gépek nélkül további szoftverek | Foglalási hozzá van rendelve a virtuális gép infrastruktúra fenntartási költségeit.|
|Linux virtuális gépek szoftver költségek (például a Red Hat) | Foglalási hozzá van rendelve az infrastruktúra fenntartási költségeit. Ha további szoftvereket van szó.|
|Windows-alapú virtuális gépek nélkül további szoftverek |Foglalási hozzá van rendelve az infrastruktúra fenntartási költségeit. A Windows szoftverfrissítési van szó.|
|Windows-alapú virtuális gépek további szoftverek (például SQL server) | Foglalási hozzá van rendelve az infrastruktúra fenntartási költségeit. Van szó, vagy további szoftvereket a Windows szoftverek.|
|Windows virtuális gépek [Azure hibrid juttatás](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Foglalási hozzá van rendelve az infrastruktúra fenntartási költségeit. A Windows szoftverek költségeit az Azure hibrid juttatás tartoznak. További szoftvereket felszámítása külön történik.| 

## <a name="application-of-reservation-discount-to-non-windows-vms"></a>A nem - Windows virtuális gépek foglalás kedvezményes alkalmazása
 A foglalási kedvezményeket futó virtuális gép példányait óránként alkalmazzák. A foglalások, amelytől vásárolta a futó virtuális gépeinek alkalmazandó a Foglalás kedvezményeket által kibocsátott kihasználtsága teljesül. A következő ábra szemlélteti számlázható virtuális gépek használata egy foglalási kérelem. Az ábrán két egyező Virtuálisgép-példányok és egy foglalás beszerzési alapul.

![Fenntartott virtuális gép példány alkalmazás](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1.  Bármely használati, amely meghaladja a fenntartott virtuális gép példány sor a lekérdezi a rendszeres használatalapú ütemben számítjuk fel. Még nem foglalt Virtuálisgép-példányok sor alatt bármely használatra kell fizetni, mivel azt már kifizetett foglalás vásárlás részeként.
2.  1 óra, a példány 1 0,75 óra fut, és példány 2 0,5 óra fut. 1 óra teljes használata 1,25 óra. A használatalapú díjszabás van szó, a fennmaradó 0,25 órában.
3.  Óra 2 és 3 óra mindkét esetben 1 óráig futott. Egy példány a Foglalás hatálya alá tartozik, és a másik használatalapú ütemben terheli.
4.  4 órán keresztül példány 1 0,5 óra fut, és példány 2 futtatása 1 óra. 1 példány teljes mértékben a Foglalás és a példány 2 0,5 óra van. A fennmaradó 0,5 órában van számítjuk fel a használatalapú sebességét.

Megértéséhez, valamint az alkalmazás a foglalások a számlázási használati jelentések megtekintése, lásd: [fenntartott Virtuálisgép-példány megértéséhez használati](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reservation-discount-to-windows-vms"></a>A Windows virtuális gépek foglalás kedvezményes alkalmazása
Ha Windows Virtuálisgép-példány futtatja, a Foglalás alkalmazva az infrastrukturális költségek. Az alkalmazás a Windows virtuális gépek esetén a virtuális gép infrastruktúra használati díjait foglalási megegyezik esetében nem - Windows virtuális gépek. Van szó, külön-külön a Windows software / vCPU alapon. Lásd: [Windows szoftverek költségeit rendelkező](https://go.microsoft.com/fwlink/?linkid=862756). A Windows-licencelési költségeit [Azure hibrid előnye a Windows Server] (https://docs.microsoft.com/en-us/azure/virtual-machines/windows/hybrid-use-benefit-licensing) is foglalkozik.

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha segítségre van szüksége, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.
