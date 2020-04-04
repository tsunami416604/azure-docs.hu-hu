---
title: Az Azure MFA-kiszolgáló használata AD FS 2.0-s rendszerrel – Azure Active Directory
description: Ez az Azure Multi-Factor Authentication-oldal leírja, hogyan kezdheti el az Azure MFA és az AD FS 2.0 használatát.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c79a42bbd60d7a1857649cffc97ed7f0103fa16
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653512"
---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-20"></a>Azure Multi-Factor Authentication-kiszolgáló konfigurálása az AD FS 2.0-val való használathoz

Ez a cikk az Azure Active Directory összevonási szolgáltatást használó szervezeteknek szól, amelyek szeretnék biztosítani a helyszíni és a felhőben található erőforrásaik védelmét. Biztosítsa erőforrásai védelmét a Multi-Factor Authentication-kiszolgáló segítségével, illetve az AD FS-sel való együttműködés beállításával a nagy értékű végpontok kétlépéses ellenőrzésének aktiválása érdekében.

Ez a dokumentáció az Azure Multi-Factor Authentication-kiszolgáló AD FS 2.0-val való használatáról szól. Az AD FS-sel kapcsolatos információkért lásd: [A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló és az AD FS használatával a Windows Server 2012 R2 rendszeren](howto-mfaserver-adfs-2012.md).

> [!IMPORTANT]
> 2019. július 1-jéig a Microsoft a továbbiakban nem ajánlja fel az MFA Server alkalmazást az új telepítésekhez. Azoknak az új ügyfeleknek, akik többtényezős hitelesítést szeretnének megkövetelni a felhasználóiktól, felhőalapú Azure többtényezős hitelesítést kell használniuk. Azok a meglévő ügyfelek, akik július 1-je előtt aktiválták az MFA-kiszolgálót, a szokásos módon letölthetik a legújabb verziót, a jövőbeli frissítéseket, és a szokásos módon létrehozhatják az aktiválási hitelesítő adatokat.

## <a name="secure-ad-fs-20-with-a-proxy"></a>Az AD FS 2.0 védelme proxyval

Az AD FS 2.0 proxyval való védelméhez telepítse az Azure Multi-Factor Authentication-kiszolgálót az AD FS-proxykiszolgálóra.

### <a name="configure-iis-authentication"></a>Az IIS-hitelesítés konfigurálása

1. Az Azure Multi-Factor Authentication-kiszolgálón kattintson a bal oldali menüben lévő **IIS-hitelesítés ikonra**.
2. Kattintson az **Űrlapalapú fülre**.
3. Kattintson a **Hozzáadás** gombra.

   ![Az MFA-kiszolgáló IIS-hitelesítési ablaka](./media/howto-mfaserver-adfs-2/setup1.png)

4. A felhasználónév, a jelszó és a tartományváltozók automatikus `https://sso.contoso.com/adfs/ls`észleléséhez írja be a bejelentkezési URL-címet (például) az Űrlapalapú webhely automatikus konfigurálása párbeszédpanelen, majd kattintson az **OK**gombra.
5. Jelölje be a **Require Azure Multi-Factor Authentication user match** (Azure Multi-Factor Authentication felhasználói egyezés megkövetelése) jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és kétlépéses hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesülni fog a kétlépéses hitelesítés alól, ne jelölje be a jelölőnégyzetet.
6. Ha az oldalváltozók nem észlelhetők automatikusan, kattintson a **Manuális megadása...** gombra az Űrlapalapú webhely automatikus konfigurálása párbeszédpanelen.
7. Az Űrlapalapú webhely hozzáadása párbeszédpanelen adja meg az AD FS bejelentkezési lapjának `https://sso.contoso.com/adfs/ls`URL-címét az URL küldése mezőben (például ), majd adja meg az alkalmazás nevét (nem kötelező). Az alkalmazásnév az Azure Multi-Factor Authentication-jelentésekben jelenik meg, illetve megjelenhet az SMS-es vagy mobilalkalmazásos hitelesítési üzenetekben.
8. Állítsa be a Kérelem formátumot **POST vagy GET**gombra.
9. Adja meg a Felhasználónév változót (ctl00$ContentPlaceHolder1$UsernameTextBox) és a Jelszó változót (ctl00$ContentPlaceHolder1$PasswordTextBox). Ha az űrlapalapú bejelentkezési oldalon egy tartomány szövegmező jelenik meg, írja be a Tartomány változót is. A bejelentkezési lapon megjelenő beviteli mezők nevének a megtekintéséhez nyissa meg a bejelentkezési lapot egy webböngészőben, majd a kattintson a jobb gombbal a **Forrás megtekintése** parancsra.
10. Jelölje be a **Require Azure Multi-Factor Authentication user match** (Azure Multi-Factor Authentication felhasználói egyezés megkövetelése) jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és kétlépéses hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesülni fog a kétlépéses hitelesítés alól, ne jelölje be a jelölőnégyzetet.

    ![Űrlapalapú webhely hozzáadása az MFA-kiszolgálóhoz](./media/howto-mfaserver-adfs-2/manual.png)

11. Kattintson a **Speciális...** elemre a speciális beállítások áttekintéséhez. A konfigurálható beállítások közé tartoznak az alábbiak:

    - Egyéni elutasítási oldalfájl kiválasztása
    - Sikeres hitelesítések gyorsítótárazása a webhelyre cookie-k használatával
    - Az elsődleges hitelesítő adatok hitelesítési módjának kiválasztása

12. Mivel az AD FS-proxykiszolgáló valószínűleg nem fog a tartományhoz csatlakozni, valószínűleg LDAP-vel fog csatlakozni a tartományvezérlőhöz a felhasználó importálásához és előhitelesítéséhez. A Speciális űrlapalapú webhely párbeszédpanelen kattintson az **Elsődleges hitelesítés** fülre, és válassza az **LDAP-kötés lehetőséget** az előhitelesítési hitelesítés típusához.
13. Amikor végzett, kattintson az **OK** gombra, hogy visszatérjen az Űrlapalapú webhely hozzáadása párbeszédpanelre.
14. A párbeszédpanel bezárásához kattintson az **OK** gombra.
15. Miután a rendszer észlelte vagy megadta az URL-címet és az oldal változóit, a webhely adatai megjelennek az Űrlapalapú panelen.
16. Kattintson a **Natív modul** fülre, és válassza ki azt a kiszolgálót, azt a webhelyet, amely alatt az AD FS-proxy fut (például "Alapértelmezett webhely"), vagy az AD FS proxyalkalmazást (például "ls" az "adfs" alatt) az IIS beépülő modul kívánt szintjén történő engedélyezéséhez.
17. Kattintson az **IIS-hitelesítés engedélyezése** jelölőnégyzetre a képernyő tetején.

Az IIS-hitelesítés ezzel engedélyezett.

### <a name="configure-directory-integration"></a>Címtárintegrálás konfigurálása

Engedélyezte az IIS-hitelesítést, de az Active Directory (AD) LDAP-n keresztül való előhitelesítéséhez azonban konfigurálnia kell az LDAP-kapcsolatot a tartományvezérlőre.

1. Kattintson a **Címtárintegrálás** ikonra.
2. A Beállítások lapon válassza a **Konkrét LDAP-konfigurációs** választógomb használata lehetőséget.

   ![LDAP-beállítások konfigurálása adott LDAP-beállításokhoz](./media/howto-mfaserver-adfs-2/ldap1.png)

3. Kattintson a **Szerkesztés** gombra.
4. Az LDAP-konfiguráció szerkesztése párbeszédpanelen adja meg az AD tartományvezérlőhöz való kapcsolódáshoz szükséges adatokat a mezőkben. A mezők leírását az Azure Multi-Factor Authentication-kiszolgáló súgófájljában találja.
5. Tesztelje az LDAP-kapcsolatot a **Teszt** gombra kattintva.

   ![LDAP-konfiguráció tesztelése az MFA-kiszolgálón](./media/howto-mfaserver-adfs-2/ldap2.png)

6. Ha az LDAP-kapcsolat tesztelése sikeres volt, kattintson az **OK** gombra.

### <a name="configure-company-settings"></a>Vállalati beállítások konfigurálása

1. Ezután kattintson a **Vállalati beállítások** ikonra, és válassza a **Felhasználónév felbontása** lapot.
2. Válassza az **LDAP egyedi azonosító használata attribútumot az egyező felhasználónevek választógombjához.**
3. Ha a felhasználók "tartomány\felhasználónév" formátumban adják meg a felhasználónevüket, a kiszolgálónak képesnek kell lennie arra, hogy az LDAP-lekérdezés létrehozásakor levegye a tartományt a felhasználónévről. Ez a beállításjegyzék beállításával végezhető el.
4. Nyissa meg a beállításszerkesztőt, és 64 bites kiszolgálón lépjen a HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor elemre. Ha egy 32 bites szerveren, hogy a "Wow6432Node" ki az útból. Hozzon létre egy "UsernameCxz_stripPrefixDomain" nevű duplaszó beállításkulcsot, és állítsa az értéket 1-re. Az Azure Multi-Factor Authentication mostantól védi az AD FS-proxyt.

Győződjön meg arról, hogy importálta a felhasználókat az Active Directoryból a kiszolgálóra. Tekintse meg a [megbízható IP-címek szakaszt,](#trusted-ips) ha engedélyezni szeretné a belső IP-címeket, hogy ne szükséges a kétlépéses ellenőrzés, amikor ezekről a helyekről jelentkezik be a webhelyre.

![Beállításszerkesztő a vállalati beállítások konfigurálásához](./media/howto-mfaserver-adfs-2/reg.png)

## <a name="ad-fs-20-direct-without-a-proxy"></a>AD FS 2.0 Direct proxy nélkül

Az AD FS védelmét akkor is biztosíthatja, ha az AD FS-proxy nincs használatban. Telepítse az Azure Multi-Factor Authentication-kiszolgálót az AD FS-kiszolgálóra, és konfigurálja a kiszolgálót az alábbi lépések szerint:

1. Az Azure Multi-Factor Authentication-kiszolgálón kattintson a bal oldali menüben lévő **IIS-hitelesítés ikonra**.
2. Kattintson a **HTTP** fülre.
3. Kattintson a **Hozzáadás** gombra.
4. Az Alap URL hozzáadása párbeszédpanelen adja meg annak az AD FS `https://sso.domain.com/adfs/ls/auth/integrated`webhelynek az URL-címét, ahol http-hitelesítést végez (például) az Alap URL-cím mezőbe. Ezután írja be az alkalmazás nevét (nem kötelező). Az alkalmazásnév az Azure Multi-Factor Authentication-jelentésekben jelenik meg, illetve megjelenhet az SMS-es vagy mobilalkalmazásos hitelesítési üzenetekben.
5. Ha szeretné, állítsa be az Üresjárati időkorlátot és a Munkamenetek maximális időtartamát.
6. Jelölje be a **Require Azure Multi-Factor Authentication user match** (Azure Multi-Factor Authentication felhasználói egyezés megkövetelése) jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és kétlépéses hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesülni fog a kétlépéses hitelesítés alól, ne jelölje be a jelölőnégyzetet.
7. Szükség esetén jelölje be a cookie-gyorsítótár jelölőnégyzetét.

   ![AD FS 2.0 Direct proxy nélkül](./media/howto-mfaserver-adfs-2/noproxy.png)

8. Kattintson az **OK** gombra.
9. Kattintson a **Natív modul** fülre, és válassza ki a kiszolgálót, a webhelyet (például az "Alapértelmezett webhely" vagy az AD FS alkalmazást (például az "adfs" alatt az "ls" parancsot), hogy az IIS beépülő modult a kívánt szinten engedélyezze.
10. Kattintson az **IIS-hitelesítés engedélyezése** jelölőnégyzetre a képernyő tetején.

Az Azure Multi-Factor Authentication mostantól védi az AD FS-t.

Győződjön meg arról, hogy importálta a felhasználókat az Active Directoryból a kiszolgálóra. Tekintse meg a megbízható IP-címek szakaszt, ha engedélyezni szeretné a belső IP-címeket, hogy ne szükséges a kétlépéses ellenőrzés, amikor ezekről a helyekről jelentkezik be a webhelyre.

## <a name="trusted-ips"></a>Megbízható IP-címek

A Megbízható IP-címek segítségével a felhasználók adott IP-címekről vagy alhálózatokról származó webhelykérések esetén kihagyhatják az Azure Multi-Factor Authenticationt. Előfordulhat például, hogy felhasználókat szeretne felmenteni a kétlépéses ellenőrzés alól, amikor bejelentkeznek az irodából. Ehhez az irodai alhálózatot Megbízható IP-címek bejegyzésként kell megadni.

### <a name="to-configure-trusted-ips"></a>Megbízható IP-címek konfigurálása

1. Az IIS hitelesítése csoportban kattintson a **Megbízható IP-k** fülre.
2. Kattintson a **Hozzáadás...** gombra.
3. A Megbízható IP-címek hozzáadása párbeszédpanel megjelenésekor válassza az **Egyetlen IP-cím**, az **IP-címtartomány** vagy az **Alhálózat** választógombot.
4. Adja meg az engedélyezendő IP-címet, IP-címet vagy alhálózatot. Ha alhálózatot ad meg, válassza ki a megfelelő hálózati maszkot, és kattintson az **OK** gombra.

![Megbízható IP-k konfigurálása az MFA-kiszolgálóra](./media/howto-mfaserver-adfs-2/trusted.png)
