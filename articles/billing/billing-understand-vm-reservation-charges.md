---
title: Megismerheti az Azure Reserved VM Instances kedvezményes |} A Microsoft Docs
description: Ismerje meg, hogyan rendszer alkalmazza a kedvezményt az Azure fenntartott VM-példány működő virtuális gépekhez.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: b112dd881d4b2e87e617111d00bc82c6151d7750
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370074"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Hogyan kell alkalmazni az Azure a foglalási kedvezményt a virtuális gépek

Miután vásárol egy Azure Reserved Virtual Machine Instance, a rendszer automatikusan alkalmazza a foglalási kedvezményt a virtuális gépek, amelyek megfelelnek a attribútumok és a Foglalás mennyiségét. A Foglalás magában foglalja a számítási költségek a virtuális gépek.

A foglalási kedvezményt az alap virtuális gépekre az Azure piactéren megvásárolt vonatkozik.

Az SQL Database szolgáltatás számára fenntartott kapacitás, lásd: [Azure Reserved Instances ismertetése kedvezményes](billing-understand-reservation-charges.md).

Az alábbi táblázat ismerteti a kapcsolódó költségek a virtuális gépet egy fenntartott VM-példány megvásárlása után. Minden esetben díjkötelesek tárolási és hálózatkezelési a normál díjakat.

| Virtuális gép típusa  | A díjat a fenntartott VM-példány |
|-----------------------|--------------------------------------------|
|Linux rendszerű virtuális gépek anélkül, hogy további szoftvereket | A Foglalás magában foglalja a virtuális gép infrastrukturális költségeit.|
|Linux rendszerű virtuális gépek szoftvereinek díjait (például a Red Hat) | A Foglalás infrastrukturális költségek ismerteti. További szoftverek díjkötelesek.|
|Windows virtuális gépek anélkül, hogy további szoftvereket |A Foglalás infrastrukturális költségek ismerteti. Windows szoftverek díjkötelesek.|
|Windows virtuális gépek további szoftvereket (például SQL server) | A Foglalás infrastrukturális költségek ismerteti. Windows szoftverek és a egy másik szoftver díjkötelesek.|
|Windows virtuális gépek [Azure Hybrid Benefit értékelem](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | A Foglalás infrastrukturális költségek ismerteti. Az Azure Hybrid Benefit a Windows a szoftverek díjait tartoznak. E kiegészítő programokat, külön számlázzuk.|

## <a name="how-reservation-discount-is-applied"></a>Hogyan alkalmazza a foglalási kedvezményt

A foglalási kedvezményt a "*használata – it-vagy-elveszíti-it*". Tehát ha az adott órán belül nem rendelkezik megfelelő erőforrásokat, majd elvesznek a foglalási mennyiség az adott órában. Nem sokat fel nem használt fenntartott órán keresztül továbbítja.

Ha leállítja egy erőforrást, a foglalási kedvezményt automatikusan alkalmazza a megadott hatókörben lévő másik egyező erőforrás. A megadott hatókörben lévő nem megfelelő erőforrások találhatók, akkor a szolgáltatás számára fenntartott órák *elveszett*.

## <a name="reservation-discount-for-non-windows-vms"></a>Foglalási kedvezményt a nem - Windows rendszerű virtuális gépek

 Óránként fut a Virtuálisgép-példányok az Azure a foglalási kedvezményt alkalmazza. A foglalások vásárolt a alkalmazni a foglalási kedvezményt a futó virtuális gépek által kibocsátott használat egyeztetését. A virtuális gépek nem futtathatnak a teljes órát a Foglalás szerepelni fog a többi virtuális gép nem használ egy foglalást, beleértve az egyidejűleg futó virtuális gépeket. Az óra végén (óra) a virtuális gépek foglalási alkalmazás zárolva van. Virtuális gép nem fut egy órára, vagy egy órán belül egyidejű virtuális gépek nem tölti ki a Foglalás órányi, a foglalást az adott órára van eredményeztek. A következő diagram azt mutatja be az alkalmazás egy foglalási számlázható Virtuálisgép-használat. Az ábrán egy foglalás megvásárlása és két egyező Virtuálisgép-példányok alapul.

![Képernyőkép egy alkalmazott foglalás és a két egyező Virtuálisgép-példányok](./media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. A Foglalás sor meghaladja a használatot a lekérdezi a normál használatalapú díjakat kell fizetnie. Ön nem foglalások vonalai alatti használat díját óta már fizettek foglalásvásárlás részeként.
2. 1 óra, a példány 1 0,75 óra fut, és példány 2 0,5 óra fut. 1 óra felhasználás teljes 1,25 órát jelent. A használatalapú díjszabás a többi 0,25 órában díjkötelesek.
3. Óra 2 és 3 óra, a példány is futásának időtartama: 1 óra. Egy példány a Foglalás hatálya alá tartozik, és a másik díját az utólagos elszámolású díjszabás szerint számlázzuk.
4. 4 órán keresztül 1 példány 0,5 óra fut, és 2 példány 1 órán keresztül futtat. 1 példány teljes mértékben a foglalást, és 2 példány 0,5 óra foglalkozik. A fennmaradó 0,5 óra használatalapú díjon díjkötelesek.

Ismertetése és használati jelentések számlázási megtekintheti az alkalmazást az Azure-foglalások: [foglalás használatának megértéséhez](billing-understand-reserved-instance-usage-ea.md).

## <a name="reservation-discount-for-windows-vms"></a>Windows virtuális gépek foglalási kedvezményt

Ha Windows Virtuálisgép-példányokat futtatja, a Foglalás alkalmazva az infrastrukturális költségek. Az alkalmazás a Windows-beli virtuális gépek a virtuális gép infrastrukturális költségek foglalási ugyanaz, mint a nem - Windows rendszerű virtuális gépek. Díjkötelesek külön vCPU / alapon Windows szoftverek. Lásd: [Windows szoftverek díjait a foglalások](billing-reserved-Instance-windows-software-costs.md). A Windows licencelési költségek lefedheti [Azure Hybrid Benefit a Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

## <a name="discount-can-apply-to-different-sizes"></a>Kedvezményt is alkalmazhat különböző méretű

Vásárlásakor a fenntartott VM-példány, ha **optimalizált**: **példány mérete rugalmasan**, a kedvezmény lefedettség választja virtuális gép méretétől függ. A Foglalás az azonos méretű adatsorozat-csoport a virtuális gépek (VM)-méretek is alkalmazhat. További információkért lásd: [virtuális gép mérete rugalmasan Reserved VM Instances](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

## <a name="discount-applies-to-matching-servicetype-only"></a>ServiceType csak megfelelő vonatkozik kedvezmény

A foglalási kedvezményt csak azokra a Virtuálisgép-használat, a `ServiceType` értékét `AdditionalInfo` megegyezik a Foglalás megvásárolható. Foglalás vonatkozó kedvezmény alkalmazása figyelmen kívül hagyja a mérőszám a virtuális gépek használják, és csak kiértékeli `ServiceType`. Tudja, melyik szolgáltatást, írja be, hogy a virtuális gép vásárolt. Egy nem prémium szintű tárolás Virtuálisgép-foglalás egy prémium szintű storage foglalás vagy ellentétes módon tudjon cserélni.

## <a name="classic-vms-and-cloud-services"></a>Klasszikus virtuális gépek és felhőszolgáltatások

Fenntartott Virtuálisgép-példányok automatikusan mindkét klasszikus virtuális gépekre vonatkozik, és a felhőszolgáltatások, ha a példány mérete rugalmasan engedélyezve van. A cloud services esetében a foglalási kedvezményt vonatkozik csak a számítási költségeket. A foglalási kedvezményt a felhőalapú szolgáltatásokhoz való alkalmazásakor a használati díjak a compute-ra (Linux mérő) osztották, és a cloud services díjak (cloud services felügyeleti mérőszám). További információkért lásd: [hogyan a foglalási kedvezményt vonatkozik a Cloud Services](billing-reserved-instance-windows-software-costs.md#cloud-services-software-meters-not-included-in-reservation-cost).

## <a name="need-help-contact-us"></a>Segítség Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

Azure-foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik azok a foglalást az Azure-hoz?](billing-save-compute-costs-reservations.md)
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Előre fizetés fenntartott Azure SQL Database-kapacitással rendelkező SQL Database számítási erőforrásokért](../sql-database/sql-database-reserved-capacity.md)
- [Azure-foglalások kezelése](billing-manage-reserved-vm-instance.md)
- [A használatalapú fizetéses előfizetést foglalás használati adatai](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalás használati adatai](billing-understand-reserved-instance-usage-ea.md)
- [CSP-előfizetésekben foglalás használati adatai](/partner-center/azure-reservations)
- [Windows szoftverek díjait nem tartalmazza a foglalások](billing-reserved-instance-windows-software-costs.md)
