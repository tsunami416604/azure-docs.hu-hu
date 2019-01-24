---
title: A probléma jelentkezik be a hozzáférési panel webhelyére |} A Microsoft Docs
description: Útmutató jelentkezzen be a hozzáférési Panel használata közben felmerülő problémák hibaelhárítása
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviwer: japere,asteen
ms.openlocfilehash: 34a4da2c14b84b9e539fc2d3e755a523cf3574c9
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54847237"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>A probléma jelentkezik be a hozzáférési panel webhelyére

A hozzáférési panelen megtekintheti és elindíthatja felhőalapú alkalmazások, amelyek az Azure AD-rendszergazda hozzáférést adott azokat a webes portál, amely lehetővé teszi a felhasználó, aki rendelkezik munkahelyi vagy iskolai fiókkal az Azure Active Directoryban (Azure AD). Önkiszolgáló csoport- és alkalmazáskezelési lehetőségeket a hozzáférési panelen keresztül felhasználó, aki rendelkezik az Azure AD-verziók is használhatja. A hozzáférési panelen elkülönül az Azure Portalon, és nem igényel a felhasználók számára, hogy rendelkezik Azure-előfizetéssel.

Felhasználók jelentkezhetnek be a hozzáférési panelen, ha rendelkeznek egy munkahelyi vagy iskolai fiókkal az Azure ad-ben.

-   Közvetlenül az Azure AD hitelesítheti a felhasználókat.

-   Felhasználók hitelesíthetők az Active Directory összevonási szolgáltatások (AD FS) használatával.

-   A Windows Server Active Directory felhasználók hitelesíteni tudja.

Ha a felhasználó rendelkezik egy előfizetést az Azure vagy Office 365 és az Azure portal vagy egy Office 365-alkalmazás használatával, ők is látni fogják a hozzáférési Panel segítségével zökkenőmentesen anélkül, jelentkezzen be újra. Nem hitelesített felhasználók a rendszer kéri, jelentkezzen be a felhasználónevet és jelszót a fiókjuk az Azure ad-ben. Ha a szervezet összevonási van konfigurálva, írja be a felhasználónevet is használhatók.

## <a name="general-issues-to-check-first"></a>Először ellenőrizze a általános problémák 

-   Ellenőrizze, hogy a felhasználó bejelentkezik a **javítsa ki az URL-cím**: <https://myapps.microsoft.com>

-   Győződjön meg arról, hogy a felhasználó böngészőjében hozzáadta az URL-címet a **megbízható helyek**

-   Ellenőrizze, hogy a felhasználó fiókjának **engedélyezve** bejelentkezéshez szükséges.

-   Ellenőrizze, hogy a felhasználó fiókjának **nincs zárolva.**

-   Győződjön meg arról, hogy a felhasználó **nem lejárt vagy elfelejtett jelszó.**

-   Győződjön meg arról, hogy **multi-factor Authentication** nem blokkolja a felhasználói hozzáférést.

-   Győződjön meg arról, hogy egy **feltételes hozzáférési szabályzat** vagy **Identity Protection** házirend nem blokkolja a felhasználói hozzáférést.

-   Győződjön meg arról, hogy a felhasználó **hitelesítési kapcsolattartási adatok** naprakész, hogy a multi-factor Authentication vagy feltételes hozzáférési házirendek kikényszerítéséhez.

-   Győződjön meg arról, hogy is megpróbálja törölje a böngésző cookie-kat, majd próbálkozzon újra bejelentkezni.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>A hozzáférési Panel a böngészőben követelmények teljesítése

A hozzáférési Panel a böngészőben, amely támogatja a JavaScript szükséges, és CSS engedélyezve van. Jelszavas egyszeri bejelentkezés (SSO) használata a hozzáférési panelen, a felhasználó böngészőjében a hozzáférési Panel bővítményt kell telepíteni. Ezzel a bővítménnyel automatikusan letöltődik, amikor a felhasználó kiválaszt egy alkalmazást, amely konfigurálva van a jelszóalapú egyszeri bejelentkezés.

A jelszóalapú egyszeri bejelentkezés a végfelhasználó böngészők lehet:

-   Az Internet Explorer 8, 9, 10, 11 – a Windows 7 vagy újabb

-   A Microsoft Edge a Windows 10 Évfordulós kiadása vagy újabb 

-   Chrome – A Windows 7 vagy újabb, és MacOS X rendszeren vagy újabb

-   A Firefox 26.0 vagy később – a Windows XP SP2 vagy újabb, és a Mac OS X 10,6 vagy újabb


## <a name="problems-with-the-users-account"></a>A felhasználói fiókkal kapcsolatos problémák

A hozzáférési panelen való hozzáférés blokkolva lesz a felhasználói fiókkal kapcsolatos hiba miatt. Az alábbiakban néhány módszert, és hibaelhárítást kapcsolatos problémák megoldásához a felhasználók és a fiók beállításait:

-   [Ellenőrizze, hogy egy felhasználói fiókot az Azure Active Directoryban](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Ellenőrizze a felhasználói fiók állapota](#check-a-users-account-status)

-   [A jelszó alaphelyzetbe állítása](#reset-a-users-password)

-   [Önkiszolgáló jelszóátállítás engedélyezése](#enable-self-service-password-reset)

-   [A felhasználó a multi-factor authentication szolgáltatás állapotának ellenőrzése](#check-a-users-multi-factor-authentication-status)

-   [A felhasználó hitelesítési kapcsolattartási adatok ellenőrzése](#check-a-users-authentication-contact-info)

-   [A vendégfelhasználói csoporttagságok ellenőrzése](#check-a-users-group-memberships)

-   [Ellenőrizze a felhasználó hozzárendelt licencek](#check-a-users-assigned-licenses)

-   [Egy felhasználó egy licenc hozzárendelése](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Ellenőrizze, hogy egy felhasználói fiókot az Azure Active Directoryban

Ellenőrizze, hogy jelen-e egy felhasználó fiókját, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresés** az Önt érdeklő felhasználó és **kattintson az adott sorra** kiválasztásához.

7.  Ellenőrizze, győződjön meg arról, hogy néznek várt, és nincs adat nem található a felhasználói objektum tulajdonságait.

### <a name="check-a-users-account-status"></a>Ellenőrizze a felhasználói fiók állapota

Egy felhasználói fiók állapotának ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresés** az Önt érdeklő felhasználó és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **profil**.

8.  A **beállítások** ügyeljen arra, hogy **bejelentkezés blokkolása** értékre van állítva **nem**.

### <a name="reset-a-users-password"></a>A jelszó alaphelyzetbe állítása

A jelszó alaphelyzetbe állítása, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresés** az Önt érdeklő felhasználó és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **jelszó alaphelyzetbe állítása** gombra a felhasználó panel tetején.

8.  Kattintson a **jelszó alaphelyzetbe állítása** gombot a **jelszó alaphelyzetbe állítása** megjelenő panelen.

9.  Másolás a **ideiglenes jelszó** vagy **adjon meg egy új jelszót** a felhasználó számára.

10. Kommunikáció az új jelszó a felhasználónak, akkor lehet szükség a jelszó módosítására bejelentkezéskor a következő Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Új jelszó önkiszolgáló kérésének engedélyezése

Új jelszó önkiszolgáló kérésének engedélyezéséhez kövesse az alábbi üzembe helyezési lépéseket:

-   [Engedélyezése a felhasználók számára az Azure Active Directory-jelszavaikat](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Segítségével a felhasználók visszaállíthassák vagy a helyszíni Active Directory-jelszavaikat](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords)

### <a name="check-a-users-multi-factor-authentication-status"></a>A felhasználó a multi-factor authentication szolgáltatás állapotának ellenőrzése

A felhasználó a multi-factor authentication szolgáltatás állapotának ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  Kattintson a **multi-factor Authentication** gombra a panel tetején.

7.  Egyszer a **multi-factor Authentication felügyeleti portál** terhelés esetén ellenőrizze, hogy az a **felhasználók** fülre.

8.  A kiválasztott felhasználók által keresése, szűrése és rendezése a felhasználó található.

9.  A felhasználók listájából válassza ki a felhasználót és **engedélyezése**, **letiltása**, vagy **érvényesítése** a multi-factor authentication igény szerint.

   >[!NOTE]
   >Ha egy felhasználó egy **kényszerített** állapotba, előfordulhat, hogy beállította azokat **letiltott** ideiglenesen ahhoz, hogy azokat vissza a fiókba. Vissza a k, módosíthatja az állapotuk **engedélyezve** újra a megköveteli tőlük, hogy regisztrálja újra a kapcsolattartási adatait a következő bejelentkezés során. Azt is megteheti, követheti a lépések a [ellenőrizze a felhasználó hitelesítési kapcsolattartási adatok](#check-a-users-authentication-contact-info) ellenőrizze vagy állítsa be ezeket az adatokat a számukra.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>A felhasználó hitelesítési kapcsolattartási adatok ellenőrzése

A felhasználó hitelesítési kapcsolattartási adatok a multi-factor Authentication hitelesítéshez használt ellenőrzéséhez feltételes hozzáférés, az Identity Protection és a jelszó-visszaállítás, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresés** az Önt érdeklő felhasználó és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **profil**.

8.  Görgessen le a **hitelesítési kapcsolattartási adatok**.

9.  **Felülvizsgálat** az adatok regisztrálva a felhasználó és a frissítési igény szerint.

### <a name="check-a-users-group-memberships"></a>A vendégfelhasználói csoporttagságok ellenőrzése

A vendégfelhasználói csoporttagságok ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresés** az Önt érdeklő felhasználó és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **csoportok** megtekintéséhez, amely csoportosítja a felhasználó tagja.

### <a name="check-a-users-assigned-licenses"></a>Ellenőrizze a felhasználó hozzárendelt licencek

A felhasználó hozzárendelt licencek ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresés** az Önt érdeklő felhasználó és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **licencek** megtekintéséhez, amelyek licencek, a felhasználó jelenleg hozzá van rendelve.

### <a name="assign-a-user-a-license"></a>Egy felhasználó egy licenc hozzárendelése 

Licenc hozzárendelése egy felhasználóhoz, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresés** az Önt érdeklő felhasználó és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **licencek** megtekintéséhez, amelyek licencek, a felhasználó jelenleg hozzá van rendelve.

8.  Kattintson a **hozzárendelése** gombra.

9.  Válassza ki **egy vagy több termék** elérhető termékek listájában.

10. **Nem kötelező** kattintson a **hozzárendelési beállítások** rendeljen kínálja a termékek az elemet. Kattintson a **Ok** amikor a művelet befejeződött.

11. Kattintson a **hozzárendelése** gomb ezek a licencek hozzárendelése a felhasználóhoz.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Ha a probléma megoldásához nem ezeket a hibaelhárítási lépéseket

Nyisson meg egy támogatási jegyet a következő adatokat, ha rendelkezésre áll:

-   Megfelelési hiba azonosítója

-   Egyszerű felhasználónév (felhasználó e-mail-címe)

-   Bérlőazonosító

-   Böngésző típusa

-   Időzóna és idő/időkeret során hiba történik.

-   Fiddler-nyomkövetés

## <a name="next-steps"></a>További lépések
[Az alkalmazásproxy egyszeri bejelentkezést az alkalmazásokba biztosít](application-proxy-configure-single-sign-on-with-kcd.md)
