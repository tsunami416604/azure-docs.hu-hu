---
title: Problémák a Microsoft-alkalmazásokba való bejelentkezéssel kapcsolatban | Microsoft dokumentumok
description: A külső Microsoft-alkalmazásokba az Azure AD használatával (például az Office 365-ben) való bejelentkezéskor felmerülő gyakori problémák elhárítása
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
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ee8802aeb2a760e255ab4f5e99010dfedc45e0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67108303"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Problémák a Microsoft-alkalmazásokba való bejelentkezéssel

A Microsoft-alkalmazások (például az Office 365 Exchange, a SharePoint, a Yammer stb.) hozzárendelése és kezelése kicsit eltérő, mint a harmadik féltől származó SaaS-alkalmazások vagy az Azure AD-vel egyetlen bejelentkezéshez integrálott alkalmazások.

A felhasználók három fő módon férhetnek hozzá egy Microsoft által közzétett alkalmazáshoz.

-   Az Office 365-ben vagy más fizetős csomagokban található alkalmazások esetében a felhasználók **licenc-hozzárendeléssel** vagy közvetlenül a felhasználói fiókjukhoz, vagy a csoportalapú licenckiosztási képességünket használó csoporton keresztül kapnak hozzáférést.

-   A Microsoft vagy egy harmadik fél által bárki számára szabadon közzétett alkalmazások esetében a felhasználók **felhasználói hozzájárulással**kaphatnak hozzáférést. Ez azt jelenti, hogy az Azure AD Work vagy school fiókjukkal jelentkeznek be az alkalmazásba, és lehetővé teszik számára, hogy hozzáférjen a fiókjukon lévő bizonyos korlátozott adathalmazhoz.

-   A Microsoft vagy egy harmadik fél által bárki számára szabadon közzétett alkalmazások esetében a felhasználók **rendszergazdai hozzájárulással**is hozzáférhetnek. Ez azt jelenti, hogy a rendszergazda megállapította, hogy az alkalmazást a szervezet minden tagja használhat, ezért globális rendszergazdai fiókkal jelentkeznek be az alkalmazásba, és hozzáférést biztosítanak a szervezet minden tagjának.

A probléma elhárításához kezdje az [alkalmazáshozzáféréssel rendelkező általános problémás területek](#general-problem-areas-with-application-access-to-consider) et, majd olvassa el a Forgatókönyv: A Microsoft-alkalmazások hozzáférésének elhárításának lépéseit a részletek megismeréséhez.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Általános problémás területek alkalmazás-hozzáféréssel, hogy fontolja meg

Az alábbiakban felsoroljuk azokat az általános problémás területeket, amelyeket részletezhet, ha van ötlete, hogy hol kezdje, de javasoljuk, hogy olvassa el a forgatókönyvet a gyors induláshoz: Forgatókönyv: A Microsoft-alkalmazások elérésének elhárításának lépései.

-   [Problémák a felhasználói fiókkal](#problems-with-the-users-account)

-   [Problémák a csoportokkal](#problems-with-groups)

-   [Problémák a feltételes hozzáférési házirendekkel](#problems-with-conditional-access-policies)

-   [Problémák az alkalmazás hozzájárulásával](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>A Microsoft Application access hibaelhárításának lépései

Az alábbiakban felkell írni néhány gyakori problémát, amelybe az emberek befutnak, amikor a felhasználók nem tudnak bejelentkezni egy Microsoft-alkalmazásba.

- Általános kérdések, amelyeket először ellenőrizni kell

  * Győződjön meg arról, hogy a felhasználó a **megfelelő URL-címre** jelentkezik be, és nem a helyi alkalmazás URL-címére.

  * Győződjön meg arról, hogy a felhasználó fiókja **nincs zárolva.**

  * Győződjön meg arról, hogy a **felhasználó fiókja létezik** az Azure Active Directoryban. [Annak ellenőrzése, hogy létezik-e felhasználói fiók az Azure Active Directoryban](#problems-with-the-users-account)

  * Ellenőrizze, hogy a felhasználó fiókja **engedélyezve** [Check a user’s account status](#problems-with-the-users-account) van-e a bejelentkezéshez.

  * Győződjön meg arról, hogy a felhasználó **jelszava nem járt le vagy nem feledje el.** [Felhasználó jelszavának alaphelyzetbe állítása](#reset-a-users-password) vagy [az önkiszolgáló jelszó alaphelyzetbe állításának engedélyezése](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

  * Győződjön meg arról, hogy a **többtényezős hitelesítés** nem blokkolja a felhasználói hozzáférést. [A felhasználó többtényezős hitelesítési állapotának ellenőrzése](#check-a-users-multi-factor-authentication-status) vagy [a felhasználó hitelesítési kapcsolattartási adatainak ellenőrzése](#check-a-users-authentication-contact-info)

  * Győződjön meg arról, hogy a **feltételes hozzáférési szabályzat** vagy az **identitásvédelmi** házirend nem blokkolja a felhasználói hozzáférést. [Adott feltételes hozzáférési házirend ellenőrzése](#problems-with-conditional-access-policies) vagy egy adott alkalmazás feltételes hozzáférési [házirendjének ellenőrzése](#check-a-specific-applications-conditional-access-policy) vagy egy adott feltételes hozzáférési házirend [letiltása](#disable-a-specific-conditional-access-policy)

  * Győződjön meg arról, hogy a felhasználó **hitelesítési kapcsolattartási adatai** naprakészek a többtényezős hitelesítési vagy feltételes hozzáférési házirendek érvényesítéséhez. [A felhasználó többtényezős hitelesítési állapotának ellenőrzése](#check-a-users-multi-factor-authentication-status) vagy [a felhasználó hitelesítési kapcsolattartási adatainak ellenőrzése](#check-a-users-authentication-contact-info)

- A **licencet igénylő Microsoft-alkalmazások** (például az Office365) esetében az alábbiakat az alábbiakban ellenőrizendő néhány konkrét probléma merül fel, amelyeket a fenti általános problémák kizárása után ellenőrizni kell: **Microsoft**

  * Győződjön meg arról, hogy a felhasználó vagy **licenc van hozzárendelve.** [A felhasználó hozzárendelt licenceinek ellenőrzése](#check-a-users-assigned-licenses) vagy [a csoport hozzárendelt licenceinek ellenőrzése](#check-a-groups-assigned-licenses)

  * Ha a licenc **statikus csoporthoz**van **rendelve,** győződjön meg arról, hogy a felhasználó a csoport **tagja.** [Felhasználó csoporttagságának ellenőrzése](#check-a-users-group-memberships)

  * Ha a licenc **dinamikus csoporthoz**van **rendelve** , győződjön meg arról, hogy a **dinamikus csoportszabály megfelelően van beállítva.** [Dinamikus csoport tagsági feltételeinek ellenőrzése](#check-a-dynamic-groups-membership-criteria)

  * Ha a licenc **dinamikus csoporthoz**van **rendelve,** győződjön meg arról, hogy a dinamikus csoport befejezte a tagság **feldolgozását,** és hogy a **felhasználó tag (ez** eltarthat egy ideig). [Felhasználó csoporttagságának ellenőrzése](#check-a-users-group-memberships)

  *  Miután meggyőződött arról, hogy a licenc hozzá van rendelve, győződjön meg arról, hogy a licenc **nem járt le.**

  *  Győződjön meg arról, hogy a licenc az általuk elérhető **alkalmazáshoz** kapcsolódik.

- A **licencet nem igénylő Microsoft-alkalmazások**esetében az alábbiakat kell ellenőriznie: **Microsoft**

  * Ha az alkalmazás **felhasználói szintű engedélyeket** kér (például "A felhasználó postaládájának elérése"), győződjön meg arról, hogy a felhasználó bejelentkezett az alkalmazásba, és **felhasználói szintű jóváhagyási műveletet** hajtott végre, hogy az alkalmazás hozzáférhessen az adataikhoz.

  * Ha az alkalmazás **rendszergazdai szintű engedélyeket** kér (például "Hozzáférés a felhasználó összes postaládájához"), győződjön meg arról, hogy a globális rendszergazda rendszergazdai szintű jóváhagyási műveletet hajtott végre a szervezet **összes felhasználója nevében.**

## <a name="problems-with-the-users-account"></a>Problémák a felhasználói fiókkal

Az alkalmazáshozzáférés letiltható az alkalmazáshoz rendelt felhasználóval kapcsolatos probléma miatt. Az alábbiakban néhány módszert olvashat a felhasználókkal és a fiókbeállításaikkal kapcsolatos problémák elhárítására és megoldására:

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

10. Közölje ezt az új jelszót a felhasználóval, akkor módosítania kell ezt a jelszót a következő bejelentkezés során az Azure Active Directoryba.

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

7. Miután a **többtényezős hitelesítésfelügyeleti portál** betöltődik, győződjön meg arról, hogy a **Felhasználók** lapon van.

8. Kereséssel, szűréssel vagy rendezéssel keresheti meg a felhasználót a felhasználók listájában.

9. Válassza ki a felhasználót a felhasználók listájából, és **a Felhasználók engedélyezése**, **letiltása**vagy a többtényezős hitelesítés **kényszerítése** kívánt módon.

   * **Megjegyzés:** Ha egy felhasználó **kényszerített** állapotban van, ideiglenesen **letiltva** értékre állíthatja, hogy visszaengedje őket a fiókjába. Miután visszatértek, módosíthatja állapotukat **újra Engedélyezve** állapotra, hogy a következő bejelentkezéskor újra regisztrálják kapcsolattartási adataikat. Másik lehetőségként a [Felhasználó hitelesítési kapcsolattartási adatainak ellenőrzése](#check-a-users-authentication-contact-info) című részben leírt lépéseket is követheti az adatok ellenőrzéséhez vagy beállításához.

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

## <a name="problems-with-groups"></a>Problémák a csoportokkal

Az alkalmazáshozzáférés letiltható az alkalmazáshoz rendelt csoporttal kapcsolatos probléma miatt. Az alábbiakban a csoportokkal és csoporttagságokkal kapcsolatos problémák elhárítására és megoldására is fellehet váltani néhány módszert:

-   [Csoport tagságának ellenőrzése](#check-a-groups-membership)

-   [Dinamikus csoport tagsági feltételeinek ellenőrzése](#check-a-dynamic-groups-membership-criteria)

-   [Csoport hozzárendelt licenceinek ellenőrzése](#check-a-groups-assigned-licenses)

-   [Csoport licenceinek újrafeldolgozása](#reprocess-a-groups-licenses)

-   [Csoport licenc hozzárendelése](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Csoport tagságának ellenőrzése

A csoport tagságának ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson a navigációs menü **Felhasználók és csoportok** parancsára.

5.  kattintson **a Minden csoport gombra.**

6.  **Keresse meg** az önt érdeklő csoportot, és **kattintson a** kijelölni kívánt sorra.

7.  A **Tagok** elemre kattintva tekintse át a csoporthoz rendelt felhasználók listáját.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Dinamikus csoport tagsági feltételeinek ellenőrzése 

A dinamikus csoportok tagsági feltételeinek ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson a navigációs menü **Felhasználók és csoportok** parancsára.

5.  kattintson **a Minden csoport gombra.**

6.  **Keresse meg** az önt érdeklő csoportot, és **kattintson a** kijelölni kívánt sorra.

7.  Kattintson **a Dinamikus tagsági szabályok elemre.**

8.  Tekintse át a csoporthoz definiált **egyszerű** vagy **speciális** szabályt, és győződjön meg arról, hogy a csoport ban tagságot használó felhasználó megfelel ezeknek a feltételeknek.

### <a name="check-a-groups-assigned-licenses"></a>Csoport hozzárendelt licenceinek ellenőrzése

A csoport hozzárendelt licenceinek ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson a navigációs menü **Felhasználók és csoportok** parancsára.

5.  kattintson **a Minden csoport gombra.**

6.  **Keresse meg** az önt érdeklő csoportot, és **kattintson a** kijelölni kívánt sorra.

7.  **Kattintson a Licencek** elemre, ha meg szeretné tekinteni, hogy a csoport jelenleg mely licenceket rendelte hozzá.

### <a name="reprocess-a-groups-licenses"></a>Csoport licenceinek újrafeldolgozása

A csoport hozzárendelt licenceinek újbóli feldolgozásához hajtsa végre az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson a navigációs menü **Felhasználók és csoportok** parancsára.

5. kattintson **a Minden csoport gombra.**

6. **Keresse meg** az önt érdeklő csoportot, és **kattintson a** kijelölni kívánt sorra.

7. **Kattintson a Licencek** elemre, ha meg szeretné tekinteni, hogy a csoport jelenleg mely licenceket rendelte hozzá.

8. kattintson az **Újrafeldolgozás** gombra annak biztosításához, hogy a csoport tagjaihoz rendelt licencek naprakészek legyenek. Ez hosszú időt vehet igénybe, a csoport méretétől és összetettségétől függően.

   >[!NOTE]
   >A gyorsabb, fontolja meg egy licenc ideiglenes hozzárendelése a felhasználó közvetlenül. [Rendeljen egy felhasználóhoz licencet.](#problems-with-application-consent)
   >
   >

### <a name="assign-a-group-a-license"></a>Csoport licenc hozzárendelése

Ha licencet szeretne hozzárendelni egy csoporthoz, kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson a navigációs menü **Felhasználók és csoportok** parancsára.

5. kattintson **a Minden csoport gombra.**

6. **Keresse meg** az önt érdeklő csoportot, és **kattintson a** kijelölni kívánt sorra.

7. **Kattintson a Licencek** elemre, ha meg szeretné tekinteni, hogy a csoport jelenleg mely licenceket rendelte hozzá.

8. kattintson a **Hozzárendelés** gombra.

9. Válasszon ki **egy vagy több terméket** az elérhető termékek listájából.

10. **Nem kötelező,** hogy a termékek részletes hozzárendeléséhez kattintson a **hozzárendelési beállítások** elemre. Ha ennek befejeződött, kattintson az **Ok** gombra.

11. A **Hozzárendelés** gombra kattintva rendelje hozzá ezeket a licenceket ehhez a csoporthoz. Ez hosszú időt vehet igénybe, a csoport méretétől és összetettségétől függően.

    >[!NOTE]
    >A gyorsabb, fontolja meg egy licenc ideiglenes hozzárendelése a felhasználó közvetlenül. [Rendeljen egy felhasználóhoz licencet.](#problems-with-application-consent)
    > 
    >

## <a name="problems-with-conditional-access-policies"></a>Problémák a feltételes hozzáférési házirendekkel

### <a name="check-a-specific-conditional-access-policy"></a>Adott feltételes hozzáférési házirend ellenőrzése

Egyetlen feltételes hozzáférési házirend ellenőrzése vagy ellenőrzése:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. Kattintson a navigációs menü **Vállalati alkalmazások parancsára.**

5. kattintson a **Feltételes hozzáférés** navigációs elemre.

6. kattintson az ellenőrzés iránt érdeklődő házirendre.

7. Tekintse át, hogy nincsenek-e olyan konkrét feltételek, hozzárendelések vagy egyéb beállítások, amelyek letiltanák a felhasználói hozzáférést.

   >[!NOTE]
   >Előfordulhat, hogy ideiglenesen le szeretné tiltani ezt a házirendet, hogy megbizonyosodjon arról, hogy az nem befolyásolja a bejelentkezéseket. Ehhez állítsa a **Házirend engedélyezése** kapcsolót **Nem-re,** és kattintson a **Mentés** gombra.
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Adott alkalmazás feltételes hozzáférési házirendjének ellenőrzése

Egyetlen alkalmazás jelenleg konfigurált feltételes hozzáférési házirendjének ellenőrzése vagy ellenőrzése:

1.  Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  Kattintson a navigációs menü **Vállalati alkalmazások parancsára.**

5.  kattintson **a Minden alkalmazás elemre.**

6.  Keresse meg az Önt érdeklő alkalmazást, vagy a felhasználó az alkalmazás megjelenítendő neve vagy alkalmazásazonosítója alapján próbál bejelentkezni.

     >[!NOTE]
     >Ha nem látja a keresett alkalmazást, kattintson a **Szűrő** gombra, és bontsa ki a lista hatókörét az **Összes alkalmazás elemre.** Ha további oszlopokat szeretne látni, kattintson az **Oszlopok** gombra az alkalmazások további részleteinek hozzáadásához.
     >
     >

7.  kattintson a **Feltételes hozzáférés** navigációs elemre.

8.  kattintson az ellenőrzés iránt érdeklődő házirendre.

9.  Tekintse át, hogy nincsenek-e olyan konkrét feltételek, hozzárendelések vagy egyéb beállítások, amelyek letiltanák a felhasználói hozzáférést.

     >[!NOTE]
     >Előfordulhat, hogy ideiglenesen le szeretné tiltani ezt a házirendet, hogy megbizonyosodjon arról, hogy az nem befolyásolja a bejelentkezéseket. Ehhez állítsa a **Házirend engedélyezése** kapcsolót **Nem-re,** és kattintson a **Mentés** gombra.
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Adott feltételes hozzáférési házirend letiltása

Egyetlen feltételes hozzáférési házirend ellenőrzése vagy ellenőrzése:

1.  Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  Kattintson a navigációs menü **Vállalati alkalmazások parancsára.**

5.  kattintson a **Feltételes hozzáférés** navigációs elemre.

6.  kattintson az ellenőrzés iránt érdeklődő házirendre.

7.  Tiltsa le a házirendet úgy, hogy a **Házirend engedélyezése** beállítást **nemre** állítja, és a **Mentés gombra** kattint.

## <a name="problems-with-application-consent"></a>Problémák az alkalmazás hozzájárulásával

Az alkalmazáshozzáférés letiltható, mert a megfelelő engedély-jóváhagyási művelet nem történt meg. Az alábbiakban néhány módszert ismerünk el az alkalmazás-jóváhagyási problémák elhárítására és megoldására:

-   [Felhasználói szintű jóváhagyási művelet végrehajtása](#perform-a-user-level-consent-operation)

-   [Rendszergazdai szintű hozzájárulási művelet végrehajtása bármely alkalmazáson](#perform-administrator-level-consent-operation-for-any-application)

-   [Rendszergazdai szintű hozzájárulás végrehajtása egybérlős alkalmazáshoz](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Rendszergazdai szintű hozzájárulás végrehajtása több-bérlős alkalmazáshoz](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Felhasználói szintű jóváhagyási művelet végrehajtása

-   Minden engedélykéréssel rendelkező Open ID Connect-kompatibilis alkalmazás esetén az alkalmazás bejelentkezési képernyőjére való navigálás felhasználói szintű jóváhagyást ad a bejelentkezett felhasználó alkalmazásához.

-   Ha ezt programozott módon szeretné megtenni, olvassa el [az Egyéni felhasználó hozzájárulásának kérése (Requesting individual user consent) (Egyéni felhasználó beleegyezésének kérése) (Egyéni felhasználó beleegyezésének kérése) ( Egyéni felhasználó beleegyezésének kérése) ( Egyéni felhasználó beleegyezésének kérése)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent)( Egyéni

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Rendszergazdai szintű hozzájárulási művelet végrehajtása bármely alkalmazáson

-   **Csak a V1 alkalmazásmodell használatával kifejlesztett alkalmazások**esetében kényszerítheti ezt a rendszergazdai szintű beleegyezést az alkalmazás bejelentkezési URL-címének befejezéséhez való hozzáadásával.**\_**

-   **A V2 alkalmazásmodell használatával kifejlesztett alkalmazások**esetében ezt a rendszergazdai szintű hozzájárulást a [Rendszergazdai hozzájárulás végpont használata](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)című **címtár-rendszergazdai** szakasz utasításainak követésével kényszerítheti.

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Rendszergazdai szintű hozzájárulás végrehajtása egybérlős alkalmazáshoz

-   Az engedélyeket kérő **egybérlős alkalmazások** (például a szervezetben fejlesztvagy saját felhasználók) esetében az összes felhasználó nevében **rendszergazdai szintű jóváhagyási** műveletet hajthat végre, ha globális rendszergazdaként bejelentkezik, és az **Alkalmazásbeállítás** - **&gt; Minden alkalmazás – Alkalmazás kiválasztása&gt; –&gt; Szükséges engedélyek** ablaktábla tetején található Engedélyek megadása gombra kattint.

-   **A V1 vagy V2 alkalmazásmodell használatával kifejlesztett alkalmazások**esetében ezt a rendszergazdai szintű hozzájárulást a [Rendszergazdai hozzájárulásvégpont használata](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)című **címtár-rendszergazdai** engedélykérése szakaszutasításait követve kényszerítheti ki.

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Rendszergazdai szintű hozzájárulás végrehajtása több-bérlős alkalmazáshoz

-   Engedélyeket kérő **több-bérlős alkalmazások** (például egy harmadik fél vagy a Microsoft fejlesztése) esetében **felügyeleti szintű jóváhagyási** műveletet hajthat végre. Jelentkezzen be globális rendszergazdaként, és kattintson az **Engedélyek megadása** gombra a Vállalati alkalmazások **–&gt; Minden alkalmazás –&gt; Alkalmazás kiválasztása –&gt; Engedélyek** ablaktábla alatt (hamarosan elérhető).

-   Ezt a rendszergazdai szintű hozzájárulást úgy is kényszerítheti, hogy kövesse a Rendszergazda idiktaengedélyeinek kérése a [rendszergazdai hozzájárulásvégpont használata](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint) **címtáradminisztrátori** szakasza című utasításait.

## <a name="next-steps"></a>További lépések
[A rendszergazdai hozzájárulási végpont használata](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)

