---
title: A jelszó egyszeri bejelentkezése az Azure AD-alkalmazásokhoz | Microsoft dokumentumok
description: Jelszó egyszeri bejelentkezés (SSO) beállítása az Azure AD vállalati alkalmazásokhoz a Microsoft identity platformon (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563bda275b73f76b042b5e57a9909ca78c504bb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063526"
---
# <a name="configure-password-single-sign-on"></a>Jelszó egyszeri bejelentkezéskonfigurálása

Amikor [hozzáad egy katalógusalkalmazást](add-gallery-app.md) vagy egy [nem galéria alapú webalkalmazást](add-non-gallery-app.md) az Azure AD Enterprise Applications alkalmazáshoz, az egyetlen bejelentkezési lehetőségek egyike a [jelszóalapú egyszeri bejelentkezés.](what-is-single-sign-on.md#password-based-sso) Ez a beállítás minden HTML-bejelentkezési lappal rendelkező webhelyen elérhető. Jelszóalapú egyszeri bejelentkezés, más néven jelszó-tároló, lehetővé teszi a felhasználói hozzáférés és jelszavak kezelését olyan webalkalmazásokhoz, amelyek nem támogatják az identitás-összevonást. Ez olyan esetekben is hasznos, amikor több felhasználónak kell megosztania egyetlen fiókot, például a szervezet közösségi média alkalmazásfiókjaival. 

A jelszóalapú egyszeri bejelentkezés segítségével gyorsan elkezdheti integrálni az alkalmazásokat az Azure AD-be, és lehetővé teszi a következőket:

-   Egyszeri bejelentkezés engedélyezése a felhasználók számára az Azure **AD-vel** integrált alkalmazás felhasználónevének és jelszavának biztonságos tárolásával és visszajátszásával

-   **Olyan alkalmazások támogatása, amelyek több bejelentkezési mezőt igényelnek** olyan alkalmazásokhoz, amelyek nem csak felhasználónév- és jelszómezőket igényelnek a bejelentkezéshez

-   A felhasználók által az [Alkalmazás-hozzáférési panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) a hitelesítő adatok megadásakor a felhasználónév- és jelszóbeviteli mezők **címkéinek testreszabása**

-   Annak engedélyezése, hogy a **felhasználók** saját felhasználónevüket és jelszavukat adják meg az [alkalmazáshoz való hozzáférés panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) beírt meglévő alkalmazásfiókokhoz.

-   Az **üzleti csoport egy tagjának** engedélyezheti, hogy az [Önkiszolgáló alkalmazáselérés](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) szolgáltatás használatával adja meg a felhasználóhoz rendelt felhasználóneveket és jelszavakat

-   A **hitelesítő** adatok frissítése szolgáltatással a rendszergazda megadhatja azt a felhasználónevet és jelszót, amelyet személyek vagy csoportok használnak az alkalmazásba való bejelentkezéskor. 

## <a name="before-you-begin"></a>Előkészületek

Ha az alkalmazás még nem lett hozzáadva az Azure AD-bérlőhöz, olvassa el [a Katalógusalkalmazás hozzáadása](add-gallery-app.md) vagy a Nem galériaalkalmazás hozzáadása című [témakört.](add-non-gallery-app.md)

## <a name="open-the-app-and-select-password-single-sign-on"></a>Az alkalmazás megnyitása és a jelszó egyszeri bejelentkezésének kiválasztása

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) egy felhőalapú alkalmazás-rendszergazdaként, vagy egy alkalmazás-rendszergazda az Azure AD-bérlő.

2. Nyissa meg az **Azure Active Directory** > **Enterprise alkalmazásokat.** Az Azure AD-bérlőben lévő alkalmazások véletlenszerű mintája jelenik meg. 

3. Az **Alkalmazás típusa** menüben válassza a **Minden alkalmazás**lehetőséget, majd az **Alkalmaz parancsot.**

4. Írja be az alkalmazás nevét a keresőmezőbe, majd válassza ki az alkalmazást az eredmények közül.

5. A **Kezelés csoportban** válassza az **Egyszeri bejelentkezés**lehetőséget. 

6. Válassza **a Jelszó alapú lehetőséget.**

7. Adja meg az alkalmazás webalapú bejelentkezési lapjának URL-címét. Ennek a karakterláncnak a felhasználónév beviteli mezőjét tartalmazó lapnak kell lennie.

   ![Jelszóalapú egyszeri bejelentkezés](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. Kattintson a **Mentés** gombra. Az Azure AD megpróbálja elemezni a bejelentkezési lapot egy felhasználónév-bemenethez és egy jelszóbemenethez. Ha a kísérlet sikerrel jár, véged. 
 
> [!NOTE]
> A következő lépés a [felhasználók vagy csoportok hozzárendelése az alkalmazáshoz.](methods-for-assigning-users-and-groups.md) Miután hozzárendelte a felhasználókat és csoportokat, megadhat hitelesítő adatokat, amelyeket a felhasználó nevében használhat, amikor bejelentkeznek az alkalmazásba. Jelölje be a **Felhasználók és csoportok**jelölőnégyzetet a felhasználó vagy a csoport sorában, majd kattintson a Hitelesítő adatok frissítése **gombra.** Ezután adja meg a felhasználó vagy csoport nevében használandó felhasználónevet és jelszót. Ellenkező esetben a rendszer felszólítja a felhasználókat, hogy az indításkor maguk adják meg a hitelesítő adatokat.
 

## <a name="manual-configuration"></a>Manuális konfigurálás

Ha az Azure AD elemzési kísérlet sikertelen, manuálisan konfigurálhatja a bejelentkezést.

1. Az ** \<alkalmazásnév> Konfiguráció**csoportban válassza az **Alkalmazásnév konfigurálása> Jelszó egyszeri bejelentkezési beállításai nak beállításához \<** lehetőséget a Bejelentkezés **konfigurálása** lap megjelenítéséhez. 

2. Jelölje **be a Bejelentkezési mezők manuális észlelése**jelölőnégyzetet. A bejelentkezési mezők manuális észlelését leíró további utasítások jelennek meg.

   ![A jelszóalapú egyszeri bejelentkezés manuális konfigurálása](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Válassza **a Bejelentkezési mezők rögzítése lehetőséget.** Egy új lapon megnyílik egy rögzítési állapotlap, amely azt mutatja, hogy az üzenet **metaadat-rögzítése folyamatban van.**

4. Ha a **Hozzáférési panel kiterjesztés kötelező mezője** egy új lapon jelenik meg, válassza a **Telepítés most** lehetőséget a My Apps **Secure Sign-in Extension** böngészőbővítmény telepítéséhez. (A böngészőbővítményhez Microsoft Edge, Chrome vagy Firefox szükséges.) Ezután telepítse, indítsa el és engedélyezze a bővítményt, és frissítse a rögzítési állapotlapot.

   A böngészőbővítmény ezután megnyit egy másik lapot, amely a megadott URL-címet jeleníti meg.
5. A beírt URL-címet bekövetkező lapon menjen végig a bejelentkezési folyamaton. Töltse ki a felhasználónév és a jelszó mezőket, és próbáljon meg bejelentkezni. (Nem kell megadnia a helyes jelszót.)

   A rendszer kéri a rögzített bejelentkezési mezők mentését.
6. Válassza **az OK gombot.** A böngészőbővítmény frissíti a rögzítésállapot-lapot azzal az üzenettel, hogy a **metaadatok frissültek az alkalmazáshoz.** A böngésző lap bezárul.

7. Az Azure AD **konfigurálása bejelentkezési** lapon válassza **az Ok lehetőséget, sikeresen be tudtak jelentkezni az alkalmazásba.**

8. Válassza **az OK gombot.**

A bejelentkezési lap rögzítése után felhasználókat és csoportokat rendelhet hozzá, és a hitelesítő adatokra vonatkozó házirendeket a hagyományos [jelszóegyszeri bejelentkezési alkalmazásokhoz](what-is-single-sign-on.md)hasonlóan állíthatja be.

> [!NOTE]
> Az alkalmazás hoz a Beállítás **lapon** található **Embléma feltöltése** gomb segítségével tölthet fel csempeemblémát az alkalmazáshoz.

## <a name="next-steps"></a>További lépések

- [Felhasználók vagy csoportok hozzárendelése az alkalmazáshoz](methods-for-assigning-users-and-groups.md)
- [A felhasználói fiókok automatikus kiépítésének konfigurálása](../app-provisioning/configure-automatic-user-provisioning-portal.md)
