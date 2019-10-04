---
title: Az Azure AD Gallery-alkalmazás jelszavas egyszeri bejelentkezésének konfigurálása | Microsoft Docs
description: Alkalmazás konfigurálása a biztonságos jelszó-alapú egyszeri bejelentkezéshez, ha az már szerepel az Azure AD Application Galleryben
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
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 9fb33c4110a590539c85364885da9a27853f6bd0
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146885"
---
# <a name="configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jelszavas egyszeri bejelentkezés konfigurálása Azure AD Gallery-alkalmazáshoz

Ha hozzáad egy alkalmazást az [Azure Active Directory (Azure ad) alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)-katalógusból, kiválaszthatja, hogyan szeretné bejelentkezni a felhasználók számára az alkalmazásba. Ezt a lehetőséget bármikor megadhatja, ha a [Azure Portal](https://portal.azure.com/)egy vállalati alkalmazásának **egyszeri bejelentkezés elemét** választja.

Az egyszeri bejelentkezés (SSO) egyik lehetséges beállítása a [jelszó alapú egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). Ez nagyszerű lehetőséget ad az alkalmazások Azure AD-be való integrálásának gyors megkezdésére. Ez a következőket teszi lehetővé:

-   Biztonságosan tárolja és visszajátssza az Azure AD-vel integrált alkalmazáshoz tartozó felhasználóneveket és jelszavakat

-   A csak a Felhasználónév és jelszó mezőkön túli több bejelentkezési mezőt igénylő alkalmazások támogatása

-   Lehetővé teszi a felhasználók által az [alkalmazás-hozzáférési panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) megjelenő felhasználónevek és jelszó mezők címkéjének testreszabását a hitelesítő adatok megadásakor

-   Lehetővé teszi a felhasználóknak saját felhasználónevek és jelszavak megadását minden olyan meglévő alkalmazás-fiókhoz, amelyet manuálisan adnak meg az [alkalmazás-hozzáférési panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Lehetővé teszi, hogy az üzleti csoport tagjai az [önkiszolgáló alkalmazás-hozzáférési](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) szolgáltatás használatával megadják a felhasználóhoz rendelt felhasználóneveket és jelszavakat

-   Lehetővé teszi a rendszergazda számára a felhasználóhoz rendelt felhasználónevek és jelszavak megadását a hitelesítő adatok frissítése funkció használatával [, amikor felhasználót rendel](#assign-a-user-to-an-application-directly) hozzá egy alkalmazáshoz

-   Lehetővé teszi, hogy a rendszergazda a hitelesítő adatok frissítése funkcióval megadhatja egy adott csoport megosztott felhasználónevét vagy jelszavát, amikor [csoportot rendel hozzá](#assign-an-application-to-a-group-directly) egy alkalmazáshoz.

A következő szakasz azt ismerteti, hogyan engedélyezheti a [jelszó-alapú egyszeri bejelentkezést](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) egy olyan alkalmazásra, amely már szerepel az [Azure ad Application Galleryben](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="overview-of-required-steps"></a>A szükséges lépések áttekintése
Egy alkalmazás Azure AD-katalógusból történő konfigurálásához a következőket kell tennie:

-   [Alkalmazás hozzáadása az Azure AD-katalógusból](#add-an-application-from-the-azure-ad-gallery)

-   [Az alkalmazás konfigurálása a jelszó egyszeri bejelentkezéséhez](#configure-the-application-for-password-single-sign-on)

-   Az alkalmazás társítása felhasználóhoz vagy csoporthoz:

    -   [Felhasználó közvetlen kiosztása egy alkalmazáshoz](#assign-a-user-to-an-application-directly)

    -   [Alkalmazás közvetlen társítása egy csoporthoz](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Alkalmazás hozzáadása az Azure AD-katalógusból

Ha alkalmazást szeretne hozzáadni az Azure AD-katalógusból, kövesse az alábbi lépéseket:

1.  Nyissa [](https://portal.azure.com)meg a Azure Portalt, és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali menü tetején található **összes szolgáltatás** kiválasztásával.

3.  Adja meg az **Azure Active Directoryt** a keresőmezőbe, majd válassza ki a **Azure Active Directory** elemet.

4.  A bal oldali Azure AD menüben válassza a **vállalati alkalmazások** lehetőséget.

5.  Válassza a **Hozzáadás** gombot a **vállalati alkalmazások** ablaktábla jobb felső sarkában.

6.  A **Hozzáadás a** katalógusból szakasz **név megadása** mezőjébe írja be az alkalmazás nevét.

7.  Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

8.  Az alkalmazás hozzáadása előtt módosíthatja a nevét a **név** mezőben.

9.  Az alkalmazás hozzáadásához válassza a **Hozzáadás** lehetőséget.

Rövid idő elteltével megtekintheti az alkalmazás konfigurációs paneljét.

## <a name="configure-the-application-for-password-single-sign-on"></a>Az alkalmazás konfigurálása a jelszó egyszeri bejelentkezéséhez

Az egyszeri bejelentkezés az alkalmazáshoz való konfigurálásához kövesse az alábbi lépéseket:

1. Nyissa meg a [Azure Portal](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali menü tetején található **összes szolgáltatás** kiválasztásával.

3. Adja meg az **Azure Active Directoryt** a keresőmezőbe, majd válassza ki a **Azure Active Directory** elemet.

4. Válassza ki a **vállalati alkalmazásokat** a bal oldali Azure Active Directory menüből.

5. A **minden alkalmazás** elemre kattintva megtekintheti az összes alkalmazás listáját.

   Ha nem látja a használni kívánt alkalmazást, használja a **szűrő** vezérlőelemet a **minden alkalmazás lista**tetején, és állítsa be a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra**.

6. Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

7. Az alkalmazás betöltése után válassza az **egyszeri bejelentkezés** lehetőséget a bal oldali alkalmazás menüjében.

8. Válassza a **jelszó alapú bejelentkezési** mód lehetőséget.

9. [Felhasználók kiosztása az alkalmazáshoz](#assign-a-user-to-an-application-directly).

10. A felhasználók nevében hitelesítő adatokat is megadhat, ha kiválasztja a felhasználó sorát, kiválasztja a **hitelesítő adatok frissítése**lehetőséget, majd beírja a felhasználónevet és a jelszót. Ellenkező esetben a felhasználóknak meg kell adniuk a hitelesítő adataikat, amikor elindítják az alkalmazást.

## <a name="assign-a-user-to-an-application-directly"></a>Felhasználó közvetlen kiosztása egy alkalmazáshoz

Ha egy vagy több felhasználót közvetlenül szeretne hozzárendelni egy alkalmazáshoz, kövesse az alábbi lépéseket:

1. Nyissa [](https://portal.azure.com/) meg a Azure Portalt, és jelentkezzen be **globális rendszergazdaként**.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali menü tetején található **összes szolgáltatás** kiválasztásával.

3. Adja meg az **Azure Active Directoryt** a keresőmezőbe, majd válassza ki a **Azure Active Directory** elemet.

4. Válassza ki a **vállalati alkalmazásokat** a bal oldali Azure Active Directory menüből.

5. A **minden alkalmazás** elemre kattintva megtekintheti az összes alkalmazás listáját.

   Ha nem látja a használni kívánt alkalmazást, használja a **szűrő** vezérlőelemet a **minden alkalmazás lista**tetején, és állítsa be a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra**.

6. Válassza ki azt az alkalmazást, amelyhez felhasználót szeretne hozzárendelni.

7. Az alkalmazás betöltése után a bal oldalon válassza a **felhasználók és csoportok** lehetőséget az alkalmazás menüjében.

8. Kattintson a **Hozzáadás** gombra a **felhasználók és csoportok** lista tetején a **hozzárendelés hozzáadása** ablaktábla megnyitásához.

9. Válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** panelen.

10. Adja meg a felhasználó teljes nevét vagy e-mail-címét a **Keresés név vagy e-mail-cím keresőmező alapján** .

11. Vigye a kurzort a listán szereplő felhasználó fölé, majd jelölje be a felhasználó profiljának fényképe vagy emblémája melletti jelölőnégyzetet a **kiválasztott** listához való hozzáadáshoz.

12. Nem kötelező: Ha egynél több felhasználót szeretne felvenni, adjon meg egy másik teljes nevet vagy e-mail-címet a **Keresés név vagy e-mail-cím** mezőbe, és jelölje be az adott felhasználóhoz tartozó jelölőnégyzetet a **kiválasztott** listához való hozzáadáshoz.

13. Ha végzett a felhasználók kiválasztásával, a **kiválasztás** gombbal adhatja hozzá őket az alkalmazáshoz hozzárendelni kívánt felhasználók és csoportok listájához.

14. Nem kötelező: A **hozzárendelés hozzáadása** panelen a **szerepkör kiválasztása** paranccsal kiválaszthatja a kiválasztott felhasználókhoz hozzárendelni kívánt szerepkört.

15. Válassza a **hozzárendelés** lehetőséget az alkalmazás a kiválasztott felhasználókhoz való hozzárendeléséhez.

## <a name="assign-an-application-to-a-group-directly"></a>Alkalmazás közvetlen társítása egy csoporthoz

Ha egy vagy több csoportot szeretne közvetlenül egy alkalmazáshoz rendelni, kövesse az alábbi lépéseket:

1. Nyissa [](https://portal.azure.com/) meg a Azure Portalt, és jelentkezzen be **globális rendszergazdaként**.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali menü tetején található **összes szolgáltatás** kiválasztásával.

3. Adja meg az **Azure Active Directoryt** a keresőmezőbe, majd válassza ki a **Azure Active Directory** elemet.

4. Válassza a bal oldali Azure AD menü **vállalati alkalmazások** elemét.

5. A **minden alkalmazás** elemre kattintva megtekintheti az összes alkalmazás listáját.

   Ha nem látja a használni kívánt alkalmazást, használja a **szűrő** vezérlőelemet a **minden alkalmazás lista** tetején, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra**.

6. Válassza ki azt az alkalmazást, amelyhez felhasználót szeretne hozzárendelni.

7. Az alkalmazás betöltése után a bal oldalon válassza a **felhasználók és csoportok** lehetőséget az alkalmazás menüjében.

8. A **hozzárendelés hozzáadása** ablaktábla megnyitásához kattintson a **felhasználók és csoportok** lista tetején található **Hozzáadás** gombra.

9. Válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** panelen.

10. Adja meg annak a csoportnak a teljes nevét, amelyet a **Keresés név vagy e-mail-cím keresőmező használatával** szeretne hozzárendelni.

11. Vigye a kurzort a listában a **csoport** fölé, majd jelölje be a csoport profiljának fényképe vagy emblémája melletti jelölőnégyzetet a csoport a **kiválasztott** listához való hozzáadásához.

12. Nem kötelező: Ha egynél több csoportot szeretne felvenni, adjon meg egy másik teljes nevet a **Keresés név vagy e-mail-cím** keresése mezőben, majd jelölje be a megfelelő jelölőnégyzetet a csoport a **kiválasztott** listához való hozzáadásához.

13. Ha végzett a csoportok kiválasztásával, a **kiválasztás** gombbal adhatja hozzá őket az alkalmazáshoz hozzárendelni kívánt felhasználók és csoportok listájához.

14. Nem kötelező: A **hozzárendelés hozzáadása** panelen a **szerepkör kiválasztása** paranccsal kiválaszthatja a kiválasztott csoportokhoz hozzárendelni kívánt szerepkört.

15. Válassza a **hozzárendelés** lehetőséget az alkalmazás kijelölt csoportokhoz való hozzárendeléséhez.

Rövid idő elteltével a kiválasztott felhasználóknak el kell tudniuk indítani ezeket az alkalmazásokat a hozzáférési panelen.

## <a name="next-steps"></a>További lépések
Az [Application proxyn keresztül egyszeri bejelentkezést biztosíthat az alkalmazásokba](application-proxy-configure-single-sign-on-with-kcd.md).
