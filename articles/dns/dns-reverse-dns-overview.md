---
title: A fordított DNS áttekintése az Azure-ban – Azure DNS
description: Ebben a tanulási útvonalon ismerkedhet meg a fordított DNS működésével és az Azure-ban való használatával
services: dns
documentationcenter: na
author: rohinkoul
manager: KumuD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: bf3da62e989f0e029efdc8e9c70f5f45e0ddd765
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932294"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>A fordított DNS és támogatás áttekintése az Azure-ban

Ez a cikk áttekintést nyújt a DNS-fordítás működéséről, valamint az Azure-ban támogatott fordított DNS-forgatókönyvekről.

## <a name="what-is-reverse-dns"></a>Mi a fordított DNS?

A hagyományos DNS-rekordok engedélyezik a DNS-név (például a "www.contoso.com") és az IP-cím (például a 64.4.6.100) leképezését.  A FORDÍTOTT DNS lehetővé teszi egy IP-cím (64.4.6.100) fordítását egy névre ("www.contoso.com").

A fordított DNS-rekordokat számos helyzetben használják. A fordított DNS-rekordokat például széles körben használják az e-mailes levélszemét elleni küzdelemben az e-mail üzenet feladójának ellenőrzésével.  A fogadó levelezőkiszolgáló lekéri a küldő kiszolgáló IP-címének fordított DNS-rekordját, és ellenőrzi, hogy az állomás jogosult-e e-mail küldésére az eredeti tartományból. 

## <a name="how-reverse-dns-works"></a>A fordított DNS működése

A fordított DNS-rekordokat speciális DNS-zónákban, más néven "ARPA" zónákban tárolják.  Ezek a zónák külön DNS-hierarchiát alkotnak a normál hierarchia tárolótartományaival, például a "contoso.com".These zones form a separate DNS hierarchy in parallel with the normal hierarchy hosting domains such as "contoso.com".

Például a "www.contoso.com" DNS-rekord egy "A" DNS-rekord használatával valósul meg, amelynek neve "www" a contoso.com zónában.  Ez az A rekord a megfelelő IP-címre mutat, ebben az esetben a 64.4.6.100.  A névlekérdezés külön-külön történik, a "6.4.64.in-addr.arpa" zónában található "100" nevű "PTR" rekord használatával (vegye figyelembe, hogy az IP-címek az ARPA zónákban sztorníroznak.)  Ez a PTR rekord, ha megfelelően van konfigurálva, a "www.contoso.com" névre mutat.

Ha egy szervezethez IP-címblokk van rendelve, akkor a megfelelő ARPA-zóna kezelésére is jogosult. Az Azure által használt IP-címblokkoknak megfelelő ARPA-zónákat a Microsoft üzemelteti és kezeli. Az internetszolgáltató üzemeltetheti az ARPA-zónát a saját IP-címeihez, vagy lehetővé teheti az ARPA-zóna üzemeltetését egy Ön által választott DNS-szolgáltatásban, például az Azure DNS-ben.

> [!NOTE]
> A továbbító DNS-keresések és a névfeloldási DNS-keresések külön, párhuzamos DNS-hierarchiákban vannak megvalósítva. A "www.contoso.com" névlekérdezése **nem** a "contoso.com" zónában található, hanem a megfelelő IP-címblokk ARPA zónájában található. Az IPv4- és IPv6-címblokkokhoz külön zónák használhatók.

### <a name="ipv4"></a>IPv4

Az IPv4 névkeresési zóna nevének a következő `<IPv4 network prefix in reverse order>.in-addr.arpa`formátumúnak kell lennie: .

Ha például a 192.0.2.0/24 előtagban lévő IP-címekkel rendelkező állomások rekordjainak tárolására fordított zónát hoz létre, a zóna neve a cím hálózati előtagjának (192.0.2) elkülönítésével, `.in-addr.arpa`majd a sorrend (2.0.192) megfordításával és az utótag hozzáadásával jön létre.

|Alhálózati osztály|Hálózati előtag  |Fordított hálózati előtag  |Szabványos utótag  |Fordított zóna neve |
|-------|----------------|------------|-----------------|---------------------------|
|A osztály|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|B osztály|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|C osztály|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Osztály nélküli IPv4-delegálás

Bizonyos esetekben a szervezet számára lefoglalt IP-tartomány kisebb, mint a C osztályú (/24) tartomány. Ebben az esetben az IP-tartomány nem esik `.in-addr.arpa` a zónahierarchia zónahatárán belüli határvonalra, ezért nem delegálható gyermekzónaként.

Ehelyett egy másik mechanizmust használ az egyes névkeresési (PTR) rekordok vezérlésének átvitelére egy dedikált DNS-zónába. Ez a mechanizmus minden IP-tartományhoz átad egy gyermekzónát, majd a tartomány minden IP-címét külön-külön leképezi az adott gyermekzónához a CNAME rekordok használatával.

Tegyük fel például, hogy egy szervezet az isp-je megkapja a 192.0.2.128/26 IP-tartományt. Ez 64 IP-címet jelent, 192.0.2.128-tól 192.0.2.191-ig. A fordított DNS ebben a tartományban a következőképpen valósítható meg:
- A szervezet létrehoz egy 128-26.2.0.192.in-addr.arpa nevű névkeresési zónát. A "128-26" előtag a C osztályú (/24) tartományon belül a szervezethez rendelt hálózati szegmenst jelöli.
- Az internetszolgáltató ns-rekordokat hoz létre a fenti zóna DNS-delegálásának beállításához a C osztályú szülőzónából. CNAME rekordokat is létrehoz a szülő (C osztály) névkeresési zónában, leképezve az IP-tartomány minden IP-címét a szervezet által létrehozott új zónához:

```
$ORIGIN 2.0.192.in-addr.arpa
; Delegate child zone
128-26    NS       <name server 1 for 128-26.2.0.192.in-addr.arpa>
128-26    NS       <name server 2 for 128-26.2.0.192.in-addr.arpa>
; CNAME records for each IP address
129       CNAME    129.128-26.2.0.192.in-addr.arpa
130       CNAME    130.128-26.2.0.192.in-addr.arpa
131       CNAME    131.128-26.2.0.192.in-addr.arpa
; etc
```
- A szervezet ezután kezeli az egyes PTR-rekordokat a gyermekzónán belül.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
A "192.0.2.129" IP-cím névlekérdezéseinek névlekérdezései a "129.2.0.192.in-addr.arpa" nevű PTR-rekordhoz. Ez a lekérdezés a szülőzónában lévő CNAME-en keresztül a gyermekzónában lévő PTR rekordra oldódik fel.

### <a name="ipv6"></a>IPv6

Az IPv6 névkeresési zóna nevének a következő formában kell lennie:`<IPv6 network prefix in reverse order>.ip6.arpa`

Például,. A 2001:db8:1000:abdc:/64 előtagban lévő IP-címekkel rendelkező állomások fordított zónájának létrehozásakor a zóna neve a cím hálózati előtagjának (2001:db8:abdc::)) elkülönítésével jön létre. Ezután bontsa ki az IPv6 hálózati előtagot a [nulla tömörítés](https://technet.microsoft.com/library/cc781672(v=ws.10).aspx)eltávolításához, ha az IPv6-címelőtag (2001:0db8:abdc:0000::)) rövidítésére használták. A fordított hálózati előtag (`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`) létrehozásához és az utótag hozzáadásához az előtag ban lévő hexadecimális szám között egy pont van határolójelként, és hozzá kell adni a sorrendet. `.ip6.arpa`


|Hálózati előtag  |Kibontott és fordított hálózati előtag |Szabványos utótag |Fordított zóna neve  |
|---------|---------|---------|---------|
|2001:db8:abdc::/64    | 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2        | .ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:db8:1000:9102::/64    | 2.0.1.9.0.0.1.8.b.d.0.1.0.0.2        | .ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Az Azure támogatása a fordított DNS-hez

Az Azure két külön forgatókönyvet támogat a DNS-kapcsolatban:

**Az IP-címblokknak megfelelő névkeresési zóna üzemeltetése.**
Az Azure DNS segítségével [üzemeltetheti a névkeresési zónákat, és kezelheti a PTR-rekordokat az egyes név-DNS-keresésekhez,](dns-reverse-dns-hosting.md)mind az IPv4, mind az IPv6 esetében.  A névkeresési (ARPA) zóna létrehozásának, a delegálás beállításának és a PTR-rekordok konfigurálásának folyamata megegyezik a normál DNS-zónák kal.  Az egyetlen különbség az, hogy a delegálást a DNS-regisztráló helyett az internetszolgáltatón keresztül kell konfigurálni, és csak a PTR-bejegyzéstípust kell használni.

**Konfigurálja az Azure-szolgáltatáshoz rendelt IP-cím fordított DNS-rekordját.** Az Azure lehetővé teszi [az Azure-szolgáltatáshoz rendelt IP-címek névlekérdezésének konfigurálását.](dns-reverse-dns-for-azure-services.md)  Ezt a névlekérdezést az Azure PTR-rekordként konfigurálja a megfelelő ARPA-zónában.  Ezeket az ARPA-zónákat, amelyek az Azure által használt összes IP-tartománynak felelnek meg, a Microsoft üzemelteti

## <a name="next-steps"></a>További lépések

A fordított DNS-ről további információt a [Wikipédia névfeloldási DNS-keresése című témakörben talál.](https://en.wikipedia.org/wiki/Reverse_DNS_lookup)
<br>
Ismerje meg, hogyan [üzemeltetheti az internetszolgáltató által hozzárendelt IP-tartomány névkeresési zónáját az Azure DNS-ben.](dns-reverse-dns-for-azure-services.md)
<br>
Ismerje meg, hogyan kezelheti az [Azure-szolgáltatások fordított DNS-rekordjait.](dns-reverse-dns-for-azure-services.md)

