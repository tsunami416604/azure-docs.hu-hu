---
title: IIS-hitelesítés és Azure Multi-Factor Authentication-kiszolgáló
description: Ez az Azure Multi-Factor Authentication-oldal segítséget nyújt az IIS-hitelesítés és az Azure Multi-Factor Authentication-kiszolgáló telepítéséhez.
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
# IIS-hitelesítés
Az Azure Multi-Factor Authentication-kiszolgáló IIS-hitelesítés szakaszában engedélyezheti és konfigurálhatja az IIS-hitelesítést a Microsoft IIS-webalkalmazásokkal való integrációra. Az Azure Multi-Factor Authentication-kiszolgáló egy beépülő modult telepít, amely képes szűrni az IIS-webkiszolgálónak küldött kéréseket az Azure Multi-Factor Authentication hozzáadása érdekében. Az IIS beépülő modul támogatja az űrlapalapú hitelesítést és az integrált Windows HTTP-hitelesítést. Ezenkívül a megbízható IP-címek konfigurálhatók úgy, hogy a belső IP-címek mentesüljenek kéttényezős hitelesítés alól.

![IIS-hitelesítés](./media/multi-factor-authentication-get-started-server-iis/iis.png)

## Az űrlapalapú IIS-hitelesítés használata az Azure Multi-Factor Authentication-kiszolgálóval
Űrlapalapú hitelesítést használó IIS-webalkalmazás védelméhez telepítse az Azure Multi-Factor Authentication-kiszolgálót az IIS-webkiszolgálón és konfigurálja a kiszolgálót a következő eljárásnak megfelelően.

1. Az Azure Multi-Factor Authentication-kiszolgálón kattintson a bal oldali menüben lévő IIS-hitelesítés ikonra.
2. Kattintson az Űrlapalapú fülre.
3. Kattintson a Hozzáadás... gombra.
4. A felhasználónév, jelszó és tartomány változók automatikus észleléséhez adja meg a bejelentkezési URL-t (például https://localhost/contoso/auth/login.aspx) az Űrlapalapú webhely automatikus konfigurálása párbeszédpanelen, és kattintson az OK gombra.
5. Jelölje be a Multi-Factor Authentication felhasználói egyeztetés megkövetelése jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és többtényezős hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesülni fog a többtényezős hitelesítés alól, ne jelölje be a jelölőnégyzetet.
6. Ha az oldal változóit nem lehet automatikusan észlelni, kattintson a Megadás manuálisan gombra az Űrlapalapú webhely automatikus konfigurálása párbeszédpanelen.
7. Az Űrlapalapú webhely hozzáadása párbeszédpanelen adja meg a bejelentkezési oldal URL-címét a Küldési URL-cím mezőben, majd adjon meg egy alkalmazásnevet (nem kötelező). Az alkalmazásnév az Azure Multi-Factor Authentication-jelentésekben jelenik meg, illetve megjelenhet az SMS-es vagy mobilalkalmazásos hitelesítési üzenetekben. A küldési URL-címről a súgófájlban talál további információt.
8. Válassza ki a megfelelő kérési formátumot. Ez a legtöbb webalkalmazás esetén „POST or GET” értékre van állítva.
9. Adja meg a Felhasználónév változót, a Jelszó változót és a Tartomány változót (ha megjelenik a bejelentkezési oldalon). Előfordulhat, hogy a beviteli mezők nevének megtekintéséhez meg kell nyitnia a bejelentkezési lapot egy webböngészőben, majd a lapon a jobb gombbal kattintva a „Forrás megtekintése” parancsot kell választania.
10. Jelölje be a Require Azure Multi-Factor Authentication user match (Azure Multi-Factor Authentication felhasználói egyezés megkövetelése) jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és többtényezős hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesülni fog a többtényezős hitelesítés alól, ne jelölje be a jelölőnégyzetet. A szolgáltatásról a súgófájlban talál további információt.
11. Kattintson a Speciális... gombra a speciális beállítások áttekintéséhez, beleértve az egyéni elutasítási oldalfájl kiválasztásának lehetőségét, a sikeres hitelesítések gyorsítótárazását a webhelyen egy adott időtartamra cookie-kkal, illetve annak kiválasztását, hogy a rendszer hitelesítse-e az elsődleges hitelesítő adatokat a Windows tartományban, LDAP-címtárban vagy RADIUS-kiszolgálón. Amikor végzett, kattintson az OK gombra az Űrlapalapú webhely hozzáadása párbeszédpanelre való visszatéréshez. A speciális beállításokról a súgófájlban talál további információt.
12. Kattintson az OK gombra.
13. Miután a rendszer észlelte vagy megadta az URL-címet és az oldal változóit, a webhely adatai megjelennek az Űrlapalapú panelen.
14. Tekintse meg az alábbi IIS beépülő modulok engedélyezése az Azure Multi-Factor Authentication-kiszolgálón című szakaszt az IIS-hitelesítés konfigurációjának befejezéséhez.

## Integrált Windows IIS-hitelesítés használata az Azure Multi-Factor Authentication-kiszolgálóval
Integrált Windows HTTP-hitelesítést használó IIS-webalkalmazás védelméhez telepítse az Azure Multi-Factor Authentication-kiszolgálót az IIS-webkiszolgálón, és konfigurálja a kiszolgálót az alábbi eljárásnak megfelelően.

1. Az Azure Multi-Factor Authentication-kiszolgálón kattintson a bal oldali menüben lévő IIS-hitelesítés ikonra.
2. Kattintson a HTTP fülre. Kattintson az Űrlapalapú fülre.
3. Kattintson a Hozzáadás... gombra.
4. Az Alap URL-cím hozzáadása párbeszédpanelen az Alap URL-cím mezőben adja meg az URL-címét annak a webhelynek, amelyen a rendszer HTTP-hitelesítést végez (például http://localhost/owa), és adjon meg egy alkalmazásnevet (nem kötelező). Az alkalmazásnév az Azure Multi-Factor Authentication-jelentésekben jelenik meg, illetve megjelenhet az SMS-es vagy mobilalkalmazásos hitelesítési üzenetekben.
5. Módosítja az Üresjárati időkorlát és a Munkamenetek maximális időtartama értékét, ha az alapértelmezett értékek nem elégségesek.
6. Jelölje be a Multi-Factor Authentication felhasználói egyeztetés megkövetelése jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és többtényezős hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesülni fog a többtényezős hitelesítés alól, ne jelölje be a jelölőnégyzetet. A szolgáltatásról a súgófájlban talál további információt.
7. Szükség esetén jelölje be a cookie-gyorsítótár jelölőnégyzetét.
8. Kattintson az OK gombra.
9. Tekintse meg az alábbi [IIS beépülő modulok engedélyezése az Azure Multi-Factor Authentication-kiszolgálón](#enable-iis-plug-ins-for-azure-multi-factor-authentication-server) című szakaszt az IIS-hitelesítés konfigurációjának elvégzéséhez.

## IIS beépülő modulok engedélyezése az Azure Multi-Factor Authentication-kiszolgálón
Miután konfigurálta az Űrlapalapú hitelesítés vagy a HTTP-hitelesítés URL-címeit és beállításait, ki kell választania a helyeket, ahol az Azure Multi-Factor Authentication IIS beépülő modulokat betölteni és engedélyezni szeretné az IIS-ben. Kövesse az alábbi eljárást:

1. Ha az IIS 6-os verzióját futtatja, kattintson az ISAPI fülre, és válassza ki a webhelyet, amely alatt a webalkalmazás fut (például alapértelmezett webhely) az Azure Multi-Factor Authentication ISAPI-szűrő beépülő modul engedélyezéséhez a webhely esetében.
2. Ha az IIS 7-es vagy újabb verzióját futtatja, kattintson a Natív modul fülre, és válassza ki a kiszolgálót, webhelye(ke)t vagy alkalmazás(oka)t az IIS beépülő modul engedélyezéséhez a kívánt szinte(ke)n.
3. Kattintson az IIS-hitelesítés engedélyezése jelölőnégyzetre a képernyő tetején. Az Azure Multi-Factor Authentication mostantól biztosítja a kiválasztott IIS-alkalmazás védelmét. Győződjön meg arról, hogy importálta a felhasználókat a kiszolgálóra. Tekintse meg az alábbi Megbízható IP-címek szakaszt, ha a belső IP-címeket engedélyezési listára szeretné helyezni, hogy a kéttényezős hitelesítés végrehajtása ne legyen kötelező, amikor ezekről a helyszínekről jelentkezik be a webhelyre.

## Megbízható IP-címek
A Megbízható IP-címek segítségével a felhasználók adott IP-címekről vagy alhálózatokról származó webhelykérések esetén kihagyhatják az Azure Multi-Factor Authenticationt. Például mentesítheti a felhasználókat az Azure Multi-Factor Authentication alól, ha az irodából jelentkeznek be. Ehhez az irodai alhálózatot Megbízható IP-címek bejegyzésként kell megadni. A Megbízható IP-címek konfigurálásához kövesse az alábbi eljárást:

1. Az IIS-hitelesítés szakaszban kattintson a Megbízható IP-címek fülre.
2. Kattintson a Hozzáadás... gombra.
3. A Megbízható IP-címek hozzáadása párbeszédpanel megjelenésekor válassza az Egyetlen IP-cím, az IP-címtartomány vagy az Alhálózat választógombot.
4. Adja meg az engedélyezési listához hozzáadni kívánt IP-címet, IP-címtartományt vagy alhálózatot. Ha alhálózatot ad meg, válassza ki a megfelelő hálózati maszkot, és kattintson az OK gombra. Ezzel az engedélyezési lista hozzáadása megtörtént.

<!--HONumber=Sep16_HO4-->


