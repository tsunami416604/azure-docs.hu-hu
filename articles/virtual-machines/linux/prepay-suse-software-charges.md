---
title: Fizessen elő az szoftvercsomagok – alap - fenntartások Azure
description: Ismerje meg, hogyan lehet szoftverfrissítési csomagok megtakarítást érhet el a használatalapú fizetéses költségekhez képest kifizetése.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: c8e9d07bf01536d7865bd79f667a937037d96837
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67489885"
---
# <a name="prepay-for-azure-software-plans"></a>Előre fizetés Azure-szoftvercsomagokért

Fizessen elő a SUSE és a RedHat szoftver kihasználtságát az Azure-ban, ha pénzt takaríthat meg el a használatalapú fizetéses költségekhez képest. A kedvezmények csak a SUSE és RedHat mérőszámok, és nem a virtuális gép használatát a alkalmazni. Megvásárolhatja a foglalásokat a virtuális gépek külön-külön is a további helymegtakarítás érdekében.

SUSE és RedHat szoftverfrissítési csomagok az Azure Portalon vásárolhat. A csomag vásárlása:

- Legalább egy vállalati vagy önálló előfizetést a használatalapú díjszabás a tulajdonosi szerepkörrel kell rendelkeznie.
- Vállalati előfizetés esetén a **fenntartott példányok hozzáadása** beállítást engedélyezni kell a [a nagyvállalati szerződések portáljának](https://ea.azure.com/). Ha a beállítás le van tiltva, az előfizetéshez tartozó nagyvállalati szerződés rendszergazdájának kell lennie.
- A Cloud Solution Provider (CSP) program a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatja meg a szoftverfrissítési csomagok.

## <a name="buy-a-software-plan"></a>Szoftverfrissítési csomag vásárlása

1. Jelentkezzen be az Azure Portalon, és nyissa meg [foglalások](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
2. Kattintson a **Hozzáadás** , és válassza ki a szoftver tervet, amely szeretne vásárolni.
Adja meg a kötelező mezőket. SUSE Linux rendszerű virtuális gép vagy a RedHat rendszerű virtuális Gépet, amely megfelel az Ön a vásárlás attribútumait lekérdezi a kedvezményt. A tényleges száma üzemelő példánya, amely a kedvezményt lekérése a hatókör és a kiválasztott mennyiség függenek.
3. Válasszon egy előfizetést. Azt a csomagot szolgál.
Az előfizetés fizetési módja az előzetes költségek, a Foglalás díját. Az előfizetés típusúnak kell lennie a nagyvállalati szerződés (számok kínálnak: MS-AZR-0017P vagy MS-AZR - 0148 P) és a használatalapú díjszabás az egyes szerződés (számok kínálnak: MS-AZR-0003P vagy MS-AZR-0023P).
    - Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva.
    - Az egyes előfizetésre, használatalapú díjszabás bankkártyával vagy számla ellenében az előfizetés fizetési módot, a díjakat számlázzuk ki.
4. Válassza ki a hatókört. A hatókör egy előfizetést vagy több előfizetés (megosztott hatókör) is foglalkozik.
    - Egyetlen előfizetés – a csomag kedvezményt van alkalmazza a megfelelő használati az előfizetésben.
    - Közös – a csomag kedvezményt van alkalmazza a megfelelő példányok a számlázási környezetben minden előfizetésben. Nagyvállalati ügyfeleink esetén a számlázási környezetben a regisztráció és az összes előfizetés tartalmazza a regisztrációt. A használatalapú díjszabási ügyfelekkel az egyes csomagok a számlázási környezetben használatalapú díjszabási előfizetések, a fiók rendszergazdája által létrehozott minden egyes csomagok.
5. Válasszon ki egy terméket, választhat a virtuális gép mérete és a kép típusa. Csak a kiválasztott virtuális gép méretét a kedvezmény érvényes.
6. Válasszon egy egyéves vagy hároméves kifejezést.
7. Válassza ki a mennyiség, amely a számlázási kedvezményt kaphat fizetett Virtuálisgép-példányok száma.
8. Adja hozzá a termék a kosár, tekintse át és beszerzési.

A foglalási kedvezményt a rendszer automatikusan alkalmazza a szoftver mérőszám, amely előzetesen fizet. Virtuális gép számítási díjakat nem terjed ki a tervet. A virtuális gép foglalások külön-külön is vásárolhat.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>Különböző méretű SUSE virtuális gépek vonatkozik kedvezmény

Fenntartott VM-példányok, például a SUSE Linux – csomagok példány mérete rugalmasságot kínálnak. A kedvezmény vonatkozik, akkor is, ha telepít egy virtuális Gépet egy eltérő méretű a beszerezte a SUSE-csomagból. További információkért lásd: [megismerheti, hogyan kell alkalmazni a szoftverfrissítési csomag kedvezményt](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="redhat-plan-discount"></a>RedHat terv kedvezmény

Csak a Red Hat Enterprise Linux virtuális gépek csomagok érhetők el. A kedvezmény nem vonatkozik, a RedHat Enterprise Linux SAP HANA rendszerű virtuális gépeken vagy RedHat Enterprise Linux SAP Business Apps virtuális gépek.

RedHat terv kedvezmények csak a Virtuálisgép-méretet választhat a vásárlás időpontjában, vonatkoznak. RHEL-csomagok nem térítjük vagy kicserélt vásárlás után.


## <a name="cancellation-and-exchanges-not-allowed"></a>Megszakítás és cseréje nem engedélyezett

Nem vonhatja vissza, vagy exchange-SUSE vagy RedHat csomag, amelytől vásárolta. A használati szokásainak megfelelő csomagot vásárolja meg. Mi a vásárlás azonosításához kapcsolatos útmutatásért lásd: [megismerheti, hogyan kell alkalmazni a szoftverfrissítési csomag kedvezményt](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan kezelheti a foglalást, lásd: [kezelése az Azure-foglalások](../../billing/billing-manage-reserved-vm-instance.md).

További tudnivalókért tekintse meg a következő cikkeket:

- [Mik az Azure-foglalásokat?](../../billing/billing-save-compute-costs-reservations.md)
- [Az Azure-ban foglalások kezelése](../../billing/billing-manage-reserved-vm-instance.md)
- [Megismerheti, hogyan kell alkalmazni a SUSE-foglalási kedvezményt](../../billing/billing-understand-suse-reservation-charges.md)
- [A használatalapú fizetéses előfizetést foglalás használati adatai](../../billing/billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalás használati adatai](../../billing/billing-understand-reserved-instance-usage-ea.md)
