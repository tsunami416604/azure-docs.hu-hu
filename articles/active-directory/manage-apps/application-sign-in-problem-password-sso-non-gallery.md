---
title: A jelszó beállított, az Azure AD katalógusából származó alkalmazásba történő bejelentkezésnél egyszeri bejelentkezés |} A Microsoft Docs
description: A cikk ismerteti a problémás területek, amely útmutatást való jelszavas egyszeri bejelentkezésre konfigurált Azure AD katalógusából származó alkalmazásokhoz való bejelentkezéssel kapcsolatos problémák elhárítása
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
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 6365cce6dee6496d87247a98333bc056679636d0
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54468762"
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>A jelszavas egyszeri bejelentkezésre beállított, az Azure AD katalógusából származó alkalmazásba történő bejelentkezésnél

A hozzáférési panelen megtekintheti és elindíthatja felhőalapú alkalmazások, amelyek az Azure AD-rendszergazda hozzáférést adott azokat a webes portál, amely lehetővé teszi a felhasználó, aki rendelkezik munkahelyi vagy iskolai fiókkal az Azure Active Directoryban (Azure AD). Önkiszolgáló csoport- és alkalmazáskezelési lehetőségeket a hozzáférési panelen keresztül felhasználó, aki rendelkezik az Azure AD-verziók is használhatja. A hozzáférési panelen elkülönül az Azure Portalon, és nem igényel a felhasználók számára, hogy rendelkezik Azure-előfizetéssel.

Jelszavas egyszeri bejelentkezés (SSO) használata a hozzáférési panelen, a felhasználó böngészőjében a hozzáférési Panel bővítményt kell telepíteni. Ezzel a bővítménnyel automatikusan letöltődik, amikor a felhasználó kiválaszt egy alkalmazást, amely konfigurálva van a jelszóalapú egyszeri bejelentkezés.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>A hozzáférési Panel a böngészőben követelmények teljesítése

A hozzáférési Panel a böngészőben, amely támogatja a JavaScript szükséges, és CSS engedélyezve van. Jelszavas egyszeri bejelentkezés (SSO) használata a hozzáférési panelen, a felhasználó böngészőjében a hozzáférési Panel bővítményt kell telepíteni. Ezzel a bővítménnyel automatikusan letöltődik, amikor a felhasználó kiválaszt egy alkalmazást, amely konfigurálva van a jelszóalapú egyszeri bejelentkezés.

A jelszóalapú egyszeri bejelentkezés a végfelhasználó böngészők lehet:

-   Az Internet Explorer 8, 9, 10, 11 – a Windows 7 vagy újabb

-   Chrome – A Windows 7 vagy újabb, és MacOS X rendszeren vagy újabb

-   A Firefox 26.0 vagy később – a Windows XP SP2 vagy újabb, és a Mac OS X 10,6 vagy újabb

>[!NOTE]
>A jelszóalapú egyszeri bejelentkezés bővítmény a Microsoft Edge a Windows 10-ben elérhetővé válnak böngészőbővítmények a Microsoft Edge válnak támogatottá.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>A hozzáférési Panel webböngésző-bővítmény telepítése

A hozzáférési Panel webböngésző-bővítmény telepítéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [hozzáférési Panel](https://myapps.microsoft.com) az egyik támogatott böngészők és való bejelentkezést egy **felhasználói** az Azure AD-ben.

2.  Kattintson egy **jelszó-SSO alkalmazás** a hozzáférési panelen.

3.  Válassza ki a rendszer kéri a szoftver telepítéséhez, **telepítés most**.

4.  A böngésző alapján kell irányítani a letöltési hivatkozás. **Adjon hozzá** a bővítményt a böngészőben.

5.  Ha a böngésző kéri, válassza ki vagy **engedélyezése** vagy **engedélyezése** a bővítményt.

6.  Miután telepítette, **indítsa újra a** a böngésző-munkamenetet.

7.  Jelentkezzen be a hozzáférési panelre, és tekintse meg, ha a **indítsa el a** a jelszó-SSO-alkalmazások

Az alábbi közvetlen hivatkozások a Chrome és a Firefox is letöltheti a bővítményt:

-   [Chrome-hozzáférési Panel bővítmény](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [A Firefox hozzáférési Panel bővítmény](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>A csoportházirend beállítása az Internet Explorer

Beállíthat olyan csoportházirenddel, amely lehetővé teszi a távoli telepítéséhez a felhasználók gépein az Internet Explorer a hozzáférési Panel bővítményt.

Az Előfeltételek a következők:

-   Beállított [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), és a felhasználók gépek csatlakozott a tartományhoz.

-   A csoportházirend-objektumot (GPO) szerkesztését a "Beállítások szerkesztése" engedéllyel kell rendelkeznie. Alapértelmezés szerint a következő biztonsági csoportok tagjai rendelkeznek a ezt az engedélyt: A tartományi rendszergazdák, a vállalati rendszergazdák és a Csoportházirend-létrehozó tulajdonosok. [További információk](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Az oktatóanyag [a hozzáférési Panel bővítmény telepítése csoportházirend használatával az Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) részletes útmutatást a csoportházirend konfigurálásához és üzembe helyezéséhez, a felhasználók számára.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>A hozzáférési panelen az Internet Explorer hibaelhárítása

Kövesse a [a hozzáférési Panel bővítmény hibaelhárítása az Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) útmutató a hozzáférést egy diagnosztikai eszköz, és részletes útmutatást a bővítmény konfigurálásáról az Internet Explorer.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jelszavas egyszeri bejelentkezés nem katalógusból származó alkalmazásra konfigurálása

Az Azure AD katalógusából származó kell az alkalmazás konfigurálása:

-   [A katalógusban nem szereplő alkalmazás hozzáadása](#add-a-non-gallery-application)

-   [Az alkalmazás a jelszavas egyszeri bejelentkezés konfigurálása](#configure-the-application-for-password-single-sign-on)

-   [Felhasználók hozzárendelése az alkalmazáshoz](#assign-users-to-the-application)

### <a name="add-a-non-gallery-application"></a>A katalógusban nem szereplő alkalmazás hozzáadása

Az Azure AD katalógusából származó alkalmazás hozzáadásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [az Azure portal](https://portal.azure.com) , és jelentkezzen be egy **globális rendszergazdai** vagy **társadminisztrátor**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **Hozzáadás** gombra a jobb felső sarokban lévő a **vállalati alkalmazások** ablaktáblán.

6.  Kattintson a **katalógusban nem szereplő alkalmazást.**

7.  Adja meg az alkalmazás nevét a **neve** szövegmezőbe. Válassza ki **hozzáadása.**

Egy rövid időszak után megtekintheti az alkalmazás konfigurációs panelen lehet.

### <a name="configure-the-application-for-password-single-sign-on"></a>Az alkalmazás a jelszavas egyszeri bejelentkezés konfigurálása

Egyszeri bejelentkezés az alkalmazás konfigurálásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazást szeretné az egyszeri bejelentkezés konfigurálása

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Válassza ki a módot **jelszóalapú bejelentkezés.**

9.  Adja meg a **bejelentkezési URL-**. Ez az az URL-címet, adja meg a felhasználók a felhasználónevével és jelszavával bejelentkezni. Győződjön meg arról, a bejelentkezési mezők láthatók az URL-címen.

10. Felhasználók hozzárendelése az alkalmazáshoz.

11. Ezenkívül is megadhatja a felhasználó nevében legyenek hitelesítő adatok a sorok, a felhasználók kiválasztva, majd kattintson a **a hitelesítő adatok frissítése** és a felhasználónév és jelszó megadásával a felhasználók nevében. Ellenkező esetben megkérdezi a felhasználókat a hitelesítő adatokat magukat az indítás után.

### <a name="assign-users-to-the-application"></a>Felhasználók hozzárendelése az alkalmazáshoz

Közvetlenül rendelni egy vagy több felhasználó egy alkalmazást, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki a kívánt rendelje hozzá egy felhasználót a listáról az alkalmazást.

7.  Ha az alkalmazás betöltött, kattintson a **felhasználók és csoportok** az alkalmazás bal oldali navigációs menüjében.

8.  Kattintson a **Hozzáadás** a gombot a **felhasználók és csoportok** listán, megnyílik a **hozzárendelés hozzáadása** ablaktáblán.

9.  Kattintson a **felhasználók és csoportok** a választó a **hozzárendelés hozzáadása** ablaktáblán.

10. Írja be a **teljes név** vagy **e-mail-cím** Önt érdeklő való hozzárendelése a felhasználó a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. A kurzort a **felhasználói** megjelenítéséhez a listában egy **jelölőnégyzet**. Jelölje be a jelölőnégyzetet, a felhasználó profilfényképének vagy adja hozzá a felhasználót az embléma mellett a **kijelölt** listája.

12. **Nem kötelező:** Ha szeretné **egynél több felhasználó hozzáadása**, írjon be egy másik **teljes név** vagy **e-mail-cím** be a **Keresés név vagy e-mail cím alapján** keresőmezőbe, majd kattintson a jelölőnégyzet bejelölésével adja hozzá a felhasználót a **kijelölt** listája.

13. Amikor elkészült, válassza a felhasználók, kattintson a **kiválasztása** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazást.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** -választójában jelenítse a **hozzárendelés hozzáadása** hozzárendelése a kiválasztott felhasználói szerepkör kiválasztása panel.

15. Kattintson a **hozzárendelése** gombra kattintva rendelje hozzá az alkalmazás a kijelölt felhasználók számára.

Rövid ideig a kijelölt felhasználók tudják elindítani ezeket az alkalmazásokat a hozzáférési panelen.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Ha ezeket a hibaelhárítási lépéseket végre nem a hárítsa el a problémát

Nyisson meg egy támogatási jegyet a következő adatokat, ha rendelkezésre áll:

-   Megfelelési hiba azonosítója

-   Egyszerű felhasználónév (felhasználó e-mail-címe)

-   TenantID

-   Böngésző típusa

-   Időzóna és idő/időkeret során hiba történik.

-   Fiddler-nyomkövetés

## <a name="next-steps"></a>További lépések
[Az alkalmazásproxy egyszeri bejelentkezést az alkalmazásokba biztosít](application-proxy-configure-single-sign-on-with-kcd.md)

