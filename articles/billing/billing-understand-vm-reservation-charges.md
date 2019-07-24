---
title: A Azure Reserved VM Instances kedvezmény ismertetése | Microsoft Docs
description: Ismerje meg, hogyan történik az Azure-beli fenntartott VM-példányra vonatkozó kedvezmény alkalmazása a futó virtuális gépekre.
author: yashesvi
manager: yashar
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2019
ms.author: banders
ms.openlocfilehash: 191160035f516d818d5537c5c47f9604998c46f7
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849993"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Az Azure foglalási kedvezmény alkalmazása a virtuális gépekre

Az Azure-beli fenntartott virtuálisgép-példány megvásárlása után a foglalási kedvezmény automatikusan a foglalás attribútumait és mennyiségét megegyező virtuális gépekre lesz alkalmazva. A foglalás lefedi a virtuális gépek számítási költségeit.

A foglalási kedvezmény az Azure piactéren vásárolt alapszintű virtuális gépekre vonatkozik.

SQL Database fenntartott kapacitásért lásd: az [Azure Reserved instances kedvezményének ismertetése](billing-understand-reservation-charges.md).

A következő táblázat a fenntartott VM-példányok megvásárlása után mutatja be a virtuális gép költségeit. Minden esetben a normál díjszabás szerint kell fizetnie a tárolásért és a hálózatkezelésért.

| Virtuális gép típusa  | Fenntartott VM-példánnyal kapcsolatos díjak |
|-----------------------|--------------------------------------------|
|Linux rendszerű virtuális gépek további szoftverek nélkül | A foglalás a virtuális gép infrastrukturális költségeire vonatkozik.|
|Linuxos virtuális gépek szoftveres költségekkel (például Red Hat) | A foglalás az infrastrukturális költségek fedezésére is vonatkozik. További szoftverekért kell fizetnie.|
|Windows rendszerű virtuális gépek további szoftverek nélkül |A foglalás az infrastrukturális költségek fedezésére is vonatkozik. Windows-szoftverekért kell fizetnie.|
|Windows rendszerű virtuális gépek további szoftverrel (például SQL Server) | A foglalás az infrastrukturális költségek fedezésére is vonatkozik. Windows-szoftverekért és további szoftverért kell fizetnie.|
|Windows rendszerű virtuális gépek [Azure Hybrid Benefit](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | A foglalás az infrastrukturális költségek fedezésére is vonatkozik. A Windows-szoftverek költségeit a Azure Hybrid Benefit szabályozza. A további szoftverekért külön díjat számítunk fel.|

## <a name="how-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazása

A foglalási kedvezmény "*use-this-or-Lose-it*". Ha tehát nem rendelkezik a megfelelő erőforrásokkal, akkor az adott órára vonatkozó foglalási mennyiség elvész. Nem lehet továbbítani a használaton kívüli órákat.

Egy erőforrás leállításakor a foglalási kedvezmény automatikusan a megadott hatókörben lévő másik egyező erőforrásra vonatkozik. Ha nem található egyező erőforrás a megadott hatókörben, akkor a lefoglalt órák elvesznek.

## <a name="reservation-discount-for-non-windows-vms"></a>Nem Windows rendszerű virtuális gépek foglalási kedvezménye

 Az Azure foglalási kedvezmény a virtuálisgép-példányok óránkénti futtatására vonatkozik. A megvásárolt foglalások megfelelnek a futó virtuális gépek által a foglalási kedvezmény alkalmazására kibocsátott használati feltételeknek. A teljes órát nem futtató virtuális gépek esetén a foglalás a foglalást nem használó más virtuális gépekről lesz kitöltve, beleértve az egyidejű futtatású virtuális gépeket is. Az óra végén az óra alatt lévő virtuális gépek foglalási alkalmazása zárolva van. Abban az esetben, ha egy virtuális gép nem fut egy órán belül egy órára vagy egyidejű virtuális gépre, nem tölti ki a foglalás óráját, a foglalást az adott órára kihasználjuk. A következő gráf a kiszámlázható virtuális gépek használatára vonatkozó foglalás alkalmazását szemlélteti. Az ábra egy foglalási vásárláson és két megfelelő virtuálisgép-példányon alapul.

![Képernyőfelvétel egy alkalmazott foglalásról és két megfelelő VM-példányról](./media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. A foglalási sor feletti használatért az utólagos elszámolású díjszabást kell fizetni. A foglalások sorban nem számítunk fel díjat, mert a foglalások vásárlásának részeként már befizették.
2. 1 óra, a példány 1 0,75 óra fut, és példány 2 0,5 óra fut. Az 1. órában felhasznált teljes használat 1,25 óra. Az utólagos elszámolású díjszabás díját a fennmaradó 0,25 óra után számítjuk fel.
3. A 2. és az Hour 3 esetében mindkét példány 1 órára futott. Az egyik példányt a foglalás tartalmazza, a másikat utólagos elszámolású díjszabással számoljuk el.
4. A 4. órában az 1. példány 0,5 óra, a 2. példány pedig 1 órára fut. Az 1. példányt a foglalás teljes egészében lefedi, a 2. példány pedig 0,5 óra. Az utólagos elszámolású díjakat a fennmaradó 0,5 óra után számítjuk fel.

Ha szeretné megismerni és megtekinteni a Azure Reservations alkalmazását a számlázási használati jelentésekben, tekintse meg a [foglalás használatának ismertetése](billing-understand-reserved-instance-usage-ea.md)című témakört.

## <a name="reservation-discount-for-windows-vms"></a>Windows rendszerű virtuális gépek foglalási kedvezménye

Windowsos virtuálisgép-példányok futtatásakor a rendszer a foglalást az infrastrukturális költségek fedezésére alkalmazza. A Windows rendszerű virtuális gépekhez tartozó virtuálisgép-infrastruktúrára fordított foglalás alkalmazása megegyezik a nem Windows rendszerű virtuális gépekkel. A Windows-szoftverekért külön díjat kell fizetnie vCPU alapján. Lásd: [a Windows-szoftverek költségei a foglalásokkal](billing-reserved-Instance-windows-software-costs.md). Az [Azure Hybrid Benefit for Windows Serverre vonatkozó](../virtual-machines/windows/hybrid-use-benefit-licensing.md)licencelési költségeket a Windows-kiszolgálóval is lefedi.

## <a name="discount-can-apply-to-different-sizes"></a>A kedvezmény eltérő méretekre is alkalmazható

A fenntartott VM-példányok megvásárlásakor, ha az **optimalizált**: **példány méretének rugalmasságát**választja, a kedvezményes lefedettség a kiválasztott virtuálisgép-mérettől függ. A foglalás a virtuális gépek (VM-EK) méretére is vonatkozhat ugyanabban a méretű adatsorozat-csoportban. További információ: [virtuális gépek méretének rugalmassága fenntartott VM](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)-példányokkal.

## <a name="discount-applies-to-matching-servicetype-only"></a>A kedvezmény csak a megfelelő ServiceType vonatkozik

A foglalási kedvezmény csak azokra a virtuális gépek használatára `ServiceType` vonatkozik, `AdditionalInfo` ahol a érték megegyezik a megvásárolt foglalással. A foglalási kedvezmény alkalmazása figyelmen kívül hagyja a virtuális gépekhez használt mérőszámot, és csak a kiértékelését `ServiceType`végzi. Adja meg, hogy melyik szolgáltatástípus vásárolta meg a virtuális gépet. A prémium szintű Storage-foglalások esetében nem prémium szintű Storage-foglalást, vagy ellenkező módon válthat.

## <a name="services-that-get-vm-reservation-discounts"></a>A virtuális gépek foglalási kedvezményeit lekérdező szolgáltatások

A VM-foglalások a több szolgáltatásból kibocsátott virtuálisgép-használatra vonatkozhatnak, nem csupán a virtuális gépek üzembe helyezésére. A foglalási kedvezményeket lefoglaló erőforrások a példány méretének rugalmassági beállításától függően változnak.

### <a name="instance-size-flexibility-setting"></a>Példány méretének rugalmassági beállítása

A példány méretének rugalmassága beállítás határozza meg, hogy mely szolgáltatások kapják meg a fenntartott példányok kedvezményeit.

Azt jelzi, hogy be van-e kapcsolva a beállítás, és a foglalási kedvezmények automatikusan érvényesek- `Microsoft.Compute`e a *ConsumedService* a megfelelő virtuális gépek használatára. Ezért a *ConsumedService* értéknél keresse meg a használati adatokat. Néhány példa:

- Virtual machines (Virtuális gépek)
- Virtuálisgép-méretezési csoportok
- Tároló szolgáltatás
- Azure Batch üzemelő példányok (felhasználói előfizetési módban)
- Azure Kubernetes Service (AKS)
- Service Fabric

Ha a beállítás be van kapcsolva, a foglalási kedvezmények automatikusan érvényesek a virtuális gépek használatára, ha a *ConsumedService* a következő elemek bármelyike:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Ellenőrizze a használati adatok *ConsumedService* értékét annak megállapításához, hogy a használat jogosult-e a foglalási kedvezményekre.

A példányok méretének rugalmasságával kapcsolatos további információkért lásd: [virtuális gépek méretének rugalmassága fenntartott VM](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)-példányokkal.


## <a name="need-help-contact-us"></a>Segítség Kapcsolat

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Azure Reservationsről, tekintse meg a következő cikkeket:

- [Mik az Azure-foglalások?](billing-save-compute-costs-reservations.md)
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Előre fizetés fenntartott Azure SQL Database-kapacitással rendelkező SQL Database számítási erőforrásokért](../sql-database/sql-database-reserved-capacity.md)
- [Azure-beli foglalások kezelése](billing-manage-reserved-vm-instance.md)
- [Az utólagos elszámolású előfizetés foglalási használatának ismertetése](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalási használatának ismertetése](billing-understand-reserved-instance-usage-ea.md)
- [A CSP-előfizetések foglalási használatának ismertetése](/partner-center/azure-reservations)
- [A Windows-szoftverek nem tartalmazzák a foglalásokat](billing-reserved-instance-windows-software-costs.md)
