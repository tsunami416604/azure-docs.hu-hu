---
title: Probléma bejelentkezés a hozzáférési panel honlapján | Microsoft dokumentumok
description: Útmutató a hozzáférési panel használata közben a bejelentkezés során felmerülő problémák elhárításához
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.reviwer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7c6a9c3f26c8939176197a2ecf2fcd6026e9928
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784310"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Probléma a hozzáférési panel webhelyére való bejelentkezéssel

A Hozzáférési panel egy webalapú portál, amely lehetővé teszi, hogy az Azure Active Directoryban (Azure AD) munkahelyi vagy iskolai fiókkal rendelkező felhasználó megtekintse és elindítsa azokat a felhőalapú alkalmazásokat, amelyekhez az Azure AD-rendszergazda hozzáférést biztosított számukra. Az Azure AD-kiadásokkal rendelkező felhasználók önkiszolgáló csoport- és alkalmazáskezelési funkciókat is használhatnak a Hozzáférési panelen keresztül. A hozzáférési panel elkülönül az Azure Portalon, és nem követeli meg a felhasználók számára, hogy egy Azure-előfizetéssel.

A felhasználók bejelentkezhetnek a Hozzáférési panelre, ha rendelkeznek munkahelyi vagy iskolai fiókkal az Azure AD-ben.

-   A felhasználók közvetlenül hitelesíthetők az Azure AD-vel.

-   A felhasználók az Active Directory összevonási szolgáltatások (AD FS) segítségével hitelesíthetők.

-   A felhasználókat a Windows Server Active Directory hitelesítheti.

Ha egy felhasználó rendelkezik azure-előfizetéssel vagy Office 365-tel, és az Azure Portalt vagy egy Office 365-alkalmazást használja, zökkenőmentesen használhatja a Hozzáférési panelt anélkül, hogy újra be kellene jelentkeznie. A nem hitelesített felhasználók a rendszer kéri, hogy jelentkezzen be a felhasználónév és a jelszó használatával a fiók az Azure AD-ben. Ha a szervezet konfigurálta az összevonást, elegendő a felhasználónév beírása.

## <a name="general-issues-to-check-first"></a>Általános kérdések, amelyeket először ellenőrizni kell 

-   Ellenőrizze, hogy a felhasználó a **megfelelő URL-címre**jelentkezik-e be:<https://myapps.microsoft.com>

-   Győződjön meg arról, hogy a felhasználó böngészője hozzáadta az URL-címet a **megbízható helyekhez**

-   Győződjön meg arról, hogy a felhasználó fiókja **engedélyezve** van a bejelentkezéshez.

-   Győződjön meg arról, hogy a felhasználó fiókja **nincs zárolva.**

-   Győződjön meg arról, hogy a felhasználó **jelszava nem járt le vagy nem feledje el.**

-   Győződjön meg arról, hogy a **többtényezős hitelesítés** nem blokkolja a felhasználói hozzáférést.

-   Győződjön meg arról, hogy a **feltételes hozzáférési szabályzat** vagy az **identitásvédelmi** házirend nem blokkolja a felhasználói hozzáférést.

-   Győződjön meg arról, hogy a felhasználó **hitelesítési kapcsolattartási adatai** naprakészek a többtényezős hitelesítési vagy feltételes hozzáférési házirendek érvényesítéséhez.

-   Győződjön meg róla, hogy megpróbálja törölni a böngésző cookie-kat, és megpróbál újra bejelentkezni.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>A Hozzáférési panel böngészőkövetelményeinek teljesítése

A hozzáférési panelhez olyan böngészőre van szükség, amely támogatja a JavaScriptet, és engedélyezve van a CSS. A hozzáférési panelen a jelszóalapú egyszeri bejelentkezés (SSO) használatához a hozzáférési panel bővítményt telepíteni kell a felhasználó böngészőjében. Ez a bővítmény automatikusan letöltődik, amikor a felhasználó olyan alkalmazást választ, amely jelszóalapú egyszeri bejelentkezésre van konfigurálva.

A jelszóalapú egyszeri bejelentkezés esetén a végfelhasználó böngészői a következők lehetnek:

-   Internet Explorer 8, 9, 10, 11 – Windows 7 vagy újabb rendszeren

-   Microsoft Edge windows 10-es évfordulós kiadásban vagy újabb verzióban 

-   Chrome – Windows 7 vagy újabb rendszeren, valamint MacOS X-en vagy újabb rendszeren

-   Firefox 26.0 vagy újabb verzió – Windows XP SP2 vagy újabb, valamint Mac OS X 10.6-os vagy újabb rendszeren


## <a name="problems-with-the-users-account"></a>Problémák a felhasználói fiókkal

A hozzáférési panelhez való hozzáférés a felhasználó fiókjával kapcsolatos probléma miatt letiltható. Az alábbiakban néhány módszert olvashat a felhasználókkal és a fiókbeállításaikkal kapcsolatos problémák elhárítására és megoldására:

-   [Annak ellenőrzése, hogy létezik-e felhasználói fiók az Azure Active Directoryban](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Felhasználó fiókállapotának ellenőrzése](#check-a-users-account-status)

-   [Felhasználó jelszavának alaphelyzetbe állítása](#reset-a-users-password)

-   [Önkiszolgáló jelszóátállítás engedélyezése](#enable-self-service-password-reset)

-   [A felhasználó többtényezős hitelesítési állapotának ellenőrzése](#check-a-users-multi-factor-authentication-status)

-   [A felhasználó hitelesítési kapcsolattartási adatainak ellenőrzése](#check-a-users-authentication-contact-info)

-   [Felhasználó csoporttagságának ellenőrzése](#check-a-users-group-memberships)

-   [A felhasználó hozzárendelt licenceinek ellenőrzése](#check-a-users-assigned-licenses)

-   [Felhasználó licenchozzárendelése](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Annak ellenőrzése, hogy létezik-e felhasználói fiók az Azure Active Directoryban

Annak ellenőrzéséhez, hogy a felhasználói fiók jelen van-e, kövesse az alábbi lépéseket:

1.  Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson a navigációs menü **Felhasználók és csoportok** parancsára.

5.  kattintson **a Minden felhasználó gombra.**

6.  **Keresse meg** az önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  Ellenőrizze a felhasználói objektum tulajdonságait, hogy megbizonyosodjon arról, hogy a várt nak tűnnek, és nincsenek adatok.

### <a name="check-a-users-account-status"></a>Felhasználó fiókállapotának ellenőrzése

A felhasználó fiókállapotának ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson a navigációs menü **Felhasználók és csoportok** parancsára.

5.  kattintson **a Minden felhasználó gombra.**

6.  **Keresse meg** az önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  kattintson a **Profil gombra.**

8.  A **Beállítások** csoportban győződjön meg arról, hogy **a Bejelentkezés blokkolása** **nem**.

### <a name="reset-a-users-password"></a>Felhasználó jelszavának alaphelyzetbe állítása

A felhasználó jelszavának alaphelyzetbe állításához kövesse az alábbi lépéseket:

1.  Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson a navigációs menü **Felhasználók és csoportok** parancsára.

5.  kattintson **a Minden felhasználó gombra.**

6.  **Keresse meg** az önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  kattintson a Felhasználó ablaktábla tetején található **Jelszó alaphelyzetbe állítása** gombra.

8.  kattintson a megjelenő **Jelszó alaphelyzetbe állítása** ablaktáblán a **Jelszó alaphelyzetbe állítása** gombra.

9.  Másolja az **ideiglenes jelszót,** vagy **adjon meg egy új jelszót** a felhasználónak.

10. Közölje ezt az új jelszót a felhasználóval, akkor módosítaniuk kell ezt a jelszót a következő bejelentkezés során az Azure Active Directoryba.

### <a name="enable-self-service-password-reset"></a>Új jelszó önkiszolgáló kérésének engedélyezése

Az önkiszolgáló jelszó-alaphelyzetbe állítás engedélyezéséhez kövesse az alábbi telepítési lépéseket:

-   [Az Azure Active Directory-jelszavak alaphelyzetbe állításának engedélyezése a felhasználók számára](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [A felhasználók számára az Active Directory helyszíni jelszavainak alaphelyzetbe állítása vagy módosítása](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>A felhasználó többtényezős hitelesítési állapotának ellenőrzése

A felhasználó többtényezős hitelesítési állapotának ellenőrzéséhez hajtsa végre az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson a navigációs menü **Felhasználók és csoportok** parancsára.

5. kattintson **a Minden felhasználó gombra.**

6. kattintson a **többtényezős hitelesítés** gombra az ablaktábla tetején.

7. A **többtényezős hitelesítésfelügyeleti portál** betöltése után győződjön meg arról, hogy a **Felhasználók** lapon van.

8. Kereséssel, szűréssel vagy rendezéssel keresheti meg a felhasználót a felhasználók listájában.

9. Válassza ki a felhasználót a felhasználók listájából, és **a Felhasználók engedélyezése**, **letiltása**vagy a többtényezős hitelesítés **kényszerítése** kívánt módon.

   >[!NOTE]
   >Ha egy felhasználó **kényszerített** állapotban van, ideiglenesen **letiltva** állíthatja be őket, hogy visszaengedje őket a fiókjába. Miután visszatértek, módosíthatja állapotukat **újra engedélyezve,** hogy a következő bejelentkezéskor újra regisztrálniuk kell a kapcsolattartási adataikat. Másik lehetőségként a [Felhasználó hitelesítési kapcsolattartási adatainak ellenőrzése](#check-a-users-authentication-contact-info) című részben leírt lépéseket is követheti az adatok ellenőrzéséhez vagy beállításához.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>A felhasználó hitelesítési kapcsolattartási adatainak ellenőrzése

A többtényezős hitelesítéshez, feltételes hozzáféréshez, identitásvédelemhez és jelszó-visszaállításhoz használt hitelesítési kapcsolattartási adatok ellenőrzéséhez hajtsa végre az alábbi lépéseket:

1.  Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson a navigációs menü **Felhasználók és csoportok** parancsára.

5.  kattintson **a Minden felhasználó gombra.**

6.  **Keresse meg** az önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  kattintson a **Profil gombra.**

8.  Görgessen le a **Hitelesítési kapcsolattartási adatok**hoz.

9.  **Tekintse át** a felhasználó számára regisztrált adatokat, és szükség szerint frissítse.

### <a name="check-a-users-group-memberships"></a>Felhasználó csoporttagságának ellenőrzése

A felhasználó csoporttagságának ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson a navigációs menü **Felhasználók és csoportok** parancsára.

5.  kattintson **a Minden felhasználó gombra.**

6.  **Keresse meg** az önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  **Kattintson a Csoportok** elemre, ha meg szeretné tekinteni, hogy a felhasználó melyik csoport tagja.

### <a name="check-a-users-assigned-licenses"></a>A felhasználó hozzárendelt licenceinek ellenőrzése

A felhasználó hozzárendelt licenceinek ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson a navigációs menü **Felhasználók és csoportok** parancsára.

5.  kattintson **a Minden felhasználó gombra.**

6.  **Keresse meg** az önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  Kattintson **a Licencek** elemre, ha meg szeretné tekinteni, hogy a felhasználó jelenleg mely licenceket rendelte hozzá.

### <a name="assign-a-user-a-license"></a>Felhasználó licenchozzárendelése 

Ha licencet szeretne hozzárendelni egy felhasználóhoz, kövesse az alábbi lépéseket:

1.  Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson a navigációs menü **Felhasználók és csoportok** parancsára.

5.  kattintson **a Minden felhasználó gombra.**

6.  **Keresse meg** az önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  Kattintson **a Licencek** elemre, ha meg szeretné tekinteni, hogy a felhasználó jelenleg mely licenceket rendelte hozzá.

8.  kattintson a **Hozzárendelés** gombra.

9.  Válasszon ki **egy vagy több terméket** az elérhető termékek listájából.

10. **Nem kötelező,** hogy a termékek részletes hozzárendeléséhez kattintson a **hozzárendelési beállítások** elemre. Ha ennek befejeződött, kattintson az **Ok** gombra.

11. A **Hozzárendelés** gombra kattintva rendelje hozzá ezeket a licenceket a felhasználóhoz.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Ha ezek a hibaelhárítási lépések nem oldják meg a problémát

nyisson meg egy támogatási jegyet a következő információkkal, ha azok rendelkezésre állnak:

-   Korrelációs hiba azonosítója

-   UPN (felhasználói e-mail cím)

-   Bérlőazonosító

-   Böngésző típusa

-   Időzóna és idő/időkeret hiba esetén

-   Hegedűs nyomok

## <a name="next-steps"></a>További lépések
[Egyszeri bejelentkezés az alkalmazásokba alkalmazásproxyval](application-proxy-configure-single-sign-on-with-kcd.md)
