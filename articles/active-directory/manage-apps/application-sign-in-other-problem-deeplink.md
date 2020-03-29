---
title: Problémák bejelentkezés egy alkalmazás segítségével deeplink | Microsoft dokumentumok
description: Alkalmazás deeplink URL-címről való elérésével kapcsolatos problémák elhárítása az Azure AD használatával
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
ms.openlocfilehash: 44825f32a13db0a221252c042dc9f23ec43a9c8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825416"
---
# <a name="problems-signing-in-to-an-application-using-a-deeplink"></a>Problémák az alkalmazásba deeplink használatával történő bejelentkezéssel kapcsolatban

A Hozzáférési panel egy webalapú portál, amely lehetővé teszi, hogy az Azure Active Directoryban (Azure AD) munkahelyi vagy iskolai fiókkal rendelkező felhasználó megtekintse és elindítsa azokat a felhőalapú alkalmazásokat, amelyekhez az Azure AD-rendszergazda hozzáférést biztosított számukra. 

Ezek az alkalmazások a felhasználó nevében vannak konfigurálva az Azure AD portálon. Az alkalmazást megfelelően kell konfigurálni, és hozzá kell rendelni ahhoz a felhasználóhoz vagy csoporthoz, amelynek a felhasználó tagja ahhoz, hogy az alkalmazást a Hozzáférési panelen láthassa.

A mélyhivatkozások vagy a felhasználói hozzáférési URL-címek olyan hivatkozások, amelyeket a felhasználók a jelszó-Egyszeri bejelentkezés alkalmazások eléréséhez használhatnak közvetlenül a böngésző URL-sávjairól. A hivatkozásra való navigálással a felhasználók automatikusan bejelentkeznek az alkalmazásba anélkül, hogy először a Hozzáférési panelre kellene lépniük. Ez ugyanaz a hivatkozás, amelyet a felhasználók az Office 365 alkalmazásindítójából való hozzáféréshez használnak.

## <a name="general-issues-to-check-first"></a>Általános kérdések, amelyeket először ellenőrizni kell

-   Győződjön meg arról, hogy olyan **böngészőt** használ, amely megfelel a Hozzáférési panel minimális követelményeinek.

-   Győződjön meg arról, hogy a felhasználó böngészője hozzáadta az alkalmazás URL-címét a **megbízható helyekhez.**

-   Ellenőrizze, hogy az alkalmazás megfelelően **van-e konfigurálva.**

-   Győződjön meg arról, hogy a felhasználó fiókja **engedélyezve** van a bejelentkezéshez.

-   Győződjön meg arról, hogy a felhasználó fiókja **nincs zárolva.**

-   Győződjön meg arról, hogy a felhasználó **jelszava nem járt le vagy nem feledje el.**

-   Győződjön meg arról, hogy a **többtényezős hitelesítés** nem blokkolja a felhasználói hozzáférést.

-   Győződjön meg arról, hogy a **feltételes hozzáférési szabályzat** vagy az **identitásvédelmi** házirend nem blokkolja a felhasználói hozzáférést.

-   Győződjön meg arról, hogy a felhasználó **hitelesítési kapcsolattartási adatai** naprakészek a többtényezős hitelesítési vagy feltételes hozzáférési házirendek érvényesítéséhez.

-   Győződjön meg róla, hogy megpróbálja törölni a böngésző cookie-kat, és megpróbál újra bejelentkezni.

## <a name="checking-the-deeplink"></a>A deeplink ellenőrzése

Annak ellenőrzéséhez, hogy a megfelelő mélyhivatkozással rendelkezik-e, kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

7. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

8. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

9. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

10. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

    * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

11. Válassza ki azt az alkalmazást, amelynek a deeplink ellenőrzését ellenőrizni szeretné.

12. Keresse meg a **User Access URL-címét.** A mélyhivatkozásnak meg kell egyeznie ezzel az URL-címmel.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Az Access Panel böngészőbővítményének telepítése

Az Access Panel böngészőbővítményének telepítéséhez hajtsa végre az alábbi lépéseket:

1.  Nyissa meg a [hozzáférési panelt](https://myapps.microsoft.com) az egyik támogatott böngészőben, és jelentkezzen be **felhasználóként** az Azure AD-ben.

2.  kattintson egy **jelszó-Egyszeri bejelentkezés alkalmazásra** a Hozzáférési panelen.

3.  A szoftver telepítését kérő üzenetben válassza a **Telepítés most**lehetőséget.

4.  A böngésző alapján a letöltési linkre irányítjuk. **Adja hozzá** a bővítményt a böngészőhöz.

5.  Ha a böngésző kérdezi, válassza a **bővítmény engedélyezése** vagy **engedélyezése** lehetőséget.

6.  A telepítés után **indítsa újra** a böngészőmunkamenetet.

7.  Jelentkezzen be a hozzáférési panelen, és nézze meg, hogy **el tudja-e indítani** a jelszó-Egyszeri bejelentkezés alkalmazásokat

A Chrome és a Firefox bővítményét az alábbi közvetlen linkekről is letöltheti:

-   [Chrome-hozzáférési panel bővítmény](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox access panel bővítmény](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Az Azure AD-katalógusalkalmazás hoz való egyszeri bejelentkezés jelszó konfigurálása

Ha az Azure AD-katalógusból szeretne konfigurálni egy alkalmazást, a következőket kell tennie:

-   [Alkalmazás hozzáadása az Azure AD-gyűjteményből](#add-an-application-from-the-azure-ad-gallery)

-   [Az alkalmazás konfigurálása egyszeri bejelentkezés jelszóhoz](#configure-the-application-for-password-single-sign-on)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Alkalmazás hozzáadása az Azure AD-gyűjteményből

Ha egy alkalmazást szeretne hozzáadni az Azure AD-galériából, kövesse az alábbi lépéseket:

1.  Nyissa meg az [Azure-portált,](https://portal.azure.com) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5.  kattintson a Vállalati **alkalmazások** ablaktábla jobb felső sarkában található **Hozzáadás** gombra.

6.  A **Enter a name** Katalógus **hozzáadás a szakaszban** írja be az alkalmazás nevét.

7.  Válassza ki azt az alkalmazást, amelyet egyszeri bejelentkezéshez szeretne konfigurálni.

8.  Az alkalmazás hozzáadása előtt a **Név** mezőben módosíthatja a nevét.

9.  Az alkalmazás hozzáadásához kattintson a **Hozzáadás**gombra.

Rövid idő elteltével láthatja az alkalmazás konfigurációs ablaktábláját.

### <a name="configure-the-application-for-password-single-sign-on"></a>Az alkalmazás konfigurálása egyszeri bejelentkezés jelszóhoz

Az alkalmazások egyszeri bejelentkezési beállításához hajtsa végre az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. Jelölje ki az egyszeri bejelentkezést konfigurálni kívánt alkalmazást.

7. Miután az alkalmazás betöltődik, kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjéből.

8. Válassza ki a **jelszóalapú bejelentkezés módot.**

9. [Felhasználók hozzárendelése az alkalmazáshoz](#how-to-assign-a-user-to-an-application-directly).

10. Ezenkívül a felhasználó nevében is megadhat hitelesítő adatokat, ha kiválasztja a felhasználók sorait, majd a **Hitelesítő adatok frissítése** elemre kattint, és a felhasználók nevében megadja a felhasználónevet és a jelszót. Ellenkező esetben a rendszer felszólítja a felhasználókat, hogy az indításkor maguk adják meg a hitelesítő adatokat.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>A jelszó egyszeri bejelentkezésének konfigurálása nem katalógusalkalmazásokhoz

Ha az Azure AD-katalógusból szeretne konfigurálni egy alkalmazást, a következőket kell tennie:

-   [Nem katalógusalkalmazás hozzáadása](#add-a-non-gallery-application)

-   [Az alkalmazás konfigurálása egyszeri bejelentkezés jelszóhoz](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Nem katalógusalkalmazás hozzáadása

Ha egy alkalmazást szeretne hozzáadni az Azure AD-galériából, kövesse az alábbi lépéseket:

1.  Nyissa meg az [Azure-portált,](https://portal.azure.com) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5.  kattintson a Vállalati **alkalmazások** ablaktábla jobb felső sarkában található **Hozzáadás** gombra.

6.  kattintson **a Nem galéria alkalmazás elemre.**

7.  Írja be az alkalmazás nevét a **Név** mezőbe. Válassza a **Hozzáadás lehetőséget.**

Rövid idő elteltével láthatja az alkalmazás konfigurációs ablaktábláját.

### <a name="configure-the-application-for-password-single-sign-on"></a>Az alkalmazás konfigurálása egyszeri bejelentkezés jelszóhoz

Az alkalmazások egyszeri bejelentkezési beállításához hajtsa végre az alábbi lépéseket:

1.  Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5.  Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

    1.  Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6.  Jelölje ki az egyszeri bejelentkezést konfigurálni kívánt alkalmazást.

7.  Miután az alkalmazás betöltődik, kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjéből.

8.  Válassza ki a **jelszóalapú bejelentkezés módot.**

9.  Adja meg a **bejelentkezési URL-címet**, azt az URL-címet, amelyben a felhasználók beírják a felhasználónevüket és a jelszavukat a bejelentkezéshez. Győződjön meg arról, hogy a bejelentkezési mezők láthatók az URL-címen.

10. Felhasználók hozzárendelése az alkalmazáshoz.

11. Ezenkívül a felhasználó nevében is megadhat hitelesítő adatokat, ha kiválasztja a felhasználók sorait, majd a **Hitelesítő adatok frissítése** elemre kattint, és a felhasználók nevében megadja a felhasználónevet és a jelszót. Ellenkező esetben a rendszer felszólítja a felhasználókat, hogy az indításkor maguk adják meg a hitelesítő adatokat.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Felhasználó közvetlen hozzárendelése egy alkalmazáshoz

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

11. A **jelölőnégyzet**felfedéséhez mutasson a **felhasználó** fölé a listában. Ha hozzá szeretné adni a felhasználót a **Kijelölt** listához, kattintson a felhasználó profilfotója vagy emblémája melletti jelölőnégyzetre.

12. **Nem kötelező:** Ha **egynél több felhasználót**szeretne felvenni, írja be a másik **teljes nevet** vagy **e-mail címet** a Keresés név vagy **e-mail cím** alapján keresőmezőbe, és kattintson a jelölőnégyzetre, ha hozzá szeretné adni a **felhasználót** a Kiválasztott listához.

13. Ha befejezte a felhasználók kiválasztását, a **Kijelölés gombra** kattintva hozzáadhatja őket az alkalmazáshoz rendelendő felhasználók és csoportok listájához.

14. **Nem kötelező:** kattintson a Hozzárendelés **hozzáadása** ablaktábla **Szerepkör kiválasztása** kijelölő gombra a kijelölt felhasználókhoz rendelő szerepkör kiválasztásához.

15. Kattintson a **Hozzárendelés** gombra az alkalmazás kijelölt felhasználókhoz rendeléséhez.

Rövid idő elteltével a kiválasztott felhasználók elindíthatják ezeket az alkalmazásokat a Hozzáférési panelen.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Ha ezek a hibaelhárítási lépések nem oldják meg a problémát. 

nyisson meg egy támogatási jegyet a következő információkkal, ha azok rendelkezésre állnak:

-   Korrelációs hiba azonosítója

-   UPN (felhasználói e-mail cím)

-   TenantID

-   Böngésző típusa

-   Időzóna és idő/időkeret hiba esetén

-   Hegedűs nyomok

## <a name="next-steps"></a>További lépések
[Egyszeri bejelentkezés az alkalmazásokba alkalmazásproxyval](application-proxy-configure-single-sign-on-with-kcd.md)
