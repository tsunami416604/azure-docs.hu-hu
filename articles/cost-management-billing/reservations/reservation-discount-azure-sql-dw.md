---
title: A foglalási kedvezmények alkalmazása az Azure SQL Data Warehouse-ban | Microsoft Docs
description: Megtudhatja, hogyan vannak alkalmazva a foglalási kedvezmények az Azure SQL Data Warehouse-ban, hogy Ön pénzt takaríthasson meg.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: b08f11799c1471af22138fefcd57ed1eb951a1a6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199381"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>A foglalási kedvezmények alkalmazása az Azure SQL Data Warehouse-ban

A fenntartott Azure SQL Data Warehouse-kapacitás megvásárlása után automatikusan alkalmazva lesz a foglalási kedvezmény az adott régióban található adattárházakra. A foglalási kedvezmény az SQL Data Warehouse cDWU-mérője által megjelenített használatra érvényes. A tárolásért és a hálózatkezelésért használatalapú díjat kell fizetnie.

## <a name="reservation-discount-application"></a>A foglalási kedvezmény alkalmazása

A fenntartott SQL Data Warehouse-kapacitásokra érvényes kedvezményt a rendszer óránként alkalmazza a futó adattárházakra. Ha egy bizonyos órában nem rendelkezik üzembe helyezett adattárházzal, akkor arra az órára elveszíti a fenntartott kapacitást. A fenntartott kapacitás nem vihető tovább.

A vásárlás után a megvásárolt foglalást a rendszer bármely időpontban megfelelteti a futó adattárházak általi SQL Data Warehouse-használatnak. Ha leállít egyes adattárházakat, a foglalási kedvezmények automatikusan más egyező adattárházakra lesznek alkalmazva.

Azon adattárházak esetében, amelyek nem futnak egy teljes órán át, az adott órában automatikusan egyéb egyező példányokra lesz alkalmazva a foglalás.

## <a name="discount-examples"></a>Példák a kedvezmény alkalmazására

Az alábbi példák bemutatják, hogy a rendszer hogyan alkalmazza a fenntartott SQL Data Warehouse-kapacitásokra érvényes kedvezményt az üzemelő példányoktól függően.

- **1. példa**: Vásárol 5 egységnyi 100 cDWU fenntartott kapacitást. Egy DW1500c SQL Data Warehouse-példányt futtat egy órán át. Ebben az esetben a használat 15 egységnyi 100 cDWU kapacitás. A foglalási kedvezmény az 5 felhasznált egységre vonatkozik. A fennmaradó 10 egységnyi 100 cDWU kapacitásért, amelyet felhasznált használatalapú díjat fizet. Más szóval a részleges lefedettség több foglalás esetén is lehetséges.

- **2. példa**: Vásárol 5 egységnyi 100 cDWU fenntartott kapacitást. Két DW100c SQL Data Warehouse-példányt futtat egy órán át. Ebben az esetben két használati esemény jön létre egyenként 1 egységnyi 100 cDWU használattal. Mindkét használati esemény megkapja a fenntartott kapacitásra érvényes kedvezményt. A fennmaradó 3 egységnyi 100 cDWU fenntartott kapacitás elveszik, és nem vihető tovább jövőbeli használatra. Más szóval egy foglalás több SQL Data Warehouse-példánnyal is egyeztethető.

- **3. példa**: Vásárol 1 egységnyi 100 cDWU fenntartott kapacitást. Két DW100c SQL Data Warehouse-példányt futtat. Mindkettő 30 percig fut. Ebben az esetben mindkét használati esemény megkapja a fenntartott kapacitásra érvényes kedvezményt. Nincs olyan használat, amelyért használatalapú díjat kellene fizetnie.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

- Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mi az az Azure Reservations?](save-compute-costs-reservations.md)
- [Foglalási tranzakciók megtekintése](view-reservations.md)
- [Foglalási tranzakciók és kihasználtság lekérése API-n keresztül](reservation-apis.md)
- [Foglalások kezelése](manage-reserved-vm-instance.md)
