---
title: "A probléma jelentkezik be a hozzáférési panel webhelyre |} Microsoft Docs"
description: "Jelentkezzen be a hozzáférési Panel használatához közben esetlegesen fellépő problémák megoldásához útmutatást"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.reviwer: japere
ms.openlocfilehash: 0f50a705d4453970d039c2219aef8e1a411fd533
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>A probléma jelentkezik be a hozzáférési panel webhelyre

A hozzáférési Panel egy webes portál, amely lehetővé teszi a felhasználó, aki rendelkezik munkahelyi vagy iskolai fiókkal az Azure Active Directory (Azure AD) egy megtekintheti és elindíthatja felhőalapú alkalmazásokhoz az Azure AD-rendszergazda engedélyezte őket a hozzáférést. Azure AD-verziók rendelkező felhasználó az önkiszolgáló csoportkezelési és a hozzáférési Panel az alkalmazás-felügyeleti képességeit használhatja. A hozzáférési Panel elkülönül az Azure-portálon, és nem igényli a felhasználók Azure-előfizetésre.

Felhasználók is jelentkeznek be a hozzáférési panelre, ha telepítve a munkahelyi vagy iskolai fiókkal az Azure ad-ben.

-   Felhasználók hitelesítése közvetlenül az Azure ad.

-   Felhasználók hitelesítése az Active Directory összevonási szolgáltatások (AD FS) használatával.

-   Windows Server Active Directory felhasználók hitelesíthetők.

Ha a felhasználó rendelkezik előfizetéssel az Azure vagy Office 365 és az Azure-portálon vagy az Office 365-alkalmazás használatával, akkor fog tudni használatát, a hozzáférési Panel zökkenőmentesen anélkül újból bejelentkezni. Jelentkezzen be a felhasználónevet és jelszót a fiókjuk Azure AD-ben nem hitelesített felhasználók kérni. Ha a szervezet összevonási van konfigurálva, írja be a felhasználónevet is használhatók.

## <a name="general-issues-to-check-first"></a>Először ellenőrizze a általános problémák 

-   Győződjön meg arról, hogy a felhasználó próbál bejelentkezni a **javítsa ki az URL-cím**: <https://myapps.microsoft.com>

-   Győződjön meg arról, hogy a felhasználó böngészője hozzáadta az URL-CÍMÉT a **megbízható helyek**

-   Győződjön meg arról, hogy a felhasználói fiók **engedélyezett** a indított bejelentkezések.

-   Győződjön meg arról, hogy a felhasználói fiók **nincs zárolva.**

-   Győződjön meg arról, hogy a felhasználó **nem lejárt vagy elfelejtett jelszó.**

-   Győződjön meg arról, hogy **multi-factor Authentication** nem blokkolja a hozzáférést.

-   Győződjön meg arról, hogy egy **feltételes hozzáférési házirend** vagy **Identity Protection** házirend nem blokkolja a hozzáférést.

-   Győződjön meg arról, hogy a felhasználó **hitelesítési kapcsolattartási adatai** esetén a multi-factor Authentication vagy feltételes hozzáférési házirendek kényszerítését dátum.

-   A győződjön meg arról is megpróbálja törlésével a böngésző cookie-kat, majd próbálkozzon újból bejelentkezni.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>A hozzáférési Panel értekezlet böngészőre vonatkozó követelményei

A hozzáférési Panel igényel, amely támogatja a JavaScript egy böngészőt, és CSS engedélyezve van. Jelszó-alapú egyszeri bejelentkezést (SSO) használ a hozzáférési panelen, a hozzáférési Panel bővítményt telepíteni kell a felhasználó böngészőben. A bővítmény le automatikusan, ha a felhasználó megadja egy alkalmazás, amely jelszóalapú SSO van konfigurálva.

Jelszó-alapú egyszeri bejelentkezéshez a végfelhasználó böngészőkkel lehet:

-   Internet Explorer 8, 9, 10, 11 – a Windows 7 vagy újabb

-   Peremhálózati Windows 10 évforduló Edition vagy újabb 

-   Chrome – A Windows 7 vagy újabb, és MacOS X rendszeren vagy újabb

-   Firefox 26.0 vagy újabb – a Windows XP SP2 vagy újabb, és a Mac OS X 10,6 vagy újabb verzió


## <a name="problems-with-the-users-account"></a>A felhasználói fiókkal kapcsolatos problémák

A hozzáférési Panel elérését a felhasználói fiók problémája miatt blokkolható. Az alábbiakban néhány módszert hibákat, és a felhasználó és a fiók beállításainak problémáinak megoldásával:

-   [Ellenőrizze, hogy egy felhasználói fiók létezik-e az Azure Active Directoryban](#check-if-a-user-account-exists-in-azure-active-directory)

-   [A felhasználói fiók állapotának ellenőrzése](#check-a-users-account-status)

-   [A jelszó alaphelyzetbe állítása](#reset-a-users-password)

-   [Önkiszolgáló jelszóátállítás engedélyezése](#enable-self-service-password-reset)

-   [A felhasználó a multi-factor authentication állapotának ellenőrzése](#check-a-users-multi-factor-authentication-status)

-   [Ellenőrizze a felhasználó hitelesítési kapcsolattartási adatai](#check-a-users-authentication-contact-info)

-   [A felhasználói csoporttagság ellenőrzése](#check-a-users-group-memberships)

-   [A felhasználó licenc-hozzárendeléseket ellenőrzése](#check-a-users-assigned-licenses)

-   [A felhasználó a licenc hozzárendelése](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Ellenőrizze, hogy egy felhasználói fiók létezik-e az Azure Active Directoryban

Ellenőrizze, hogy jelen-e a felhasználói fiók, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresési** érdekli, felhasználó és **sorára kattintson** kiválasztásához.

7.  Ellenőrizze, győződjön meg arról, hogy azok meg, mint a várt, és nincs adat hiányzik a user objektum tulajdonságait.

### <a name="check-a-users-account-status"></a>A felhasználói fiók állapotának ellenőrzése

A felhasználói fiók állapotának ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresési** érdekli, felhasználó és **sorára kattintson** kiválasztásához.

7.  Kattintson a **profil**.

8.  A **beállítások** ügyeljen arra, hogy **blokk bejelentkezés** értékre van állítva **nem**.

### <a name="reset-a-users-password"></a>A jelszó alaphelyzetbe állítása

A jelszó visszaállításához kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresési** érdekli, felhasználó és **sorára kattintson** kiválasztásához.

7.  Kattintson a **jelszó-átállítási** gombra a felhasználó panel tetején.

8.  Kattintson a **jelszó-átállítási** gombra a **jelszó-átállítási** panel, amely akkor jelenik meg.

9.  Másolás a **ideiglenes jelszó** vagy **adjon meg egy új jelszót** a felhasználó számára.

10. Az új jelszót, amely a felhasználó kommunikációhoz, azok szükséges jelszó módosításához a következő bejelentkezéskor a során az Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Az önkiszolgáló jelszó-visszaállítás engedélyezése

Önkiszolgáló jelszóváltoztatás, kövesse az alábbi telepítési lépéseket:

-   [Segítségével a felhasználók visszaállíthassák az Azure Active Directory-jelszavaikat](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-their-azure-ad-passwords)

-   [Lehetővé teszi a felhasználók visszaállíthassák vagy módosíthassák a helyszíni Active Directory-jelszavaikat](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords)

### <a name="check-a-users-multi-factor-authentication-status"></a>A felhasználó a multi-factor authentication állapotának ellenőrzése

A felhasználó a multi-factor authentication állapotának ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  Kattintson a **multi-factor Authentication** gomb a panel tetején.

7.  Egyszer a **multi-factor Authentication felügyeleti portál** terhelés esetén gondoskodjon arról, hogy a **felhasználók** fülre.

8.  Keresés, rendezés vagy szűréssel keresse meg a felhasználót a felhasználók listáját.

9.  Válassza ki a felhasználót a felhasználók listájának és **engedélyezése**, **tiltsa le a**, vagy **érvényesítése** többtényezős hitelesítést a kívánt módon működjenek.

   >[!NOTE]
   >Ha egy felhasználó egy **kényszerített** állapotba kerül, előfordulhat, hogy meg őket **letiltott** ideiglenesen ahhoz, hogy azokat vissza a figyelembe. Miután bekerültek vissza, módosíthatja az állapot **engedélyezve** újra a megköveteli tőlük, hogy regisztrálja újra a során a következő bejelentkezéskor a kapcsolattartási adatait. Azt is megteheti, hogy is kövesse a [ellenőrizni kell a felhasználó hitelesítési kapcsolattartási adatait](#check-a-users-authentication-contact-info) ellenőrizze vagy állítsa be ezeket az adatokat a számukra.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>Ellenőrizze a felhasználó hitelesítési kapcsolattartási adatai

A felhasználó hitelesítési használt kapcsolattartási adatokat a többtényezős hitelesítést, a feltételes hozzáférés, a Identity Protection és a jelszó-átállítási ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresési** érdekli, felhasználó és **sorára kattintson** kiválasztásához.

7.  Kattintson a **profil**.

8.  Görgessen le a **hitelesítési kapcsolattartási adatai**.

9.  **Tekintse át** az adatok regisztrálva a felhasználó és a frissítési igény szerint.

### <a name="check-a-users-group-memberships"></a>A felhasználói csoporttagság ellenőrzése

A felhasználói csoporttagság ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresési** érdekli, felhasználó és **sorára kattintson** kiválasztásához.

7.  Kattintson a **csoportok** megtekintéséhez, amely csoportosítja a felhasználó tagja legyen.

### <a name="check-a-users-assigned-licenses"></a>A felhasználó licenc-hozzárendeléseket ellenőrzése

A felhasználó licenc-hozzárendeléseket ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresési** érdekli, felhasználó és **sorára kattintson** kiválasztásához.

7.  Kattintson a **licencek** megtekintéséhez, amely licencek, a felhasználó jelenleg hozzá van rendelve.

### <a name="assign-a-user-a-license"></a>A felhasználó a licenc hozzárendelése 

A licenc hozzárendelése egy felhasználóhoz, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresési** érdekli, felhasználó és **sorára kattintson** kiválasztásához.

7.  Kattintson a **licencek** megtekintéséhez, amely licencek, a felhasználó jelenleg hozzá van rendelve.

8.  Kattintson a **hozzárendelése** gombra.

9.  Válassza ki **egy vagy több termék** választható termékek közül.

10. **Nem kötelező** kattintson a **hozzárendelés beállításai** elem termékek granularly hozzárendelni. Kattintson a **Ok** amikor ez befejeződik.

11. Kattintson a **hozzárendelése** gomb a licencek hozzárendelése a felhasználóhoz.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Ha ezek a hibaelhárítási lépéseket nem oldja meg a problémát

támogatási jegy megnyitása a következő információkat, ha rendelkezésre áll:

-   Megfelelési hiba azonosítója

-   Egyszerű felhasználónév (felhasználó e-mail címe)

-   Bérlőazonosító

-   Böngésző típusa

-   Időzóna és idő/időkeretre során hiba történik.

-   Fiddler nyomkövetések

## <a name="next-steps"></a>Következő lépések
[Adja meg az egyszeri bejelentkezés az alkalmazásokba a Proxy](active-directory-application-proxy-sso-using-kcd.md)
