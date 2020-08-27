---
title: A foglalási kedvezmények ismertetése – Azure Database for MySQL
description: Megtudhatja, hogyan alkalmazható foglalási kedvezmény az Azure Database for MySQL-kiszolgálókra.
author: kummanish
ms.author: manishku
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 4ef19b1fc8f0d503f1a35c60afd1faec2a548ca4
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88681991"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mysql"></a>A foglalási kedvezmény alkalmazása Azure Database for MySQL-re

A foglalási kedvezmény a fenntartott Azure Database for MySQL-kapacitás megvásárlása után automatikusan alkalmazva lesz a foglalás attribútumaival és mennyiségével egyező MySQL-kiszolgálókra. A foglalás csak az Azure Database for MySQL számítási költségeit fedezi. A tárolásért és a hálózatkezelésért a normál díjakat kell fizetnie.

## <a name="how-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazása

A foglalási kedvezmény csak akkor érvényes, ha ***folyamatosan igénybe veszi***. Ez azt jelenti, hogy ha nem rendelkezik megfelelő erőforrásokkal egy adott órában, akkor az arra az órára vonatkozó foglalási mennyiség elveszik. A lefoglalt, de fel nem használt órák nem vihetők tovább.</br>

Egy erőforrás leállításakor a rendszer a foglalási kedvezményt automatikusan a megadott hatókör egy másik egyező erőforrására alkalmazza. Ha nem találhatók egyező erőforrások a megadott hatókörben, akkor a lefoglalt órák elvesznek.

## <a name="discount-applied-to-azure-database-for-mysql"></a>Azure Database for MySQL-re alkalmazott kedvezmény

A fenntartott Azure Database for MySQL-kapacitásra érvényes kedvezményt a rendszer óránként alkalmazza a futó MySQL-kiszolgálókra. A megvásárolt foglalást a rendszer megfelelteti a futó Azure Database for MySQL-kiszolgálók általi számításierőforrás-használatnak. Azon MySQL-kiszolgálók esetén, amelyek nem futnak egy teljes órán át, a rendszer automatikusan a foglalási attribútumokkal egyező más Azure Database for MySQL-kiszolgálóra alkalmazza a foglalást. A kedvezmény párhuzamosan futó Azure Database for MySQL-kiszolgálókra is alkalmazható. Ha nem rendelkezik a foglalási attribútumokkal egyező olyan MySQL-kiszolgálóval, amely egy teljes órán át fut, akkor arra az órára nem kapja meg a foglalási kedvezménnyel járó teljes előnyt.

Az alábbi példák bemutatják, hogyan lesz alkalmazva a fenntartott Azure Database for MySQL-kapacitásra érvényes kedvezmény a megvásárolt magok száma alapján, és az alapján, mikor futnak.

* **1. példa**: Fenntartott Azure Database for MySQL-kapacitást vásárolt 8 virtuális maghoz. Ha 16 virtuális magos Azure Database for MySQL-kiszolgálót futtat, amely egyezik a foglalás többi attribútumával, a MySQL-kiszolgáló 8 virtuális magjának számításierőforrás-használata után a használatalapú árat kell fizetnie, a foglalási kedvezményt pedig a 8 magos MySQL-kiszolgáló egy órányi számításierőforrás-használatára kapja meg.</br>

A többi példa esetében azt feltételezzük, hogy a fenntartott Azure Database for MySQL-kapacitást egy 16 virtuális magos Azure Database for MySQL-kiszolgálóhoz vásárolta, és a többi foglalási attribútum megegyezik a futó MySQL-kiszolgálókkal.

* **2. példa**: Két 8 virtuális magos Azure Database for MySQL-kiszolgálót futtat egy-egy óráig. A 16 virtuális magos foglalási kedvezményt a rendszer mindkét 8 virtuális magos Azure Database for MySQL-kiszolgáló számításierőforrás-használatára alkalmazza.

* **3. példa**: Egy 16 virtuális magos Azure Database for MySQL-kiszolgálót futtat 13:00 órától 13:30-ig. Egy másik 16 virtuális magos Azure Database for MySQL-kiszolgálót 13:30-tól 14:00 óráig futtat. A foglalási kedvezmény mindkettőt fedezi.

* **4. példa**: Egy 16 virtuális magos Azure Database for MySQL-kiszolgálót futtat 13:00 órától 13:45-ig. Egy másik 16 virtuális magos Azure Database for MySQL-kiszolgálót 13:30-tól 14:00 óráig futtat. A 15 perces átfedésért használatalapú díjat kell fizetnie. A fennmaradó idő számításierőforrás-használatára érvényes a foglalási kedvezmény.

Az Azure Reservations számlázási használati jelentésekben történő alkalmazásának megismeréséhez és megtekintéséhez lásd [az Azure Reservations használatát ismertető](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) cikket.

## <a name="next-steps"></a>További lépések

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
