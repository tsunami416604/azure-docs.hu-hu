---
title: Az Azure Reserved VM Instances-kedvezmény ismertetése
description: Megtudhatja, hogyan érvényesül az Azure Reserved VM Instances-kedvezmény az üzemelő virtuális gépek esetében.
author: yashesvi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: banders
ms.openlocfilehash: ddf232dbe6c6ff61f685e2910286188fb92e1f17
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192220"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Az Azure-foglalási kedvezmény alkalmazása virtuális gépekre

Miután megvásárolt egy Azure Reserved Virtual Machine Instances-példányt, a foglalási kedvezmény automatikusan érvényesül azokon a virtuális gépeken, amelyek megfelelnek a foglalás attribútumainak és mennyiségének. A foglalás fedezi a virtuális gépei számítási költségeit.

A foglalási kedvezmény az Azure Marketplace-en vásárolt alap virtuális gépekre vonatkozik.

A lefoglalt SQL Database-kapacitással kapcsolatban tekintse meg [az Azure Reserved Instances-kedvezmény ismertetését](../reservations/understand-reservation-charges.md).

A következő táblázat a virtuális gépek költségeit ábrázolja egy Reserved VM Instances-példány megvásárlását követően. A tárolásért és a hálózatkezelésért minden esetben a normál díjszabás szerint kell fizetnie.

| Virtuális gép típusa  | Költségek Reserved VM Instances-példányonként |
|-----------------------|--------------------------------------------|
|Linux rendszerű virtuális gépek további szoftverekkel | A foglalás kiterjed a virtuális gép infrastrukturális költségeire.|
|Linuxos virtuális gépek szoftveres költségekkel (például Red Hat) | A foglalás kiterjed az infrastrukturális költségekre. A további szoftverekért díjakat kell fizetnie.|
|Windows rendszerű virtuális gépek további szoftverekkel |A foglalás kiterjed az infrastrukturális költségekre. A Windows-szoftverekért díjakat kell fizetnie.|
|Windows rendszerű virtuális gépek további szoftverekkel (például SQL Server) | A foglalás kiterjed az infrastrukturális költségekre. A Windows-szoftverekért és a további szoftverekért díjakat kell fizetnie.|
|Windows rendszerű virtuális gépek [Azure Hybrid Benefittel](../../virtual-machines/windows/hybrid-use-benefit-licensing.md) | A foglalás kiterjed az infrastrukturális költségekre. Az Azure Hybrid Benefit kiterjed a Windows-szoftverek költségeire. Az esetleges további szoftverekért külön kell fizetnie.|

## <a name="how-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazása

A foglalási kedvezmény csak akkor érvényes, ha *folyamatosan igénybe veszi*. Ez azt jelenti, hogy ha nem rendelkezik megfelelő erőforrásokkal egy adott órában, akkor az arra az órára vonatkozó foglalási mennyiség elveszik. A lefoglalt, de fel nem használt órák nem vihetők tovább.

Egy erőforrás leállításakor a rendszer a foglalási kedvezményt automatikusan a megadott hatókör egy másik egyező erőforrására alkalmazza. Ha nem találhatók egyező erőforrások a megadott hatókörben, akkor a lefoglalt órák *elvesznek*.

## <a name="reservation-discount-for-non-windows-vms"></a>Foglalási kedvezmény nem Windows rendszerű virtuális gépek esetében

 Az Azure Reservations-kedvezményt a rendszer óránként alkalmazza a futó virtuális gépekre. A megvásárolt foglalásokat a rendszer megfelelteti a futó virtuális gépek általi használatnak, és ez alapján alkalmazza a foglalási kedvezményt. Az olyan virtuális gépek esetében, amelyek nem végig futnak az óra teljes időtartama alatt, a foglalás más, foglalást nem használó virtuális gépekkel lesz kitöltve, beleértve az egyidejűleg futó virtuális gépeket is. Az óra végén a virtuális gépek általi, arra az órára vonatkozó foglalásra való jelentkezés lezárul. Ha egy virtuális gép nem végig fut az óra időtartama alatt, vagy az egyidejűleg futó virtuális gépek nem töltik ki az egy órányi foglalást, az a foglalás alulkihasználtságát jelenti. A következő diagram egy foglalás számlázható virtuálisgép-használatra való alkalmazását mutatja be. Az ábra egy foglalási vásárláson és két egyező virtuálisgép-példányon alapul.

![Képernyőfelvétel egy alkalmazott foglalásról és két egyező virtuálisgép-példányról](./media/understand-vm-reservation-charges/billing-reserved-vm-instance-application.png)

1. A foglalást meghaladó használatért a normál használatalapú fizetési díjszabás szerint kell fizetni. A foglalást meg nem haladó használatért nem számítunk fel díjat, mert az a foglalás vásárlásának részeként már be lett fizetve.
2. Az 1. órában az 1. példány 0,75 órán keresztül, a 2. példány pedig 0,5 órán keresztül fut. Az 1. óra összesített használata 1,25 óra. A fennmaradó 0,25 óráért használatalapú díjszabás szerint kell fizetni.
3. A 2. és a 3. óra esetében mindkét példány 1 órán keresztül futott. Az egyik példány használatát a foglalás fedezi, a másikra használatalapú fizetési díjszabást alkalmazunk.
4. A 4. órában az 1. példány 0,5 órán keresztül, a 2. példány pedig 1 órán keresztül fut. A foglalás az 1. példány használatát teljesen, a 2. példány használatából pedig 0,5 órát fedez. A fennmaradó 0,5 óráért használatalapú díjszabás szerint kell fizetni.

Az Azure Reservations számlázási használati jelentésekben történő alkalmazásának megismeréséhez és megtekintéséhez lásd [a foglalások használatának ismertetését](../reservations/understand-reserved-instance-usage-ea.md).

## <a name="reservation-discount-for-windows-vms"></a>Foglalási kedvezmény Windows rendszerű virtuális gépek esetében

Windows rendszerű virtuálisgép-példányok futtatásakor a foglalás az infrastrukturális költségeket fedezi. A Windows rendszerű virtuális gépekhez tartozó virtuálisgép-infrastruktúrára fordított foglalás alkalmazásának módja megegyezik a nem Windows rendszerű virtuális gépekével. A Windows-szoftverekért vCPU-nként külön díjat kell fizetnie. Lásd: [Windows-szoftverköltségek foglalás esetén](../reservations/reserved-instance-windows-software-costs.md). A [Windows Serverhez készült Azure Hybrid Benefit](../../virtual-machines/windows/hybrid-use-benefit-licensing.md) igénybevételével fedezheti Windows-licencelési költségeit.

## <a name="discount-can-apply-to-different-sizes"></a>A kedvezmény különböző méretekre alkalmazható

Ha egy Reserved VM Instances-példány megvásárlásakor kiválasztja a **Példányméret-rugalmasságra optimalizálva** beállítást, akkor a kedvezmény általi lefedettség a virtuális gép kiválasztott méretére vonatkozik. Ez vonatkozhat az ugyanazon sorozatú példányméret-rugalmassági csoportban található más virtuálisgép-méretekre is. További információkért tekintse meg [a Reserved VM Instances virtuális gépeinek méretrugalmasságát](../../virtual-machines/windows/reserved-vm-instance-size-flexibility.md) ismertető cikket.

## <a name="premium-storage-vms-dont-get-non-premium-discounts"></a>A Premium Storage virtuális gépekre a nem prémium szintű kedvezmények nem vonatkoznak

Íme egy példa. Tegyük fel, hogy öt Standard_D1 virtuális géphez vásárolt foglalást. A foglalási kedvezmény csak a Standard_D1 virtuális gépekre vagy az ugyanazon példánycsaládban lévő virtuális gépekre vonatkozik. A kedvezmény nem vonatkozik a Standard_DS1 virtuális gépekre vagy a DS1 példányméret-rugalmassági csoportban lévő más méretekre.

A foglalási kedvezmény alkalmazása figyelmen kívül hagyja a virtuális gépekhez használt mérőszámot, és csak a ServiceType kiértékelését végzi. Tekintse meg az `ServiceType` értékét az `AdditionalInfo` területen, és állapítsa meg a virtuális gép példányrugalmassági csoportjára/sorozatára vonatkozó információkat. Az értékek a használati adatokat tartalmazó CSV-fájlban találhatók.

Nem módosíthatja közvetlenül a foglalás példányrugalmassági csoportját/sorozatát a vásárlás után. Azonban a virtuális gép foglalását *lecserélheti* az egyik példányrugalmassági csoportról/sorozatról egy másikra.

## <a name="services-that-get-vm-reservation-discounts"></a>A virtuálisgép-foglalási kedvezményekre jogosult szolgáltatások

A virtuálisgép-foglalások a több szolgáltatás általi virtuálisgép-használatra is alkalmazhatók, nem csupán a virtuális gépek üzemelő példányaira. A foglalási kedvezményekre jogosult erőforrások a példány méretének rugalmassági beállításától függően változnak.

### <a name="instance-size-flexibility-setting"></a>Példány méretrugalmassági beállítása

A példány méretrugalmassági beállítása határozza meg, hogy mely szolgáltatásokra vonatkozik a fenntartott példányokhoz kapcsolódó kedvezmény.

Ha a *ConsumedService* értéke `Microsoft.Compute`, a foglalási kedvezmények automatikusan érvényesülnek minden megfelelő virtuálisgép-használat esetében, attól függetlenül, hogy a beállítás be vagy ki van-e kapcsolva. Ebből kifolyólag ellenőrizze a használati adatait a *ConsumedService* értékének megtekintéséhez. Néhány példa:

- Virtuális gépek
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

A példányok méretrugalmasságára vonatkozó további információkért tekintse meg [a Reserved VM Instances virtuális gépeinek méretrugalmasságát](../../virtual-machines/windows/reserved-vm-instance-size-flexibility.md) ismertető cikket.


## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik azok a foglalások az Azure-ban?](../reservations/save-compute-costs-reservations.md)
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Előre fizetés fenntartott Azure SQL Database-kapacitással rendelkező SQL Database számítási erőforrásokért](../../azure-sql/database/reserved-capacity-overview.md)
- [Foglalások kezelése az Azure-ban](../reservations/manage-reserved-vm-instance.md)
- [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](../reservations/understand-reserved-instance-usage.md)
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](../reservations/understand-reserved-instance-usage-ea.md)
- [A foglalási kihasználtság ismertetése CSP-előfizetésnél](/partner-center/azure-reservations)
- [A Reservations díjában nem szereplő Windows-szoftverköltségek](../reservations/reserved-instance-windows-software-costs.md)
