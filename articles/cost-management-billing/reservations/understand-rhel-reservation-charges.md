---
title: A Red Hat foglalási csomag kedvezményei – Azure
description: Tudnivalók a Red Hat-csomagkedvezmények alkalmazásáról a virtuális gépeken található Red Hat szoftveren.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: banders
ms.openlocfilehash: 4314a52488473bcacd4aa62a0e8f44d88d2b75f1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88681600"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Tudnivalók a Red Hat Linux Enterprise szoftver foglalásicsomag-kedvezményeinek alkalmazásáról az Azure-ban

Red Hat Linux-csomag vásárlása után a kedvezmény automatikusan alkalmazva lesz a foglalással egyező, üzembe helyezett Red Hat rendszerű virtuális gépekre. A Red Hat Linux-csomagok a Red Hat szoftver Azure-beli virtuális gépen történő futtatásának költségét fedezik.

A megfelelő Red Hat Linux-csomag megvásárlásához tudnia kell, milyen Red Hat rendszerű virtuális gépeket futtat, és azok hány virtuális processzorral rendelkeznek. Az alábbi szakaszok segítségével meghatározhatja a használati CSV-fájlból, hogy melyik csomagot érdemes megvásárolnia.

## <a name="discount-applies-to-different-vm-sizes"></a>A kedvezmény különböző virtuálisgép-méretekre érvényes

A Reserved VM Instanceshez hasonlóan a Red Hat-csomagok is biztosítják a példányok méretrugalmasságát. Ez azt jelenti, hogy a kedvezmény akkor is érvényes, ha eltérő számú virtuális processzorral rendelkező virtuális gépet helyez üzembe. A kedvezmény különböző virtuálisgép-méretekre érvényes a szoftvercsomagban.

A kedvezmény mértéke az alábbi táblázatokban szereplő arányoktól függ. Az arány a csoportban lévő virtuálisgép-méretek relatív lábnyomát hasonlítja össze. Az arány a virtuális gépek virtuális processzoraitól függ. Az arány használatával kiszámíthatja, hány virtuálisgép-példány kapja meg a Red Hat Linux-csomaggal járó kedvezményt.

Ha például Red Hat Linux Enterprise Server-csomagot vásárol egy 1–4 virtuális processzorral rendelkező virtuális gép számára, akkor az adott foglalás aránya 1. A kedvezmény az alábbiak esetében fedezi a Red Hat szoftver költségét:

- 1 üzembe helyezett virtuális gép 1–4 virtuális processzorral,
- vagy egy 5 vagy több virtuális processzorral rendelkező virtuális gép Red Hat Enterprise Linux-költségeinek 0,46 része vagy 46%-a.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Azure Portal Marketplace-en szereplő nevek:
- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 8.2

[A Red Hat Enterprise Linux azon mérőszámainak megtekintése, amelyekre a csomag vonatkozik](https://isfratio.blob.core.windows.net/isfratio/RHELRatios.csv)

## <a name="next-steps"></a>További lépések

A foglalással kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik azok az Azure-beli foglalások?](save-compute-costs-reservations.md)
- [Előre fizetés Azure Reservationsbeli Red Hat-szoftvercsomagokért](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Foglalások kezelése az Azure-ban](manage-reserved-vm-instance.md)
- [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](understand-reserved-instance-usage.md)
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
