---
title: SUSE Linux – csomagok – Azure foglalások vásárlásával |} A Microsoft Docs
description: Ismerje meg, hogyan fizessen elő az a SUSE-használat és megtakarítást érhet el a használatalapú fizetéses költségekhez képest.
services: virtual-machines-linux
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2018
ms.author: yashar
ms.openlocfilehash: 9c3976a5fa98049de03f2a65b71f1fc927947142
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307830"
---
# <a name="prepay-for-suse-software-plans-from-azure-reservations"></a>Fizessen elő az Azure foglalásai közül SUSE szoftvercsomagok – alap

Fizessen elő a SUSE-használatra vonatkozóan, és megtakarítást érhet el a használatalapú fizetéses költségekhez képest. A kedvezmények csak SUSE-mérőszámok és nem a virtuális gép használatát a alkalmazni. Virtuális gépek külön-külön még nagyobb megtakarítás foglalást vásárolhat.

SUSE szoftver tervek az Azure Portalon vásárolhat. A csomag vásárlása:

- Meg kell egy tulajdonosi szerepkör legalább egy vállalati vagy használatalapú fizetéses előfizetésre.
- Vállalati előfizetés esetén foglalás vásárlások engedélyezve kell lennie a [a nagyvállalati szerződések portáljának](https://ea.azure.com).
- A Cloud Solution Provider (CSP) program a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatja meg a SUSE-csomagok.

## <a name="buy-a-suse-software-plan"></a>SUSE szoftver csomag vásárlása

1. Lépjen a [foglalások](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) az Azure Portalon.
1. Válassza ki **Hozzáadás** , és válassza ki a SUSE Linux.
1. Adja meg a kötelező mezőket. SUSE Linux rendszerű virtuális gép, megfelelő attribútumait, vásároljon lekérdezi a kedvezményt. A tényleges száma üzemelő példánya, amely a kedvezményt lekérése a hatókör és a kiválasztott mennyiség függenek.

    | Mező      | Leírás|
    |:------------|:--------------|
    |Name (Név)        |Ez a vásárlás neve.|
    |Előfizetés|Ezt a csomagot használt előfizetés. A fizetési módot, az előfizetés fel van töltve a Foglalás az előzetes költségek. Az előfizetés típusa kétféle lehet: nagyvállalati szerződés (ajánlatszám: MS-AZR-0017P) vagy használatalapú fizetéses (ajánlatszám: MS-AZR-0003P). Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva. Használatalapú fizetéses előfizetéseknél a díjakat az előfizetéshez tartozó hitelkártyára terheljük vagy a számlafizetési módnak megfelelően számlázzuk.|
    |Hatókör       |A hatókör egy előfizetést vagy több előfizetés (megosztott hatókör) is foglalkozik. Ha ki: <ul><li>SUSE Linux használati ebben az előfizetésben egyetlen előfizetés – a csomag kedvezmény érvényes. </li><li>Közös – a csomag rendszer alkalmazza a kedvezményt SUSE Linux-használatra a számlázási környezeten belül minden előfizetésben. A vállalati ügyfelek a megosztott hatókörrel a regisztráció és előfizetéseken belül a regisztráció (kivéve a fejlesztési és tesztelési előfizetések) magában foglalja. Használatalapú fizetéses ügyfelek számára a megosztott hatókörrel a fiók rendszergazdája által létrehozott összes utólagos elszámolású előfizetések.</li></ul>|
    |Szoftvercsomag     |Válassza ki a SUSE Linux-csomagot. Segítségre van szüksége az azonosításához, mit kell vásárolnia, lásd: [megismerheti, hogyan kell alkalmazni a SUSE Linux Enterprise szoftvert foglalási kedvezményt](../../billing/billing-understand-suse-reservation-charges.md).|
    |Virtuális gép mérete     |SUSE Linux-díjszabás attól függ, hogy a virtuális gép vcpu-k számát. Válassza ki, a SUSE Linux rendszerű virtuális gépeken vcpu-k számát jelenti.|
    |Időtartam        |Egy vagy három év.|
    |Mennyiség    |A SUSE Linux tervezése vásárol az virtuális gépek száma. A mennyiség a futó kérheti le a számlázási kedvezményt SUSE Linux-példányok számát.|
1. Válassza a **Beszerzés** lehetőséget.
1. Válassza ki **megtekintése a Foglalás** a vásárlás állapotának megjelenítéséhez.

A foglalási kedvezményt a rendszer automatikusan alkalmazza minden futó SUSE virtuális gépek, amely megfelel a foglalást. A kedvezmény kizárólag a SUSE-fogyasztásmérő vonatkozik. Ez a csomag nem vonatkozik a virtuális gép számítási díjakat.

## <a name="discount-applies-to-different-vm-sizes-with-instance-size-flexibility"></a>Példány mérete rugalmasan különböző méretű virtuális gépek vonatkozik kedvezmény

Fenntartott VM-példányok, például a SUSE Linux – csomagok rugalmasabbá példány mérete. Ez azt jelenti, hogy a kedvezmény érvényes-e még a SUSE tervből vásárolt egy eltérő méretű virtuális gép telepítésekor. További információkért lásd: [megismerheti, hogyan kell alkalmazni a SUSE Linux Enterprise szoftvert foglalási kedvezményt](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="cancellation-and-exchanges-not-allowed"></a>Megszakítás és cseréje nem engedélyezett

Nem vonhatja vissza, vagy exchange a SUSE-csomag, amelytől vásárolta. Ellenőrizze, hogy a megfelelő csomag vásárlása a használat. Segítségre van szüksége az azonosításához, mit kell vásárolnia, lásd: [megismerheti, hogyan kell alkalmazni a SUSE Linux Enterprise szoftvert foglalási kedvezményt](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan kezelheti a foglalást, lásd: [kezelése az Azure-foglalások](../../billing/billing-manage-reserved-vm-instance.md).

További tudnivalókért tekintse meg a következő cikkeket:

- [Mik az Azure-foglalásokat?](../../billing/billing-save-compute-costs-reservations.md)
- [Az Azure-ban foglalások kezelése](../../billing/billing-manage-reserved-vm-instance.md)
- [Megismerheti, hogyan kell alkalmazni a SUSE-foglalási kedvezményt](../../billing/billing-understand-suse-reservation-charges.md)
- [A használatalapú fizetéses előfizetést foglalás használati adatai](../../billing/billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalás használati adatai](../../billing/billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdése van, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.