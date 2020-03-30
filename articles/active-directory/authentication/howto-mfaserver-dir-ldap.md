---
title: LDAP-hitelesítés és Azure MFA-kiszolgáló – Azure Active Directory
description: LDAP-hitelesítés és az Azure többtényezős hitelesítési kiszolgáló telepítése.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf7a5400fce2fce1b75cbd579830f929fcc0d0dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454465"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>LDAP-hitelesítés és Az Azure többtényezős hitelesítési kiszolgálója

Alapértelmezés szerint az Azure Multi-Factor Authentication-kiszolgáló úgy van konfigurálva, hogy a felhasználókat az Active Directoryból importálja vagy szinkronizálja. Azonban konfigurálható úgy különböző LDAP-címtárakhoz való kötésre is, például ADAM-címtárhoz vagy adott Active Directory-tartományvezérlőkhöz. Ha LDAP-n keresztül csatlakozik egy címtárhoz, az Azure többtényezős hitelesítési kiszolgáló ldap-proxyként működhet a hitelesítések végrehajtásához. Ezenkívül lehetővé teszi, hogy az LDAP-kötést felhasználja RADIUS-célként az IIS-hitelesítést használó felhasználók előhitelesítésére illetve elsődleges hitelesítésre az Azure MFA felhasználói portálon.

Ha Az Azure többtényezős hitelesítést LDAP-proxyként szeretné használni, helyezze be az Azure többtényezős hitelesítési kiszolgálót az LDAP-ügyfél (például VPN-berendezés, alkalmazás) és az LDAP-címtárkiszolgáló közé. Az Azure Multi-Factor Authentication-kiszolgálót úgy kell konfigurálni, hogy az ügyfélkiszolgálókkal és az LDAP-címtárral is kommunikáljon. Ebben a konfigurációban az Azure Multi-Factor Authentication-kiszolgáló fogadja az ügyfélkiszolgálóktól és alkalmazásoktól érkező LDAP-kéréseket, majd továbbítja őket a cél LDAP-címtárkiszolgálónak az elsődleges hitelesítő adatok ellenőrzéséhez. Ha az LDAP-címtár érvényesíti az elsődleges hitelesítő adatokat, az Azure többtényezős hitelesítés e második identitás-ellenőrzést hajt végre, és választ küld az LDAP-ügyfélnek. A teljes hitelesítés csak akkor lesz sikeres, ha az LDAP-kiszolgálón való hitelesítés és a kétlépéses hitelesítés is sikeres.

> [!IMPORTANT]
> 2019. július 1-jéig a Microsoft a továbbiakban nem ajánlja fel az MFA Server alkalmazást az új telepítésekhez. Azoknak az új ügyfeleknek, akik többtényezős hitelesítést szeretnének megkövetelni a felhasználóiktól, felhőalapú Azure többtényezős hitelesítést kell használniuk. Azok a meglévő ügyfelek, akik július 1-je előtt aktiválták az MFA-kiszolgálót, a szokásos módon letölthetik a legújabb verziót, a jövőbeli frissítéseket, és a szokásos módon létrehozhatják az aktiválási hitelesítő adatokat.

## <a name="configure-ldap-authentication"></a>LDAP-hitelesítés konfigurálása

Az LDAP-hitelesítés konfigurálásához telepítse az Azure Multi-Factor Authentication-kiszolgálót egy Windows-kiszolgálón. Kövesse az alábbi eljárást:

### <a name="add-an-ldap-client"></a>LDAP-ügyfél hozzáadása

1. Az Azure többtényezős hitelesítési kiszolgálón válassza ki az LDAP-hitelesítés ikont a bal oldali menüben.
2. Jelölje be az **LDAP-hitelesítés engedélyezése** jelölőnégyzetet.

   ![LDAP-hitelesítés az MFA-kiszolgálón](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. Az Ügyfelek lapon módosítsa a TCP-port ot és az SSL(TLS) portot, ha az Azure többtényezős hitelesítési LDAP-szolgáltatásnak nem szabványos portokhoz kell kötnie az LDAP-kérelmek figyeléséhez.
4. Ha az ügyféltől az Azure multi-factor authentication serverkiszolgálóig ldaps-t kíván használni, tls/Ssl-tanúsítványt kell telepíteni ugyanarra a kiszolgálóra, mint az MFA-kiszolgáló. Kattintson az SSL (TLS) tanúsítványmező melletti **Tallózás** gombra, és válassza ki a biztonságos kapcsolathoz használni kívánt tanúsítványt.
5. Kattintson a **Hozzáadás** gombra.
6. Az LDAP-ügyfél hozzáadása párbeszédpanelen adja meg annak a készüléknek, kiszolgálónak vagy alkalmazásnak az IP-címét, amely hitelesíti magát a kiszolgálón, valamint egy alkalmazásnevet (nem kötelező). Az alkalmazásnév az Azure Multi-Factor Authentication-jelentésekben jelenik meg, illetve megjelenhet az SMS-es vagy mobilalkalmazásos hitelesítési üzenetekben.
7. Jelölje be a **Require Azure Multi-Factor Authentication user match** (Azure Multi-Factor Authentication felhasználói egyezés megkövetelése) jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és kétlépéses hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nem lett importálva a kiszolgálóra, és/vagy mentesülnek a kétlépéses ellenőrzés alól, hagyja bejelölve a négyzetet. A szolgáltatásról további információt az MFA-kiszolgáló súgófájljában talál.

További LDAP-ügyfelek hozzáadásához ismételje meg ezeket a lépéseket.

### <a name="configure-the-ldap-directory-connection"></a>Az LDAP-címtár kapcsolatának konfigurálása

Ha az Azure Multi-Factor Authentication LDAP-hitelesítések fogadására van konfigurálva, a hitelesítéseket az LDAP-címtárnak kell proxykapcsolaton keresztül átadnia. Ezért a Cél lapon csak egyetlen, szürkén megjelenő lehetőség látható LDAP-cél használatához.

> [!NOTE]
> A címtár-integráció nem garantált, hogy az Active Directory tartományi szolgáltatásoktól eltérő könyvtárakkal is együttműködik.

1. Az LDAP-címtárkapcsolat konfigurálásához kattintson a **Címtár-integráció** ikonra.
2. A Beállítások lapon válassza a **Konkrét LDAP-konfigurációs** választógomb használata lehetőséget.
3. Válassza a **Szerkesztés lehetőséget...**
4. Az LDAP-konfiguráció szerkesztése párbeszédpanelen adja meg az LDAP-címtárhoz való kapcsolódáshoz szükséges adatokat a mezőkben. A mezők leírását az Azure Multi-Factor Authentication-kiszolgáló súgófájljában találja.

    ![Címtár-integráció LDAP-konfigurációja](./media/howto-mfaserver-dir-ldap/ldap.png)

5. Tesztelje az LDAP-kapcsolatot a **Teszt** gombra kattintva.
6. Ha az LDAP-kapcsolat tesztje sikeres volt, kattintson az **OK** gombra.
7. Kattintson a **Szűrők** fülre. A kiszolgáló előre be van állítva a tárolók, biztonsági csoportok és felhasználók Active Directoryból való betöltésére. Másik LDAP-címtárhoz való kötés esetén valószínűleg szerkesztenie kell a megjelenített szűrőket. A **Help** szűrőkkel kapcsolatos további információkért kattintson a súgóhivatkozásra.
8. Kattintson az **Attribútumok** fülre. A kiszolgáló előre be van állítva az Active Directory attribútumainak leképezésére.
9. Másik LDAP-címtárhoz való kötéshez vagy az előre konfigurált attribútumleképezések módosításához kattintson a **Szerkesztés…** elemre
10. Az Attribútumok szerkesztése párbeszédpanelen módosítsa az LDAP-attribútumleképezéseket a címtárra vonatkozóan. Attribútumnevek beírása vagy kiválasztása a **...** gombra kattintva. Az **Help** attribútumokkal kapcsolatos további információkért kattintson a súgóhivatkozásra.
11. Kattintson az **OK** gombra.
12. Kattintson a **Vállalati beállítások** ikonra, és válassza a **Felhasználónév felbontása** lapot.
13. Ha az Active Directoryhoz egy tartományhoz csatlakoztatott kiszolgálóról csatlakozik, hagyja bejelölve a **Windows biztonsági azonosítók (SID-k) használata a felhasználónevek egyeztetéséhez** választógombot. Ellenkező esetben válassza az **LDAP egyedi azonosító használata attribútumot az egyező felhasználónevek választógombjához.**

Ha az **Egyedi LDAP azonosító attribútum használata a felhasználónevek egyeztetéséhez** lehetőséget bejelöli, az Azure Multi-Factor Authentication-kiszolgáló a felhasználóneveket megkísérli egy egyedi azonosítóvá feloldani az LDAP-címtárban. LDAP-keresés történik a Címtárintegráció -> attribútumok lapon definiált Felhasználónév attribútumokon. Amikor egy felhasználó hitelesíti magát, a felhasználónév feloldódik az LDAP-könyvtárban lévő egyedi azonosítóra. Az egyedi azonosító az Azure többtényezős hitelesítési adatfájlban lévő felhasználó egyeztetésére szolgál. Ez lehetővé teszi a kis- és nagybetűk megkülönböztetését, valamint a hosszú és rövid felhasználónevek formátumát.

A lépések elvégzése után az MFA-kiszolgáló figyeli a konfigurált ügyfelek LDAP-hozzáférési kérelmeinek konfigurált portjait, és proxyként működik az LDAP-címtárba a hitelesítéshez.

## <a name="configure-ldap-client"></a>LDAP-ügyfél konfigurálása

Az LDAP-ügyfél konfigurálásához kövesse az alábbi útmutatást:

* Konfigurálja a készüléket, kiszolgálót vagy alkalmazást, hogy úgy végezzen hitelesítést LDAP-n keresztül az Azure Multi-Factor Authentication-kiszolgálón, mintha az az Ön LDAP-címtára lenne. Használja ugyanazokat a beállításokat, amelyeket általában használna az LDAP-címtárához való közvetlen csatlakozáshoz, azonban az Azure Multi-Factor Authentication-kiszolgáló kiszolgálónevét vagy IP-címét adja meg.
* Konfigurálja az LDAP időtúllépését 30–60 másodpercre úgy, hogy van idő a felhasználó hitelesítő adatainak érvényesítésére az LDAP-könyvtárral, a második lépésből álló ellenőrzés végrehajtására, a válaszfogadásra és az LDAP-hozzáférési kérelem megválaszolására.
* LDAPS használata esetén az LDAP-lekérdezéseket végző készüléknek vagy kiszolgálónak meg kell bíznia az Azure többtényezős hitelesítési kiszolgálón telepített TLS/SSL tanúsítványban.
