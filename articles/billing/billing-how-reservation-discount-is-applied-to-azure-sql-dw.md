---
title: Hogyan alkalmazni a Foglalás kedvezmények az Azure SQL Data Warehouse |} A Microsoft Docs
description: Ismerje meg, hogyan a Foglalás kedvezmények az Azure SQL Data Warehouse, pénz megtakarítása érdekében érvényesek.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 10e19377d31489cd19465fe6171ffb530bd58c28
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014216"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>Hogyan a Foglalás kedvezmények az Azure SQL Data Warehouse érvényesek

Miután az Azure SQL Data Warehouse szolgáltatás számára fenntartott kapacitást vásárol, a rendszer automatikusan alkalmazza a foglalási kedvezményt adattárházak, amely az adott régióban található. Az SQL Data Warehouse cDWU mérőszám alapján kibocsátott használat alkalmazza a foglalási kedvezményt. Tárolási és hálózatkezelési utólagos elszámolású díjszabás szerint számoljuk el.

## <a name="reservation-discount-application"></a>Foglalás vonatkozó kedvezmény alkalmazása

Adattárházak óránként fut az SQL Data Warehouse tartalékkapacitást kedvezmény érvényes. Ha nem rendelkezik egy adattárház üzembe helyezett egy órára, majd a lefoglalt kapacitás az adott órára van adattisztítást. Ez nem jelenik meg.

Miután megvásárolta a foglalást, amely a vásárlás megfeleltetett futtatásával adattárházakhoz bármikor időben kibocsátott az SQL Data Warehouse-használat. Fel, ha bizonyos adattárházakhoz, Foglalás kedvezmények automatikusan alkalmazhatja bármely más egyező adattárházakhoz.

A adattárházaknál ne egy teljes órányi a Foglalás rendszer automatikusan alkalmazza többi egyező példány tartalmaz az adott órában.

## <a name="discount-examples"></a>Példák kedvezmény

Az alábbi példák bemutatják, hogyan az SQL Data Warehouse lefoglalt kapacitás kedvezményes vonatkozik, attól függően, a központi telepítéseket.

- **1. példa**: 100 fenntartott cDWU kapacitás 5 egység vásárolható meg. Egy óráig futtat egy DW1500c SQL Data Warehouse-példányhoz. Ebben az esetben kihasználtsága 100 cDWU használati 15 egység bocsásson ki. A foglalási kedvezményt a 5 egységek használt vonatkozik. Akkor kell fizetnie, a fennmaradó 10 egység 100 cDWU használati használt utólagos elszámolású díjszabás szerint használatával.

- **2. példa**: 100 fenntartott cDWU kapacitás 5 egység vásárolható meg. Két DW100c az SQL Data Warehouse-példányt futtat egy óra. Ebben az esetben a két használati események kibocsátott 100 cDWU használati 1 egység. Mindkét használati események lekérése tartalékkapacitást kedvezményeket jelentenek. A fennmaradó 3 egységet a 100 fenntartott cDWU kapacitás adattisztítást vannak, és nem jelenik meg későbbi használat céljából.

- **3. példa**: 1 egység 100 fenntartott cDWU-kapacitást vásárolhat. Két DW100c az SQL Data Warehouse-példányt futtat. Minden 30 percig fut. Ebben az esetben mindkét használati események lekérése tartalékkapacitást kedvezményeket jelentenek. Nem kell fizetni használatalapú fizetési díjszabás vonatkozik.

## <a name="need-help-contact-us"></a>Segítség Kapcsolat

- Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

Azure-foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik az Azure-foglalásokat?](billing-save-compute-costs-reservations.md)
- [Foglalás tranzakciók megtekintése](billing-view-reservations.md)
- [Foglalás tranzakciók és a kihasználtság API-n keresztül](billing-reservation-apis.md)
- [Foglalások kezelése](billing-manage-reserved-vm-instance.md)
