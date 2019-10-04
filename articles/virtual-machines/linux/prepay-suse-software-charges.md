---
title: Előre fizetés a szoftvercsomagok számára – Azure Reservations
description: Megtudhatja, hogyan fizethet elő előre a szoftveres csomagokra az utólagos elszámolású költségek megtakarítása érdekében.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: e4cab8963e3083d265d0254c81b78fbabb84a269
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091541"
---
# <a name="prepay-for-azure-software-plans"></a>Előre fizetés Azure-szoftvercsomagokért

Ha az Azure-ban előre fizetett SUSE-és RedHat-szoftveres használatot, az utólagos elszámolású költségekkel megtakaríthat pénzt. A kedvezmények csak a SUSE és a RedHat mérőszámokra vonatkoznak, és nem a virtuális gépek használatára. A további megtakarítások érdekében a virtuális gépek foglalása külön vásárolható meg.

A Azure Portalban a SUSE és a RedHat szoftvercsomag is megvásárolható. Csomag megvásárlása:

- Az utólagos elszámolású díjszabáshoz legalább egy Enterprise vagy egyéni előfizetéshez tulajdonosi szerepkörrel kell rendelkeznie.
- Vállalati előfizetések esetén a **fenntartott példányok hozzáadása** beállítást engedélyezni kell az [EA portálon](https://ea.azure.com/). Ha a beállítás le van tiltva, az előfizetés nagyvállalati rendszergazdájának kell lennie.
- A Cloud Solution Provider (CSP) program esetében a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatják meg a szoftvercsomagot.

## <a name="buy-a-software-plan"></a>Szoftvercsomag vásárlása

1. Jelentkezzen be a Azure Portalba, és [](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)lépjen a foglalások pontra.
2. Kattintson a **Hozzáadás** elemre, majd válassza ki a megvásárolni kívánt szoftvercsomagot.
Töltse ki a kötelező mezőket. Minden olyan SUSE Linux rendszerű virtuális gép vagy RedHat virtuális gép, amely megfelel a megvásárolni kívánt mennyiségnek, a kedvezményt kapja. A kedvezményt megkapó központi telepítések tényleges száma a kiválasztott hatókörtől és mennyiségtől függ.
3. Válasszon egy előfizetést. A csomagért kell fizetni.
Az előfizetés fizetési módja a foglalás előzetes díjait terheli. Az előfizetés típusának Nagyvállalati Szerződésnak kell lennie (ajánlat száma: MS-AZR-0017P vagy MS-AZR-0148P) vagy külön szerződés az utólagos elszámolású díjszabással (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P).
    - Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva.
    - Az utólagos elszámolású előfizetések díját az előfizetés bankkártyája vagy a számla fizetési módja alapján számítjuk fel.
4. Válasszon ki egy hatókört. A hatókör egyetlen előfizetésre vagy több előfizetésre (megosztott hatókörre) is vonatkozhat.
    - Egyszeri előfizetés – a csomagra vonatkozó kedvezményt a rendszer az előfizetésben szereplő használatra alkalmazza.
    - Megosztott – a csomagra vonatkozó kedvezményt a rendszer a számlázási környezetben található bármely előfizetésben szereplő példányokra alkalmazza. A nagyvállalati ügyfelek esetében a számlázási környezet a regisztráció, és tartalmazza a beléptetés összes előfizetését. Az utólagos elszámolású díjszabást használó egyéni csomagok esetében a számlázási környezet minden egyes, a fiók rendszergazdája által létrehozott, utólagos elszámolású díjszabási előfizetéssel rendelkező csomag.
5. Válasszon ki egy terméket a virtuális gép méretének és a rendszerkép típusának kiválasztásához. A kedvezmény csak a kiválasztott VM-méretre vonatkozik.
6. Válasszon egy vagy három éves időszakot.
7. Válasszon ki egy mennyiséget, amely az előre fizetett virtuálisgép-példányok száma, amelyek beszerezhetik a számlázási kedvezményt.
8. Vegye fel a terméket a kosárba, tekintse át és vásárolja meg.

A foglalási kedvezményt a rendszer automatikusan alkalmazza a szoftveres fogyasztásmérőre, amelyre előre fizet. A csomag nem fedi le a virtuális gépek számítási díjait. A virtuális gép foglalásait külön is megvásárolhatja.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>A kedvezmény a különböző SUSE VM-méretekre vonatkozik

A fenntartott VM-példányokhoz hasonlóan a SUSE Linux-csomagok is a példányok méretének rugalmasságát kínálnak. A kedvezmény akkor is érvényes, ha olyan virtuális gépet telepít, amely a vásárolt SUSE-csomagtól eltérő méretű. További információ: [a szoftveres csomagra vonatkozó kedvezmény alkalmazásának megismerése](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="redhat-plan-discount"></a>RedHat csomag kedvezménye

A csomagok csak Red Hat Enterprise Linux virtuális gépek esetében érhetők el. A kedvezmény nem vonatkozik a RedHat Enterprise Linux SAP HANA virtuális gépekre vagy a RedHat Enterprise Linux SAP Business apps virtuális gépekre.

A RedHat csomagra vonatkozó kedvezmények csak a vásárláskor kiválasztott virtuálisgép-méretre vonatkoznak. A RHEL terveket nem lehet a vásárlás után visszatéríteni vagy kicserélni.


## <a name="cancellation-and-exchanges-not-allowed"></a>A törlés és az adatcsere nem engedélyezett

A vásárolt SUSE vagy RedHat csomagot nem lehet megszakítani vagy kicserélni. A használati szokásainak megfelelő csomagot vásárolja meg. A megvásárolni kívánt elemek azonosításával kapcsolatban lásd: [a szoftveres csomagra vonatkozó kedvezmény alkalmazásának megismerése](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

A foglalások kezelésével kapcsolatos információkért lásd: [Azure-foglalások kezelése](../../billing/billing-manage-reserved-vm-instance.md).

További információt a következő cikkekben talál:

- [Mi a Azure Reservations?](../../billing/billing-save-compute-costs-reservations.md)
- [Fenntartások kezelése az Azure-ban](../../billing/billing-manage-reserved-vm-instance.md)
- [A SUSE foglalási kedvezmény alkalmazásának ismertetése](../../billing/billing-understand-suse-reservation-charges.md)
- [Az utólagos elszámolású előfizetés foglalási használatának ismertetése](../../billing/billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalási használatának ismertetése](../../billing/billing-understand-reserved-instance-usage-ea.md)
