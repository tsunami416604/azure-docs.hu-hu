---
title: Megismerheti az Azure Reserved VM Instances kedvezményes |} A Microsoft Docs
description: Ismerje meg, hogyan rendszer alkalmazza a kedvezményt az Azure fenntartott VM-példány működő virtuális gépekhez.
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
ms.date: 08/08/2018
ms.author: cwatson
ms.openlocfilehash: 460cd3a5b05680563661e2f6519eaa3506eee03b
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392081"
---
# <a name="understand-how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Megismerheti, hogyan kell alkalmazni az Azure a foglalási kedvezményt a virtuális gépek

Miután vásárol egy Azure Reserved Virtual Machine Instance, a rendszer automatikusan alkalmazza a foglalási kedvezményt a virtuális gépek, amelyek megfelelnek a attribútumok és a Foglalás mennyiségét. A Foglalás magában foglalja a számítási költségek a virtuális gépek.

Az SQL Database szolgáltatás számára fenntartott kapacitás, lásd: [Azure Reserved Instances ismertetése kedvezményes](billing-understand-reservation-charges.md).

Az alábbi táblázat ismerteti a kapcsolódó költségek a virtuális gépet egy fenntartott VM-példány megvásárlása után. Minden esetben díjkötelesek tárolási és hálózatkezelési a normál díjakat.

| Virtuális gép típusa  | A díjat a fenntartott VM-példány |
|-----------------------|--------------------------------------------|
|Linux rendszerű virtuális gépek anélkül, hogy további szoftvereket | A Foglalás magában foglalja a virtuális gép infrastrukturális költségeit.|
|Linux rendszerű virtuális gépek szoftvereinek díjait (például a Red Hat) | A Foglalás infrastrukturális költségek ismerteti. További szoftverek díjkötelesek.|
|Windows virtuális gépek anélkül, hogy további szoftvereket |A Foglalás infrastrukturális költségek ismerteti. Windows szoftverek díjkötelesek.|
|Windows virtuális gépek további szoftvereket (például SQL server) | A Foglalás infrastrukturális költségek ismerteti. Windows szoftverek és a egy másik szoftver díjkötelesek.|
|Windows virtuális gépek [Azure Hybrid Benefit értékelem](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | A Foglalás infrastrukturális költségek ismerteti. Az Azure Hybrid Benefit a Windows a szoftverek díjait tartoznak. E kiegészítő programokat, külön számlázzuk.| 

## <a name="application-of-reservation-discount-to-non-windows-vms"></a>Alkalmazás a foglalási kedvezményt a nem - Windows rendszerű virtuális gépek

 Óránként fut a Virtuálisgép-példányok az Azure a foglalási kedvezményt alkalmazza. A foglalások vásárolt a alkalmazni a foglalási kedvezményt a futó virtuális gépek által kibocsátott használat egyeztetését. A virtuális gépek nem futtathatnak a teljes órát a Foglalás szerepelni fog a többi virtuális gép nem használ egy foglalást, beleértve az egyidejűleg futó virtuális gépeket. Az óra végén (óra) a virtuális gépek foglalási alkalmazás zárolva van. Virtuális gép nem fut egy órára, vagy egy órán belül egyidejű virtuális gépek nem tölti ki a Foglalás órányi, a foglalást az adott órára van eredményeztek. A következő diagram azt mutatja be az alkalmazás egy foglalási számlázható Virtuálisgép-használat. Az ábrán egy foglalás megvásárlása és két egyező Virtuálisgép-példányok alapul.

![Képernyőkép egy alkalmazott foglalás és a két egyező Virtuálisgép-példányok](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. A Foglalás sor meghaladja a használatot a lekérdezi a normál használatalapú díjakat kell fizetnie. Ön nem foglalások vonalai alatti használat díját óta már fizettek foglalásvásárlás részeként.
2. 1 óra, a példány 1 0,75 óra fut, és példány 2 0,5 óra fut. 1 óra felhasználás teljes 1,25 órát jelent. A használatalapú díjszabás a többi 0,25 órában díjkötelesek.
3. Óra 2 és 3 óra, a példány is futásának időtartama: 1 óra. Egy példány a Foglalás hatálya alá tartozik, és a másik díját az utólagos elszámolású díjszabás szerint számlázzuk.
4. 4 órán keresztül 1 példány 0,5 óra fut, és 2 példány 1 órán keresztül futtat. 1 példány teljes mértékben a foglalást, és 2 példány 0,5 óra foglalkozik. A fennmaradó 0,5 óra használatalapú díjon díjkötelesek.

Ismertetése és használati jelentések számlázási megtekintheti az alkalmazást az Azure-foglalások: [foglalás használatának megértéséhez](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reservation-discount-to-windows-vms"></a>Windows virtuális gépek foglalási kedvezményt alkalmazását

Ha Windows Virtuálisgép-példányokat futtatja, a Foglalás alkalmazva az infrastrukturális költségek. Az alkalmazás a Windows-beli virtuális gépek a virtuális gép infrastrukturális költségek foglalási ugyanaz, mint a nem - Windows rendszerű virtuális gépek. Díjkötelesek külön vCPU / alapon Windows szoftverek. Lásd: [Windows szoftverek díjait a foglalások](https://go.microsoft.com/fwlink/?linkid=862756). A Windows licencelési költségeit az [Azure Hybrid Benefit a Windows Server] lefedheti (https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="next-steps"></a>További lépések

Azure-foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik az Azure-foglalásokat?](billing-save-compute-costs-reservations.md)
- [Fizessen elő az Azure fenntartott VM-példányok a virtuális gépekkel](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Fizessen elő az SQL-adatbázis számítási erőforrásokat, hogy az Azure SQL Database szolgáltatás számára fenntartott kapacitás](../sql-database/sql-database-reserved-capacity.md)
- [Az Azure Reservations kezelése](billing-manage-reserved-vm-instance.md)
- [A használatalapú fizetéses előfizetést foglalás használati adatai](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalás használati adatai](billing-understand-reserved-instance-usage-ea.md)
- [CSP-előfizetésekben foglalás használati adatai](https://docs.microsoft.com/partner-center/azure-reservations)
- [Windows szoftverek díjait nem tartalmazza a foglalások](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdése van, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
