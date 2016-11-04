---
title: RADIUS-hitelesítés és Azure Multi-Factor Authentication-kiszolgáló
description: Ez az Azure Multi-Factor Authentication-oldal segítséget nyújt a RADIUS-hitelesítés és az Azure Multi-Factor Authentication-kiszolgáló telepítéséhez.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtand

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2016
ms.author: kgremban

---
# RADIUS-hitelesítés és Azure Multi-Factor Authentication-kiszolgáló
A RADIUS-hitelesítés szakaszban engedélyezheti és konfigurálhatja a RADIUS-hitelesítést az Azure Multi-Factor Authentication-kiszolgálón. A RADIUS egy szabványos protokoll hitelesítési kérések fogadására és feldolgozására. Az Azure Multi-Factor Authentication-kiszolgáló RADIUS-kiszolgálóként működik az Azure Multi-Factor Authentication biztosítása érdekében a RADIUS-ügyfél (például VPN-készülék) és a hitelesítési cél között, amely Active Directory (AD), LDAP-címtár vagy egy másik RADIUS-kiszolgáló lehet. Az Azure Multi-Factor Authentication működéséhez az Azure Multi-Factor Authentication-kiszolgálót úgy kell konfigurálni, hogy az ügyfélkiszolgálókkal és a hitelesítési céllal is tudjon kommunikálni. Az Azure Multi-Factor Authentication-kiszolgáló fogadja a RADIUS-ügyfél kéréseit, ellenőrzi a hitelesítő adatokat a hitelesítési célon, Azure Multi-Factor Authenticationt biztosít és választ küld a RADIUS-ügyfélnek. A teljes hitelesítés csak akkor lesz sikeres, ha az elsődleges hitelesítés és az Azure Multi-Factor Authentication is sikeres.

> [!NOTE]
> Az MFA-kiszolgáló csak a PAP (Password Authentication Protocol) és az MSCHAPv2 (Microsoft Challenge-Handshake Authentication Protocol) RADIUS-protokollt támogatja, ha RADIUS-kiszolgálóként működik.  Egyéb protokollok, mint az EAP (Extensible Authentication Protocol), akkor használhatók, ha az MFA-kiszolgáló egy másik olyan RADIUS-kiszolgáló RADIUS-proxyjaként működik, amely támogatja az adott protokollt, például a Microsoft NPS-t.
> </br>
> Ha ebben a konfigurációban más protokollokat használ, az egyirányú SMS és OATH tokenek nem fognak működni, mivel az MFA-kiszolgáló nem tud sikeres RADIUS kérdés-választ kezdeményezni azzal a protokollal.
> 
> 

![Radius-hitelesítés](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## A RADIUS-hitelesítés konfigurálása
A RADIUS-hitelesítés konfigurálásához telepítse az Azure Multi-Factor Authentication-kiszolgálót egy Windows-kiszolgálóra. Ha Active Directory-környezetet használ, a kiszolgálót a hálózaton belüli tartományhoz kell csatlakoztatni. Az Azure Multi-Factor Authentication-kiszolgáló konfigurálásához kövesse az alábbi eljárást:

1. Az Azure Multi-Factor Authentication-kiszolgálón kattintson a bal oldali menüben lévő RADIUS-hitelesítés ikonra.
2. Jelölje be a RADIUS-hitelesítés engedélyezése jelölőnégyzetet.
3. Az Ügyfelek lapon módosítsa a hitelesítési portokat és a nyilvántartási portokat, ha az Azure Multi-Factor Authentication RADIUS szolgáltatását nem szabványos portokhoz szeretné kötni a konfigurálandó ügyfelek RADIUS-kéréseinek fogadásához.
4. Kattintson a Hozzáadás... gombra.
5. A RADIUS-ügyfél hozzáadása párbeszédpanelen adja meg az Azure Multi-Factor Authentication-kiszolgálón hitelesítendő készülék/kiszolgáló IP-címét, egy alkalmazásnevet (nem kötelező) és egy közös titkos kulcsot. A közös titkos kulcsnak azonosnak kell lennie az Azure Multi-Factor Authentication-kiszolgálón és a készüléken/kiszolgálón. Az alkalmazásnév az Azure Multi-Factor Authentication-jelentésekben jelenik meg, illetve megjelenhet az SMS-es vagy mobilalkalmazásos hitelesítési üzenetekben.
6. Jelölje be a Multi-Factor Authentication felhasználói egyeztetés megkövetelése jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és többtényezős hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesülni fog a többtényezős hitelesítés alól, ne jelölje be a jelölőnégyzetet. A szolgáltatásról a súgófájlban talál további információt.
7. Jelölje be a Tartalék OATH token engedélyezése jelölőnégyzetet, ha a felhasználók az Azure Multi-Factor Authentication mobilalkalmazásos hitelesítését fogják használni, és az OATH PIN-kódokat tartalék hitelesítésre szeretné használni a sávon kívül telefonhívások, SMS-ek vagy leküldéses értesítések mellett.
8. Kattintson az OK gombra.
9. A 4–8. lépés megismétlésével további RADIUS-ügyfeleket adhat hozzá.
10. Kattintson a Cél fülre.
11. Ha az Azure Multi-Factor Authentication-kiszolgáló tartományhoz csatlakoztatott kiszolgálón van telepítve Active Directory-környezetben, válasszon egy Windows-tartományt.
12. Ha a felhasználókat egy LDAP-címtár alapján kell hitelesíteni, válassza az LDAP-kötést. LDAP-kötés használatakor kattintson a Címtár-integráció ikonra, és szerkessze úgy az LDAP-konfigurációt a Beállítások lapon, hogy a kiszolgáló kötést hozhasson létre a címtárával. Az LDAP konfigurálásával kapcsolatos utasításokat az LDAP-proxy konfigurációs útmutatójában talál.
13. Ha a felhasználókat egy másik RADIUS-kiszolgálón kell hitelesíteni, válassza a RADIUS-kiszolgáló(k) lehetőséget.
14. A Hozzáadás... gombra.
15. A RADIUS-kiszolgáló hozzáadása párbeszédpanelen adja meg a RADIUS-kiszolgáló IP-címét és egy közös titkos kulcsot. A közös titkos kulcsnak azonosnak kell lennie az Azure Multi-Factor Authentication-kiszolgálón és a RADIUS-kiszolgálón. Módosítsa a hitelesítési portot és a nyilvántartási portot, ha a RADIUS-kiszolgáló eltérő portokat használ.
16. Kattintson az OK gombra.
17. Az Azure Multi-Factor Authentication-kiszolgálót RADIUS-ügyfélként kell hozzáadnia a másik RADIUS-kiszolgálón, hogy feldolgozza az Azure Multi-Factor Authentication-kiszolgálóról érkező hozzáférési kéréseket. Ugyanazt a közös titkos kulcsot kell használnia, amelyet az Azure Multi-Factor Authentication-kiszolgálón konfigurált.
18. Ezt a lépést megismételve további RADIUS-kiszolgálókat adhat hozzá, illetve a Feljebb és Lejjebb gombokkal konfigurálhatja a sorrendet, ami alapján a kiszolgáló meghívja őket. Ezzel befejeződött az Azure Multi-Factor Authentication-kiszolgáló konfigurálása. A kiszolgáló mostantól fogadja a konfigurált ügyfelek RADIUS hozzáférési kéréseit a beállított portokon.   

## A RADIUS-ügyfél konfigurálása
A RADIUS-ügyfél konfigurálásához kövesse az alábbi útmutatást:

* Konfigurálja a RADIUS-on keresztül hitelesítendő készüléket/kiszolgálót azon Azure Multi-Factor Authentication-kiszolgáló IP-címével, amely a RADIUS-kiszolgálóként fog működni.
* Használja a fentiekben konfigurált közös titkos kulcsot.
* A RADIUS-időtúllépést állítsa 30–60 másodpercre, hogy elegendő idő álljon rendelkezésre a felhasználó hitelesítő adatainak ellenőrzésére, a többtényezős hitelesítés végrehajtására, a válasz fogadására és a RADIUS hozzáférési kérés megválaszolására.

<!--HONumber=Sep16_HO4-->


