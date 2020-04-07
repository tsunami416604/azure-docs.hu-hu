---
title: Szoftvercsomagok előre fizetése – Azure-foglalások
description: Ismerje meg, hogyan fizethet előre a szoftvercsomagokért, hogy pénzt takarítson meg a felosztó-ki-fel -költségeken keresztül.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/06/2020
ms.author: banders
ms.openlocfilehash: 3e05920e495dd4aa14be6c849590a37a2bafa33f
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757229"
---
# <a name="prepay-for-azure-software-plans"></a>Előre fizetés Azure-szoftvercsomagokért

Ha előre kifizeti a SUSE és a RedHat szoftverhasználatát az Azure-ban, pénzt takaríthat meg a használatalapú fizetésköltségeivel szemben. A kedvezmények csak a SUSE és a RedHat mérőkre vonatkoznak, a virtuális gép használatára nem. A virtuális gépekfoglalásait külön vásárolhatja meg további megtakarítások érdekében.

SUSE és RedHat szoftvercsomagokat vásárolhat az Azure Portalon. Csomag vásárlása:

- Legalább egy vállalati vagy egyéni előfizetéshez rendelkeznie kell, ha csak felosztó-ki-felárszabást ad.
- Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com/). Ha a beállítás le van tiltva, az előfizetéshez eA-rendszergazdának kell lennie.
- A Felhőszolgáltató (CSP) program esetében a rendszergazdai ügynökök vagy az értékesítési ügynökök megvásárolhatják a szoftvercsomagokat.

## <a name="buy-a-software-plan"></a>Szoftvercsomag vásárlása

1. Jelentkezzen be az Azure Portalra, és nyissa meg a [Foglalások című kaput.](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)
2. Kattintson a **Hozzáadás** gombra, majd válassza ki a megvásárolni kívánt szoftvercsomagot.
Töltse ki a szükséges mezőket. Minden SUSE Linux virtuális gép vagy RedHat virtuális gép, amely megfelel a megvásárolt tulajdonságoknak, megkapja a kedvezményt. Az engedményt kapó központi telepítések tényleges száma a kiválasztott hatókörtől és mennyiségtől függ.
3. Válasszon egy előfizetést. Arra használják, hogy kifizessék a tervet.
Az előfizetéses fizetési mód nak a foglalás előzetes költségeit számítják fel. Az előfizetéstípusnak nagyvállalati szerződésnek (ajánlatszámok: MS-AZR-0017P vagy MS-AZR-0148P) vagy egyéni megállapodásnak kell lennie a felosztó-kiosztó díjszabással (ajánlatszámok: MS-AZR-0003P vagy MS-AZR-0023P).
    - Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva.
    - A felosztó-kiosztó díjszabással rendelkező egyéni előfizetések esetén a díjakat az előfizetés hitelkártyájának vagy számlafizetési módjának terheli.
4. Válassza ki a hatókört. A hatókör egy előfizetést vagy több előfizetést (megosztott hatókört) fedezhet.
    - Egyszeri előfizetés – A csomagkedvezmény az előfizetés megfelelő használatára vonatkozik.
    - Megosztott – A csomag kedvezmény a számlázási környezetben bármely előfizetésmegfelelő példányokra vonatkozik. A vállalati ügyfelek számára a számlázási környezet a regisztráció, és tartalmazza az összes előfizetést a regisztrációban. A felosztó-ki-ki-díjszabásügyfelek egyéni csomag, a számlázási környezet minden egyéni csomagok pay-as-you-go díjszabási előfizetések által létrehozott fiók adminisztrátora.
    - Egyetlen erőforráscsoport – A foglalási engedményt csak a kijelölt erőforráscsoport egyező erőforrásaira alkalmazza.
5. Válasszon ki egy terméket a virtuális gép méretének és a képtípusnak a kiválasztásához. Az engedmény csak a kiválasztott virtuális gép méretére vonatkozik.
6. Válasszon egy éves vagy hároméves időszakot.
7. Válasszon egy mennyiséget, amely a számlázási engedményt lekérdező előre fizetett virtuálisgép-példányok száma.
8. Adja hozzá a terméket a kosárba, tekintse át és vásárolja meg.

A foglalási kedvezmény automatikusan vonatkozik a szoftvermérőre, amelyet előre kifizet. A virtuális gép számítási díjait a terv nem fedezi. A virtuális gép foglalásait külön vásárolhatja meg.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>A kedvezmény a különböző SUSE virtuális gépméretekre vonatkozik

A fenntartott virtuálisgép-példányokhoz hasonlóan a SUSE Linux-csomagok is rugalmasságot biztosítanak a példánymérethez. A kedvezmény akkor is érvényes, ha üzembe helyez egy virtuális gép, amely eltér a megvásárolt SUSE-csomag. További [információ: A szoftvercsomag-kedvezmény alkalmazásának ismertetése.](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)

## <a name="redhat-plan-discount"></a>RedHat terv kedvezmény

A csomagok csak Red Hat Enterprise Linux virtuális gépekhez érhetők el. A kedvezmény nem vonatkozik a RedHat Enterprise Linux SAP HANA virtuális gépekre vagy a RedHat Enterprise Linux SAP Business Apps virtuális gépekre.

A RedHat-csomagkedvezmények csak a vásárláskor kiválasztott virtuális gépméretre vonatkoznak. Az RHEL-csomagok nem téríthetők vissza és nem cserélhetők ki a vásárlás után.


## <a name="cancellation-and-exchanges-not-allowed"></a>A lemondás és a cserék nem engedélyezettek

Nem mondhatja le vagy cserélheti ki a megvásárolt SUSE- vagy RedHat-csomagot. A használati szokásainak megfelelő csomagot vásárolja meg. Ha segítségre van szüksége a vásárláshoz, [olvassa el A szoftvercsomag-kedvezmény alkalmazásának megértése](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)című témakört.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

A foglalások kezeléséről az [Azure-foglalások kezelése témakörben](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)olvashat.

További információ: a következő cikkek:

- [Mik azok az Azure-foglalások?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [A Reservations kezelése az Azure-ban](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [A SUSE foglalási kedvezmény alkalmazásának ismertetése](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)
- [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
