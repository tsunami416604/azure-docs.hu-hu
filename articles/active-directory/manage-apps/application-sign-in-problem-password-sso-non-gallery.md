---
title: Jelszó-alapú egyszeri bejelentkezés (SSO) a hozzáférési panelen | Microsoft Docs
description: A problémás területeket ismerteti, amelyek útmutatást nyújtanak a jelszó egyszeri bejelentkezéshez konfigurált Azure AD Gallery-alkalmazásokba való bejelentkezéssel kapcsolatos problémák elhárításához.
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ca192c28757df189e531aee0ba2d8da288ba7e6
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381243"
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>Problémák a jelszó egyszeri bejelentkezéséhez konfigurált Azure AD Gallery-alkalmazásba való bejelentkezéskor

A hozzáférési panel egy webalapú portál, amely lehetővé teszi, hogy egy Azure Active Directory (Azure AD) munkahelyi vagy iskolai fiókkal rendelkező felhasználó megtekintse és elindítsa azokat a felhőalapú alkalmazásokat, amelyekhez az Azure AD-rendszergazda hozzáférést kapott. Az Azure AD-kiadásokkal rendelkező felhasználók az önkiszolgáló csoportok és az alkalmazások felügyeleti képességeit is használhatják a hozzáférési panelen. A hozzáférési panel el van különítve a Azure Portaltól, és nem igényli, hogy a felhasználók Azure-előfizetéssel rendelkezzenek.

Ha jelszó-alapú egyszeri bejelentkezést (SSO) szeretne használni a hozzáférési panelen, a hozzáférési panel kiterjesztését telepíteni kell a felhasználó böngészőjébe. A rendszer automatikusan letölti a bővítményt, ha a felhasználó egy jelszó alapú egyszeri bejelentkezéshez konfigurált alkalmazást választ ki.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>A hozzáférési panel Meeting Browser követelményei

A hozzáférési panel egy olyan böngészőt igényel, amely támogatja a JavaScript használatát, és lehetővé teszi a CSS használatát. Ha jelszó-alapú egyszeri bejelentkezést (SSO) szeretne használni a hozzáférési panelen, a hozzáférési panel kiterjesztését telepíteni kell a felhasználó böngészőjébe. A rendszer automatikusan letölti a bővítményt, ha a felhasználó egy jelszó alapú egyszeri bejelentkezéshez konfigurált alkalmazást választ ki.

Jelszó-alapú egyszeri bejelentkezés esetén a végfelhasználók böngészőjében a következő lehet:

-   Internet Explorer 8, 9, 10, 11 – Windows 7 vagy újabb rendszeren

-   Chrome – Windows 7 vagy újabb, illetve MacOS X vagy újabb rendszereken

-   Firefox 26,0 vagy újabb verzió – Windows XP SP2 vagy újabb rendszeren, valamint Mac OS X 10,6 vagy újabb rendszeren

>[!NOTE]
>A jelszó-alapú SSO-bővítmény elérhetővé válik a Microsoft Edge számára a Windows 10 rendszerben, ha a böngésző bővítményei támogatják a Microsoft Edge-t.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>A hozzáférési panel böngésző bővítményének telepítése

A hozzáférési panel böngésző bővítményének telepítéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [hozzáférési panelt](https://myapps.microsoft.com) az egyik támogatott böngészőben, és jelentkezzen  be FELHASZNÁLÓként az Azure ad-ben.

2.  kattintson egy **jelszó-SSO-alkalmazásra** a hozzáférési panelen.

3.  A szoftver telepítését kérő üzenetben válassza a **Telepítés most**lehetőséget.

4.  A böngészője alapján a letöltési hivatkozásra kell irányítani. **Adja hozzá** a bővítményt a böngészőjéhez.

5.  Ha a böngésző kéri, válassza ki a bővítmény **engedélyezése** vagy **engedélyezése** lehetőséget.

6.  A telepítés után **indítsa újra** a böngésző-munkamenetet.

7.  Jelentkezzen be a hozzáférési panelre, és ellenőrizze,  hogy elindíthatja-e a jelszó-SSO-alkalmazásokat

A Chrome és a Firefox bővítményét az alábbi közvetlen hivatkozásokból is letöltheti:

-   [Chrome hozzáférési panel bővítmény](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox hozzáférési panel bővítmény](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Csoportházirend beállítása az Internet Explorerhez

Beállíthat egy csoportházirendet, amely lehetővé teszi, hogy távolról telepítse a hozzáférési panel bővítményt az Internet Explorer számára a felhasználói gépeken.

Az előfeltételek a következők:

-   Beállította [Active Directory tartományi szolgáltatások](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), és csatlakoztatta a felhasználói gépeket a tartományhoz.

-   A Csoportházirend objektum (GPO) szerkesztéséhez a "beállítások szerkesztése" engedéllyel kell rendelkeznie. Alapértelmezés szerint a következő biztonsági csoportok tagjai rendelkeznek ezzel az engedéllyel: A tartományi rendszergazdák, a vállalati rendszergazdák és a Csoportházirend létrehozói tulajdonosai. [További információk](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

A csoportházirend konfigurálásához és a felhasználók számára történő üzembe helyezéséhez kövesse a következő oktatóanyagot, [amely bemutatja, hogyan helyezheti üzembe a hozzáférési panel bővítményét az Internet Explorer alkalmazásban csoportházirend használatával](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) : részletes útmutató.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Az Internet Explorer hozzáférési paneljének hibáinak megoldása

Kövesse az [Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) útmutatóhoz készült hozzáférési panel bővítményét a diagnosztika eszköz eléréséhez, és részletes útmutatást az IE-bővítmény konfigurálásához.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>A jelszó egyszeri bejelentkezésének konfigurálása nem katalógusos alkalmazásokhoz

Egy alkalmazás Azure AD-katalógusból való konfigurálásához a következőket kell tennie:

-   [Nem Gallery-alkalmazás hozzáadása](#add-a-non-gallery-application)

-   [Az alkalmazás konfigurálása a jelszó egyszeri bejelentkezéséhez](#configure-the-application-for-password-single-sign-on)

-   [Felhasználók kiosztása az alkalmazáshoz](#assign-users-to-the-application)

### <a name="add-a-non-gallery-application"></a>Nem Gallery-alkalmazás hozzáadása

Egy alkalmazás Azure AD-katalógusból való hozzáadásához kövesse az alábbi lépéseket:

1.  Nyissa [](https://portal.azure.com) meg a Azure Portalt, és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5.  kattintson a **Hozzáadás** gombra a **vállalati alkalmazások** ablaktábla jobb felső sarkában.

6.  kattintson a **nem Gallery-alkalmazás** lehetőségre.

7.  Adja meg az alkalmazás nevét a Name ( **név** ) szövegmezőben. Válassza a **Hozzáadás lehetőséget.**

Rövid idő elteltével megtekintheti az alkalmazás konfigurációs paneljét.

### <a name="configure-the-application-for-password-single-sign-on"></a>Az alkalmazás konfigurálása a jelszó egyszeri bejelentkezéséhez

Az alkalmazások egyszeri bejelentkezésének konfigurálásához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki az egyszeri bejelentkezést konfigurálni kívánt alkalmazást

7. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

8. Válassza ki a mód **jelszavas alapú bejelentkezését.**

9. Adja meg a **bejelentkezési URL-címet**. Itt adhatja meg az URL-címet, ahol a felhasználók bejelentkeznek a felhasználónevével és jelszavával. Győződjön meg arról, hogy a bejelentkezési mezők láthatók az URL-címen.

10. Felhasználók kiosztása az alkalmazáshoz.

11. Emellett a felhasználók nevében is megadhat hitelesítő adatokat. Ehhez válassza ki a felhasználókat, majd kattintson a **hitelesítő adatok frissítése** lehetőségre, és írja be a felhasználónevet és a jelszót a felhasználók nevében. Ellenkező esetben a rendszer a felhasználók számára az indításkor kéri a hitelesítő adatok megadását.

### <a name="assign-users-to-the-application"></a>Felhasználók hozzárendelése az alkalmazáshoz

Közvetlenül rendelni egy vagy több felhasználó egy alkalmazást, kövesse az alábbi lépéseket:

1. Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki a kívánt rendelje hozzá egy felhasználót a listáról az alkalmazást.

7. Az alkalmazás betöltése után kattintson a **felhasználók és csoportok** elemre az alkalmazás bal oldali navigációs menüjében.

8. Kattintson a **Hozzáadás** a gombot a **felhasználók és csoportok** listán, megnyílik a **hozzárendelés hozzáadása** ablaktáblán.

9. Kattintson a **felhasználók és csoportok** a választó a **hozzárendelés hozzáadása** ablaktáblán.

10. Írja be a **teljes név** vagy **e-mail-cím** Önt érdeklő való hozzárendelése a felhasználó a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. A kurzort a **felhasználói** megjelenítéséhez a listában egy **jelölőnégyzet**. Jelölje be a jelölőnégyzetet, a felhasználó profilfényképének vagy adja hozzá a felhasználót az embléma mellett a **kijelölt** listája.

12. **Választható** Ha **egynél több felhasználót**szeretne felvenni, írjon be egy másik **teljes nevet** vagy **e-mail-címet** a **Keresés név vagy e-mail-cím** Keresés mezőbe, és kattintson a jelölőnégyzetre a felhasználó a **kiválasztott** listához való hozzáadásához.

13. Amikor elkészült, válassza a felhasználók, kattintson a **kiválasztása** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazást.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** -választójában jelenítse a **hozzárendelés hozzáadása** hozzárendelése a kiválasztott felhasználói szerepkör kiválasztása panel.

15. Kattintson a **hozzárendelése** gombra kattintva rendelje hozzá az alkalmazás a kijelölt felhasználók számára.

Rövid idő elteltével a kiválasztott felhasználók el tudják indítani ezeket az alkalmazásokat a hozzáférési panelen.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Ha ezek a hibaelhárítási lépések nem a probléma megoldására szolgálnak

Ha elérhető, nyisson meg egy támogatási jegyet a következő információkkal:

-   Korrelációs hiba azonosítója

-   UPN (felhasználói e-mail-cím)

-   TenantID

-   Böngésző típusa

-   Az időzóna és az idő/időkeret a hiba bekövetkezésekor

-   Hegedűs nyomkövetései

## <a name="next-steps"></a>További lépések
[Az alkalmazásproxy egyszeri bejelentkezést az alkalmazásokba biztosít](application-proxy-configure-single-sign-on-with-kcd.md)

