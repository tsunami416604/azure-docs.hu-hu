---
title: Probléma jelentkezik be a hozzáférési panel webhelyére | Microsoft Docs
description: Útmutató a bejelentkezés során felmerülő problémák elhárításához a hozzáférési panel használatának megkísérlésekor
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviwer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f2ad4db231c616b3022ecafc62b12d6d81b67fc
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2020
ms.locfileid: "84760813"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Probléma jelentkezik be a hozzáférési panel webhelyére

A hozzáférési panel egy webalapú portál, amely lehetővé teszi, hogy egy Azure Active Directory (Azure AD) munkahelyi vagy iskolai fiókkal rendelkező felhasználó megtekintse és elindítsa azokat a felhőalapú alkalmazásokat, amelyekhez az Azure AD-rendszergazda hozzáférést kapott. Az Azure AD-kiadásokkal rendelkező felhasználók az önkiszolgáló csoportok és az alkalmazások felügyeleti képességeit is használhatják a hozzáférési panelen. A hozzáférési panel el van különítve a Azure Portaltól, és nem igényli, hogy a felhasználók Azure-előfizetéssel rendelkezzenek.

Ha munkahelyi vagy iskolai fiókja van az Azure AD-ben, a felhasználók bejelentkezhetnek a hozzáférési panelre.

-   A felhasználókat az Azure AD közvetlenül is hitelesítheti.

-   A felhasználók hitelesítése Active Directory összevonási szolgáltatások (AD FS) (AD FS) használatával végezhető el.

-   A felhasználókat a Windows Server Active Directory hitelesítheti.

Ha a felhasználó rendelkezik Azure-vagy Office 365-előfizetéssel, és a Azure Portal vagy Office 365 alkalmazást használta, zökkenőmentesen használhatja a hozzáférési panelt anélkül, hogy újra be kellene jelentkeznie. A nem hitelesített felhasználókat a rendszer felszólítja a bejelentkezésre az Azure AD-beli fiókjához tartozó felhasználónévvel és jelszóval. Ha a szervezet konfigurálta az összevonást, a Felhasználónév beírása elegendő.

## <a name="general-issues-to-check-first"></a>Általános problémák az első kereséshez 

-   Győződjön meg arról, hogy a felhasználó bejelentkezik a **megfelelő URL-címre**:<https://myapps.microsoft.com>

-   Győződjön meg arról, hogy a felhasználó böngészőjében hozzá lett adva az URL-cím a **megbízható helyekhez**

-   Győződjön meg arról, hogy a felhasználó fiókja **engedélyezve** van a bejelentkezésekhez.

-   Győződjön meg arról, hogy a felhasználó fiókja **nincs zárolva.**

-   Győződjön meg arról, hogy a felhasználó **jelszava nem járt le vagy nem felejtette el.**

-   Győződjön meg arról, hogy **multi-Factor Authentication** nem blokkolja a felhasználói hozzáférést.

-   Győződjön meg arról, hogy a **feltételes hozzáférési szabályzat** vagy az **Identity Protection** -házirend nem blokkolja a felhasználói hozzáférést.

-   Győződjön meg arról, hogy a felhasználó **hitelesítési kapcsolattartási adatai** naprakészek multi-Factor Authentication vagy feltételes hozzáférési szabályzatok érvényesítéséhez.

-   Ügyeljen arra, hogy a böngésző cookie-jait is törölje, és próbálja meg újból bejelentkezni.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>A hozzáférési panel Meeting Browser követelményei

A hozzáférési panel egy olyan böngészőt igényel, amely támogatja a JavaScript használatát, és lehetővé teszi a CSS használatát. Ha jelszó-alapú egyszeri bejelentkezést (SSO) szeretne használni a hozzáférési panelen, a hozzáférési panel kiterjesztését telepíteni kell a felhasználó böngészőjébe. A rendszer automatikusan letölti a bővítményt, ha a felhasználó egy jelszó alapú egyszeri bejelentkezéshez konfigurált alkalmazást választ ki.

Jelszó-alapú egyszeri bejelentkezés esetén a végfelhasználók böngészőjében a következő lehet:

-   Internet Explorer 8, 9, 10, 11 – Windows 7 vagy újabb rendszeren

-   Microsoft Edge a Windows 10 évfordulós kiadásán vagy később 

-   Chrome – Windows 7 vagy újabb, illetve MacOS X vagy újabb rendszereken

-   Firefox 26,0 vagy újabb verzió – Windows XP SP2 vagy újabb rendszeren, valamint Mac OS X 10,6 vagy újabb rendszeren


## <a name="problems-with-the-users-account"></a>A felhasználói fiókkal kapcsolatos problémák

A hozzáférési panel hozzáférése a felhasználói fiókkal fennálló probléma miatt blokkolható. Az alábbiakban néhány módon elháríthatja a felhasználókat és a fiókjuk beállításait:

-   [Annak ellenőrzése, hogy létezik-e felhasználói fiók a Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Felhasználói fiók állapotának megkeresése](#check-a-users-account-status)

-   [Felhasználó jelszavának alaphelyzetbe állítása](#reset-a-users-password)

-   [Új jelszó önkiszolgáló kérésének engedélyezése](#enable-self-service-password-reset)

-   [A felhasználó többtényezős hitelesítési állapotának keresése](#check-a-users-multi-factor-authentication-status)

-   [A felhasználó hitelesítési kapcsolattartási adatainak megkeresése](#check-a-users-authentication-contact-info)

-   [Felhasználó csoport-tagságának keresése](#check-a-users-group-memberships)

-   [Felhasználóhoz rendelt licencek keresése](#check-a-users-assigned-licenses)

-   [Licenc felhasználóhoz rendelése](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Annak ellenőrzése, hogy létezik-e felhasználói fiók a Azure Active Directory

Az alábbi lépéseket követve ellenőrizheti, hogy van-e felhasználó fiókja:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5.  kattintson **a minden felhasználó**elemre.

6.  **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  Ellenőrizze a felhasználói objektum tulajdonságait, és győződjön meg róla, hogy a vártnak megfelelően van-e megadva, és nincs hiányzó adatmennyiség.

### <a name="check-a-users-account-status"></a>Felhasználói fiók állapotának megkeresése

A felhasználó fiókja állapotának megtekintéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5.  kattintson **a minden felhasználó**elemre.

6.  **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  kattintson a **profil**elemre.

8.  A **Beállítások** területen győződjön meg arról, hogy a **Letiltás tiltása** beállítás **nem**értékre van állítva.

### <a name="reset-a-users-password"></a>Felhasználó jelszavának alaphelyzetbe állítása

A felhasználó jelszavának alaphelyzetbe állításához kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5.  kattintson **a minden felhasználó**elemre.

6.  **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  kattintson a **jelszó alaphelyzetbe állítása** gombra a felhasználó ablaktábla tetején.

8.  kattintson a **jelszó alaphelyzetbe állítása** gombra a megjelenő **jelszó visszaállítása** panelen.

9.  Másolja ki az **ideiglenes jelszót** , vagy **adjon meg egy új jelszót** a felhasználó számára.

10. Ezt az új jelszót a felhasználónak kell megadnia, hogy a következő bejelentkezéskor módosítania kell a jelszót a Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Új jelszó önkiszolgáló kérésének engedélyezése

Az önkiszolgáló jelszó-visszaállítás engedélyezéséhez kövesse az alábbi telepítési lépéseket:

-   [A Azure Active Directory jelszavának alaphelyzetbe állításának engedélyezése a felhasználók számára](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Az Active Directory helyszíni jelszavak alaphelyzetbe állításának vagy módosításának engedélyezése a felhasználók számára](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>A felhasználó többtényezős hitelesítési állapotának keresése

A felhasználó többtényezős hitelesítési állapotának megtekintéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5. kattintson **a minden felhasználó**elemre.

6. kattintson a panel tetején található **multi-Factor Authentication** gombra.

7. Miután a **multi-Factor Authentication felügyeleti portál** betöltődik, győződjön meg arról, hogy a **felhasználók** lapon van.

8. Keresse meg a felhasználót a felhasználók listájában keresés, szűrés vagy rendezés alapján.

9. Válassza ki a felhasználót a felhasználók listájából, és **engedélyezze**, **Tiltsa le**vagy **kényszerítse** ki a többtényezős hitelesítést igény szerint.

   >[!NOTE]
   >Ha egy felhasználó **kényszerített** állapotban van, beállíthatja, hogy ideiglenesen **letiltsa** őket a fiókjába való visszatéréshez. Ha ismét bejelentkeznek, a következő bejelentkezéskor **újra meg** lehet változtatni az állapotukat, hogy újra regisztrálni tudják a kapcsolattartási adatokat. Azt is megteheti, hogy a [felhasználó hitelesítési kapcsolattartási adatainak ellenőrzése](#check-a-users-authentication-contact-info) lapon található lépéseket követve ellenőrizheti vagy beállítja ezeket az adatokat.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>A felhasználó hitelesítési kapcsolattartási adatainak megkeresése

A többtényezős hitelesítéshez, a feltételes hozzáféréshez, az Identitáskezelés és a jelszó alaphelyzetbe állításához használt hitelesítési kapcsolattartási adatok vizsgálatához kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5.  kattintson **a minden felhasználó**elemre.

6.  **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  kattintson a **profil**elemre.

8.  Görgessen le a **hitelesítési kapcsolattartási adatokhoz**.

9.  **Tekintse át** a felhasználó számára regisztrált és a frissítéshez szükséges, igény szerinti frissítést.

### <a name="check-a-users-group-memberships"></a>Felhasználó csoport-tagságának keresése

A felhasználók csoporttagságok vizsgálatához kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5.  kattintson **a minden felhasználó**elemre.

6.  **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  a **csoportok** elemre kattintva megtekintheti, hogy a felhasználó mely csoportok tagjai.

### <a name="check-a-users-assigned-licenses"></a>Felhasználóhoz rendelt licencek keresése

A felhasználóhoz hozzárendelt licencek vizsgálatához kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5.  kattintson **a minden felhasználó**elemre.

6.  **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  a **licencek** elemre kattintva megtekintheti, hogy a felhasználó mely licenceket rendeli hozzá jelenleg.

### <a name="assign-a-user-a-license"></a>Licenc felhasználóhoz rendelése 

Ha licencet szeretne hozzárendelni egy felhasználóhoz, kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5.  kattintson **a minden felhasználó**elemre.

6.  **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  a **licencek** elemre kattintva megtekintheti, hogy a felhasználó mely licenceket rendeli hozzá jelenleg.

8.  kattintson a **hozzárendelés** gombra.

9.  Válasszon ki **egy vagy több terméket** az elérhető termékek listájából.

10. A termékek részletes hozzárendeléséhez kattintson a **hozzárendelési beállítások** **elemre** . Ha a művelet befejeződött, kattintson **az OK gombra** .

11. Kattintson a **hozzárendelés** gombra a licencek ehhez a felhasználóhoz való hozzárendeléséhez.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Ha ezek a hibaelhárítási lépések nem oldják meg a problémát

Ha elérhető, nyisson meg egy támogatási jegyet a következő információkkal:

-   Korrelációs hiba azonosítója

-   UPN (felhasználói e-mail-cím)

-   Bérlőazonosító

-   Böngésző típusa

-   Az időzóna és az idő/időkeret a hiba bekövetkezésekor

-   Hegedűs nyomkövetései

## <a name="next-steps"></a>További lépések
[Egyszeri bejelentkezés biztosítása az alkalmazásokba az Application proxy használatával](application-proxy-configure-single-sign-on-with-kcd.md)
