---
title: A foglalási kedvezmény alkalmazása az Azure Cache for Redisre | Microsoft Docs
description: Megtudhatja, hogyan lehet alkalmazni a foglalási kedvezményt az Azure Cache for Redis-példányokra.
author: yegu-ms
manager: maiye
ms.service: cache
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: yegu
ms.openlocfilehash: 10fedaf5680976ae93984c9b2c607882f650019d
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213265"
---
# <a name="how-the-reservation-discount-is-applied-to-azure-cache-for-redis"></a>A foglalási kedvezmény alkalmazása az Azure Cache for Redisre

A foglalási kedvezmény a fenntartott Azure Cache for Redis-kapacitás megvásárlása után automatikusan alkalmazva lesz a foglalás attribútumaival és mennyiségével egyező gyorsítótárpéldányokra. A foglalás csak az Azure Cache for Redis számítási költségeit fedezi. A tárolásért és a hálózatkezelésért a normál díjakat kell fizetnie. A fenntartott kapacitás csak [prémium szintű](/https://docs.microsoft.com/azure/azure-cache-for-redis/quickstart-create-redis) gyorsítótárakhoz érhető el.

## <a name="how-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazása

A foglalási kedvezmény csak akkor érvényes, ha ***folyamatosan igénybe veszi***. Ez azt jelenti, hogy ha nem rendelkezik megfelelő erőforrásokkal egy adott órában, akkor az arra az órára vonatkozó foglalási mennyiség elveszik. A lefoglalt, de fel nem használt órák nem vihetők tovább.

Egy erőforrás leállításakor a rendszer a foglalási kedvezményt automatikusan a megadott hatókör egy másik egyező erőforrására alkalmazza. Ha nem találhatók egyező erőforrások a megadott hatókörben, akkor a lefoglalt órák elvesznek.

## <a name="discount-applied-to-azure-cache-for-redis"></a>Azure Cache for Redisre alkalmazott kedvezmény

A fenntartott Azure Cache for Redis-kapacitásokra érvényes kedvezményt a rendszer óránként alkalmazza a gyorsítótárakra. A megvásárolt foglalást a rendszer megfelelteti a futó gyorsítótárak általi számításierőforrás-használatnak. Amikor ezek a gyorsítótárak nem futnak egy teljes órán át, a rendszer automatikusan a foglalási attribútumokkal egyező más gyorsítótárakra alkalmazza a foglalást. A kedvezmény párhuzamosan futó gyorsítótárakra is alkalmazható. Ha nem rendelkezik a foglalási attribútumokkal egyező olyan gyorsítótárral, amely egy teljes órán át fut, akkor arra az órára nem kapja meg a foglalási kedvezménnyel járó teljes előnyt.

Az alábbi példák bemutatják, hogyan lesz alkalmazva a fenntartott Azure Cache for Redis-kapacitásra érvényes kedvezmény a megvásárolt gyorsítótárak száma alapján, valamint az alapján, mikor futnak.

* **1. példa**: Fenntartott Azure Cache for Redis-kapacitást vásárolt egy 6 GB-os gyorsítótárhoz. Ha 13 GB-os gyorsítótárat futtat, amely egyezik a foglalás többi attribútumával, az Azure Cache for Redis 7 GB-os számításierőforrás-használata után a használatalapú árat kell fizetnie, a foglalási kedvezményt pedig a 6 GB-os gyorsítótár egyórányi számításierőforrás-használatára kapja meg.

A többi példa esetében azt feltételezzük, hogy a fenntartott Azure Cache for Redis-kapacitást egy 26 GB-os gyorsítótárhoz vásárolta, és a többi foglalási attribútum megegyezik a futó gyorsítótárral.

* **2. példa**: Két 13 GB-os gyorsítótárat futtat egy órán át. A 26 GB-os foglalási kedvezményt a rendszer mindkét gyorsítótár számításierőforrás-használatára alkalmazza.

* **3. példa**: Egy 26 GB-os gyorsítótárat futtat 13:00-tól 13:30-ig. Egy másik 26 GB-os gyorsítótárat 13:30-tól 14:00-ig futtat. A foglalási kedvezmény mindkettőt fedezi.

* **4. példa**: Egy 26 GB-os gyorsítótárat futtat 13:00-tól 13:45-ig. Egy másik 26 GB-os gyorsítótárat 13:30-tól 14:00-ig futtat. A 15 perces átfedésért használatalapú díjat kell fizetnie. A fennmaradó idő számításierőforrás-használatára érvényes a foglalási kedvezmény.

Az Azure Reservations számlázási használati jelentésekben történő alkalmazásának megismeréséhez és megtekintéséhez lásd [az Azure Reservations használatát ismertető](/azure/billing/billing-understand-reserved-instance-usage-ea) cikket.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat
Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
