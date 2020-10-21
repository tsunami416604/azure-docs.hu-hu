---
title: Az Azure-beli dedikált gazdagépek Reserved Instances-kedvezményének ismertetése
description: Megtudhatja, hogyan érvényesül az Azure Reserved VM Instances-kedvezmény Azure-beli dedikált gazdagépek esetében.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 8d273aae3588a006f7b0a55d2798b7a43c040c9b
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148370"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-azure-dedicated-hosts"></a>Az Azure-foglalási kedvezmény alkalmazása Azure-beli dedikált gazdagépek esetében

Miután megvásárolt egy Azure-beli fenntartott, dedikált gazdagéppéldányt, a foglalási kedvezmény automatikusan érvényesül azokon a dedikált gazdagépeken, amelyek megfelelnek a foglalás attribútumainak és mennyiségének. A foglalás fedezi a dedikált gazdagépek számítási költségeit.

## <a name="how-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazása

A foglalási kedvezmény csak akkor érvényes, ha *folyamatosan igénybe veszi*. Ez azt jelenti, hogy ha nem rendelkezik megfelelő erőforrásokkal egy adott órában, akkor az arra az órára vonatkozó foglalási mennyiség elveszik. A lefoglalt, de fel nem használt órák nem vihetők tovább.

Egy dedikált gazdagép törlésekor a rendszer a foglalási kedvezményt automatikusan a megadott hatókör egy másik egyező erőforrására alkalmazza. Ha nem találhatók egyező erőforrások a megadott hatókörben, akkor a lefoglalt órák *elvesznek*.

## <a name="reservation-discount-for-dedicated-hosts"></a>Foglalási kedvezmény dedikált gazdagépek esetében

Az Azure-beli fenntartott, dedikált gazdagéppéldány kedvezményt biztosít a dedikált gazdagépekhez használt számítási infrastruktúrához kapcsolódó költségek esetében. A kedvezmény a dedikált gazdagépekre vonatkozik, függetlenül attól, hogy a virtuális gépek használják-e őket. A foglalás nem fedezi a dedikált gazdagépen üzembe helyezett virtuális géphez kapcsolódó további (például licencelési, hálózathasználati vagy tárolási) költségeket.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége,  [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik azok a foglalások az Azure-ban?](./save-compute-costs-reservations.md)

- [Azure-beli dedikált gazdagépek használata](../../virtual-machines/dedicated-hosts.md)

- [Dedikált gazdagépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Foglalások kezelése az Azure-ban](./manage-reserved-vm-instance.md)

- [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](./understand-reserved-instance-usage.md)

- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](./understand-reserved-instance-usage-ea.md)

- [A foglalási kihasználtság ismertetése CSP-előfizetésnél](/partner-center/azure-reservations)