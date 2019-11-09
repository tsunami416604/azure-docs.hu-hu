---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 261ae22348cd82b129727261c619727917e19c96
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832056"
---
### <a name="record-names"></a>Rekordnevek

Az Azure DNS-ben a rekordok relatív nevek használatával vannak meghatározva. A *teljes* tartománynév (FQDN) tartalmazza a zóna nevét, a *relatív* név azonban nem. Például a zóna relatív rekordjának neve `www` `contoso.com` megadja a teljes rekord nevét `www.contoso.com`.

Egy *csúcs*rekord a gyökérnél egy DNS-rekord vagy egy DNS-zóna *csúcsa*. Például a DNS-zónában `contoso.com`egy APEX-rekordhoz a teljes név is `contoso.com` (ezt néha egy *meztelen* tartománynak is nevezik).  Az egyezmény szerint a "\@" relatív név az APEX-rekordok ábrázolására szolgál.

### <a name="record-types"></a>Rekordtípusok

Minden DNS-rekord rendelkezik névvel és típussal. A rekordok különféle típusokba vannak rendezve attól függően, hogy milyen adatokat tartalmaznak. A leggyakoribb típus az „A” rekord, amely egy nevet képez le egy IPv4-címhez. Egy másik gyakori típus, az „MX” rekord, egy nevet képez le egy levelezési kiszolgálóhoz.

A Azure DNS az összes gyakori DNS-bejegyzéstípust támogatja: A, AAAA, CAA, CNAME, MX, NS, PTR, SOA, SRV és TXT. Vegye figyelembe, hogy az [SPF-rekordok TXT-rekordok használatával vannak jelölve](../articles/dns/dns-zones-records.md#spf-records).

### <a name="record-sets"></a>Rekordhalmazok

Előfordulhat, hogy több, azonos nevű és típusú DNS-rekordot is létre kell hoznia. Tegyük fel például, hogy a „www.contoso.com” webhely két különböző IP-címről is üzemel. A webhelynek két különböző A-rekordra van szüksége a két IP-címhez. Példa egy rekordhalmazra:

    www.contoso.com.        3600    IN    A    134.170.185.46
    www.contoso.com.        3600    IN    A    134.170.188.221

Az Azure DNS minden DNS-rekordot a *rekordhalmazok* használatával kezel. A rekordhalmazok (más néven az *erőforrás*-rekordhalmazok) az egy zónába tartozó, ugyanazzal a névvel és típussal rendelkező DNS-rekordok gyűjteményei. A legtöbb rekordhalmaz egyetlen rekordot tartalmaz. Azonban a fenti példa, melyben a rekordhalmaz egynél több rekordot tartalmaz, sem ritka.

Tegyük fel például, hogy már létrehozott egy A „www” rekordot a „contoso.com” zónában, amely a „134.170.185.46” IP-címre mutat (a fenti első rekord).  A második rekord létrehozása esetén a rekordot a meglévő rekordhalmazhoz kellene hozzáadnia, egy további rekordhalmaz létrehozása helyett.

A SOA és CNAME típusú rekordok kivételt jelentenek ez alól. A DNS-szabványok nem engedélyeznek ugyanazzal a névvel több rekordot ezen típusok esetén, ezért ezek a rekordhalmazok csak egy rekordot tartalmazhatnak.