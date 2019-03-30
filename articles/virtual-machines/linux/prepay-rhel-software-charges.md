---
title: Red Hat Enterprise Linux-csomagok – Azure foglalások vásárlásával |} A Microsoft Docs
description: Ismerje meg, hogyan fizessen elő az a Red Hat-használat és megtakarítást érhet el a használatalapú fizetéses költségekhez képest.
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
ms.date: 01/18/2019
ms.author: yashar
ms.openlocfilehash: e90241d9da140ca33d1aae8ffc5636f8eb2fbc74
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58653017"
---
# <a name="prepay-for-red-hat-enterprise-linux-plans-from-azure-reservations"></a>Fizessen elő az Azure foglalásai közül Red Hat Enterprise Linux-csomagok

Fizessen elő a Red Hat használatra vonatkozóan, és megtakarítást érhet el a használatalapú fizetéses költségekhez képest. A kedvezmény csak a Red Hat-mérőszámok és nem a virtuális gép használatát a alkalmazni. Virtuális gépek külön-külön még nagyobb megtakarítás foglalást vásárolhat.

Red Hat szoftverfrissítési csomagok az Azure Portalon vásárolhat. A csomag vásárlása:

- Meg kell egy tulajdonosi szerepkör legalább egy vállalati vagy használatalapú fizetéses előfizetésre.
- Vállalati előfizetés esetén **fenntartott példányok hozzáadása** engedélyezve kell lennie a [a nagyvállalati szerződések portáljának](https://ea.azure.com). Vagy, ha ez a beállítás le van tiltva, az előfizetés egy nagyvállalati szerződés rendszergazdájának kell lennie.
- A Cloud Solution Provider (CSP) program a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatja meg a Red Hat-csomagok.

## <a name="buy-a-red-hat-software-plan"></a>Red Hat szoftverfrissítési csomag vásárlása

1. Lépjen a [foglalások](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) az Azure Portalon.
1. Válassza ki **Hozzáadás** , és válassza ki a Red Hat Linux.
1. Adja meg a kötelező mezőket. Bármely Red Hat Linux rendszerű virtuális gép, amely megfelel az Ön a vásárlás attribútumait a kedvezményt kap. A tényleges száma üzemelő példánya, amely a kedvezményt lekérése a hatókör és a kiválasztott mennyiség függenek.

    | Mező      | Leírás|
    |:------------|:--------------|
    |Name (Név)        |Ez a vásárlás neve.|
    |Előfizetés|Ezt a csomagot használt előfizetés. A fizetési módot, az előfizetés fel van töltve a Foglalás az előzetes költségek. Az előfizetés típusúnak kell lennie a nagyvállalati szerződés (számok kínálnak: MS-AZR-0017P vagy MS-AZR - 0148 P) vagy a használatalapú fizetés (számok kínálnak: MS-AZR-0003P vagy MS-AZR - 0023 P). Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva. Használatalapú fizetéses előfizetéseknél a díjakat az előfizetéshez tartozó hitelkártyára terheljük vagy a számlafizetési módnak megfelelően számlázzuk.|
    |Hatókör       |A hatókör egy előfizetést vagy több előfizetés (megosztott hatókör) is foglalkozik. Ha ki: <ul><li>Red Hat Linux-használat ebben az előfizetésben egyetlen előfizetés – a csomag kedvezmény érvényes. </li><li>Közös – a csomag rendszer alkalmazza a kedvezményt Red Hat Linux-használatra a számlázási környezeten belül minden előfizetésben. A vállalati ügyfelek a megosztott hatókörrel a regisztráció és a regisztráció belüli összes előfizetésre tartalmazza. Használatalapú fizetéses ügyfelek számára a megosztott hatókörrel a fiók rendszergazdája által létrehozott összes utólagos elszámolású előfizetések.</li></ul>|
    |Felkészülés     |Válassza ki a Red Hat Linux-csomagot. A azonosítja megvásárlása milyen segítségre van szüksége tekintse meg megismerése, hogyan kell alkalmazni a Red Hat Linux Enterprise szoftvert foglalási kedvezményt.|
    |Virtuális gép mérete     |Red Hat Linux-díjszabás attól függ, hogy a virtuális gép vcpu-k számát. Válassza ki, a Red Hat Linux rendszerű virtuális gépeken vcpu-k számát jelenti.|
    |Időtartam        |Egy vagy három év.|
    |Mennyiség    |Virtuális gépek, hogy a Red Hat Linux vásárol, tervezze meg. A mennyiség a futó kérheti le a számlázási kedvezményt Red Hat Linux-példányok számát.|
1. Válassza a **Beszerzés** lehetőséget.
1. Válassza ki **megtekintése a Foglalás** a vásárlás állapotának megjelenítéséhez.

A foglalási kedvezményt a rendszer automatikusan alkalmazza minden futó Red Hat virtuális gépeket, amely megfelel a foglalást. A kedvezmény kizárólag a Red Hat mérő vonatkozik. Ez a csomag nem vonatkozik a virtuális gép számítási díjakat.

## <a name="discount-applies-to-different-vm-sizes"></a>Különböző méretű virtuális gépek vonatkozik kedvezmény

Fenntartott VM-példányok, például a Red Hat Linux-csomagok példány mérete rugalmasan kínálnak. Ez azt jelenti, hogy a kedvezmény érvényes-e még a Red Hat tervből vásárolt a különböző méretű virtuális gép telepítésekor. További információkért lásd: megismerés, hogyan kell alkalmazni a Red Hat Linux Enterprise szoftvert foglalási kedvezményt.

## <a name="cancellation-and-exchanges-not-allowed"></a>Megszakítás és cseréje nem engedélyezett

Nem vonhatja vissza, vagy exchange egy Red Hat-csomag, amelytől vásárolta. A használati szokásainak megfelelő csomagot vásárolja meg. A azonosítja megvásárlása milyen segítségre van szüksége tekintse meg megismerése, hogyan kell alkalmazni a Red Hat Linux Enterprise szoftvert foglalási kedvezményt.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan kezelheti a foglalást, lásd: [kezelése az Azure-foglalások](../../billing/billing-manage-reserved-vm-instance.md).

További tudnivalókért tekintse meg a következő cikkeket:

- [Mik az Azure-foglalásokat?](../../billing/billing-save-compute-costs-reservations.md)
- [Az Azure-ban foglalások kezelése](../../billing/billing-manage-reserved-vm-instance.md)
- Megismerheti, hogyan kell alkalmazni a Red Hat-foglalási kedvezményt
- [A használatalapú fizetéses előfizetést foglalás használati adatai](../../billing/billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalás használati adatai](../../billing/billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdése van, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.