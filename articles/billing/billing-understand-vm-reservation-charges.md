---
title: Azure fenntartott virtuálisgép-példányok kedvezményes alkalmazás megértése |} Microsoft Docs
description: Ismerje meg, hogyan Azure fenntartott Virtuálisgép-példány kedvezményes alkalmazott futó virtuális gépek.
services: billing
documentationcenter: ''
author: vikramdesai01
manager: vikdesai
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: vikdesai
ms.openlocfilehash: 6e551fddfc5ba8924cd8c71a1775307e7569b847
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="understand-how-the-reserved-virtual-machine-instance-discount-is-applied"></a>A fenntartott virtuális gép példány kedvezményeket alkalmazásának a módját ismertetése
Után vásárol egy fenntartott Virtuálisgép-példány, a rendszer automatikusan alkalmaz a Foglalás kedvezményeket az attribútumokat és a foglalási mennyiség virtuális gépek. A foglalási hozzá van rendelve az infrastrukturális költségek a virtuális gépek. A következő táblázat bemutatja a virtuális gép költségek foglalás megvásárlása után. Minden esetben van szó, a tárolás és a hálózatkezelés, a szokásosnál.

| Virtuális gép típusa  | A foglalási díjak |    
|-----------------------|--------------------------------------------| 
|Linux virtuális gépek nélkül további szoftverek | Foglalási hozzá van rendelve a virtuális gép infrastruktúra fenntartási költségeit.|
|Linux virtuális gépek szoftver költségek (például a Red Hat) | Foglalási hozzá van rendelve az infrastruktúra fenntartási költségeit. Ha további szoftvereket van szó.|
|Windows-alapú virtuális gépek nélkül további szoftverek |Foglalási hozzá van rendelve az infrastruktúra fenntartási költségeit. A Windows szoftverfrissítési van szó.|
|Windows-alapú virtuális gépek további szoftverek (például SQL server) | Foglalási hozzá van rendelve az infrastruktúra fenntartási költségeit. Van szó, vagy további szoftvereket a Windows szoftverek.|
|Windows virtuális gépek [Azure hibrid juttatás](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Foglalási hozzá van rendelve az infrastruktúra fenntartási költségeit. A Windows szoftverek költségeit az Azure hibrid juttatás tartoznak. További szoftvereket felszámítása külön történik.| 

## <a name="application-of-reservation-discount-to-non-windows-vms"></a>A nem - Windows virtuális gépek foglalás kedvezményes alkalmazása
 A foglalási kedvezményeket futó virtuális gép példányait óránként alkalmazzák. A futó virtuális gépeinek alkalmazandó a Foglalás kedvezményeket által kibocsátott használatát a megvásárolt lefoglalását egyeztetését. Virtuális gépek nem futtathatnak a a teljes óra a Foglalás tölti meg más virtuális gépek nem használja a foglalást, beleértve a párhuzamosan futó virtuális gépek. Az órát végén a Foglalás az alkalmazás virtuális gépek órájában zárolva van. Abban az esetben a virtuális gép nem fut egy óráig vagy egyidejű virtuális gépek órán belül nem tölti ki az órát lefoglalás, a foglalást, hogy egy óra van túl. A következő ábra szemlélteti számlázható virtuális gépek használata egy foglalási kérelem. Az ábrán két egyező Virtuálisgép-példányok és egy foglalás beszerzési alapul.

![Fenntartott virtuális gép példány alkalmazás](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1.  Bármely használati, amely meghaladja a fenntartott virtuális gép példány sor a lekérdezi a rendszeres használatalapú ütemben számítjuk fel. Még nem foglalt Virtuálisgép-példányok sor alatt bármely használatra kell fizetni, mivel azt már kifizetett foglalás vásárlás részeként.
2.  1 óra, a példány 1 0,75 óra fut, és példány 2 0,5 óra fut. 1 óra teljes használata 1,25 óra. A használatalapú díjszabás van szó, a fennmaradó 0,25 órában.
3.  Óra 2 és 3 óra mindkét esetben 1 óráig futott. Egy példány a Foglalás hatálya alá tartozik, és a másik használatalapú ütemben terheli.
4.  4 órán keresztül példány 1 0,5 óra fut, és példány 2 futtatása 1 óra. 1 példány teljes mértékben a Foglalás és a példány 2 0,5 óra van. A fennmaradó 0,5 órában van számítjuk fel a használatalapú sebességét.

Megértéséhez, valamint az alkalmazás a foglalások a számlázási használati jelentések megtekintése, lásd: [fenntartott Virtuálisgép-példány megértéséhez használati](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reservation-discount-to-windows-vms"></a>A Windows virtuális gépek foglalás kedvezményes alkalmazása
Ha Windows Virtuálisgép-példány futtatja, a Foglalás alkalmazva az infrastrukturális költségek. Az alkalmazás a Windows virtuális gépek esetén a virtuális gép infrastruktúra használati díjait foglalási megegyezik esetében nem - Windows virtuális gépek. Van szó, külön-külön a Windows software / vCPU alapon. Lásd: [Windows szoftverek költségeit rendelkező](https://go.microsoft.com/fwlink/?linkid=862756). Is fedik le a Windows-licencelési költségeit [Azure hibrid előnye a Windows Server] (https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="next-steps"></a>További lépések
Tudhat meg többet a fenntartott virtuálisgép-példányok, tekintse meg a következő cikkekben talál.

- [A virtuális gépek fenntartott virtuális gép osztályt előre fizetés](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Fenntartott virtuálisgép-példányok kezelése](billing-manage-reserved-vm-instance.md)
- [Kevesebbet költeni a virtuális gépek a fenntartott virtuálisgép-példányok](billing-save-compute-costs-reservations.md)
- [A használatalapú fizetéses előfizetésre fenntartott példány használatának megértéséhez](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés használata fenntartott példány ismertetése](billing-understand-reserved-instance-usage-ea.md)
- [A Windows szoftverek költségeit fenntartott példányok nem találhatók](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha segítségre van szüksége, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.
