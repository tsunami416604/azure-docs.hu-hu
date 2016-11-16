---
title: "Azure MFA-kiszolgáló használata AD FS 2.0-val | Microsoft Docs"
description: "Ez az Azure Multi-Factor Authentication-oldal leírja, hogyan kezdheti el az Azure MFA és az AD FS 2.0 használatát."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 96168849-241a-4499-a224-d829913caa7e
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/14/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 639f11856fcb0828db2375e428d9f7efe640b5d7


---
# <a name="secure-cloud-and-onpremises-resources-using-azure-multifactor-authentication-server-with-ad-fs-20"></a>A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló és az AD FS 2.0-s verziójának használatával
Ez a cikk az Azure Active Directory összevonási szolgáltatást használó szervezeteknek szól, amelyek szeretnék biztosítani a helyszíni és a felhőben található erőforrásaik védelmét. Biztosítsa erőforrásai védelmét a Multi-Factor Authentication-kiszolgáló segítségével, illetve az AD FS-sel való együttműködés beállításával a nagy értékű végpontok kétlépéses ellenőrzésének aktiválása érdekében.

Ez a dokumentáció az Azure Multi-Factor Authentication-kiszolgáló AD FS 2.0-val való használatáról szól.  További információt [A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló és a Windows Server 2012 R2 AD FS használatával](multi-factor-authentication-get-started-adfs-w2k12.md) című cikkben talál.

## <a name="secure-ad-fs-20-with-a-proxy"></a>Az AD FS 2.0 védelme proxyval
Az AD FS 2.0 proxyval való védelméhez telepítse az Azure Multi-Factor Authentication-kiszolgálót az ADFS-proxykiszolgálóra, és konfigurálja a kiszolgálót.

### <a name="configure-iis-authentication"></a>Az IIS-hitelesítés konfigurálása
1. Az Azure Multi-Factor Authentication-kiszolgálón kattintson a bal oldali menüben lévő **IIS-hitelesítés ikonra**.
2. Kattintson az **Űrlapalapú fülre**.
3. Kattintson a **Hozzáadás...** gombra.
   <center>![Telepítés](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>
4. A felhasználónév, jelszó és tartomány változók automatikus észleléséhez adja meg a bejelentkezési URL-t (pl. https://sso.contoso.com/adfs/ls) az Űrlapalapú webhely automatikus konfigurálása párbeszédpanelen, és kattintson az OK gombra.
5. Jelölje be a **Require Azure Multi-Factor Authentication user match** (Azure Multi-Factor Authentication felhasználói egyezés megkövetelése) jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és kétlépéses hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesülni fog a kétlépéses hitelesítés alól, ne jelölje be a jelölőnégyzetet. A szolgáltatásról a súgófájlban talál további információt.
6. Ha az oldal változóit nem lehet automatikusan észlelni, kattintson a **Megadás manuálisan** gombra az Űrlapalapú webhely automatikus konfigurálása párbeszédpanelen.
7. Az Űrlapalapú webhely hozzáadása párbeszédpanelen adja meg az ADFS bejelentkezési oldal URL-címét (pl. https://sso.contoso.com/adfs/ls) a Küldési URL-cím mezőben, majd adjon meg egy alkalmazásnevet (nem kötelező). Az alkalmazásnév az Azure Multi-Factor Authentication-jelentésekben jelenik meg, illetve megjelenhet az SMS-es vagy mobilalkalmazásos hitelesítési üzenetekben. A küldési URL-címről a súgófájlban talál további információt.
8. A kérelmet állítsa „POST vagy GET” formátumúra.
9. Adja meg a Felhasználónév változót (ctl00$ContentPlaceHolder1$UsernameTextBox) és a Jelszó változót (ctl00$ContentPlaceHolder1$PasswordTextBox). Ha az űrlapalapú bejelentkezési oldalon egy tartomány szövegmező jelenik meg, írja be a Tartomány változót is. Előfordulhat, hogy a beviteli mezők nevének megtekintéséhez meg kell nyitnia a bejelentkezési lapot egy webböngészőben, majd a bejelentkezési lapon a jobb gombbal kattintva a **Forrás megtekintése** parancsot kell választania.
10. Jelölje be a **Require Azure Multi-Factor Authentication user match** (Azure Multi-Factor Authentication felhasználói egyezés megkövetelése) jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és kétlépéses hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesülni fog a kétlépéses hitelesítés alól, ne jelölje be a jelölőnégyzetet.
    <center>![Telepítés](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>
11. Kattintson a **Speciális...** gombra a speciális beállítások áttekintéséhez. A beállítások konfigurálása olyan műveleteket foglalhat magában, mint az egyéni elutasítási oldalfájl kiválasztásának lehetősége, a sikeres hitelesítések gyorsítótárazása a webhelyen cookie-kkal, illetve annak kiválasztása, hogy a rendszer hogyan hitelesítse az elsődleges hitelesítő adatokat.
12. Mivel az ADFS-proxykiszolgáló valószínűleg nem fog a tartományhoz csatlakozni, valószínűleg LDAP-vel fog csatlakozni a tartományvezérlőhöz a felhasználó importálásához és előhitelesítéséhez. A Speciális űrlapalapú webhely párbeszédpanelen kattintson az **Elsődleges hitelesítés fülre**, és válassza az **LDAP-kötést** az előhitelesítés hitelesítési típusaként.
13. Amikor végzett, kattintson az **OK** gombra az Űrlapalapú webhely hozzáadása párbeszédpanelre való visszatéréshez. A speciális beállításokról a súgófájlban talál további információt.
14. A párbeszédpanel bezárásához kattintson az **OK** gombra.
15. Miután a rendszer észlelte vagy megadta az URL-címet és az oldal változóit, a webhely adatai megjelennek az Űrlapalapú panelen.
16. Kattintson a **Natív modul** fülre, és jelölje ki a kiszolgálót, az ADFS-proxyt futtató webhelyet (például az „Alapértelmezett webhelyet”) vagy az ADFS-proxyalkalmazást (például az „adfs” alatt található „Is”-t) az IIS beépülő modul kívánt szinten való engedélyezéséhez.
17. Kattintson az **IIS-hitelesítés engedélyezése** jelölőnégyzetre a képernyő tetején.
18. Az IIS-hitelesítés ezzel engedélyezett.

### <a name="configure-directory-integration"></a>Címtárintegrálás konfigurálása
Engedélyezte az IIS-hitelesítést, de az Active Directory (AD) LDAP-n keresztül való előhitelesítéséhez azonban konfigurálnia kell az LDAP-kapcsolatot a tartományvezérlőre.

1. Kattintson a **Címtárintegrálás** ikonra.
2. A Beállítások lapon jelölje be a **Megadott LDAP-konfiguráció használata** választógombot.
   <center>![Telepítés](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>
3. Kattintson a **Szerkesztés...** gombra.
4. Az LDAP-konfiguráció szerkesztése párbeszédpanelen adja meg az AD tartományvezérlőhöz való kapcsolódáshoz szükséges adatokat a mezőkben. A mezők leírását az alábbi táblázatban találja. Ezeket az információkat az Azure Multi-Factor Authentication-kiszolgáló súgófájlja is tartalmazza.
5. Tesztelje az LDAP-kapcsolatot a **Tesztelés** gombra kattintva.
   <center>![Telepítés](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
6. Ha az LDAP-kapcsolat tesztelése sikeres volt, kattintson az **OK** gombra.

### <a name="configure-company-settings"></a>Vállalati beállítások konfigurálása
1. Ezután kattintson a **Vállalati beállítások** ikonra, majd a **Felhasználónevek feloldása** fülre.
2. Jelölje be az **Egyedi LDAP azonosító attribútum használata a felhasználónevek egyeztetéséhez** választógombot.
3. Ha a felhasználók „tartomány\felhasználónév” formátumban adják meg a felhasználónevüket, a kiszolgálónak le kell tudnia választani a tartományt a felhasználónévről az LDAP-lekérdezés létrehozásakor. Ez a beállításjegyzék beállításával végezhető el.
4. Nyissa meg a beállításszerkesztőt, és 64 bites kiszolgálón lépjen a HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor elemre. Vagy 32 bites kiszolgálón ugorjon az elérési út „Wow6432Node” csomópontjára. Hozzon létre egy „UsernameCxz_stripPrefixDomain” nevű DWORD beállításjegyzék-kulcsot, és állítsa az értékét 1-re. Az Azure Multi-Factor Authentication mostantól védi az ADFS-proxyt.

Győződjön meg arról, hogy importálta a felhasználókat az Active Directoryból a kiszolgálóra. Tekintse meg a [Megbízható IP-címek szakaszt](#trusted-ips), ha a belső IP-címeket engedélyezési listára szeretné helyezni, hogy a kétlépcsős hitelesítés végrehajtása ne legyen kötelező, amikor ezekről a helyszínekről jelentkezik be a webhelyre.

<center>![Telepítés](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## <a name="ad-fs-20-direct-without-a-proxy"></a>AD FS 2.0 Direct proxy nélkül
Az AD FS védelmét akkor is biztosíthatja, ha az AD FS-proxy nincs használatban. Telepítse az Azure Multi-Factor Authentication-kiszolgálót az AD FS-kiszolgálóra, és konfigurálja a kiszolgálót az alábbi lépések szerint:

1. Az Azure Multi-Factor Authentication-kiszolgálón kattintson a bal oldali menüben lévő **IIS-hitelesítés ikonra**.
2. Kattintson a **HTTP** fülre.
3. Kattintson a **Hozzáadás...** gombra.
4. Az Alap URL-cím hozzáadása párbeszédpanelen az Alap URL-cím mezőben adja meg az URL-címét annak az ADFS-webhelynek, amelyen a rendszer a HTTP-hitelesítést végzi (pl. https://sso.domain.com/adfs/ls/auth/integrated). Ezután írja be az alkalmazás nevét (nem kötelező). Az alkalmazásnév az Azure Multi-Factor Authentication-jelentésekben jelenik meg, illetve megjelenhet az SMS-es vagy mobilalkalmazásos hitelesítési üzenetekben.
5. Ha szeretné, állítsa be az Üresjárati időkorlátot és a Munkamenetek maximális időtartamát.
6. Jelölje be a **Require Azure Multi-Factor Authentication user match** (Azure Multi-Factor Authentication felhasználói egyezés megkövetelése) jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és kétlépéses hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesülni fog a kétlépéses hitelesítés alól, ne jelölje be a jelölőnégyzetet. A szolgáltatásról a súgófájlban talál további információt.
7. Szükség esetén jelölje be a cookie-gyorsítótár jelölőnégyzetét.
   <center>![Telepítés](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>
8. Kattintson az **OK** gombra.
9. Kattintson a **Natív modul** fülre, és jelölje ki a kiszolgálót, a webhelyet (például az „Alapértelmezett webhelyet”) vagy az ADFS-alkalmazást (például az „adfs” alatta található „Is”-t) az IIS beépülő modul kívánt szinten való engedélyezéséhez.
10. Kattintson az **IIS-hitelesítés engedélyezése** jelölőnégyzetre a képernyő tetején. Az Azure Multi-Factor Authentication mostantól védi az ADFS-t.

Győződjön meg arról, hogy importálta a felhasználókat az Active Directoryból a kiszolgálóra. Tekintse meg a Megbízható IP-címek szakaszt, ha a belső IP-címeket engedélyezési listára szeretné helyezni, hogy a kétlépcsős hitelesítés végrehajtása ne legyen kötelező, amikor ezekről a helyszínekről jelentkezik be a webhelyre.

## <a name="trusted-ips"></a>Megbízható IP-címek
A Megbízható IP-címek segítségével a felhasználók adott IP-címekről vagy alhálózatokról származó webhelykérések esetén kihagyhatják az Azure Multi-Factor Authenticationt. Előfordulhat például, hogy felhasználókat szeretne felmenteni a kétlépéses ellenőrzés alól, amikor bejelentkeznek az irodából. Ehhez az irodai alhálózatot Megbízható IP-címek bejegyzésként kell megadni.

### <a name="to-configure-trusted-ips"></a>Megbízható IP-címek konfigurálása
1. Az IIS-hitelesítés szakaszban kattintson a **Megbízható IP-címek** fülre.
2. Kattintson a **Hozzáadás...** gombra.
3. A Megbízható IP-címek hozzáadása párbeszédpanel megjelenésekor válassza az **Egyetlen IP-cím**, az **IP-címtartomány** vagy az **Alhálózat** választógombot.
4. Adja meg az engedélyezési listához hozzáadni kívánt IP-címet, IP-címtartományt vagy alhálózatot. Ha alhálózatot ad meg, válassza ki a megfelelő hálózati maszkot, és kattintson az **OK** gombra. A rendszer hozzáadta a megbízható IP-címet.

<center>![Telepítés](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>



<!--HONumber=Nov16_HO2-->


