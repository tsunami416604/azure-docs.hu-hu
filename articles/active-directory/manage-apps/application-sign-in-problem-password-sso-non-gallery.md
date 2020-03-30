---
title: Jelszóalapú egyszeri bejelentkezés (SSO) a hozzáférési panelen | Microsoft dokumentumok
description: A cikk ismerteti azokat a problémás területeket, amelyek útmutatást nyújtanak a jelszó egyszeri bejelentkezésre konfigurált Azure AD Gallery-alkalmazásokba való bejelentkezéssel kapcsolatos problémák elhárításához.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68381243"
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>Problémák az azure-beli AD-gyűjtemény alkalmazásba való bejelentkezéskor, amely egyetlen bejelentkezéssel van konfigurálva

A Hozzáférési panel egy webalapú portál, amely lehetővé teszi, hogy az Azure Active Directoryban (Azure AD) munkahelyi vagy iskolai fiókkal rendelkező felhasználó megtekintse és elindíthassa azokat a felhőalapú alkalmazásokat, amelyekhez az Azure AD-rendszergazda hozzáférést biztosított számukra. Az Azure AD-kiadásokkal rendelkező felhasználók önkiszolgáló csoport- és alkalmazáskezelési funkciókat is használhatnak a Hozzáférési panelen keresztül. A hozzáférési panel elkülönül az Azure Portalon, és nem követeli meg a felhasználók számára, hogy egy Azure-előfizetéssel.

A hozzáférési panelen a jelszóalapú egyszeri bejelentkezés (SSO) használatához a hozzáférési panel bővítményt telepíteni kell a felhasználó böngészőjében. Ez a bővítmény automatikusan letöltődik, amikor a felhasználó olyan alkalmazást választ, amely jelszóalapú egyszeri bejelentkezésre van konfigurálva.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>A Hozzáférési panel böngészőkövetelményeinek teljesítése

A hozzáférési panelhez olyan böngészőre van szükség, amely támogatja a JavaScriptet, és engedélyezve van a CSS. A hozzáférési panelen a jelszóalapú egyszeri bejelentkezés (SSO) használatához a hozzáférési panel bővítményt telepíteni kell a felhasználó böngészőjében. Ez a bővítmény automatikusan letöltődik, amikor a felhasználó olyan alkalmazást választ, amely jelszóalapú egyszeri bejelentkezésre van konfigurálva.

A jelszóalapú egyszeri bejelentkezés esetén a végfelhasználó böngészői a következők lehetnek:

-   Internet Explorer 8, 9, 10, 11 – Windows 7 vagy újabb rendszeren

-   Chrome – Windows 7 vagy újabb rendszeren, valamint MacOS X-en vagy újabb rendszeren

-   Firefox 26.0 vagy újabb verzió – Windows XP SP2 vagy újabb, valamint Mac OS X 10.6-os vagy újabb rendszeren

>[!NOTE]
>A jelszóalapú Egyszeri bejelentkezés bővítmény elérhetővé válik a Microsoft Edge számára a Windows 10-ben, amikor a böngészőbővítmények a Microsoft Edge számára támogatottak lesznek.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>Az Access Panel böngészőbővítményének telepítése

Az Access Panel browser bővítményének telepítéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [hozzáférési panelt](https://myapps.microsoft.com) az egyik támogatott böngészőben, és jelentkezzen be **felhasználóként** az Azure AD-ben.

2.  kattintson egy **jelszó-Egyszeri bejelentkezés alkalmazásra** a Hozzáférési panelen.

3.  A szoftver telepítését kérő üzenetben válassza a **Telepítés most**lehetőséget.

4.  A böngésző alapján a letöltési linkre irányítjuk. **Adja hozzá** a bővítményt a böngészőhöz.

5.  Ha a böngésző kérdezi, válassza a **bővítmény engedélyezése** vagy **engedélyezése** lehetőséget.

6.  A telepítés után **indítsa újra** a böngészőmunkamenetet.

7.  Jelentkezzen be a hozzáférési panelen, és nézze meg, hogy **el tudja-e indítani** a jelszó-Egyszeri bejelentkezés alkalmazásokat

Ön is letöltheti a kiterjesztést a Chrome és a Firefox a közvetlen alábbi linkekre:

-   [Chrome-hozzáférési panel bővítmény](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox access panel bővítmény](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Csoportházirend beállítása az Internet Explorer böngészőhöz

Olyan csoportházirendet is beállíthat, amely lehetővé teszi az Internet Explorer Access Panel bővítményének távoli telepítését a felhasználók gépein.

Az előfeltételek a következők:

-   Beállította az [Active Directory tartományi szolgáltatásokat,](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)és csatlakozott a felhasználók gépeihez a tartományhoz.

-   A csoportházirend-objektum szerkesztéséhez "Beállítások szerkesztése" engedéllyel kell rendelkeznie. Alapértelmezés szerint a következő biztonsági csoportok tagjai rendelkeznek ezzel az engedéllyel: Tartományi rendszergazdák, Vállalati rendszergazdák és Csoportházirend-létrehozó tulajdonosok. [További információ](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Kövesse az [oktatóanyagot, hogyan telepítheti az Internet Explorer hozzáférési panelbővítményét a csoportházirend használatával,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) lépésről lépésre, hogyan konfigurálja és telepítse a csoportházirendet a felhasználók számára.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Az Internet Explorer Hozzáférési paneljének – problémamegoldás

Kövesse [az Internet Explorer access panelbővítményének hibaelhárítási útmutatóját a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) diagnosztikai eszköz eléréséhez, és lépésről lépésre az IE bővítmény ének konfigurálásához.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>A jelszó egyszeri bejelentkezésének konfigurálása nem katalógusalkalmazásokhoz

Egy alkalmazás konfigurálásához az Azure AD-gyűjteményben meg kell:

-   [Nem katalógusalkalmazás hozzáadása](#add-a-non-gallery-application)

-   [Az alkalmazás konfigurálása egyszeri bejelentkezés jelszóhoz](#configure-the-application-for-password-single-sign-on)

-   [Felhasználók hozzárendelése az alkalmazáshoz](#assign-users-to-the-application)

### <a name="add-a-non-gallery-application"></a>Nem katalógusalkalmazás hozzáadása

Ha alkalmazást szeretne hozzáadni az Azure AD-galériából, kövesse az alábbi lépéseket:

1.  Nyissa meg az [Azure-portált,](https://portal.azure.com) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5.  kattintson a Vállalati **alkalmazások** ablaktábla jobb felső sarkában található **Hozzáadás** gombra.

6.  kattintson **a Nem galéria alkalmazás elemre.**

7.  Írja be az alkalmazás nevét a **Név** mezőbe. Válassza a **Hozzáadás lehetőséget.**

Rövid idő elteltével láthatja az alkalmazás konfigurációs ablaktábláját.

### <a name="configure-the-application-for-password-single-sign-on"></a>Az alkalmazás konfigurálása egyszeri bejelentkezés jelszóhoz

Az alkalmazás egyszeri bejelentkezésének konfigurálásához kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. Válassza ki az egyszeri bejelentkezést konfigurálni kívánt alkalmazást

7. Miután az alkalmazás betöltődik, kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjéből.

8. Válassza ki a **jelszóalapú bejelentkezés módot.**

9. Adja meg a **bejelentkezési URL-címet**. Ez az az URL-cím, ahová a felhasználók beadják a felhasználónevüket és jelszavukat, hogy bejelentkezhessenek. Győződjön meg arról, hogy a bejelentkezési mezők láthatók az URL-címen.

10. Felhasználók hozzárendelése az alkalmazáshoz.

11. Ezenkívül a felhasználó nevében is megadhat hitelesítő adatokat, ha kiválasztja a felhasználók sorait, majd a **Hitelesítő adatok frissítése** elemre kattint, és a felhasználók nevében megadja a felhasználónevet és a jelszót. Ellenkező esetben a rendszer felszólítja a felhasználókat, hogy az indításkor maguk adják meg a hitelesítő adatokat.

### <a name="assign-users-to-the-application"></a>Felhasználók hozzárendelése az alkalmazáshoz

Ha egy vagy több felhasználót közvetlenül szeretne egy alkalmazáshoz rendelni, kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. A listából jelölje ki azt az alkalmazást, amelyhez felhasználót szeretne rendelni.

7. Miután az alkalmazás betöltődik, kattintson az alkalmazás bal oldali navigációs menüjének **Felhasználók és csoportok** parancsára.

8. Kattintson a **Hozzáadás** gombra a **Felhasználók és csoportok** lista tetején a Hozzárendelés **hozzáadása** ablaktábla megnyitásához.

9. kattintson a **Felhasználók és csoportok** választóra a Hozzárendelés **hozzáadása** ablaktáblán.

10. Írja be annak a felhasználónak a **teljes nevét** vagy **e-mail címét,** akit szeretne hozzárendelni a **Keresés név vagy e-mail cím** alapján keresőmezőbe.

11. A **jelölőnégyzet**felfedéséhez mutasson a **felhasználó** fölé a listában. Kattintson a felhasználó profilfotója vagy emblémája melletti jelölőnégyzetre, ha hozzá szeretné adni a felhasználót a **Kijelölt** listához.

12. **Nem kötelező:** Ha **egynél több felhasználót**szeretne felvenni, írja be a másik **teljes nevet** vagy **e-mail címet** a Keresés név vagy **e-mail cím** alapján keresőmezőbe, és kattintson a jelölőnégyzetre, ha hozzá szeretné adni a **felhasználót** a Kiválasztott listához.

13. Ha befejezte a felhasználók kiválasztását, a **Kijelölés gombra** kattintva hozzáadhatja őket az alkalmazáshoz rendelendő felhasználók és csoportok listájához.

14. **Nem kötelező:** kattintson a Hozzárendelés **hozzáadása** ablaktábla **Szerepkör kiválasztása** kijelölő gombra a kijelölt felhasználókhoz rendelő szerepkör kiválasztásához.

15. Kattintson a **Hozzárendelés** gombra az alkalmazás kijelölt felhasználókhoz rendeléséhez.

Rövid idő elteltével a kiválasztott felhasználók elindíthatják ezeket az alkalmazásokat a Hozzáférési panelen.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Ha ezek a hibaelhárítási lépések nem oldják meg a problémát

nyisson meg egy támogatási jegyet a következő információkkal, ha azok rendelkezésre állnak:

-   Korrelációs hiba azonosítója

-   UPN (felhasználói e-mail cím)

-   TenantID

-   Böngésző típusa

-   Időzóna és idő/időkeret hiba esetén

-   Hegedűs nyomok

## <a name="next-steps"></a>További lépések
[Egyszeri bejelentkezés az alkalmazásokba alkalmazásproxyval](application-proxy-configure-single-sign-on-with-kcd.md)

