---
title: Azure fenntartott példányok kedvezményes - Azure számlázási megértése |} Microsoft Docs
description: Ismerje meg, hogyan Azure fenntartott Virtuálisgép-példány kedvezményes alkalmazott futó virtuális gépek.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: yashar
ms.openlocfilehash: a0800bafc3d6b858387e28a3b75bc7b3a6bfe6e8
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="understand-how-the-reserved-instance-discount-is-applied"></a>A fenntartott példány kedvezményeket alkalmazásának a módját ismertetése
Után vásárol egy Azure fenntartott Virtuálisgép-példány, a rendszer automatikusan alkalmaz a Azure fenntartott példány kedvezményeket az attribútumokat és a fenntartott példány mennyiségét virtuális gépek. A fenntartott példánya hozzá van rendelve az infrastrukturális költségek a virtuális gépek. A következő táblázat bemutatja a költségek a virtuális gép, egy fenntartott példány megvásárlása után. Minden esetben van szó, a tárolás és a hálózatkezelés, a szokásosnál.

| Virtuális gép típusa  | Fenntartott példánnyal díjak |    
|-----------------------|--------------------------------------------|
|Linux virtuális gépek nélkül további szoftverek | A fenntartott példány hozzá van rendelve a VM-infrastruktúra használati díjait.|
|Linux virtuális gépek szoftver költségek (például a Red Hat) | A fenntartott példány hozzá van rendelve az infrastruktúra használati díjait. Ha további szoftvereket van szó.|
|Windows-alapú virtuális gépek nélkül további szoftverek |A fenntartott példány hozzá van rendelve az infrastruktúra használati díjait. A Windows szoftverfrissítési van szó.|
|Windows-alapú virtuális gépek további szoftverek (például SQL server) | A fenntartott példány hozzá van rendelve az infrastruktúra használati díjait. Van szó, vagy további szoftvereket a Windows szoftverek.|
|Windows virtuális gépek [Azure hibrid juttatás](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | A fenntartott példány hozzá van rendelve az infrastruktúra használati díjait. A Windows szoftverek költségeit az Azure hibrid juttatás tartoznak. További szoftvereket felszámítása külön történik.| 

## <a name="application-of-reserved-instance-discount-to-non-windows-vms"></a>A nem - Windows virtuális gépek fenntartott példány kedvezményes alkalmazása
 A fenntartott példány kedvezményeket érvényes Virtuálisgép-példány fut óránként. A fenntartott példányok megvásárolt teljesül, a futó virtuális gépeinek alkalmazandó a fenntartott példány kedvezményeket által kibocsátott kihasználtsága. Virtuális gépek nem futtathatnak a a teljes óra a fenntartott példány tölti meg más virtuális gépek nem példánnyal foglalt, beleértve a párhuzamosan futó virtuális gépek. Az órát végén a fenntartott példány az alkalmazás virtuális gépek órájában zárolva van. Abban az esetben a virtuális gép nem fut egy óráig vagy egyidejű virtuális gépek órán belül nem tölti a fenntartott példány az órát, a program, hogy egy óra túl a fenntartott példány. A következő ábra mutatja be az alkalmazás egy fenntartott példány számlázható virtuális gépek használata. Az ábrán két egyező Virtuálisgép-példányok és egy fenntartott példány beszerzési alapul.

![Képernyőfelvétel egy alkalmazott fenntartott példány és a két egyező Virtuálisgép-példányok](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1.  Bármely használati, amely meghaladja a fenntartott példány sor a lekérdezi a rendszeres használatalapú ütemben számítjuk fel. Még nem foglalt példányok sor alatt bármely használatra kell fizetni, mivel azt már kifizetett fenntartott példány vásárlás részeként.
2.  1 óra, a példány 1 0,75 óra fut, és példány 2 0,5 óra fut. 1 óra teljes használata 1,25 óra. A használatalapú díjszabás van szó, a fennmaradó 0,25 órában.
3.  Óra 2 és 3 óra mindkét esetben 1 óráig futott. Egy példány a fenntartott példány hatálya alá tartozik, és a másik használatalapú ütemben terheli.
4.  4 órán keresztül példány 1 0,5 óra fut, és példány 2 futtatása 1 óra. A fenntartott példány teljes mértékben példányt: 1, amelyeket példány 2 0,5 óra. A fennmaradó 0,5 órában van számítjuk fel a használatalapú sebességét.

Megértéséhez, valamint az alkalmazás a fenntartott példányok megtekintése a használati jelentések számlázási, lásd: [fenntartott példány megértéséhez használati](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reserved-instance-discount-to-windows-vms"></a>A Windows virtuális gépek fenntartott példány kedvezményes alkalmazása
Ha Windows Virtuálisgép-példány futtatja, a fenntartott példány alkalmazva az infrastrukturális költségek. Az alkalmazás foglalt-példány a VM-infrastruktúra használati díjait Windows virtuális gépek megegyezik esetében nem - Windows virtuális gépek. Van szó, külön-külön a Windows software / vCPU alapon. Lásd: [Windows szoftverek költségeit fenntartott osztályt](https://go.microsoft.com/fwlink/?linkid=862756). Is fedik le a Windows-licencelési költségeit [Azure hibrid előnye a Windows Server] (https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="next-steps"></a>További lépések
Fenntartott példányok kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- [Kevesebbet költeni a virtuális gépek Azure fenntartott osztályt](billing-save-compute-costs-reservations.md)
- [A virtuális gépek fenntartott osztályt előre fizetés](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Fenntartott példányok kezelése](billing-manage-reserved-vm-instance.md)
- [A fenntartott példány kedvezményeket alkalmazásának a módját ismertetése](billing-understand-vm-reservation-charges.md)
- [A használatalapú fizetéses előfizetésre fenntartott példány használatának megértéséhez](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés használata fenntartott példány ismertetése](billing-understand-reserved-instance-usage-ea.md)
- [A kriptográfiai Szolgáltató előfizetések fenntartott példány használatának megértéséhez](https://docs.microsoft.com/partner-center/azure-reservations)
- [A Windows szoftverek költségeit fenntartott példányok nem találhatók](billing-reserved-instance-windows-software-costs.md)


## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdései további, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.
