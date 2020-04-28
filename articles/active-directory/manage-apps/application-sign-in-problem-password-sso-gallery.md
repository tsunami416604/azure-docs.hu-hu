---
title: Hiba történt az egyszeri bejelentkezésre konfigurált Azure AD Gallery-alkalmazásba való bejelentkezéskor | Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "68381307"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Bejelentkezési problémák az egyszeri bejelentkezéshez konfigurált Azure AD Gallery-alkalmazással

A hozzáférési panel egy webalapú portál. Lehetővé teszi, hogy a Azure Active Directory (Azure AD) munkahelyi vagy iskolai fiókkal rendelkező felhasználók hozzáférhessenek a felhőalapú alkalmazásokhoz, amelyekhez engedélyük van. Az Azure AD-kiadásokkal rendelkező felhasználók az önkiszolgáló csoportokat és az alkalmazás-felügyeleti funkciókat is használhatják a hozzáférési panelen.

A hozzáférési panel elkülönül a Azure Portaltól. A felhasználók nem igényelnek Azure-előfizetést a hozzáférési panel használatához.

Ha jelszó-alapú egyszeri bejelentkezést (SSO) szeretne használni a hozzáférési panelen, a hozzáférési panel bővítményét telepíteni kell a böngészőjébe. A bővítmény automatikusan töltődik le, ha olyan alkalmazást választ, amely jelszó alapú egyszeri bejelentkezéshez van konfigurálva.

## <a name="browser-requirements-for-access-panel"></a>A hozzáférési panel böngészőre vonatkozó követelményei

A hozzáférési panel egy olyan böngészőt igényel, amely támogatja a JavaScript használatát, és lehetővé teszi a CSS használatát.

A következő böngészők támogatják a jelszó-alapú egyszeri bejelentkezést:

- Internet Explorer 8, 9, 10 és 11 Windows 7 vagy újabb rendszeren

- Chrome Windows 7 vagy újabb, vagy MacOS X vagy újabb rendszeren

- Firefox 26,0 vagy újabb verziójú Windows XP SP2 vagy újabb, vagy Mac OS X 10,6 vagy újabb verzióban

>[!NOTE]
>A jelszó-alapú SSO-bővítmény elérhetővé válik a Microsoft Edge számára a Windows 10 rendszerben, ha a böngésző bővítményeinek támogatását hozzáadták a Microsoft Edge-hez.

## <a name="install-the-access-panel-browser-extension"></a>A hozzáférési panel böngésző bővítményének telepítése

Kövesse az alábbi lépéseket:

1. Nyissa meg a [hozzáférési panelt](https://myapps.microsoft.com) egy támogatott böngészőben, és jelentkezzen be felhasználóként az Azure ad-ben.

2. Válassza ki a jelszó-SSO-kompatibilis alkalmazást a hozzáférési panelen.

3. Ha a rendszer kéri, válassza a **Telepítés most**lehetőséget.

4. A böngészőn alapuló letöltési hivatkozásra kell átirányítani. A böngésző bővítmény telepítéséhez válassza a **Hozzáadás** lehetőséget.

5. Ha a rendszer kéri, válassza az **Engedélyezés** vagy az **Engedélyezés**lehetőséget.

6. A telepítés után indítsa újra a böngészőt.

7.  Jelentkezzen be a hozzáférési panelre, és ellenőrizze, hogy el tudja-e indítani a jelszó-SSO-kompatibilis alkalmazásokat.

A következő hivatkozásokon keresztül közvetlenül is letöltheti a Chrome és a Firefox bővítményeit:

-   [Chrome hozzáférési panel bővítmény](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox hozzáférési panel bővítmény](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Csoportházirend beállítása az Internet Explorerhez

Beállíthat egy csoportházirendet, amely lehetővé teszi, hogy távolról telepítse a hozzáférési panel bővítményt az Internet Explorer számára a felhasználói gépeken.

Ezek az előfeltételek:

-   A [Active Directory tartományi szolgáltatásokt](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) be kell állítani, és a felhasználók számítógépeit csatlakoztatni kell a tartományhoz.

-   "Beállítások szerkesztése" engedéllyel rendelkezik a Csoportházirend objektum (GPO) szerkesztéséhez. Alapértelmezés szerint a következő biztonsági csoportok tagjai rendelkeznek ezzel az engedéllyel: tartományi rendszergazdák, vállalati rendszergazdák és Csoportházirend létrehozói tulajdonosok. [További információ](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

A csoportházirend konfigurálásához és a felhasználók számára történő központi telepítéséhez lásd: [a hozzáférési panel kiterjesztésének telepítése az Internet Explorerhez csoportházirend használatával](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy).

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Az Internet Explorer hozzáférési paneljének hibakeresése

A diagnosztika eszközhöz és a bővítmény konfigurálásához szükséges utasításokért tekintse meg [az Internet Explorer hozzáférési panel bővítményének hibáit](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting)ismertető témakört.

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Jelszó egyszeri bejelentkezésének konfigurálása Azure AD Gallery-alkalmazáshoz

Az alkalmazások Azure AD-katalógusból való konfigurálásához ezeket a dolgokat kell tennie:

-   Az alkalmazás hozzáadása az Azure AD-katalógusból
-   [Az alkalmazás konfigurálása a jelszó egyszeri bejelentkezéséhez](#configure-the-app-for-password-sso)
-   [Felhasználók hozzárendelése az alkalmazáshoz](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Az alkalmazás hozzáadása az Azure AD-katalógusból

Kövesse az alábbi lépéseket:

1. Nyissa meg a [Azure Portal](https://portal.azure.com) , és jelentkezzen be globális rendszergazdaként vagy társ-rendszergazdaként.

2. Az Azure AD-bővítmény megnyitásához kattintson a bal oldalon található navigációs ablaktábla tetején található **összes szolgáltatás** elemre.

3. Írja be a **Azure Active Directory** kifejezést a szűrő keresőmezőbe, majd válassza a **Azure Active Directory**lehetőséget.

4. Válassza a **vállalati alkalmazások** lehetőséget az Azure ad navigációs paneljén.

5. A **vállalati alkalmazások** ablaktábla jobb felső sarkában válassza a **Hozzáadás** lehetőséget.

6. A **Hozzáadás a** katalógusból szakaszban írja be az alkalmazás nevét a **név megadása** mezőbe.

7. Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

8. Nem *kötelező:* Az alkalmazás hozzáadása előtt módosíthatja a nevét a **név** mezőben.

9. Az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

   Rövid késleltetés után megtekintheti az alkalmazás konfigurációs paneljét.

### <a name="configure-the-app-for-password-sso"></a>Az alkalmazás konfigurálása jelszó-egyszeri bejelentkezéshez

Kövesse az alábbi lépéseket:

1. Nyissa meg a [Azure Portal](https://portal.azure.com/) , és jelentkezzen be globális rendszergazdaként vagy társ-rendszergazdaként.

2. Az Azure AD-bővítmény megnyitásához kattintson a bal oldalon található navigációs ablaktábla tetején található **összes szolgáltatás** elemre.

3. Írja be a **Azure Active Directory** kifejezést a szűrő keresőmezőbe, majd válassza a **Azure Active Directory**lehetőséget.

4. Válassza a **vállalati alkalmazások** lehetőséget az Azure ad navigációs ablaktábláján.

5. Az alkalmazások listájának megtekintéséhez kattintson a **minden alkalmazás** elemre.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja a **minden alkalmazás lista**tetején található **szűrő** vezérlőelemet. Állítsa a **show (megjelenítés** ) lehetőséget a "minden alkalmazás" értékre.

6. Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

7. Az alkalmazás betöltése után válassza az **egyszeri bejelentkezés** lehetőséget az alkalmazás bal oldalán található ablaktáblán.

8. Válassza a **jelszó alapú bejelentkezési** mód lehetőséget.

9. Felhasználók társítása az alkalmazáshoz.

10. A felhasználók hitelesítő adatait is megadhatja. (Máskülönben a felhasználókat a rendszer a hitelesítő adatok megadására kéri az alkalmazás indításakor.) Ehhez válassza ki a felhasználók sorait. Ezután válassza a **hitelesítő adatok frissítése** lehetőséget, és adja meg a felhasználónevét és a jelszavát.

### <a name="assign-users-to-the-app"></a>Felhasználók hozzárendelése az alkalmazáshoz

A felhasználók közvetlenül az alkalmazásokhoz való hozzárendeléséhez kövesse az alábbi lépéseket:

1. Nyissa meg a [Azure Portalt](https://portal.azure.com/) , és jelentkezzen be globális rendszergazdaként.

2. Az Azure AD bővítmény megnyitásához válassza a bal oldalon található navigációs fájdalom **összes szolgáltatás** elemét.

3. Írja be a **Azure Active Directory** kifejezést a szűrő keresőmezőbe, majd válassza a **Azure Active Directory**lehetőséget.

4. Válassza a **vállalati alkalmazások** lehetőséget az Azure ad navigációs ablaktábláján.

5. Az alkalmazások listájának megtekintéséhez kattintson a **minden alkalmazás** elemre.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja a **minden alkalmazás lista**tetején található **szűrő** vezérlőelemet. Állítsa a **show (megjelenítés** ) lehetőséget a "minden alkalmazás" értékre.

6. A listából válassza ki azt az alkalmazást, amelyhez felhasználót szeretne hozzárendelni.

7. Az alkalmazás betöltése után válassza a **felhasználók és csoportok** lehetőséget az alkalmazás navigációs paneljén a bal oldalon.

8. A **felhasználók és csoportok** lista tetején a **Hozzáadás** gombra kattintva nyissa meg a **hozzárendelés hozzáadása** ablaktáblát.

9. A **hozzárendelés hozzáadása** panelen válassza a **felhasználók és csoportok** lehetőséget.

10. A **Keresés név vagy e-mail-cím** mezőbe írja be a hozzárendelni kívánt felhasználó teljes nevét vagy e-mail-címét.

11. Vigye a kurzort a listában szereplő felhasználó fölé. Jelölje be a felhasználó profiljának fényképe vagy emblémája melletti jelölőnégyzetet, hogy hozzáadja a felhasználót a **kiválasztott** listához.

12. Nem *kötelező:* Egy másik felhasználó hozzáadásához írjon be egy másik nevet vagy e-mail-címet a **Keresés név vagy e-mail-cím** mezőbe, majd jelölje be a jelölőnégyzetet, ha hozzá szeretné adni a felhasználót a **kiválasztott** listához.

13. Ha végzett a felhasználók kiválasztásával, kattintson a **kiválasztás** gombra az alkalmazáshoz hozzárendelt felhasználók és csoportok listájához való hozzáadásához.

14. Nem *kötelező:* Kattintson a **szerepkör kiválasztása** elemre a **hozzárendelés hozzáadása** panelen, és válassza ki a kiválasztott felhasználókhoz hozzárendelni kívánt szerepkört.

15. Válassza a **hozzárendelés** lehetőséget az alkalmazás a kiválasztott felhasználókhoz való hozzárendeléséhez.

    Rövid késleltetés után a felhasználók hozzáférhetnek az alkalmazásokhoz a hozzáférési panelen.

## <a name="request-support"></a>Támogatás kérése 
Ha az egyszeri bejelentkezés beállítása és a felhasználók kiosztása során hibaüzenet jelenik meg, nyisson meg egy támogatási jegyet. A lehető legtöbbet a következő információkból állhat:

-   Korrelációs hiba azonosítója
-   UPN (felhasználói e-mail-cím)
-   TenantID
-   Böngésző típusa
-   Az időzóna és az idő/idő keret a hiba bekövetkezésekor
-   Hegedűs nyomkövetései

## <a name="next-steps"></a>További lépések
[Egyszeri bejelentkezés biztosítása az alkalmazásokba az Application proxy használatával](application-proxy-configure-single-sign-on-with-kcd.md)
