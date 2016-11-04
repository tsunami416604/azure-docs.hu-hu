---
title: LDAP-hitelesítés és Azure Multi-Factor Authentication-kiszolgáló
description: Ez az Azure Multi-Factor Authentication-oldal segítséget nyújt az LDAP-hitelesítés és az Azure Multi-Factor Authentication-kiszolgáló telepítéséhez.
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
ms.date: 08/04/2016
ms.author: kgremban

---
# LDAP-hitelesítés és Azure Multi-Factor Authentication-kiszolgáló
Alapértelmezés szerint az Azure Multi-Factor Authentication-kiszolgáló úgy van konfigurálva, hogy a felhasználókat az Active Directoryból importálja vagy szinkronizálja. Azonban konfigurálható úgy különböző LDAP-címtárakhoz való kötésre is, például ADAM-címtárhoz vagy adott Active Directory-tartományvezérlőkhöz. Ha az Azure Multi-Factor Authentication-kiszolgáló úgy van beállítva, hogy egy címtárhoz LDAP-n keresztül csatlakozzon, konfigurálható úgy, hogy LDAP-proxyként működjön a hitelesítések végrehajtásához. Ezenkívül lehetővé teszi, hogy az LDAP-kötést felhasználja RADIUS-célként a felhasználók előhitelesítésére IIS-hitelesítés használatakor, illetve elsődleges hitelesítésre az Azure Multi-Factor Authentication felhasználói portálon.

Ha az Azure Multi-Factor Authenticationt LDAP-proxyként használja, az Azure Multi-Factor Authentication-kiszolgáló az LDAP-ügyfél (például VPN-készülék, alkalmazás) és az LDAP-címtárkiszolgáló között található, hogy többtényezős hitelesítést biztosítson. Az Azure Multi-Factor Authentication működéséhez az Azure Multi-Factor Authentication-kiszolgálót úgy kell konfigurálni, hogy az ügyfélkiszolgálókkal és az LDAP-címtárral is kommunikáljon. Ebben a konfigurációban az Azure Multi-Factor Authentication-kiszolgáló fogadja az ügyfélkiszolgálóktól és alkalmazásoktól érkező LDAP-kéréseket, majd továbbítja őket a cél LDAP-címtárkiszolgálónak az elsődleges hitelesítő adatok ellenőrzéséhez. Ha az LDAP-címtár válasza szerint az elsődleges hitelesítő adatok érvényesek, az Azure Multi-Factor Authentication végrehajtja a második tényezős hitelesítést, majd visszaküldi a választ az LDAP-ügyfélnek. A teljes hitelesítés csak akkor lesz sikeres, ha az LDAP-kiszolgálón való hitelesítés és a többtényezős hitelesítés is sikeres.

## Az LDAP-hitelesítés konfigurálása
Az LDAP-hitelesítés konfigurálásához telepítse az Azure Multi-Factor Authentication-kiszolgálót egy Windows-kiszolgálón. Kövesse az alábbi eljárást:

1. Az Azure Multi-Factor Authentication-kiszolgálón kattintson a bal oldali menüben lévő LDAP-hitelesítés ikonra.
2. Jelölje be az LDAP-hitelesítés engedélyezése jelölőnégyzetet.![LDAP-hitelesítés](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)
3. Az Ügyfelek lapon módosítsa a TCP-portot és az SSL-portot, ha az Azure Multi-Factor Authentication LDAP-szolgáltatását nem szabványos portokhoz szeretné kötni, hogy a konfigurálandó ügyfelek LDAP-kéréseit fogadni tudja.
4. Ha LDAPS-t szeretne használni az ügyfél és az Azure Multi-Factor Authentication-kiszolgáló között, egy SSL-tanúsítványt kell telepíteni a kiszolgálón, amelyen a kiszolgáló fut. Kattintson a Tallózás... gombra az SSL-tanúsítvány mező mellett, és válassza ki a telepített tanúsítványt, amelyet a biztonságos kapcsolathoz szeretne használni.
5. Kattintson a Hozzáadás... gombra.
6. Az LDAP-ügyfél hozzáadása párbeszédpanelen adja meg a kiszolgálón hitelesítendő készülék, kiszolgáló vagy alkalmazás IP-címét és egy alkalmazásnevet (nem kötelező). Az alkalmazásnév az Azure Multi-Factor Authentication-jelentésekben jelenik meg, illetve megjelenhet az SMS-es vagy mobilalkalmazásos hitelesítési üzenetekben.
7. Jelölje be az Azure Multi-Factor Authentication felhasználói egyeztetés megkövetelése jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és többtényezős hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesülni fog a többtényezős hitelesítés alól, ne jelölje be a jelölőnégyzetet. A szolgáltatásról a súgófájlban talál további információt.
8. Az 5–7. lépés megismétlésével további LDAP-ügyfeleket adhat hozzá.
9. Ha az Azure Multi-Factor Authentication LDAP-hitelesítések fogadására van konfigurálva, a hitelesítéseket az LDAP-címtárnak kell proxykapcsolaton keresztül átadnia. Ezért a Cél lapon csak egyetlen, szürkén megjelenő lehetőség látható LDAP-cél használatához. Az LDAP-címtár kapcsolatának konfigurálásához kattintson a Címtár-integráció ikonra.
10. A Beállítások lapon jelölje be a Megadott LDAP-konfiguráció használata választógombot.
11. Kattintson a Szerkesztés... gombra.
12. Az LDAP-konfiguráció szerkesztése párbeszédpanelen adja meg az LDAP-címtárhoz való kapcsolódáshoz szükséges adatokat a mezőkben. A mezők leírását az alábbi táblázatban találja. Megjegyzés: Ezeket az információkat az Azure Multi-Factor Authentication-kiszolgáló súgófájlja is tartalmazza.![Címtár-integráció](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)
13. Tesztelje az LDAP-kapcsolatot a Tesztelés gombra kattintva.
14. Ha az LDAP-kapcsolat tesztelése sikeres volt, kattintson az OK gombra.
15. Kattintson a Szűrők fülre. A kiszolgáló előre konfigurálva van a tárolók, biztonsági csoportok és felhasználók az Active Directoryból való betöltésére. Másik LDAP-címtárhoz való kötés esetén valószínűleg szerkesztenie kell a megjelenített szűrőket. A szűrőkkel kapcsolatos további információért kattintson a súgóhivatkozásra.
16. Kattintson az Attribútumok fülre. A kiszolgáló előre konfigurálva van az Active Directoryból származó attribútumok leképezésére.
17. Másik LDAP-címtárhoz való kötéshez vagy az előre konfigurált attribútumleképezések módosításához kattintson a Szerkesztés... gombra.
18. Az Attribútumok szerkesztése párbeszédpanelen módosítsa az LDAP-attribútumleképezéseket a címtárra vonatkozóan. Az attribútumneveket beírhatja, vagy kiválaszthatja a mezők melletti ... gombra kattintva.
19. Az attribútumokkal kapcsolatos további információért kattintson a súgóhivatkozásra.
20. Kattintson az OK gombra.
21. Kattintson a Vállalati beállítások ikonra, és válassza a Felhasználónév feloldása fület.
    22.Ha az Active Directoryhoz egy tartományhoz csatlakoztatott kiszolgálóról csatlakozik, hagyja bejelölve a Windows biztonsági azonosítók (SID-k) használata a felhasználónevek egyeztetéséhez választógombot. Egyéb esetben jelölje be az Egyedi LDAP azonosító attribútum használata a felhasználónevek egyeztetéséhez választógombot. Ha bejelöli, az Azure Multi-Factor Authentication-kiszolgáló a felhasználóneveket megkísérli egy egyedi azonosítóvá feloldani az LDAP-címtárban. A rendszer végrehajt egy LDAP-keresést a Címtár-integráció -> Attribútumok lapon meghatározott felhasználónév-attribútumokon. Amikor egy felhasználó hitelesítést végez, a felhasználónevet a rendszer az LDAP-címtárban található egyedi azonosítóra oldja fel, és az egyedi azonosító segítségével egyezteti az Azure Multi-Factor Authentication adatfájlban található felhasználóval. Ez lehetővé teszi a kis- és nagybetűket nem megkülönböztető összehasonlításokat, illetve a hosszú és rövid felhasználónév-formátumok használatát. Ezzel befejeződik az Azure Multi-Factor Authentication-kiszolgáló konfigurálása. A kiszolgáló mostantól fogadja a konfigurált ügyfelek LDAP-hozzáférési kéréseit a beállított portokon, és a kéréseket proxykapcsolaton keresztül átadja az LDAP-címtárnak hitelesítésre.

## Az LDAP-ügyfél konfigurálása
Az LDAP-ügyfél konfigurálásához kövesse az alábbi útmutatást:

* Konfigurálja a készüléket, kiszolgálót vagy alkalmazást, hogy úgy végezzen hitelesítést LDAP-n keresztül az Azure Multi-Factor Authentication-kiszolgálón, mintha az az Ön LDAP-címtára lenne. Ugyanazokat a beállításokat használja, amelyeket általában használna az LDAP-címtárához való közvetlen csatlakozáshoz, azonban az Azure Multi-Factor Authentication-kiszolgáló kiszolgálónevét vagy IP-címét adja meg.
* Az LDAP-időtúllépést állítsa 30–60 másodpercre, hogy elegendő idő álljon rendelkezésre a felhasználó hitelesítő adatainak ellenőrzésére az LDAP-címtárral, a második tényezős hitelesítés végrehajtására, a válasz fogadására és az LDAP-hozzáférési kérés megválaszolására.
* Ha LDAPS-t használ, az LDAP-lekérdezéseket kezdeményező készüléknek vagy kiszolgálónak meg kell bíznia az Azure Multi-Factor Authentication-kiszolgálón telepített SSL-tanúsítványban.

<!--HONumber=Sep16_HO4-->


