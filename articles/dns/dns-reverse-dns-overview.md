---
title: "Az Azure-ban címfeloldási DNS áttekintése |} Microsoft Docs"
description: "Megtudhatja, hogyan címfeloldási DNS működik, és hogyan használható az Azure-ban"
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: jonatul
ms.openlocfilehash: 70a1ad070e812951fca3d2b19da12c67f0725dd0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>A címfeloldási DNS- és támogatás az Azure-ban – áttekintés

Ez a cikk lehetőséget nyújt az hogyan címfeloldási DNS works áttekintését, és a címfeloldási DNS-forgatókönyvek az Azure-ban támogatott.

## <a name="what-is-reverse-dns"></a>Mi az a címfeloldási DNS?

Hagyományos DNS-rekordokat a DNS-nevét (például "www.contoso.com") egy IP-címre (például 64.4.6.100) leképezéseket engedélyezhetik.  Névkeresési DNS lehetővé teszi, hogy a fordítás egy IP-cím (64.4.6.100) vissza a nevét ("www.contoso.com").

Névkeresési DNS-rekordok számos különféle helyzetekben is használja. Például címfeloldási DNS-rekordok széles körben használt e-mail levélszemét elleni egy e-mail üzenet feladójának ellenőrzésével.  A fogadó levelezési kiszolgáló lekéri a címfeloldási DNS-rekord a küldő kiszolgáló IP-cím, és ellenőrzi, hogy üzemeltető jogosult-e e-mail küldése a származó tartományból. 

## <a name="how-reverse-dns-works"></a>Hogyan címfeloldási DNS működik

Speciális DNS-zónák, úgynevezett "ARPA" zónák tárolt címfeloldási DNS-rekordokat.  A zónák egy külön DNS-hierarchiában, a normál hierarchiája, például "contoso.com" tartományt üzemeltet, párhuzamosan alkotnak.

Például a DNS rekord "www.contoso.com" a "contoso.com" zóna a "www" nevet a DNS-"A" rekord segítségével van megvalósítva.  Ezt A rekordot a megfelelő IP-cím, ebben az esetben 64.4.6.100 mutat.  A névkeresési segítségével van megvalósítva külön-külön, a "PTR" rekord nevű "100" a "6.4.64.in-addr.arpa" zónában (vegye figyelembe, hogy IP-címek felcseréli ARPA-zónák.)  A PTR típusú rekord, ha megfelelően lett konfigurálva mutat a neve "www.contoso.com".

Amikor egy szervezet hozzá van rendelve egy IP-Címblokk, azokat is szerezzen be a megfelelő ARPA zónát irányítására. Az ARPA-zónák az IP-címblokkok Azure által használt megfelelő tárolt és kezelt Microsoft által. Az Internetszolgáltató meg az ARPA időzónáját a saját IP-címek is tartalmazhat, vagy előfordulhat, hogy engedélyezni szeretné a működteti a ARPA zónát a DNS-szolgáltatás az Ön által választott, például az Azure DNS-ben.

> [!NOTE]
> DNS-címkeresés és a DNS-névlekérdezés különálló, párhuzamos DNS hierarchiákban valósíthatók meg. A "www.contoso.com" névlekérdezés **nem** üzemelteti a "contoso.com" zónában, hanem helyezkedik el a megfelelő IP-Címblokk ARPA zónában. Különálló zónákra az IPv4 és IPv6-címterületet használnak.

### <a name="ipv4"></a>IPv4-alapú

Egy IPv4 névkeresési zóna neve a következő formátumban kell megadni: `<IPv4 network prefix in reverse order>.in-addr.arpa`.

Például a névkeresési zónát, hogy állomásrekordokat állomások IP-címek, amelyek szerepelnek a 192.0.2.0/24 előtag létrehozásakor Zónanév hozhatók létre azoknak a hálózati előtag a cím (192.0.2) és a sorrend (2.0.192) felcserélni és Utótaghozzáadása`.in-addr.arpa`.

|Alhálózati osztály|Hálózati előtag  |Fordított hálózati előtag  |Standard utótag  |Névkeresési zóna neve |
|-------|----------------|------------|-----------------|---------------------------|
|A osztály|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|B osztály|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|C osztály|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Classless IPv4-delegálás

Bizonyos esetekben a szervezetek számára lefoglalt IP-címtartomány kisebb, mint egy C osztályú (/ 24) tartományon. Ebben az esetben az IP-címtartomány nem tartozik a zóna keretén belül a `.in-addr.arpa` hierarchia zónát, és ezért nem delegálható gyermek zónának.

Ehelyett egy másik módszer használata vezérlő egyes névkeresési (PTR) rekordok átvitele egy dedikált DNS-zónát. A mechanizmus delegálja a gyermekzónát minden IP-címtartomány, majd külön-külön leképezhető minden IP-cím a tartományban, hogy a gyermekzóna CNAME rekordok használatával.

Tegyük fel például, egy szervezet megkapja az IP-címtartomány 192.0.2.128/26 az Internetszolgáltató által. A 192.0.2.191 192.0.2.128 64 IP-címek, ez jelöli. Névkeresési DNS ezt a tartományt a következőképpen történik:
- A szervezet egy névkeresési zónát 128-26.2.0.192.in-addr.arpa hoz létre. Az előtag "128-26' jelöli a szervezeten belül a C osztályú hozzárendelt hálózati szegmens (/ 24) tartományon.
- Az internetszolgáltató által biztosított hoz létre a Névkiszolgálói rekordokat a DNS-delegálás, a fenti zóna a C osztályú szülőzónában beállítása. Is létre CNAME rekordok a szülő (C osztályú) névkeresési zónát, az új zóna a szervezet által létrehozott minden IP-címet az IP-címtartomány leképezése:

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
- A szervezet kezeli az egyes PTR-rekordok a gyermekzóna belül.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
A PTR típusú rekord IP-cím "192.0.2.129" lekérdezésekhez névlekérdezés "129.2.0.192.in-addr.arpa" nevű. Ez a lekérdezés szülőzóna a gyermekzóna PTR rekordjához CNAME keresztül oldható fel.

### <a name="ipv6"></a>IPv6

Egy IPv6-névkeresési zóna neve a következő formátumban kell lennie:`<IPv6 network prefix in reverse order>.ip6.arpa`

Például. Ha vannak létrehozása egy névkeresési zónát, hogy állomásrekordokat állomások IP-címek, amelyek a 2001:db8:1000:abdc:: / 64 előtag Zónanév hozhatók létre azoknak a hálózati előtag a cím (2001:db8:abdc::). Ezután bontsa ki az IPv6 hálózati előtag eltávolításához [tömörítés nulla](https://technet.microsoft.com/library/cc781672(v=ws.10).aspx), ha való jelentős mértékben csökkentse az IPv6-címelőtagot (2001:0db8:abdc:0000::). Fordított sorrendben, időszak használja, mint a fordított hálózati előtag létrehozásához az előtag hexadecimális számoknak közötti elválasztó karaktert (`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`), és adja hozzá a utótag `.ip6.arpa`.


|Hálózati előtag  |Bővített és a fordított hálózati előtag |Standard utótag |Névkeresési zóna neve  |
|---------|---------|---------|---------|
|2001:db8:abdc:: / 64    | 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2        | . ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:db8:1000:9102:: / 64    | 2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2        | . ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>A címfeloldási DNS Azure-támogatás

Azure DNS fordított vonatkozó két külön forgatókönyveket támogat:

**A megfelelő az IP-Címblokk névkeresési zónát üzemeltető.**
Az Azure DNS a következőkre használható [a névkeresési zónák tárolásához és kezeléséhez minden DNS-címkeresés a PTR rekordok](dns-reverse-dns-hosting.md), az IPv4 és IPv6.  A folyamat a (ARPA) névkeresési zóna létrehozásának, a delegálás beállítása és konfigurálása a PTR-rekordok számára ugyanúgy rendszeres DNS-zónák.  Csak olyan, hogy a delegálási kell konfigurálni a DNS-regisztráló webhelyén, hanem az Internetszolgáltató, és csak a PTR típusú rekord típust kell használni.

**Konfigurálja az IP-cím, az Azure szolgáltatáshoz a fordított DNS-rekordot.** Azure lehetővé teszi [konfigurálása az Azure service lefoglalt IP-címek névkeresési](dns-reverse-dns-for-azure-services.md).  A névkeresési PTR-rekordot a megfelelő ARPA zónát az Azure-ban van konfigurálva.  Ezek az ARPA-zónák, az Azure által használt összes IP-címtartományok megfelelő Microsoft által üzemeltetett

## <a name="next-steps"></a>Következő lépések

A címfeloldási DNS-további információkért lásd: [Wikipedia névkeresési DNS](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Megtudhatja, hogyan [az az Internetszolgáltató által hozzárendelt IP-címtartományt, az Azure DNS-névkeresési zóna](dns-reverse-dns-for-azure-services.md).
<br>
Megtudhatja, hogyan [címfeloldási DNS-rekordjait az Azure-szolgáltatások kezelése](dns-reverse-dns-for-azure-services.md).

