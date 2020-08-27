---
title: A foglalási kedvezmények alkalmazása az Azure Synapse Analyticsben | Microsoft Docs
description: Megtudhatja, hogy a megtakarítások eléréséhez hogyan alkalmazhatók a foglalási kedvezmények az Azure Synapse Analyticsben.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: banders
ms.openlocfilehash: f0bd57befc0ec30473065ac69026a77e21e30e5c
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682603"
---
# <a name="how-reservation-discounts-apply-to-azure-synapse-analytics"></a>A foglalási kedvezmények alkalmazása az Azure Synapse Analyticsben

A fenntartott Azure Synapse Analytics-kapacitás megvásárlása után automatikusan alkalmazva lesz a foglalási kedvezmény az adott régióban található kiépített példányokra. A foglalási kedvezmény az Azure Synapse Analytics cDWU-mérője által megjelenített használatra érvényes. A tárolásért és a hálózatkezelésért használatalapú díjat kell fizetnie.

## <a name="reservation-discount-application"></a>A foglalási kedvezmény alkalmazása

A fenntartott Azure Synapse Analytics-kapacitásokra érvényes kedvezményt a rendszer óránként alkalmazza a futó adattárházakra. Ha egy bizonyos órában nem rendelkezik üzembe helyezett adattárházzal, akkor arra az órára elveszíti a fenntartott kapacitást. A kapacitás nem vihető át a következő órára.

A vásárlás után a megvásárolt foglalást a rendszer adott időpontban a futó adattárházak általi Azure Synapse Analytics-használatnak felelteti meg. Ha leállít egyes adattárházakat, a foglalási kedvezmények automatikusan más egyező adattárházakra lesznek alkalmazva.

Azon adattárházak esetében, amelyek nem futnak egy teljes órán át, az adott órában automatikusan egyéb egyező példányokra lesz alkalmazva a foglalás.

## <a name="discount-examples"></a>Példák a kedvezményre

Az alábbi példák bemutatják, hogy a rendszer hogyan alkalmazza a fenntartott Azure Synapse Analytics-kapacitásokra érvényes kedvezményt az üzemelő példányoktól függően.

- **1. példa**: Vásárol 5 egységnyi 100 cDWU fenntartott kapacitást. Egy DW1500c Azure Synapse Analytics-példányt futtat egy órán át. Ebben az esetben a használat 15 egységnyi 100 cDWU kapacitás. A foglalási kedvezmény az 5 felhasznált egységre vonatkozik. A fennmaradó 10 egységnyi 100 cDWU kapacitásért, amelyet felhasznált használatalapú díjat fizet. Más szóval a részleges lefedettség több foglalás esetén is lehetséges.

- **2. példa**: Vásárol 5 egységnyi 100 cDWU fenntartott kapacitást. Két DW100c Azure Synapse Analytics-példányt futtat egy órán át. Ebben az esetben két használati esemény jön létre egyenként 1 egységnyi 100 cDWU használattal. Mindkét használati esemény megkapja a fenntartott kapacitásra érvényes kedvezményt. A fennmaradó 3 egységnyi 100 cDWU fenntartott kapacitás elveszik, és nem vihető tovább jövőbeli használatra. Más szóval egy foglalás több Azure Synapse Analytics-példányra is érvényesíthető.

- **3. példa**: Vásárol 1 egységnyi 100 cDWU fenntartott kapacitást. Két DW100c Azure Synapse Analytics-példányt futtat. Mindkettő 30 percig fut. Ebben az esetben mindkét használati esemény megkapja a fenntartott kapacitásra érvényes kedvezményt. Nincs olyan használat, amelyért használatalapú díjat kellene fizetnie.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

- Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mi az az Azure Reservations?](save-compute-costs-reservations.md)
- [Foglalási tranzakciók megtekintése](view-reservations.md)
- [Foglalási tranzakciók és kihasználtság lekérése API-n keresztül](reservation-apis.md)
- [Foglalások kezelése](manage-reserved-vm-instance.md)
