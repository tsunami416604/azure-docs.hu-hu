<properties
    pageTitle="A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló és az AD FS 2.0-s verziójának használatával"
    description="Ez az Azure Multi-Factor Authentication-oldal leírja, hogyan kezdheti el az Azure MFA és az AD FS 2.0 használatát."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló és az AD FS 2.0-s verziójának használatával

Ha a szervezete Azure Active Directory-összevonást használ, és olyan helyszíni vagy felhőbeli erőforrásokkal rendelkezik, amelyeket meg szeretne védeni, konfigurálja az Azure Multi-Factor Authentication-kiszolgálót az AD FS-sel való együttműködésre, hogy a többtényezős hitelesítés elinduljon a magas értékű végpontokhoz.

Ez a dokumentáció az Azure Multi-Factor Authentication-kiszolgáló AD FS 2.0-val való használatáról szól.  Az Azure Multi-Factor Authentication Windows Server 2012 R2 AD FS-sel való használatáról információért lásd: [Secure cloud and on-premises resources using Azure Multi-Factor Authentication Server with Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md) (A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgálóval és Windows Server 2012 R2 AD FS szolgáltatásokkal).


## AD FS 2.0-proxy
Az AD FS 2.0 proxyval való védelméhez telepítse az Azure Multi-Factor Authentication-kiszolgálót az ADFS-proxykiszolgálóra, és konfigurálja a kiszolgálót az alábbi lépések szerint.

### Az AD FS 2.0 védelme proxyval

1. Az Azure Multi-Factor Authentication-kiszolgálón kattintson a bal oldali menüben lévő IIS-hitelesítés ikonra.
2. Kattintson az Űrlapalapú fülre.
3. Kattintson a Hozzáadás... gombra.
<center>![Telepítés](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>
4. A felhasználónév, jelszó és tartomány változók automatikus észleléséhez adja meg a bejelentkezési URL-t (pl. https://sso.contoso.com/adfs/ls) az Űrlapalapú webhely automatikus konfigurálása párbeszédpanelen, és kattintson az OK gombra.
5. Jelölje be a Require Azure Multi-Factor Authentication user match (Azure Multi-Factor Authentication felhasználói egyezés megkövetelése) jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és többtényezős hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesülni fog a többtényezős hitelesítés alól, ne jelölje be a jelölőnégyzetet. A szolgáltatásról a súgófájlban talál további információt.
6. Ha az oldal változóit nem lehet automatikusan észlelni, kattintson a Megadás manuálisan gombra az Űrlapalapú webhely automatikus konfigurálása párbeszédpanelen.
7. Az Űrlapalapú webhely hozzáadása párbeszédpanelen adja meg az ADFS bejelentkezési oldal URL-címét (pl. https://sso.contoso.com/adfs/ls) a Küldési URL-cím mezőben, majd adjon meg egy alkalmazásnevet (nem kötelező). Az alkalmazásnév az Azure Multi-Factor Authentication-jelentésekben jelenik meg, illetve megjelenhet az SMS-es vagy mobilalkalmazásos hitelesítési üzenetekben. A küldési URL-címről a súgófájlban talál további információt.
8. A kérelmet állítsa „POST vagy GET” formátumúra.
9. Adja meg a Felhasználónév változót (ctl00$ContentPlaceHolder1$UsernameTextBox) és a Jelszó változót (ctl00$ContentPlaceHolder1$PasswordTextBox). Ha az űrlapalapú bejelentkezési oldalon egy tartomány szövegmező jelenik meg, írja be a Tartomány változót is. Előfordulhat, hogy a beviteli mezők nevének megtekintéséhez meg kell nyitnia a bejelentkezési lapot egy webböngészőben, majd a bejelentkezési lapon a jobb gombbal kattintva a „Forrás megtekintése” parancsot kell választania.
10. Jelölje be a Require Azure Multi-Factor Authentication user match (Azure Multi-Factor Authentication felhasználói egyezés megkövetelése) jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és többtényezős hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesülni fog a többtényezős hitelesítés alól, ne jelölje be a jelölőnégyzetet.
<center>![Telepítés](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>
11. Kattintson a Speciális... gombra a speciális beállítások áttekintéséhez, beleértve az egyéni elutasítási oldalfájl kiválasztásának lehetőségét, a sikeres hitelesítések gyorsítótárazását a webhelyen egy adott időtartamra cookie-kkal, illetve annak kiválasztását, hogy a rendszer hogyan hitelesítse az elsődleges hitelesítő adatokat.
12. Mivel az ADFS-proxykiszolgáló valószínűleg nem fog a tartományhoz csatlakozni, valószínűleg LDAP-vel fog csatlakozni a tartományvezérlőhöz a felhasználó importálásához és előhitelesítéséhez. A Speciális űrlapalapú webhely párbeszédpanelen kattintson az Elsődleges hitelesítés fülre, és válassza az „LDAP-kötést” az előhitelesítés hitelesítési típusaként.
13. Amikor végzett, az OK gombra kattintva visszatérhet az Űrlapalapú webhely hozzáadása párbeszédpanelre. A speciális beállításokról a súgófájlban talál további információt.
14. A párbeszédpanel bezárásához kattintson az OK gombra.
15. Miután a rendszer észlelte vagy megadta az URL-címet és az oldal változóit, a webhely adatai megjelennek az Űrlapalapú panelen.
16. Kattintson a Natív modul fülre, és válassza ki a kiszolgálót, az ADFS-proxyt futtató webhelyet (pl. „Alapértelmezett webhely”) vagy az ADFS-proxyalkalmazást (pl. „ls” az „adfs” területen) az IIS beépülő modul engedélyezéséhez a kívánt szinten.
17. Kattintson az IIS-hitelesítés engedélyezése jelölőnégyzetre a képernyő tetején.
18. Az IIS-hitelesítés ezzel engedélyezett. Az Active Directory (AD) LDAP-n keresztül való előhitelesítéséhez azonban konfigurálnia kell az LDAP-kapcsolatot a tartományvezérlőre. Ehhez kattintson a Címtár-integráció ikonra.
19. A Beállítások lapon jelölje be a Megadott LDAP-konfiguráció használata választógombot.
<center>![Telepítés](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>
20. Kattintson a Szerkesztés... gombra.
21. Az LDAP-konfiguráció szerkesztése párbeszédpanelen adja meg az AD tartományvezérlőhöz való kapcsolódáshoz szükséges adatokat a mezőkben. A mezők leírását az alábbi táblázatban találja. Megjegyzés: Ezeket az információkat az Azure Multi-Factor Authentication-kiszolgáló súgófájlja is tartalmazza.
22. Tesztelje az LDAP-kapcsolatot a Tesztelés gombra kattintva.
<center>![Telepítés](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
23. Ha az LDAP-kapcsolat tesztelése sikeres volt, kattintson az OK gombra.
24. Ezután kattintson a Vállalati beállítások ikonra, majd a Felhasználónevek feloldása fülre.
25. Jelölje be az Egyedi LDAP azonosító attribútum használata a felhasználónevek egyeztetéséhez választógombot.
26. Ha a felhasználók a „tartomány\felhasználónév” formátumban adják meg a felhasználónevüket az ADFS-proxy bejelentkezési űrlapján, a kiszolgálónak le kell tudnia választani a tartományt a felhasználónévről az LDAP-lekérdezés létrehozásakor. Ez a beállításjegyzék beállításával végezhető el.
27. Nyissa meg a beállításszerkesztőt, és 64 bites kiszolgálón lépjen a HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor elemre. Vagy 32 bites kiszolgálón ugorjon az elérési út „Wow6432Node” csomópontjára. Hozzon létre egy új „UsernameCxz_stripPrefixDomain” nevű DWORD beállításjegyzék-kulcsot, és állítsa az értékét 1-re. Az Azure Multi-Factor Authentication mostantól védi az ADFS-proxyt. Győződjön meg arról, hogy importálta a felhasználókat az Active Directoryból a kiszolgálóra. Tekintse meg az alábbi Megbízható IP-címek szakaszt, ha a belső IP-címeket engedélyezési listára szeretné helyezni, hogy a kéttényezős hitelesítés végrehajtása ne legyen kötelező, amikor ezekről a helyszínekről jelentkezik be a webhelyre.

<center>![Telepítés](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## AD FS 2.0 Direct proxy nélkül

Az AD FS AD FS proxy nélküli védelméhez telepítse az Azure Multi-Factor Authentication-kiszolgálót az AD FS-kiszolgálóra, és konfigurálja a kiszolgálót az alábbi lépések szerint.

### Az AD FS 2.0 védelme proxy nélkül
1. Az Azure Multi-Factor Authentication-kiszolgálón kattintson a bal oldali menüben lévő IIS-hitelesítés ikonra.
2. Kattintson a HTTP fülre.
3. Kattintson a Hozzáadás... gombra.
4. Az Alap URL-cím hozzáadása párbeszédpanelen az Alap URL-cím mezőben adja meg az URL-címét annak az ADFS-webhelynek, amelyen a rendszer a HTTP-hitelesítést végzi (pl. https://sso.domain.com/adfs/ls/auth/integrated), és adjon meg egy Alkalmazásnevet (nem kötelező). Az alkalmazásnév az Azure Multi-Factor Authentication-jelentésekben jelenik meg, illetve megjelenhet az SMS-es vagy mobilalkalmazásos hitelesítési üzenetekben.
5. Ha szeretné, állítsa be az Üresjárati időkorlátot és a Munkamenetek maximális időtartamát.
6. Jelölje be a Require Azure Multi-Factor Authentication user match (Azure Multi-Factor Authentication felhasználói egyezés megkövetelése) jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és többtényezős hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesülni fog a többtényezős hitelesítés alól, ne jelölje be a jelölőnégyzetet. A szolgáltatásról a súgófájlban talál további információt.
7. Szükség esetén jelölje be a cookie-gyorsítótár jelölőnégyzetét.
<center>![Telepítés](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>
8. Kattintson az OK gombra.
9. Kattintson a Natív modul fülre, és válassza ki a kiszolgálót, a webhelyet, amelyen az ADFS fut (pl. „Alapértelmezett webhely”) vagy az ADFS-alkalmazást (pl. „ls” az „adfs” területen) az IIS beépülő modul engedélyezéséhez a kívánt szinten.
10. Kattintson az IIS-hitelesítés engedélyezése jelölőnégyzetre a képernyő tetején. Az Azure Multi-Factor Authentication mostantól védi az ADFS-t. Győződjön meg arról, hogy importálta a felhasználókat az Active Directoryból a kiszolgálóra. Tekintse meg az alábbi Megbízható IP-címek szakaszt, ha a belső IP-címeket engedélyezési listára szeretné helyezni, hogy a kéttényezős hitelesítés végrehajtása ne legyen kötelező, amikor ezekről a helyszínekről jelentkezik be a webhelyre.


## Megbízható IP-címek
A Megbízható IP-címek segítségével a felhasználók adott IP-címekről vagy alhálózatokról származó webhelykérések esetén kihagyhatják az Azure Multi-Factor Authenticationt. Például mentesítheti a felhasználókat az Azure Multi-Factor Authentication alól, ha az irodából jelentkeznek be. Ehhez az irodai alhálózatot Megbízható IP-címek bejegyzésként kell megadni.

### Megbízható IP-címek konfigurálása


1. Az IIS-hitelesítés szakaszban kattintson a Megbízható IP-címek fülre.
1. Kattintson a Hozzáadás... gombra.
1. A Megbízható IP-címek hozzáadása párbeszédpanel megjelenésekor válassza az Egyetlen IP-cím, az IP-címtartomány vagy az Alhálózat választógombot.
1. Adja meg az engedélyezési listához hozzáadni kívánt IP-címet, IP-címtartományt vagy alhálózatot. Ha alhálózatot ad meg, válassza ki a megfelelő hálózati maszkot, és kattintson az OK gombra. A rendszer hozzáadta a megbízható IP-címet.


<center>![Telepítés](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>



<!--HONumber=Sep16_HO4-->


