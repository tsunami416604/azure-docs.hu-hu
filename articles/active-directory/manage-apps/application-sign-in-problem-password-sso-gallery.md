---
title: Nem lehet bejelentkezni a jelszavas egyszeri Bejelentkezést konfigurált Azure AD katalógusban alkalmazás |} A Microsoft Docs
description: Hogyan lehet egy Azure AD katalógusából származó alkalmazás, amely konfigurálva van a jelszavas egyszeri bejelentkezés problémáinak hibaelhárítása.
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 742df882fb64e09ff63ef2eceb5514ca070dc227
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190324"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Jelentkezzen be az Azure AD-katalógus alkalmazás egyszeri Bejelentkezésre konfigurálta problémák

Hozzáférési Panel egy olyan webes portál. Lehetővé teszi a felhasználók, akik rendelkeznek az Azure Active Directory (Azure AD) munkahelyi vagy iskolai fiókok felhőalapú alkalmazásokat, amelyek rendelkeznek az engedélyeket az eléréséhez. Az Azure AD-verziók rendelkező felhasználók önkiszolgáló csoportkezelési és alkalmazás-kezelési lehetőségei a hozzáférési panelen is használhatja.

Hozzáférési Panel elkülönül az Azure Portalon. Felhasználóknak nem kell Azure-előfizetés használatára a hozzáférési panelen.

Jelszavas egyszeri bejelentkezés (SSO) használata a hozzáférési panelen, telepíteni kell a hozzáférési Panel kiterjesztése a böngészőben. Amikor kiválaszt egy alkalmazást, amely konfigurálva van a jelszóalapú egyszeri bejelentkezés automatikusan letölti a bővítményt.

## <a name="browser-requirements-for-access-panel"></a>Hozzáférési Panel böngészőkövetelményei

Hozzáférési Panel a böngészőben, amely támogatja a JavaScript szükséges, és CSS engedélyezve van.

Az alábbi böngészők támogatják a jelszóalapú egyszeri bejelentkezés:

- Az Internet Explorer 8, 9, 10-es és 11 Windows 7-es vagy újabb

- Chrome, a Windows 7 vagy újabb, vagy MacOS X rendszeren vagy újabb

- Firefox 26.0 vagy újabb, Windows XP SP2 vagy újabb, vagy a Mac OS X 10.6 vagy újabb

>[!NOTE]
>A jelszóalapú egyszeri bejelentkezés bővítmény válnak elérhetővé a Microsoft Edge a Windows 10-ben Ha támogatása, a Microsoft Edge böngésző bővítmények hozzá lett adva.

## <a name="install-the-access-panel-browser-extension"></a>A hozzáférési Panel webböngésző-bővítmény telepítése

Kövesse az alábbi lépéseket:

1. Nyissa meg [hozzáférési Panel](https://myapps.microsoft.com) egy támogatott böngésző, és jelentkezzen be az Azure AD-felhasználóként.

2. Válassza ki a jelszó-SSO-kompatibilis alkalmazások a hozzáférési panelen.

3. Amikor a rendszer kéri, válassza ki a **telepítés most**.

4. Akkor jelenik meg a letöltési hivatkozást a böngészőtől függően. Válassza ki **Hozzáadás** a webböngésző-bővítmény telepítése.

5. Ha az kéri, válassza ki a **engedélyezése** vagy **engedélyezése**.

6. A telepítés után indítsa újra a böngészőt.

7.  Jelentkezzen be a hozzáférési panelen, és tekintse meg, ha elindíthatja a jelszó-SSO-kompatibilis alkalmazások.

Emellett közvetlenül is letöltheti a bővítmények a Chrome és a Firefox keresztül ezeket a hivatkozásokat:

-   [Chrome-hozzáférési Panel kiterjesztése](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [A Firefox hozzáférési Panel kiterjesztése](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>A csoportházirend beállítása az Internet Explorer

Beállíthat olyan csoportházirenddel, amely lehetővé teszi, hogy távolról telepíteni a hozzáférési Panel bővítményt az Internet Explorerben a felhasználók gépein is.

Az Előfeltételek a következők:

-   [Az Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) kell beállítani, és a felhasználók gépek a tartományhoz kell csatlakoznia.

-   "Beállítások szerkesztése" jogosultsággal a csoportházirend-objektumot (GPO) rendelkezik. Alapértelmezés szerint a következő biztonsági csoportok tagjai rendelkeznek a ezt az engedélyt: A tartományi rendszergazdák, a vállalati rendszergazdák és a Csoportházirend-létrehozó tulajdonosok. [További információk](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

A csoportházirend konfigurálásához és üzembe helyezni a felhasználók számára: [a hozzáférési Panel bővítmény telepítése csoportházirend használatával az Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy).

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Az Internet Explorerben a hozzáférési Panel – hibaelhárítás

Egy diagnosztikai eszköz és a bővítmény konfigurálása utasítások eléréséről, lásd a [a hozzáférési Panel bővítmény hibaelhárítása az Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Jelszavas egyszeri Bejelentkezést az Azure AD-katalógusban alkalmazás konfigurálása

Adja meg az Azure AD katalógusából származó alkalmazás, mindezt a kell:

-   Az alkalmazás hozzáadása az Azure AD katalógusából
-   [Az alkalmazás a jelszavas egyszeri bejelentkezés konfigurálása](#configure-the-app-for-password-sso)
-   [Felhasználók hozzárendelése az alkalmazáshoz](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Az alkalmazás hozzáadása az Azure AD katalógusából

Kövesse az alábbi lépéseket:

1. Nyissa meg a [az Azure portal](https://portal.azure.com) , és jelentkezzen be egy globális rendszergazdai vagy társadminisztrátorként.

2. Válassza ki **minden szolgáltatás** az Azure AD-bővítmény megnyitásához a bal oldali navigációs ablak tetején.

3. Típus **Azure Active Directory** a szűrőt a keresési mezőbe, és válassza ki a **Azure Active Directory**.

4. Válassza ki **vállalati alkalmazások** az Azure ad-ben navigációs ablaktáblán.

5. Válassza ki **Hozzáadás** jobb felső sarkában a **vállalati alkalmazások** ablaktáblán.

6. Az a **Hozzáadás a katalógusból** területén adja meg az alkalmazás nevét a **adjon meg egy nevet** mezőbe.

7. Válassza ki az alkalmazást, amely az egyszeri bejelentkezéshez konfigurálandó.

8. *Nem kötelező:* Mielőtt hozzáadja az alkalmazást, módosíthatja annak a **neve** mezőbe.

9. Kattintson a **Hozzáadás** az alkalmazás hozzáadásához.

   Rövid késleltetés után lesz az alkalmazás konfigurációs panelen láthatók.

### <a name="configure-the-app-for-password-sso"></a>Jelszavas egyszeri Bejelentkezést az alkalmazás konfigurálása

Kövesse az alábbi lépéseket:

1. Nyissa meg a [az Azure portal](https://portal.azure.com/) , és jelentkezzen be egy globális rendszergazdai vagy társadminisztrátorként.

2. Válassza ki **minden szolgáltatás** az Azure AD-bővítmény megnyitásához a bal oldali navigációs ablak tetején.

3. Típus **Azure Active Directory** a szűrőt a keresési mezőbe, és válassza ki a **Azure Active Directory**.

4. Válassza ki **vállalati alkalmazások** az Azure ad-ben navigációs ablaktáblán.

5. Válassza ki **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista**. Állítsa be a **megjelenítése** beállítást az "Összes alkalmazás."

6. Válassza ki az alkalmazást, amely az egyszeri bejelentkezéshez konfigurálandó.

7. Miután betölti az alkalmazást, válassza ki **egyszeri bejelentkezési** a az alkalmazás a bal oldali ablaktáblán.

8. Válassza ki **jelszóalapú bejelentkezés** mód.

9. Felhasználók hozzárendelése az alkalmazáshoz.

10. Hitelesítő adatok is megadhatja a felhasználók számára. (Ellenkező esetben kéri a felhasználótól megadnia alkalmazás indításakor.) Ehhez válassza ki a felhasználók a sorokat. Válassza ki **a hitelesítő adatok frissítése** , és adja meg a felhasználóneveket és jelszavakat.

### <a name="assign-users-to-the-app"></a>Felhasználók hozzárendelése az alkalmazáshoz

Felhasználók hozzárendelése egy alkalmazáshoz közvetlenül, kövesse az alábbi lépéseket:

1. Nyissa meg a [az Azure portal](https://portal.azure.com/) , és jelentkezzen be globális rendszergazdaként

2. Válassza ki **minden szolgáltatás** a az Azure AD-bővítmény megnyitásához a bal oldali navigációs problémás.

3. Típus **Azure Active Directory** a szűrőt a keresési mezőbe, és válassza ki a **Azure Active Directory**.

4. Válassza ki **vállalati alkalmazások** az Azure ad-ben navigációs ablaktáblán.

5. Válassza ki **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista**. Állítsa be a **megjelenítése** beállítást az "Összes alkalmazás."

6. A listában jelölje ki az alkalmazást, amelyet szeretne rendelje hozzá egy felhasználót.

7. Miután betölti az alkalmazást, válassza ki **felhasználók és csoportok** az alkalmazás navigációs ablak bal oldalán.

8. Válassza ki **Hozzáadás** felső részén a **felhasználók és csoportok** listán, megnyílik a **hozzárendelés hozzáadása** ablaktáblán.

9. Válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** ablaktáblán.

10. Az a **Keresés név vagy e-mail cím alapján** mezőbe írja be a teljes nevet vagy e-mail-cím, amelyet szeretne hozzárendelni.

11. A kurzort a felhasználónévre a listában. Jelölje be a jelölőnégyzetet, a felhasználó profilfényképének vagy, hogy a felhasználó hozzáadása embléma mellett a **kijelölt** listája.

12. *Nem kötelező:* Egy másik felhasználó hozzáadásához írja be egy másik nevet vagy e-mail-cím a **Keresés név vagy e-mail cím alapján** mezőbe, majd válassza ki a jelölőnégyzetet, hogy a felhasználó hozzáadása a **kijelölt** listája.

13. Ha elkészült, válassza a felhasználók, kattintson a **kiválasztása** , vegye fel a listára a felhasználók és az alkalmazáshoz hozzárendelt csoportok.

14. *Nem kötelező:* Kattintson a **Szerepkörválasztás** a a **hozzárendelés hozzáadása** ablaktáblán válassza ki a szerepkör hozzárendelése a kiválasztott felhasználókhoz.

15. Válassza ki **hozzárendelése** az alkalmazás hozzárendelése a kiválasztott felhasználók számára.

    Rövid késleltetés után a felhasználók fognak tudni elérni ezeket az alkalmazásokat a hozzáférési Panel.

## <a name="request-support"></a>Támogatás kérése 
Ha hibaüzenet jelenik meg az egyszeri bejelentkezés beállítása és hozzárendelése a felhasználók, nyisson egy támogatási jegyet. A lehető a következő információkat tartalmazza:

-   Megfelelési hiba azonosítója
-   Egyszerű felhasználónév (felhasználó e-mail-címe)
-   TenantID
-   Böngésző típusa
-   Időzóna és ideje és időtartama a hiba előfordulásakor
-   Fiddler-nyomkövetés

## <a name="next-steps"></a>További lépések
[Az alkalmazásproxy egyszeri bejelentkezést az alkalmazásokba biztosít](application-proxy-configure-single-sign-on-with-kcd.md)
