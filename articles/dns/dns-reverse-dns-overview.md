---
title: Az Azure-ban a fordított DNS áttekintése |} A Microsoft Docs
description: Ismerje meg, hogyan fordított DNS működik, és hogyan használható az Azure-ban
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: victorh
ms.openlocfilehash: fa3798a35804998936e0ac166fceff02b01231a0
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171510"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Fordított DNS és támogatás az Azure-ban – áttekintés

Ez a cikk a fordított irányú DNS-forgatókönyvek az Azure-ban támogatott, és hogyan fordított DNS works áttekintését biztosítja.

## <a name="what-is-reverse-dns"></a>Mi a fordított DNS?

A hagyományos DNS-rekordok engedélyezése a leképezés egy DNS-nevet (például "www.contoso.com") egy IP-címet (például 64.4.6.100).  Fordított DNS lehetővé teszi, hogy a fordítás vissza ("www.contoso.com") nevet az IP-cím (64.4.6.100).

Fordított DNS-rekordok számos helyzetben kell használni. Fordított DNS-rekordok például széles körben használják a elleni küzdelemben e-mail levélszemét szóló e-mail üzenet küldője ellenőrzésével.  A fogadó levelezési kiszolgáló lekéri a fordított irányú DNS-rekord a küldő kiszolgáló IP-cím, és ellenőrzi, hogy jogosult-e, amelyeken e-mailek küldését az eredeti tartományból. 

## <a name="how-reverse-dns-works"></a>Hogyan fordított DNS működése

Fordított DNS-rekordok speciális DNS-zónák, más néven "az ARPA"-zónák üzemeltetik.  A zónák párhuzamosan, a normál hierarchiája, például a "contoso.com" tartományt üzemeltet egy külön DNS-hierarchia alkotnak.

Például a DNS rekord "www.contoso.com" a "contoso.com" zóna a "www" name "A" DNS rekord segítségével van megvalósítva.  Ez A rekordot a megfelelő IP-cím, ebben az esetben 64.4.6.100 mutat.  A névkeresési használatával lett megvalósítva külön-külön "PTR" rekord nevű, "100" a "6.4.64.in-addr.arpa" zónában (vegye figyelembe, hogy IP-címek felcseréli ARPA-zónák.)  A PTR típusú rekord, ha megfelelően lett konfigurálva mutat a neve "www.contoso.com".

Amikor egy szervezet hozzá van rendelve egy IP-Címblokk, azokat is beszerezheti a jogot arra, hogy a megfelelő ARPA-zónák kezelése. Az ARPA-zónák az Azure által használt IP-címblokkok megfelelő üzemeltetett és a Microsoft felügyeli. Az Internetszolgáltató az Ön számára a saját IP-címek az ARPA zóna is tartalmazhat, vagy lehetővé teheti, hogy a szolgáltatás egy szerkesztőprogramban, például az Azure DNS DNS ARPA-zónát üzemeltessen.

> [!NOTE]
> DNS-címkeresés és a fordított DNS-lekérdezések külön, párhuzamos DNS hierarchiákban van megvalósítva. A "www.contoso.com" névlekérdezés **nem** a "contoso.com" zónában lévő üzemeltetett, hanem vannak tárolva a megfelelő IP-Címblokk ARPA zónában. Különálló zónákra IPv4 és IPv6-címterületet szolgálnak.

### <a name="ipv4"></a>IPv4

Egy IPv4-névkeresési zóna neve a következő formátumúnak kell lennie: `<IPv4 network prefix in reverse order>.in-addr.arpa`.

Ha például az IP-címek lévő gazdagépek 192.0.2.0/24 előtagot a gazdagép-rekordok a névkeresési zóna létrehozásakor a zóna nevét jönnek létre elkülönítése a hálózati előtagot a cím (192.0.2) és a rendelés (2.0.192) megfordítása és Utótaghozzáadása`.in-addr.arpa`.

|Alhálózat-osztály|Hálózati előtagok  |Fordított hálózati előtagok  |Standard utótag  |Névkeresési zóna neve |
|-------|----------------|------------|-----------------|---------------------------|
|A osztály|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|B. osztály|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|C osztály|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Classless IPv4-delegálás

Bizonyos esetekben egy szervezet számára lefoglalt IP-címtartomány kisebb, mint egy osztály C (/ 24) tartomány. Ebben az esetben az IP-címtartomány nem tartozik egy zóna határon belül a `.in-addr.arpa` hierarchia zónát, és ezért nem delegálható, egy gyermekzónát.

Ehelyett egy másik mechanizmust használ a vezérlőelem az egyes névkeresési (PTR) rekordok átvitele egy dedikált DNS-zónát. Ez a mechanizmus delegálja a gyermekzónát az egyes IP-címtartományban, majd a CNAME rekordok használatával a gyermekzóna névkiszolgálóira külön-külön rendeli minden IP-cím a tartományban.

Tegyük fel, hogy egy szervezet megkapja az IP-címtartomány 192.0.2.128/26 az Internetszolgáltató által. A 192.0.2.191 192.0.2.128 64 IP-címek, ez jelöli. Fordított DNS ezt a tartományt a következőképpen történik:
- A szervezet egy névkeresési zónát 128-26.2.0.192.in-addr.arpa hoz létre. A "128-26' előtag a szervezeten belül a C osztályú hozzárendelt hálózati szegmenst (/ 24) tartomány.
- Az Internetszolgáltató hoz létre a Névkiszolgálói rekordokat a DNS-delegálás, a fenti zóna C osztályú szülőzónában beállításához. Is létrehoz CNAME-rekordok a szülő (C osztályú) névkeresési zónát, az egyes IP-címet az IP-címtartomány leképezése a szervezet által létrehozott új zóna:

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
- Ezután a szervezeten belül a gyermekzónát az egyes PTR-rekordok kezeli.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
A névkeresési PTR típusú rekord IP-cím "192.0.2.129" lekérdezést a "129.2.0.192.in-addr.arpa" nevű. Ez a lekérdezés a CNAME REKORDOT a PTR típusú rekord a gyermekzónát a szülőzónában keresztül oldható fel.

### <a name="ipv6"></a>IPv6

Egy IPv6-névkeresési zóna neve a következő formátumban kell lennie: `<IPv6 network prefix in reverse order>.ip6.arpa`

Ha például. Ha egy gazdagép-rekordok névkeresési zóna gazdagépek kialakításának az IP-címek, amelyek vannak a 2001:db8:1000:abdc:: / 64 előtagjával, a zóna nevét jönnek létre a cím előtagja hálózati elkülönítése (2001:db8:abdc::). Ezután bontsa ki a hálózat IPv6-előtag eltávolítása [tömörítési nulla](https://technet.microsoft.com/library/cc781672(v=ws.10).aspx), ha az IPv6-címelőtagot lerövidítheti használta azt (2001:0db8:abdc:0000::). A rendelés egy időszak használja, mint a elválasztó hozhat létre a fordított hálózati előtagot az előtag, az egyes hexadecimális szám között fordított (`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`) és az utótag hozzáadása `.ip6.arpa`.


|Hálózati előtagok  |Bővített és megfordítva hálózati előtagok |Standard utótag |Névkeresési zóna neve  |
|---------|---------|---------|---------|
|2001:db8:abdc:: / 64    | 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2        | . ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:db8:1000:9102:: / 64    | 2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2        | . ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Azure-támogatás a fordított DNS

Az Azure támogatja a címfeloldási DNS vonatkozó két külön forgatókönyvek:

**A megfelelő az IP-Címblokk névkeresési zónát üzemeltető.**
Az Azure DNS az alábbiakra használhatók [a névkeresési zónák üzemeltetése és a PTR-rekordok kezelése az egyes DNS-névkeresési](dns-reverse-dns-hosting.md), IPv4 és IPv6-alapú.  A (ARPA) névkeresési zóna létrehozásának, a delegálás beállítása és PTR-rekordok konfigurálásával való ugyanaz, mint a rendszeres DNS-zónák.  Az egyetlen különbség, hogy a delegálást konfigurálni kell a DNS-regisztráló helyett az Internetszolgáltató keresztül, és csak a PTR típusú rekord típust kell használni olyan.

**A fordított irányú DNS-rekordot az Azure-szolgáltatáshoz rendelt IP-cím konfigurálása.** Az Azure lehetővé teszi, hogy [konfigurálása az Azure-szolgáltatás számára lefoglalt IP-címek névkeresési](dns-reverse-dns-for-azure-services.md).  A névkeresési PTR-rekordot a megfelelő ARPA-zónát az Azure-ban van konfigurálva.  A Microsoft által üzemeltetett ezeket az ARPA-zónák, megfelelő Azure-ban, által használt IP-címtartományai

## <a name="next-steps"></a>További lépések

Fordított DNS további információkért lásd: [Wikipedia DNS-címkeresés](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Ismerje meg, hogyan [a névkeresési zóna Azure DNS-ben az Internetszolgáltató által kiosztott IP-címtartományt a gazdagép](dns-reverse-dns-for-azure-services.md).
<br>
Ismerje meg, hogyan [fordított DNS-rekordjait az Azure-szolgáltatások kezelése](dns-reverse-dns-for-azure-services.md).

