---
title: A foglalási kedvezmény alkalmazása Azure Database for MariaDB-re
description: A foglalási kedvezmény alkalmazása Azure Database for MariaDB-re
author: kummanish
ms.author: manishku
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2019
ms.openlocfilehash: 340d395278eb924570e07d106308e63be3c2f9af
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995519"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mariadb"></a>A foglalási kedvezmény alkalmazása Azure Database for MariaDB-re

A fenntartott Azure Database for MariaDB-kapacitás megvásárlása után automatikusan alkalmazva lesz a foglalási kedvezmény a foglalás attribútumaival és mennyiségével egyező MariaDB-kiszolgálókra. A foglalás csak az Azure Database for MariaDB számítási költségeit fedezi. A tárolásért és a hálózatkezelésért a normál díjakat kell fizetnie. 

## <a name="how-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazása

A foglalási kedvezmény csak akkor érvényes, ha ***folyamatosan igénybe veszi***. Ez azt jelenti, hogy ha nem rendelkezik megfelelő erőforrásokkal egy adott órában, akkor az arra az órára vonatkozó foglalási mennyiség elveszik. A lefoglalt, de fel nem használt órák nem vihetők tovább.</br>

Egy erőforrás leállításakor a rendszer a foglalási kedvezményt automatikusan a megadott hatókör egy másik egyező erőforrására alkalmazza. Ha nem találhatók egyező erőforrások a megadott hatókörben, akkor a lefoglalt órák elvesznek.

## <a name="discount-applied-to-azure-database-for-mariadb"></a>Azure Database for MariaDB-re alkalmazott kedvezmény

A fenntartott Azure Database for MariaDB-kapacitásra érvényes kedvezményt a rendszer óránként alkalmazza a futó MariaDB-kiszolgálókra. A megvásárolt foglalást a rendszer megfelelteti a futó Azure Database for MariaDB-kiszolgálók általi számításierőforrás-használatnak. Azon MariaDB-kiszolgálók esetén, amelyek nem futnak egy teljes órán át, a rendszer automatikusan a foglalási attribútumokkal egyező más Azure Database for MariaDB-kiszolgálóra alkalmazza a foglalást. A kedvezmény párhuzamosan futó Azure Database for MariaDB-kiszolgálókra is alkalmazható. Ha nem rendelkezik a foglalási attribútumokkal egyező olyan MariaDB-kiszolgálóval, amely egy teljes órán át fut, akkor arra az órára nem kapja meg a foglalási kedvezménnyel járó teljes előnyt.

Az alábbi példák bemutatják, hogyan lesz alkalmazva a fenntartott Azure Database for MariaDB-kapacitásra érvényes kedvezmény a megvásárolt magok száma alapján, és annak alapján, hogy mikor futnak.

* **1. példa**: egy Azure Database for MariaDB fenntartott kapacitást vásárol egy 8 virtuális mag. Ha 16 virtuális magos Azure Database for MariaDB-kiszolgálót futtat, amely egyezik a foglalás többi attribútumával, a MariaDB-kiszolgáló 8 virtuális magjának számításierőforrás-használata után a használatalapú árat kell fizetnie, a foglalási kedvezményt pedig a 8 magos MariaDB-kiszolgáló egy órányi számításierőforrás-használatára kapja meg.</br>

A többi példa esetében azt feltételezzük, hogy a fenntartott Azure Database for MariaDB-kapacitást egy 16 virtuális magos Azure Database for MariaDB-kiszolgálóhoz vásárolta, és a többi foglalási attribútum megegyezik a futó MariaDB-kiszolgálókkal.

* **2. példa**: két Azure Database for MariaDB kiszolgáló futtatása óránként 8 virtuális mag. A 16 virtuális magos foglalási kedvezményt a rendszer mindkét 8 virtuális magos Azure Database for MariaDB-kiszolgáló számításierőforrás-használatára alkalmazza.

* **3. példa**: a 1 16-es virtuális mag Azure Database for MariaDB-kiszolgálót 1 órától 1:30 óráig futtathatja. Egy másik 16 virtuális magos Azure Database for MariaDB-kiszolgálót 13:30-tól 14:00 óráig futtat. A foglalási kedvezmény mindkettőt fedezi.

* **4. példa**: a 1 16 virtuális mag Azure Database for MariaDB-kiszolgálót 1 órától 1:45 óráig futtatja. Egy másik 16 virtuális magos Azure Database for MariaDB-kiszolgálót 13:30-tól 14:00 óráig futtat. A 15 perces átfedésért használatalapú díjat kell fizetnie. A fennmaradó idő számításierőforrás-használatára érvényes a foglalási kedvezmény.

Az Azure Reservations számlázási használati jelentésekben történő alkalmazásának megismeréséhez és megtekintéséhez lásd [az Azure Reservations használatát ismertető](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) cikket.

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel
Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
