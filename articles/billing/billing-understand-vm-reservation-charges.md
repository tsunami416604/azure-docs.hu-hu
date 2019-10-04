---
title: Az Azure Reserved VM Instances-kedvezmény ismertetése | Microsoft Docs
description: Megtudhatja, hogyan érvényesül az Azure Reserved VM Instances-kedvezmény az üzemelő virtuális gépek esetében.
author: yashesvi
manager: yashar
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 543cf81cdb9dad2b0f16a29c85accab820c82ec1
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719572"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Az Azure-foglalási kedvezmény alkalmazása virtuális gépekre

Miután megvásárolt egy Azure Reserved Virtual Machine Instances-példányt, a foglalási kedvezmény automatikusan érvényesül azokon a virtuális gépeken, amelyek megfelelnek a foglalás attribútumainak és mennyiségének. A foglalás fedezi a virtuális gépei számítási költségeit.

A foglalási kedvezmény az Azure Marketplace-en vásárolt alap virtuális gépekre vonatkozik.

A lefoglalt SQL Database-kapacitással kapcsolatban tekintse meg [az Azure Reserved Instances-kedvezmény ismertetését](billing-understand-reservation-charges.md).

A következő táblázat a virtuális gépek költségeit ábrázolja egy Reserved VM Instances-példány megvásárlását követően. A tárolásért és a hálózatkezelésért minden esetben a normál díjszabás szerint kell fizetnie.

| Virtuális gép típusa  | Költségek Reserved VM Instances-példányonként |
|-----------------------|--------------------------------------------|
|Linux rendszerű virtuális gépek további szoftverekkel | A foglalás kiterjed a virtuális gép infrastrukturális költségeire.|
|Linuxos virtuális gépek szoftveres költségekkel (például Red Hat) | A foglalás kiterjed az infrastrukturális költségekre. A további szoftverekért díjakat kell fizetnie.|
|Windows rendszerű virtuális gépek további szoftverekkel |A foglalás kiterjed az infrastrukturális költségekre. A Windows-szoftverekért díjakat kell fizetnie.|
|Windows rendszerű virtuális gépek további szoftverekkel (például SQL Server) | A foglalás kiterjed az infrastrukturális költségekre. A Windows-szoftverekért és a további szoftverekért díjakat kell fizetnie.|
|Windows rendszerű virtuális gépek [Azure Hybrid Benefittel](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | A foglalás kiterjed az infrastrukturális költségekre. Az Azure Hybrid Benefit kiterjed a Windows-szoftverek költségeire. Az esetleges további szoftverekért külön kell fizetnie.|

## <a name="how-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazása

A foglalási kedvezmény csak akkor érvényes, ha *folyamatosan igénybe veszi*. Ez azt jelenti, hogy ha nem rendelkezik megfelelő erőforrásokkal egy adott órában, akkor az arra az órára vonatkozó foglalási mennyiség elveszik. A lefoglalt, de fel nem használt órák nem vihetők tovább.

Egy erőforrás leállításakor a rendszer a foglalási kedvezményt automatikusan a megadott hatókör egy másik egyező erőforrására alkalmazza. Ha nem találhatók egyező erőforrások a megadott hatókörben, akkor a lefoglalt órák *elvesznek*.

## <a name="reservation-discount-for-non-windows-vms"></a>Foglalási kedvezmény nem Windows rendszerű virtuális gépek esetében

 Az Azure Reservations-kedvezményt a rendszer óránként alkalmazza a futó virtuális gépekre. A megvásárolt foglalásokat a rendszer megfelelteti a futó virtuális gépek általi használatnak, és ez alapján alkalmazza a foglalási kedvezményt. Az olyan virtuális gépek esetében, amelyek nem végig futnak az óra teljes időtartama alatt, a foglalás más, foglalást nem használó virtuális gépekkel lesz kitöltve, beleértve az egyidejűleg futó virtuális gépeket is. Az óra végén a virtuális gépek általi, arra az órára vonatkozó foglalásra való jelentkezés lezárul. Ha egy virtuális gép nem végig fut az óra időtartama alatt, vagy az egyidejűleg futó virtuális gépek nem töltik ki az egy órányi foglalást, az a foglalás alulkihasználtságát jelenti. A következő diagram egy foglalás számlázható virtuálisgép-használatra való alkalmazását mutatja be. Az ábra egy foglalási vásárláson és két egyező virtuálisgép-példányon alapul.

![Képernyőfelvétel egy alkalmazott foglalásról és két egyező virtuálisgép-példányról](./media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. A foglalást meghaladó használatért a normál használatalapú fizetési díjszabás szerint kell fizetni. A foglalást meg nem haladó használatért nem számítunk fel díjat, mert az a foglalás vásárlásának részeként már be lett fizetve.
2. Az 1. órában az 1. példány 0,75 órán keresztül, a 2. példány pedig 0,5 órán keresztül fut. Az 1. óra összesített használata 1,25 óra. A fennmaradó 0,25 óráért használatalapú díjszabás szerint kell fizetni.
3. A 2. és a 3. óra esetében mindkét példány 1 órán keresztül futott. Az egyik példány használatát a foglalás fedezi, a másikra használatalapú fizetési díjszabást alkalmazunk.
4. A 4. órában az 1. példány 0,5 órán keresztül, a 2. példány pedig 1 órán keresztül fut. A foglalás az 1. példány használatát teljesen, a 2. példány használatából pedig 0,5 órát fedez. A fennmaradó 0,5 óráért használatalapú díjszabás szerint kell fizetni.

Az Azure Reservations számlázási használati jelentésekben történő alkalmazásának megismeréséhez és megtekintéséhez lásd [a foglalások használatának ismertetését](billing-understand-reserved-instance-usage-ea.md).

## <a name="reservation-discount-for-windows-vms"></a>Foglalási kedvezmény Windows rendszerű virtuális gépek esetében

Windows rendszerű virtuálisgép-példányok futtatásakor a foglalás az infrastrukturális költségeket fedezi. A Windows rendszerű virtuális gépekhez tartozó virtuálisgép-infrastruktúrára fordított foglalás alkalmazásának módja megegyezik a nem Windows rendszerű virtuális gépekével. A Windows-szoftverekért vCPU-nként külön díjat kell fizetnie. Lásd: [Windows-szoftverköltségek foglalás esetén](billing-reserved-Instance-windows-software-costs.md). A [Windows Serverhez készült Azure Hybrid Benefit](../virtual-machines/windows/hybrid-use-benefit-licensing.md) igénybevételével fedezheti Windows-licencelési költségeit.

## <a name="discount-can-apply-to-different-sizes"></a>A kedvezmény különböző méretekre alkalmazható

Ha egy Reserved VM Instances-példány megvásárlásakor kiválasztja az **Optimalizálva a következőre:** **Példányméreti rugalmasság** beállítást, akkor a kedvezmény általi lefedettség a virtuális gép kiválasztott méretétől függ. A foglalást az ugyanabban a méretsorozat-csoportban található virtuálisgép-méretekre lehet alkalmazni. További információkért tekintse meg [a Reserved VM Instances virtuális gépeinek méretrugalmasságát](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md) ismertető cikket.

## <a name="discount-applies-to-matching-servicetype-only"></a>A kedvezmény csak a megfelelő szolgáltatástípusra érvényes

A foglalási kedvezmény csak az olyan virtuálisgép-használatra vonatkozik, ahol az `AdditionalInfo` `ServiceType` értéke megfelel a megvásárolt foglalásnak. A foglalási kedvezmény alkalmazása figyelmen kívül hagyja a virtuális gépekhez használt mérőszámot, és csak a `ServiceType` kiértékelését végzi. Legyen tudatában annak, hogy melyik szolgáltatástípushoz vásárolta meg a virtuális gépet. A nem prémium szintű tárolási virtuálisgép-foglalást prémium szintű tárolási foglalásra cserélheti, vagy fordítva.

## <a name="services-that-get-vm-reservation-discounts"></a>A virtuálisgép-foglalási kedvezményekre jogosult szolgáltatások

A virtuálisgép-foglalások a több szolgáltatás általi virtuálisgép-használatra is alkalmazhatók, nem csupán a virtuális gépek üzemelő példányaira. A foglalási kedvezményekre jogosult erőforrások a példány méretének rugalmassági beállításától függően változnak.

### <a name="instance-size-flexibility-setting"></a>Példány méretrugalmassági beállítása

A példány méretrugalmassági beállítása határozza meg, hogy mely szolgáltatásokra vonatkozik a fenntartott példányokhoz kapcsolódó kedvezmény.

Ha a *ConsumedService* értéke `Microsoft.Compute`, a foglalási kedvezmények automatikusan érvényesülnek minden megfelelő virtuálisgép-használat esetében, attól függetlenül, hogy a beállítás be vagy ki van-e kapcsolva. Ebből kifolyólag ellenőrizze a használati adatait a *ConsumedService* értékének megtekintéséhez. Néhány példa:

- Virtual machines (Virtuális gépek)
- Virtuálisgép-méretezési csoportok
- Container Service
- Azure Batch-üzemelőpéldányok (felhasználói előfizetési módban)
- Azure Kubernetes Service (AKS)
- Service Fabric

Ha a *ConsumedService* az alábbiak egyike, a foglalási kedvezmények automatikusan érvényesülnek minden megfelelő virtuálisgép-használat esetében, attól függetlenül, hogy a beállítás be vagy ki van-e kapcsolva:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Ellenőrizze a *ConsumedService* értékét a használati adatokban annak megállapításához, hogy a használatra alkalmazhatók-e a foglalási kedvezmények.

A példányok méretrugalmasságára vonatkozó további információkért tekintse meg [a Reserved VM Instances virtuális gépeinek méretrugalmasságát](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md) ismertető cikket.


## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik azok a foglalások az Azure-ban?](billing-save-compute-costs-reservations.md)
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Előre fizetés fenntartott Azure SQL Database-kapacitással rendelkező SQL Database számítási erőforrásokért](../sql-database/sql-database-reserved-capacity.md)
- [Foglalások kezelése az Azure-ban](billing-manage-reserved-vm-instance.md)
- [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](billing-understand-reserved-instance-usage.md)
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](billing-understand-reserved-instance-usage-ea.md)
- [A foglalási kihasználtság ismertetése CSP-előfizetésnél](/partner-center/azure-reservations)
- [A Reservations díjában nem szereplő Windows-szoftverköltségek](billing-reserved-instance-windows-software-costs.md)
