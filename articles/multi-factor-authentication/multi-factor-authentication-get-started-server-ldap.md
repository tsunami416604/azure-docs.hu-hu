---
title: "LDAP-hitelesítés és Azure MFA-kiszolgáló | Microsoft Docs"
description: "Ez az Azure Multi-Factor Authentication-oldal segítséget nyújt az LDAP-hitelesítés és az Azure Multi-Factor Authentication-kiszolgáló telepítéséhez."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: e1a68568-53d1-4365-9e41-50925ad00869
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/03/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1c6386dda94a3e0ca6eb340f542d04cb336159c3
ms.openlocfilehash: 0de97050e385e3efb9e63bbf934712157ab0d0af


---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>LDAP-hitelesítés és Azure Multi-Factor Authentication-kiszolgáló
Alapértelmezés szerint az Azure Multi-Factor Authentication-kiszolgáló úgy van konfigurálva, hogy a felhasználókat az Active Directoryból importálja vagy szinkronizálja. Azonban konfigurálható úgy különböző LDAP-címtárakhoz való kötésre is, például ADAM-címtárhoz vagy adott Active Directory-tartományvezérlőkhöz. Ha az Azure Multi-Factor Authentication-kiszolgáló egy címtárhoz LDAP-n keresztül csatlakozik, konfigurálható úgy, hogy LDAP-proxyként működjön a hitelesítések végrehajtásához. Ezenkívül lehetővé teszi, hogy az LDAP-kötést felhasználja RADIUS-célként az IIS-hitelesítést használó felhasználók előhitelesítésére illetve elsődleges hitelesítésre az Azure MFA felhasználói portálon.

Ha az Azure Multi-Factor Authenticationt LDAP-proxyként szeretné használni, helyezze az Azure Multi-Factor Authentication-kiszolgálót az LDAP-ügyfél (például VPN-készülék, alkalmazás) és az LDAP-címtárkiszolgáló közé. Az Azure Multi-Factor Authentication-kiszolgálót úgy kell konfigurálni, hogy az ügyfélkiszolgálókkal és az LDAP-címtárral is kommunikáljon. Ebben a konfigurációban az Azure Multi-Factor Authentication-kiszolgáló fogadja az ügyfélkiszolgálóktól és alkalmazásoktól érkező LDAP-kéréseket, majd továbbítja őket a cél LDAP-címtárkiszolgálónak az elsődleges hitelesítő adatok ellenőrzéséhez. Ha az LDAP-címtár válasza szerint az elsődleges hitelesítő adatok érvényesek, az Azure Multi-Factor Authentication végrehajtja a második személyazonosság-ellenőrzést, majd visszaküldi a választ az LDAP-ügyfélnek. A teljes hitelesítés csak akkor lesz sikeres, ha az LDAP-kiszolgálón való hitelesítés és a kétlépéses hitelesítés is sikeres.

## <a name="ldap-authentication-configuration"></a>Az LDAP-hitelesítés konfigurálása
Az LDAP-hitelesítés konfigurálásához telepítse az Azure Multi-Factor Authentication-kiszolgálót egy Windows-kiszolgálón. Kövesse az alábbi eljárást:

### <a name="add-an-ldap-client"></a>LDAP-ügyfél hozzáadása

1. Az Azure Multi-Factor Authentication-kiszolgálón válassza a bal oldali menüben lévő LDAP-hitelesítés ikont.
2. Jelölje be az **LDAP-hitelesítés engedélyezése** jelölőnégyzetet.

        ![LDAP Authentication](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)

3. Az Ügyfelek lapon módosítsa a TCP-portot és az SSL-portot, ha az Azure Multi-Factor Authentication LDAP-szolgáltatását nem szabványos portokhoz szeretné kötni, hogy fogadni tudjon LDAP-kéréseket.
4. Ha LDAPS-t szeretne használni az ügyfél és az Azure Multi-Factor Authentication-kiszolgáló között, egy SSL-tanúsítványt kell telepíteni a kiszolgálón, amelyen a kiszolgáló fut. Kattintson a **Tallózás…** gombra az SSL-tanúsítvány mező mellett, és válassza ki a telepített tanúsítványt, amelyet a biztonságos kapcsolathoz szeretne használni.
5. Kattintson a **Hozzáadás…** gombra.
6. Az LDAP-ügyfél hozzáadása párbeszédpanelen adja meg a kiszolgálón hitelesítendő készülék, kiszolgáló vagy alkalmazás IP-címét és egy alkalmazásnevet (nem kötelező). Az alkalmazásnév az Azure Multi-Factor Authentication-jelentésekben jelenik meg, illetve megjelenhet az SMS-es vagy mobilalkalmazásos hitelesítési üzenetekben.
7. Jelölje be a **Require Azure Multi-Factor Authentication user match** (Azure Multi-Factor Authentication felhasználói egyezés megkövetelése) jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és kétlépéses hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesülni fog a kétlépéses hitelesítés alól, ne jelölje be a jelölőnégyzetet. A szolgáltatásról a súgófájlban talál további információt.

További LDAP-ügyfelek hozzáadásához ismételje meg ezeket a lépéseket.

### <a name="configure-the-ldap-directory-connection"></a>Az LDAP-címtár kapcsolatának konfigurálása

Ha az Azure Multi-Factor Authentication LDAP-hitelesítések fogadására van konfigurálva, a hitelesítéseket az LDAP-címtárnak kell proxykapcsolaton keresztül átadnia. Ezért a Cél lapon csak egyetlen, szürkén megjelenő lehetőség látható LDAP-cél használatához.

1. Az LDAP-címtár kapcsolatának konfigurálásához kattintson a **Címtár-integráció** ikonra.
2. A Beállítások lapon jelölje be a **Megadott LDAP-konfiguráció használata** választógombot.
3. Válassza a **Szerkesztés…** elemet.
4. Az LDAP-konfiguráció szerkesztése párbeszédpanelen adja meg az LDAP-címtárhoz való kapcsolódáshoz szükséges adatokat a mezőkben. A mezők leírását az Azure Multi-Factor Authentication-kiszolgáló súgófájljában találja.

    ![Címtár-integráció](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)

5. Tesztelje az LDAP-kapcsolatot a **Tesztelés** gombra kattintva.
6. Ha az LDAP-kapcsolat tesztelése sikeres volt, kattintson az **OK** gombra.
7. Kattintson a **Szűrők** fülre. A kiszolgáló előre konfigurálva van a tárolók, biztonsági csoportok és felhasználók az Active Directoryból való betöltésére. Másik LDAP-címtárhoz való kötés esetén valószínűleg szerkesztenie kell a megjelenített szűrőket. A szűrőkkel kapcsolatos további információért kattintson a **Súgó** hivatkozásra.
8. Kattintson az **Attribútumok** fülre. A kiszolgáló előre konfigurálva van az Active Directoryból származó attribútumok leképezésére.
9. Másik LDAP-címtárhoz való kötéshez vagy az előre konfigurált attribútumleképezések módosításához kattintson a **Szerkesztés…** elemre
10. Az Attribútumok szerkesztése párbeszédpanelen módosítsa az LDAP-attribútumleképezéseket a címtárra vonatkozóan. Az attribútumneveket beírhatja, vagy kiválaszthatja a mezők melletti **…** gombra kattintva. Az attribútumokkal kapcsolatos további információért kattintson a **Súgó** hivatkozásra.
11. Kattintson az **OK** gombra.
12. Kattintson a **Vállalati beállítások** ikonra, és válassza a **Felhasználónév feloldása** fület.
13. Ha az Active Directoryhoz egy tartományhoz csatlakoztatott kiszolgálóról csatlakozik, hagyja bejelölve a **Windows biztonsági azonosítók (SID-k) használata a felhasználónevek egyeztetéséhez** választógombot. Egyéb esetben jelölje be az **Egyedi LDAP azonosító attribútum használata a felhasználónevek egyeztetéséhez** választógombot. 

Ha az **Egyedi LDAP azonosító attribútum használata a felhasználónevek egyeztetéséhez** lehetőséget bejelöli, az Azure Multi-Factor Authentication-kiszolgáló a felhasználóneveket megkísérli egy egyedi azonosítóvá feloldani az LDAP-címtárban. A rendszer végrehajt egy LDAP-keresést a Címtár-integráció -> Attribútumok lapon meghatározott felhasználónév-attribútumokon. Amikor egy felhasználó hitelesítést végez, a felhasználónevet a rendszer az LDAP-címtárban található egyedi azonosítóra oldja fel, és az egyedi azonosító segítségével egyezteti az Azure Multi-Factor Authentication-adatfájlban található felhasználóval. Ez lehetővé teszi a kis- és nagybetűket nem megkülönböztető összehasonlításokat, illetve a hosszú és rövid felhasználónév-formátumok használatát.

A lépések befejezése után az MFA-kiszolgáló fogadja a konfigurált ügyfelek LDAP-hozzáférési kéréseit a beállított portokon, és a kéréseket proxykapcsolaton keresztül átadja az LDAP-címtárnak hitelesítésre.

## <a name="ldap-client-configuration"></a>Az LDAP-ügyfél konfigurálása
Az LDAP-ügyfél konfigurálásához kövesse az alábbi útmutatást:

* Konfigurálja a készüléket, kiszolgálót vagy alkalmazást, hogy úgy végezzen hitelesítést LDAP-n keresztül az Azure Multi-Factor Authentication-kiszolgálón, mintha az az Ön LDAP-címtára lenne. Használja ugyanazokat a beállításokat, amelyeket általában használna az LDAP-címtárához való közvetlen csatlakozáshoz, azonban az Azure Multi-Factor Authentication-kiszolgáló kiszolgálónevét vagy IP-címét adja meg.
* Az LDAP-időtúllépést állítsa 30–60 másodpercre, hogy elegendő idő álljon rendelkezésre a felhasználó hitelesítő adatainak ellenőrzésére az LDAP-címtárral, a kétlépéses hitelesítés végrehajtására, a válasz fogadására és az LDAP-hozzáférési kérés megválaszolására.
* Ha LDAPS-t használ, az LDAP-lekérdezéseket kezdeményező készüléknek vagy kiszolgálónak meg kell bíznia az Azure Multi-Factor Authentication-kiszolgálón telepített SSL-tanúsítványban.




<!--HONumber=Jan17_HO1-->


