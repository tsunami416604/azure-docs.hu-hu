---
title: Probléma az SSO-hoz konfigurált Azure AD-gyűjtemény alkalmazásba való bejelentkezéssel kapcsolatban | Microsoft dokumentumok
description: A jelszó egyszeri bejelentkezéshez konfigurált Azure AD Gallery-alkalmazással kapcsolatos problémák elhárítása.
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
ms.openlocfilehash: e9fd17d9e066be6a1abff5165436a09b8921184e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68381307"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Bejelentkezési problémák az Egyszeri bejelentkezéshez konfigurált Azure AD-katalógusalkalmazással

Az Access Panel egy webalapú portál. Lehetővé teszi, hogy az Azure Active Directory (Azure AD) munkahelyi vagy iskolai fiókkal rendelkező felhasználók hozzáférjenek azokhoz a felhőalapú alkalmazásokhoz, amelyekhez engedéllyel rendelkeznek. Az Azure AD-kiadásokkal rendelkező felhasználók önkiszolgáló csoport- és alkalmazáskezelési funkciókat is használhatnak a Hozzáférési panelen keresztül.

Az Access Panel elkülönül az Azure Portaltól. A hozzáférési panel használatához a felhasználóknak nincs szükségük Azure-előfizetésre.

A hozzáférési panelen a jelszóalapú egyszeri bejelentkezés (SSO) használatához a hozzáférési panel bővítményt telepíteni kell a böngészőben. A bővítmény automatikusan letöltődik, amikor kiválaszt egy olyan alkalmazást, amely jelszóalapú egyszeri bejelentkezésre van konfigurálva.

## <a name="browser-requirements-for-access-panel"></a>A Hozzáférési panel böngészőkövetelményei

Az Access Panel használatához olyan böngészőre van szükség, amely támogatja a JavaScriptet, és engedélyezve van a CSS.

A következő böngészők támogatják a jelszóalapú egyszeri bejelentkezést:

- Internet Explorer 8, 9, 10 és 11 Windows 7 vagy újabb rendszeren

- Chrome Windows 7-es vagy újabb vagy újabb vagy MacOS X vagy újabb rendszeren

- Firefox 26.0 vagy újabb verzió Windows XP SP2 vagy újabb, illetve Mac OS X 10.6 vagy újabb rendszeren

>[!NOTE]
>A jelszóalapú Egyszeri bejelentkezés bővítmény elérhetővé válik a Microsoft Edge számára a Windows 10-ben, amikor a böngészőbővítmények támogatása hozzá lett adva a Microsoft Edge-hez.

## <a name="install-the-access-panel-browser-extension"></a>Az Access Panel böngészőbővítményének telepítése

Kövesse az alábbi lépéseket:

1. Nyissa [meg a Hozzáférési panelt](https://myapps.microsoft.com) egy támogatott böngészőben, és jelentkezzen be felhasználóként az Azure AD-ben.

2. Válasszon egy jelszóval rendelkező SSO-kompatibilis alkalmazást a Hozzáférési panelen.

3. Amikor a rendszer kéri, válassza a **Telepítés most**lehetőséget.

4. A böngészője alapján a letöltési linkre irányítjuk. A böngészőbővítmény telepítéséhez válassza a **Hozzáadás** lehetőséget.

5. Ha a rendszer kéri, válassza az **Engedélyezés** vagy **az Engedélyezés**lehetőséget.

6. A telepítés után indítsa újra a böngészőt.

7.  Jelentkezzen be a Hozzáférési panelre, és nézze meg, hogy el tudja-e indítani a jelszó-Egyszeri bejelentkezést engedélyező alkalmazásokat.

Közvetlenül is letöltheti a Chrome és a Firefox bővítményeit ezeken a linkeken keresztül:

-   [A Chrome access panel bővítménye](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox Access Panel bővítmény](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Csoportházirend beállítása az Internet Explorer programhoz

Beállíthat egy csoportházirendet, amely lehetővé teszi az Internet Explorer Access Panel bővítményének távoli telepítését a felhasználók gépein.

Ezek az előfeltételek:

-   [Az Active Directory tartományi szolgáltatásokat](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) be kell állítani, és a felhasználók gépeit csatlakoztatni kell a tartományhoz.

-   A csoportházirend-objektum szerkesztéséhez "Beállítások szerkesztése" engedéllyel rendelkezik. Alapértelmezés szerint a következő biztonsági csoportok tagjai rendelkeznek ezzel az engedéllyel: Tartományi rendszergazdák, Vállalati rendszergazdák és Csoportházirend-létrehozó tulajdonosok. [További információ](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

A csoportházirend konfigurálásához és a felhasználókszámára történő központi telepítéséhez olvassa el [A Hozzáférési panel bővítmény telepítése az Internet Explorer hez csoportházirend használatával című témakört.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy)

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>A Hozzáférési panel hibaelhárítása az Internet Explorerben

A diagnosztikai eszközök és a bővítmény konfigurálására vonatkozó utasítások eléréséről az [Internet Explorer Hozzáférési panel bővítményének hibaelhárítása című témakörében talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting)

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Jelszó-sSO konfigurálása egy Azure AD-katalógusalkalmazáshoz

Az Azure AD-katalógusból származó alkalmazás konfigurálásához az alábbi dolgokat kell megtennie:

-   Az alkalmazás hozzáadása az Azure AD-gyűjteményből
-   [Az alkalmazás konfigurálása egyszeri bejelentkezés jelszóhoz](#configure-the-app-for-password-sso)
-   [Felhasználók hozzárendelése az alkalmazáshoz](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Az alkalmazás hozzáadása az Azure AD-gyűjteményből

Kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt,](https://portal.azure.com) és jelentkezzen be globális rendszergazdaként vagy társrendszergazdaként.

2. Válassza a navigációs ablak tetején a bal oldali összes **szolgáltatás** lehetőséget az Azure AD-bővítmény megnyitásához.

3. Írja be az **Azure Active Directory** kifejezést a szűrő keresőmezőjébe, majd válassza az Azure Active **Directory**lehetőséget.

4. Válassza ki a **vállalati alkalmazások** az Azure AD navigációs ablaktáblán.

5. Válassza a **Hozzáadás** lehetőséget a **Vállalati alkalmazások** ablaktábla jobb felső sarkában.

6. A **Hozzáadás a gyűjteményből** szakaszban írja be az alkalmazás nevét a **Név megadása** mezőbe.

7. Válassza ki azt az alkalmazást, amelyet az SSO-hoz szeretne konfigurálni.

8. *Nem kötelező:* Az alkalmazás hozzáadása előtt módosíthatja a nevét a **Név** mezőben.

9. Az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

   Rövid késleltetés után láthatja az alkalmazás konfigurációs ablaktábláját.

### <a name="configure-the-app-for-password-sso"></a>Az alkalmazás beállítása a jelszó-sso-hoz

Kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt,](https://portal.azure.com/) és jelentkezzen be globális rendszergazdaként vagy társrendszergazdaként.

2. Válassza a navigációs ablak tetején a bal oldali összes **szolgáltatás** lehetőséget az Azure AD-bővítmény megnyitásához.

3. Írja be az **Azure Active Directory** kifejezést a szűrő keresőmezőjébe, majd válassza az Azure Active **Directory**lehetőséget.

4. Válassza ki a **vállalati alkalmazások** az Azure AD navigációs ablaktáblán.

5. Válassza **a Minden alkalmazás lehetőséget** az alkalmazások listájának megtekintéséhez.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista**tetején. Állítsa a **Megjelenítés** beállítást "Minden alkalmazás" beállításra.

6. Válassza ki azt az alkalmazást, amelyet az SSO-hoz szeretne konfigurálni.

7. Az alkalmazás betöltése után válassza az **Egyszeri bejelentkezés** lehetőséget az alkalmazás bal oldalán lévő ablaktáblában.

8. Válassza **a Jelszóalapú bejelentkezési** mód lehetőséget.

9. Felhasználók hozzárendelése az alkalmazáshoz.

10. A felhasználók számára hitelesítő adatokat is megadhat. (Ellenkező esetben a rendszer kéri a felhasználóktól, hogy az alkalmazás indításakor adjanak meg hitelesítő adatokat.) Ehhez jelölje ki a felhasználók sorait. Ezután válassza **a Hitelesítő adatok frissítése lehetőséget,** és adja meg a felhasználónevüket és jelszavukat.

### <a name="assign-users-to-the-app"></a>Felhasználók hozzárendelése az alkalmazáshoz

Ha közvetlenül szeretne felhasználókat hozzárendelni egy alkalmazáshoz, kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt,](https://portal.azure.com/) és jelentkezzen be globális rendszergazdaként.

2. Válassza ki az **összes szolgáltatás** a navigációs fájdalom a bal oldalon az Azure AD-bővítmény megnyitásához.

3. Írja be az **Azure Active Directory** kifejezést a szűrő keresőmezőjébe, majd válassza az Azure Active **Directory**lehetőséget.

4. Válassza ki a **vállalati alkalmazások** az Azure AD navigációs ablaktáblán.

5. Válassza **a Minden alkalmazás lehetőséget** az alkalmazások listájának megtekintéséhez.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista**tetején. Állítsa a **Megjelenítés** beállítást "Minden alkalmazás" beállításra.

6. A listából válassza ki azt az alkalmazást, amelyhez felhasználót szeretne rendelni.

7. Az alkalmazás betöltése után válassza a **Felhasználók és csoportok** lehetőséget az alkalmazás bal oldali navigációs ablaktábláján.

8. A **Hozzárendelés hozzáadása** ablaktábla megnyitásához válassza a **Hozzáadás** gombot a **Felhasználók és csoportok** lista tetején.

9. Válassza a **Felhasználók és csoportok** lehetőséget a Hozzárendelés **hozzáadása** ablaktáblán.

10. A **Keresés név vagy e-mail cím** alapján mezőbe írja be a hozzárendelni kívánt felhasználó teljes nevét vagy e-mail címét.

11. Vigye az egérmutatót a felhasználó fölé a listában. Jelölje be a felhasználó profilfotója vagy emblémája melletti jelölőnégyzetet, ha hozzá szeretné adni a **felhasználót** a Kijelölt listához.

12. *Nem kötelező:* Másik felhasználó hozzáadásához írjon be egy másik nevet vagy e-mail címet a **Keresés név vagy e-mail cím** alapján mezőbe, majd jelölje be a jelölőnégyzetet, ha hozzá szeretné adni a **felhasználót** a Kijelölt listához.

13. Ha befejezte a felhasználók kiválasztását, a **Kijelölés** gombra kattintva vegye fel őket az alkalmazáshoz rendelt felhasználók és csoportok listájára.

14. *Nem kötelező:* Kattintson a **Szerepkör** **kijelölése** elemre a Hozzárendelés hozzáadása ablaktáblán a kijelölt felhasználókhoz rendelő szerepkör kiválasztásához.

15. Válassza a **Hozzárendelés** lehetőséget, ha hozzá szeretné rendelni az alkalmazást a kijelölt felhasználókhoz.

    Rövid késleltetés után a felhasználók hozzáférhetnek ezekhez az alkalmazásokhoz a Hozzáférési panelről.

## <a name="request-support"></a>Támogatás kérése 
Ha hibaüzenet jelenik meg az SSO beállításakor és a felhasználók hozzárendelésekénél, nyisson meg egy támogatási jegyet. A lehető legtöbb információt adja meg:

-   Korrelációs hiba azonosítója
-   UPN (felhasználói e-mail cím)
-   TenantID
-   Böngésző típusa
-   A hiba bekövetkezésének időzónája és idő/időkeret
-   Hegedűs nyomok

## <a name="next-steps"></a>További lépések
[Egyszeri bejelentkezés az alkalmazásokba alkalmazásproxyval](application-proxy-configure-single-sign-on-with-kcd.md)
