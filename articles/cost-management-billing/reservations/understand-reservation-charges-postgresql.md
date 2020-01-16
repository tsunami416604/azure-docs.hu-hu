---
title: A foglalási kedvezmény alkalmazása egykiszolgálós Azure Database for PostgreSQL-re
description: A foglalási kedvezmény alkalmazása egykiszolgálós Azure Database for PostgreSQL-re
author: kummanish
ms.author: manishku
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2019
ms.openlocfilehash: 2f1843690861a463c634f523e82facffc72b6743
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995506"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-postgresql-single-server"></a>A foglalási kedvezmény alkalmazása egykiszolgálós Azure Database for PostgreSQL-re

A fenntartott egykiszolgálós Azure Database for PostgreSQL-kapacitás megvásárlása után automatikusan alkalmazva lesz a foglalási kedvezmény a foglalás attribútumaival és mennyiségével egyező egykiszolgálós PostgreSQL-adatbázisokra. A foglalás csak az egykiszolgálós Azure Database for PostgreSQL számítási költségeit fedezi. A tárolásért és a hálózatkezelésért a normál díjakat kell fizetnie. 

## <a name="how-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazása

A foglalási kedvezmény csak akkor érvényes, ha ***folyamatosan igénybe veszi***. Ez azt jelenti, hogy ha nem rendelkezik megfelelő erőforrásokkal egy adott órában, akkor az arra az órára vonatkozó foglalási mennyiség elveszik. A lefoglalt, de fel nem használt órák nem vihetők tovább.</br>

Egy erőforrás leállításakor a rendszer a foglalási kedvezményt automatikusan a megadott hatókör egy másik egyező erőforrására alkalmazza. Ha nem találhatók egyező erőforrások a megadott hatókörben, akkor a lefoglalt órák elvesznek.

## <a name="discount-applied-to-azure-database-for-postgresql-single-server"></a>Az egykiszolgálós Azure Database for PostgreSQL-re érvényesített kedvezmény

A fenntartott egykiszolgálós Azure Database for PostgreSQL-kapacitásra érvényes kedvezményt a rendszer óránként alkalmazza a futó egykiszolgálós PostgreSQL-re. A megvásárolt foglalást a rendszer megfelelteti a futó egykiszolgálós Azure Database for PostgreSQL általi számításierőforrás-használatnak. Azon egykiszolgálós PostgreSQL-ek esetén, amelyek nem futnak egy teljes órán át, a rendszer automatikusan a foglalási attribútumokkal egyező más egykiszolgálós Azure Database for PostgreSQL-re alkalmazza a foglalást. A kedvezmény a párhuzamosan futó egykiszolgálós Azure Database for PostgreSQL-ekre is alkalmazható. Ha nem rendelkezik a foglalási attribútumokkal egyező olyan önálló PostgreSQL-kiszolgálóval, amely egy teljes órán át fut, akkor arra az órára nem kapja meg a foglalási kedvezménnyel járó teljes előnyt.

Az alábbi példák bemutatják, hogyan lesz alkalmazva a fenntartott önálló Azure Database for PostgreSQL-kiszolgáló kapacitására érvényes kedvezmény a megvásárolt magok száma alapján, és annak alapján, hogy mikor futnak.

* **1. példa**: egy Azure Database for PostgreSQL egyetlen kiszolgáló számára fenntartott kapacitást vásárol egy 8 virtuális mag. Ha 16 virtuális magos önálló Azure Database for PostgreSQL-kiszolgálót futtat, amely egyezik a foglalás többi attribútumával, az önálló PostgreSQL-kiszolgáló 8 virtuális magjának számításierőforrás-használata után a használatalapú árat kell fizetnie, a foglalási kedvezményt pedig a 8 magos önálló PostgreSQL-kiszolgáló egy órányi számításierőforrás-használatára kapja meg.</br>

A többi példa esetében azt feltételezzük, hogy a fenntartott önálló Azure Database for PostgreSQL-kiszolgáló kapacitását egy 16 virtuális magos önálló Azure Database for PostgreSQL-kiszolgálóhoz vásárolta, és a többi foglalási attribútum megegyezik a futó önálló PostgreSQL-kiszolgálókkal.

* **2. példa**: két Azure Database for PostgreSQL egyetlen kiszolgálót futtat, 8 virtuális mag óránként. A 16 virtuális magos foglalási kedvezményt a rendszer mindkét 8 virtuális magos önálló Azure Database for PostgreSQL-kiszolgáló számításierőforrás-használatára alkalmazza.

* **3. példa**: a 1 16-es virtuális mag-t az 1. és a 1:30 pm közötti Azure Database for PostgreSQL. Egy másik 16 virtuális magos önálló Azure Database for PostgreSQL-kiszolgálót 13:30-tól 14:00 óráig futtat. A foglalási kedvezmény mindkettőt fedezi.

* **4. példa**: a 1 16-es virtuális mag-t a-es Azure Database for PostgreSQL-es verzióban futtatja 1 órától 1:45 óráig. Egy másik 16 virtuális magos önálló Azure Database for PostgreSQL-kiszolgálót 13:30-tól 14:00 óráig futtat. A 15 perces átfedésért használatalapú díjat kell fizetnie. A fennmaradó idő számításierőforrás-használatára érvényes a foglalási kedvezmény.

Az Azure Reservations számlázási használati jelentésekben történő alkalmazásának megismeréséhez és megtekintéséhez lásd [az Azure Reservations használatát ismertető](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) cikket.

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel
Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
